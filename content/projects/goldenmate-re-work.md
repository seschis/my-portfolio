---
title: 'GoldenMate BMS Reverse Engineering'
date: 2026-10-01T18:09:42-04:00
summary: 'Tearing down a GoldenMate battery management system: PCB analysis, firmware extraction over SWD, and Ghidra on an ARM Cortex-M0.'
tags: ['reverse-engineering', 'embedded', 'ghidra', 'hardware']
repo: 'seschis/goldenmate-re-work'
---

{{< github-card >}}

## TL;DR

A hardware reverse-engineering project on the GoldenMate BMS (Battery Management System). The repo documents the full path: annotated PCB analysis, firmware extraction via an ST-LINK SWD probe (with a custom OpenOCD build to support the Fudan Micro FM33LC026N Cortex-M0), decompilation and analysis in Ghidra, and signal tracing across the BMS ASIC, EEPROM, and RS485 interface.

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
If so, uncomment the github-readme shortcode line below.
*/%}}
