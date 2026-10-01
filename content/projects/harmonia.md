---
title: 'Harmonia'
date: 2026-09-23
summary: 'Multi-LLM triage for security scanner findings: sandboxed code reading, majority vote, tie adjudication, and CVSS 4.0 scoring.'
tags: ['go', 'security', 'llm']
repo: 'seschis/harmonia'
draft: true
---

{{< github-card >}}

## TL;DR

Harmonia is a Go CLI that triages security scanner findings using multiple LLMs: each model reads the flagged code in a sandbox, votes, ties are adjudicated, and results are scored with CVSS 4.0.

## The problem

<!-- TODO: why did you build this? what was wrong with manual triage / single-model scoring? -->

## Approach

<!-- TODO: architecture overview — how the sandboxed code reading, voting, and adjudication pipeline fit together. Diagram or key snippet. -->

## Decisions & tradeoffs

<!-- TODO: e.g. why a CLI, why these models, why CVSS 4.0, what the sandbox protects against and what it doesn't. -->

## Results

<!-- TODO: e.g. precision/recall vs scanner baseline, triage time saved, findings processed. -->

## What I'd do differently

<!-- TODO -->
