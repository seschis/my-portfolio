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

The gap, itch, or incident that motivated the project.

## Approach

Architecture, key design choices, diagrams, or code snippets.

## Decisions & tradeoffs

Why you picked X over Y — the part that makes a portfolio stand out.

## Results

Numbers where possible: latency, throughput, users, hours saved.

## What I'd do differently

{{%/*
MODE B: embed the repo's live README instead of (or in addition to) your own narrative.
Requires a README.md in the repo.
CAUTION: this couples the page to the repo's README — future edits there change this
page. The build detects references that won't resolve on this site (relative links/
images) and warns in the CI log, but the deploy still ships. Prefer MODE A unless the
repo's README is self-contained (absolute asset URLs).
If so, add this shortcode on its own line:

    {{< github-readme >}}
*/%}}
