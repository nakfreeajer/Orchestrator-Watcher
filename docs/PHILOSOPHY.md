# Philosophy

## Core idea

Orchestrator Watcher is designed around **asymmetric model utilization**.

A high-capability ChatGPT model acts as the Project Architect and does most of the expensive reasoning. Codex is used as a bounded Executor only when repository inspection, implementation, deterministic validation, or evidence collection is required.

The optimization target is not the maximum number of agents. It is the minimum amount of execution needed to produce a correct, coherent, independently verified result.

## Reasoning–Execution Separation Principle

> Spend reasoning where reasoning capacity is abundant; spend Executor capacity only where repository execution is required.

The Project Architect should normally:

- understand the whole project;
- preserve long-term architecture and governance context;
- reason about business and technical tradeoffs;
- inspect authoritative evidence directly when available;
- decide the smallest still-open problem;
- narrow the repository scope before dispatch;
- write one complete bounded Executor instruction;
- independently verify Executor evidence;
- maintain project governance and handover context;
- ask the human only when real authority is missing.

The Executor should normally:

- inspect the exact requested scope;
- implement only the authorized change;
- run the specified validation;
- preserve accepted invariants;
- return concise evidence or a precise blocker;
- stop.

Canonical Executor pattern:

```text
audit -> code -> test -> report
```

## Human control

The final human authority remains above every model and every automation component.

The AI supplies reasoning and labor. It does not own the product.

The Project Architect may recommend, analyze, and define bounded next actions, but must not invent human business authority when the decision genuinely belongs to the human.

## Why not many autonomous agents by default?

Multiple Executors can increase throughput when work is truly independent. Multiple competing sources of architecture and product authority increase context divergence, duplicated reasoning, and integration risk.

Therefore:

> **Parallelism is optional. Authority is not.**

A future multi-Executor mode should retain one Project Architect and one acceptance path.

## Prompt-efficiency contract

Before dispatching an Executor task, the Project Architect should be able to answer:

1. Why does Executor need to do this?
2. What uncertainty cannot be resolved by Architect-accessible evidence?
3. What is the smallest repository scope required?
4. What exact mutation is authorized?
5. What must remain unchanged?
6. What deterministic validation is required?
7. What exact evidence must come back?

If these are unclear, the task is probably not ready for Executor dispatch.

## Quality objective

Enterprise-grade engineering quality should come from:

- explicit authority;
- coherent architecture;
- bounded mutation;
- deterministic validation;
- independent verification;
- durable evidence;
- exactly-once workflow semantics;
- non-regression governance;
- reproducible recovery.

It should not depend on maximizing AI compute consumption.

## Cost-efficiency objective

Useful efficiency indicators include:

- duplicate implementation count;
- transport-caused reruns;
- unnecessary broad audits;
- Executor retries;
- average files touched per task;
- successful first-pass bounded tasks;
- same-task HUMAN_REQUIRED continuations;
- exactly-once result deliveries.

Ideal direction:

```text
transport-caused reruns = 0
duplicate implementation = 0
broad exploratory Executor work -> rare
bounded first-pass success -> high
Architect reasoning quality -> high
```
