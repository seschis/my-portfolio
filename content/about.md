---
title: 'About'
summary: 'Building tools, solving puzzles, and going deep from the kernel up to the application.'
ShowToc: false
ShowReadingTime: false
---

I like building tools that solve real problems, and I like the hard puzzles that come with them. Over 25 years that has taken me through cybersecurity, embedded systems, kernel and driver development, reverse engineering, high-performance systems, and deep learning. I've worked at every layer from the kernel to the application, and the problems I enjoy most are the ones where you need to understand several of those layers at once.

## How I got here

I started at Booz Allen writing Linux kernel drivers and doing 802.11 security research. At G2 I built hypervisor introspection for malware analysis and reverse engineered firmware on x86, ST10, and ARM. At Alithix I led a team that wrote a Type-1 hypervisor from scratch in C, and built the distributed test infrastructure that kept it and our embedded work honest.

Since 2018 I've been at Contrast Security, now as a Distinguished Engineer. The puzzles there have ranged from a Linux kernel module that cut Java agent installation friction by 90%, to a petabyte-scale S3 data lake on Iceberg, to a Kafka/Flink event-driven architecture.

## What I'm working on now

All of that led me to LLMs and agents, and to finding out what they can actually do on security problems. Most recently I built an agentic security-testing tool in Go. It stands up arbitrary applications, discovers their services, generates the container infrastructure, injects the Contrast runtime agent, and drives a multi-turn LLM loop until every API endpoint is proven reachable. Making it dependable meant per-run spend caps, token-budget scheduling, stuck-loop detection, and an eval harness that benchmarks agent behavior against OWASP vulnerable-application targets.

Knowing the whole stack changes how I look at agents. Prompt injection, nondeterministic output, and runaway cost are the obvious failure modes. The interesting one is an agent that is confidently wrong, and catching it usually means going a layer deeper than the agent did.

## Side projects

I build and publish on my own time too. [Harmonia]({{< relref "projects/harmonia.md" >}}) is a multi-LLM triage engine where a panel of models votes on security scanner findings and scores them with CVSS 4.0. [dbi-research]({{< relref "projects/dbi-research.md" >}}) tests whether Intel Pin can taint-track data through a compiled Go binary with no source-level hooks. For [GoldenMate]({{< relref "projects/goldenmate-re-work.md" >}}) I patched OpenOCD to debug an obscure Cortex-M0 MCU in a commercial battery management system.

I also [write]({{< relref "blog" >}}) about AI and code security, including where AI coding agents get security right by default and why AI security evaluations need clinical-trial blinding rigor.
