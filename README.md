# Orchestrator Watcher

**ChatGPT thinks. Codex executes. Humans retain authority.**

Orchestrator Watcher is a project-neutral AI development orchestration system built around an **Architect-heavy / Executor-light** model.

The design uses a high-capability ChatGPT conversation as the long-lived Project Architect for system reasoning, planning, verification, governance, and human discussion. Codex is deliberately kept narrow: inspect the requested repository scope, implement a bounded change, validate it, and report evidence.

The Orchestrator sits between them as durable transport and workflow supervision. It does not become a software architect or business authority.

## Why this exists

Many agentic development systems increase capability by adding more autonomous agents and more model spend. Orchestrator Watcher takes a different approach:

> **Enterprise-grade engineering quality should come from architecture, evidence, validation, and authority discipline—not from maximizing AI compute consumption.**

The central efficiency principle is:

> **Spend reasoning where reasoning capacity is abundant; spend Executor capacity only where repository execution is required.**

Instead of asking Codex to repeatedly rediscover the entire project, decide architecture, plan milestones, and then code, the Project Architect does the reasoning first and sends Codex one tightly bounded task.

Typical Executor responsibility:

`audit -> code -> test -> report`

Typical Project Architect responsibility:

`understand -> reason -> decide -> bound -> verify -> preserve`

## Authority model

```text
Final Human Authority
        |
        v
Project Architect (ChatGPT)
        |
        v
Orchestrator Watcher
        |
        v
Codex Executor
        |
        v
Orchestrator Watcher
        |
        v
Project Architect verification
```

The human remains final authority. The Project Architect owns project meaning. The Executor performs bounded repository work. The Orchestrator only preserves the workflow.

## Core invariant

```text
recover where stopped
-> run one bounded task
-> capture one result
-> deliver once
-> wait for Architect decision
-> stage one next action
-> repeat
```

## Design goals

- project-neutral runtime;
- one authoritative Project Architect per project;
- one bounded Executor task at a time by default;
- durable task identity and recovery;
- exactly-once result delivery semantics;
- no rerunning completed project work because transport failed;
- resident `HUMAN_REQUIRED` discussion without losing workflow state;
- explicit machine-readable Architect envelope;
- project-specific Architect and Executor bootstraps;
- reproducible setup for every new project;
- strict separation between project governance and Orchestrator mechanics;
- low Executor-context waste and minimal duplicated reasoning.

## Canonical Architect envelope

Every Project Architect must understand and preserve the Orchestrator protocol. The current canonical envelope is:

```text
<ORCHESTRATOR_RESULT>
classification=ACCEPTED|BLOCKED|INCONCLUSIVE|NO_NEW_REPORT
action=EXECUTE|HUMAN_REQUIRED|STOP
taskId=<completed task id>
documentation=NOT_REQUIRED|REQUIRED|COMPLETE
promptBegin
<complete next bounded Executor prompt only when action=EXECUTE>
promptEnd
</ORCHESTRATOR_RESULT>
```

The Project Architect uses the completed task ID. The Orchestrator allocates the next sequential task ID.

Ordinary discussion is not machine execution authority.

## Production reference extraction

The universal design is being extracted from the production-mature AFFOTECH Local Orchestrator reference implementation without importing AFFOTECH business/project identity.

The accepted reference runtime checkpoint used for extraction is:

`57a3a914b35ed0c715aaaf0267ed0bd36a39cc78`

What is preserved:

- state-machine semantics;
- exactly-once result transport and reconciliation;
- restart/recovery behavior;
- resident human-decision waiting;
- discussion-pause semantics;
- browser/session ownership lessons;
- non-preemptive rollover principle;
- Project Architect / Orchestrator / Executor authority separation;
- production qualification lessons.

What is not copied into the generic core:

- AFFOTECH repository/branch/path identity;
- AFFOTECH Executor session and Architect conversation IDs;
- tenant/business rules;
- AFFOTECH-specific bootstrap/governance;
- workstation-specific paths;
- project-specific validation assumptions.

## Repository direction

This repository is intentionally independent from any one software project. Project-specific identities such as repository paths, branches, Executor sessions, Architect conversations, validation rules, and governance belong in a **project profile** and project-owned bootstrap/governance files.

The first implementation target is to extract the proven workflow into a reusable Windows-first universal runtime without redesigning the mature state-machine semantics.

See:

- `docs/PHILOSOPHY.md`
- `docs/ARCHITECTURE.md`
- `docs/PROTOCOL.md`
- `docs/NEW_PROJECT_SETUP.md`
- `docs/REFERENCE_IMPLEMENTATION_EXTRACTION.md`
- `docs/RECOVERY_RUNBOOK.md`
- `docs/PRODUCTION_LESSONS.md`
- `docs/QUALIFICATION.md`
- `docs/UNIVERSALIZATION_BACKLOG.md`
- `templates/PROJECT_ARCHITECT_BOOTSTRAP.md`
- `templates/PROJECT_EXECUTOR_BOOTSTRAP.md`
- `templates/project-profile.example.json`

## Status

**Foundation / universalization phase.**

The architecture is based on a production-mature reference workflow, but this repository starts clean and project-neutral. Project-specific runtime wiring must be extracted and qualified before this repository should be treated as a drop-in universal orchestrator.
