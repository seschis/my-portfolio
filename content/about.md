---
title: 'About'
summary: 'Twenty-five years from kernels and hypervisors to making AI agents do security work reliably.'
ShowToc: false
ShowReadingTime: false
---

The strongest security engineers I know have spent enough time at the low level (kernels, hypervisors, machine code) that when something new and powerful arrives, their first question is "where does it break, and how do we prove it doesn't?" That question has shaped twenty-five years of my career. It matters more than ever now that AI agents are writing, reviewing, and operating production software.

## What I work on now

I'm a Distinguished Engineer at Contrast Security, where I build the tooling that makes AI agents do security work reliably. Most recently I architected an agentic security-testing tool in Go. It stands up arbitrary applications, discovers their services, generates the container infrastructure, injects the Contrast runtime agent, and drives a multi-turn LLM loop until every API endpoint is proven reachable. Getting it production-ready meant per-run spend caps, token-budget scheduling, stuck-loop detection, and an eval harness that benchmarks agent behavior against OWASP vulnerable-application targets.

Before that I architected a petabyte-scale S3 data lake on Iceberg, led the design of a Kafka/Flink event-driven architecture, and cut Java agent installation friction by 90% with a Linux kernel module.

## How I got here

My career runs from the ground up. I started with kernel drivers and 802.11 security research at Booz Allen, moved to hypervisor introspection and firmware reverse engineering at G2, and wrote a Type-1 hypervisor from scratch in C at Alithix. That depth is why I can look at an LLM agent loop and see where the failure modes live. Prompt injection, nondeterministic outputs, and runaway costs are the obvious ones. The subtle one is an agent that is confidently wrong.

## Outside of work

I build and publish. [Harmonia]({{< relref "projects/harmonia.md" >}}) is a multi-LLM triage engine where a panel of models votes on security scanner findings and scores them with CVSS 4.0. [dbi-research]({{< relref "projects/dbi-research.md" >}}) tests whether Intel Pin can taint-track data through a compiled Go binary with no source-level hooks. For [GoldenMate]({{< relref "projects/goldenmate-re-work.md" >}}) I patched OpenOCD to debug an obscure Cortex-M0 MCU in a commercial battery management system.

I also [write]({{< relref "blog" >}}) about the governance of AI code security, including where AI coding agents get security right by default and why AI security evaluations need clinical-trial blinding rigor.

The questions I keep coming back to are where an LLM can be trusted, how you prove it, how you bound its cost, and how you make the whole thing production-grade.
