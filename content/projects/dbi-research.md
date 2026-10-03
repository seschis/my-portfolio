---
title: 'Tracing Tainted Data in Go Binaries with Intel Pin'
date: 2026-10-01T19:50:06-04:00
summary: 'A research proof of concept: tracking tainted string data through a compiled Go binary at the machine-code level using Intel Pin, with no source-level hooks.'
tags: ['security', 'reverse-engineering', 'go', 'binary-instrumentation']
repo: 'seschis/dbi-research'
---

{{< github-card >}}

## TL;DR

A research proof of concept asking whether Intel Pin can observe a source-to-sink string data flow in a Go-compiled binary without modifying or recompiling the target. A small Go fixture is compiled normally; Intel Pin instruments the machine code, Pintools mark the `read(2)` buffer as tainted, a Boolean taint propagates through x86-64 memory and registers via shadow state, and the arguments to Go's `fmt.Fprintf` are inspected to recover the string and observe where a complete analyzer would report the flow. It deliberately stops one step short of joining the sink argument to the shadow state — establishing feasibility and exposing the hard engineering problems of binary-level source-to-sink analysis for Go rather than claiming a finished detector.

## The problem

Go sits between your input and the machine instructions — the runtime, goroutines, stack growth, and a calling ABI that changed under the hood (stack-based before Go 1.17, register-based after). I wanted to know how hard binary-level source-to-sink taint tracking is for a Go binary: can Intel Pin observe a tainted string flowing from `read(2)` to a security-relevant function, with no source-level hooks and no recompilation?

## Approach

A small Go fixture reads `data.txt`, turns the bytes into a string, and uses that string as the `fmt.Printf` format string. Intel Pin instruments the compiled binary in three stages:

1. **Source** — a syscall-entry callback marks the `read(2)` destination range as tainted.
2. **Propagation** — three pre-instruction routines (`ReadMem`, `WriteMem`, `spreadRegTaint`) move a Boolean taint through x86-64 memory and registers via shadow state, with `[TAINT]`/`[READ]`/`[WRITE]`/`[SPREAD]`/`[FOLLOW]` trace labels.
3. **Sink** — symbol lookup finds `fmt.Fprintf` in the loaded image and hooks its entry; the callback decodes the Go string ABI (`Data` pointer + `Len` at recorded stack offsets under the older stack-based amd64 ABI) and prints the bytes.

CI (GitHub Actions, ubuntu-24.04) pins Intel Pin 4.3.1 (SHA-256 verified) and Go 1.16.15, builds both Pintools, verifies `fmt.Fprintf` is in the symbol table, and runs the fixture directly and under each tool.

## Decisions & tradeoffs

- **Deliberately stop one step short of the finding.** The sink hook prints the string but never queries taint state, so the repo demonstrates the architecture and the hard problems instead of claiming a detector.
- **The old Go is deliberate.** The recorded sink offsets assume the stack-based ABI Go 1.17 replaced on amd64; pinning Go 1.16.15 keeps the historical experiment valid. The compiler version is part of the experiment.
- **Coarse, documented limitations over hidden ones.** Every `read(2)` after the first is tainted regardless of file descriptor, taint lives in unsynchronized global `std::list`s, and the sink callback dereferences target addresses without `PIN_SafeCopy`. Each of these is in the repo's limitations table rather than a surprise.
- **A Go format string, not a C printf hole.** A user-controlled Go format string is not a memory-corruption vulnerability; it's a useful, observable sink candidate, and the repo says so explicitly.

## Results

A working end-to-end architecture at the compiled-binary level: syscall-sourced taint, shadow-state propagation across loads, stores, and register moves, and a symbol-based sink hook that decodes a Go string under the target's ABI. The checked-in tool can emit evidence for the first three of the five conditions a convincing end-to-end result needs (source selected, taint survives the `[]byte`→`string` copies, sink reached). It cannot claim that taint reached the sink — the final correlation is unimplemented — and no traces are committed. The deliverable is feasibility plus a documented catalog of failure modes, reproducible in CI.

## What I'd do differently

- Define a testable vertical slice first: one positive fixture and one negative control as an executable correctness contract, before building more machinery.
- Model current Go (register-based `ABIInternal`) instead of the legacy stack ABI, and treat the toolchain matrix (Go versions × Pin releases) as a first-class test surface.
- Replace the global lists with per-thread, byte-addressable shadow memory, and bound every target read with `PIN_SafeCopy`.
- Make one narrow flow defensible — golden traces, measured false positives/negatives, overhead — before adding more sources or sinks.
