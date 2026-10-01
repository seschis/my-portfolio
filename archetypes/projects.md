---
title: '{{ replace .File.ContentBaseName "-" " " | title }}'
date: {{ .Date }}
summary: 'One-line hook shown under the title in the projects list'
tags: []
repo: 'seschis/your-repo'
draft: true
---

{{< github-card >}}

## TL;DR

Two or three sentences: what it does and why you built it.

## The problem

The gap, itch, or incident that motivated the project.

## Approach

Architecture, key design choices, diagrams, or code snippets.

## Decisions & tradeoffs

Why you picked X over Y — the part that makes a portfolio stand out.

## Results

Numbers where possible: latency, throughput, users, hours saved.

## What I'd do differently

{{% /*
MODE B: embed the repo's live README instead of (or in addition to) your own narrative.
Requires a README.md in the repo.
CAUTION: this couples the page to the repo's README — future edits there change this
page. The build detects references that won't resolve on this site (relative links/
images) and warns in the CI log, but the deploy still ships. Prefer MODE A unless the
repo's README is self-contained (absolute asset URLs).
If so, uncomment the line below:

{{< github-readme >}}
*/ -%}}
