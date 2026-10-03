---
title: 'Harmonia'
date: 2026-09-23
summary: 'Multi-LLM triage for security scanner findings: sandboxed code reading, majority vote, tie adjudication, and CVSS 4.0 scoring.'
tags: ['go', 'security', 'llm']
repo: 'seschis/harmonia'
---

{{< github-card >}}

## TL;DR

Harmonia is a Go CLI that triages security scanner findings using multiple LLMs: each model reads the flagged code in a sandbox, votes, ties are adjudicated, and results are scored with CVSS 4.0.

## The problem

A single model's verdict on a scanner finding is unstable: it depends on the model, the prompt, and the order the evidence was read. I wanted triage that's steadier than one model's opinion — an ensemble of models voting on each finding, ties broken by a panel of judges — plus CVSS 4.0 scoring, and something that runs entirely on local files: no backend, no LLM proxy, no telemetry.

## Approach

`harmonia` is a Go CLI with a pipeline:

1. **Ingest** — SARIF, JSON, CSV, Markdown, and XLSX, normalized into findings.
2. **Context** — models read the flagged source through a tool-calling agent loop sandboxed to `--srcroot`, with repeatable `--context-dir` roots for cross-repo context and a `TRIAGE_CONTEXT.md` map. Two strategies: `shared` (one explorer writes a brief every voter reads — cheaper) or `per-model` (each model crawls the repo itself).
3. **Vote** — up to four preset models (Claude, Gemini, OpenAI, Azure OpenAI), plus custom `ModelSpec`s speaking the openai or anthropic wire protocol (a local vLLM server is the usual case), judge each finding independently, each with a CVSS 4.0 score.
4. **Resolve** — a category-majority vote (real / not-real / needs-more-context); on a split, the most conservative verdict in the winning category stands, so a finding is treated as more real, never as safe. Ties convene a judge panel — persona-driven reviewers (`adjudicator`, `strict`, `business`, `codeflow`, or custom prompt files) — combined with the same vote. `--analyst` lenses apply the same persona idea to the voters, restoring a real ensemble when you only have one model's credentials.
5. **Report** — `results.json` (a `harmoniaAnalysis` block per finding), a human-readable `report.md`, per-finding agentic transcripts (JSONL, every tool call with args and result), and a live TUI with cost accounting.

`harmonia context export/import` moves a whole context directory around as a single `.tar.gz` manifest (repo origin + pinned commit SHA, shallow re-clone on import) so CI and teammates can reproduce the same context set.

## Decisions & tradeoffs

- **The tiebreak bias runs toward "real."** When the vote splits, `CONFIRMED_REAL` beats `LIKELY_REAL` and `UNLIKELY` beats `NOT_EXPLOITABLE`. Clearing a live vulnerability is the expensive outcome, so the ensemble errs on the side of flagging.
- **Personas are the primary axis of diversity, not models.** Several judges or analysts on the *same* model still reach different verdicts — that's what makes a single-provider setup, or a local vLLM, a real ensemble instead of one verdict with extra steps.
- **Models are data, not code.** Every model is a `ModelSpec` (protocol, endpoint, id, credentials, context window, price) built through one factory, so adding a local model is configuration, not code. Presets are overridable per field with flag > file > preset precedence.
- **The sandbox is read-only file access scoped to the roots you pass.** Models get a short inventory and open files on demand, so a large context set is cheap to add. The tradeoff to know: the flagged source reaches the model provider. A local vLLM or Amazon Bedrock (`--bedrock`) are the levers for keeping that inside your own infrastructure.

## Results

Everything in the design doc is implemented: ingest for all five formats, the agentic tool loop, the quad-model vote and judge-panel tie-breaking, both report writers, context bundles, and goreleaser publishing to GitHub releases (also available via Homebrew). A representative demo run — two findings against the sample app, Claude plus two analyst lenses, shared context — produced a CONFIRMED_REAL command injection (CVSS 4.0 10.0, with the exploit scenario and a considered counter-argument) and a NOT_EXPLOITABLE SQL injection (the anchored allowlist rejects every SQL metacharacter), at a total run cost of $0.33. Known gap: Gemini's `thinking_budget` is passed but not honored by langchaingo v0.1.14.

## What I'd do differently

- I'd close the Gemini `thinking_budget` gap first — wait for langchaingo to honor it or drive the parameter on the provider side — so every preset runs at the effort level requested.
- I'd invest in cross-repo context auto-discovery. `--context-dir` is reachable but not discoverable today, so a human-written `TRIAGE_CONTEXT.md` map is a burden I'd rather not leave to the operator.
- I'd make the built-in demo multi-voter by default; it would show the ensemble's value better than one model with analyst lenses.
