# Universalization Backlog

## Goal

Turn the production-mature AFFOTECH reference implementation into a project-neutral runtime without redesigning the proven workflow semantics.

The migration target is configuration extraction, isolation and qualification.

## Phase 1 — Project identity extraction

Introduce one project profile consumed by the runtime.

Move these kinds of values out of generic code:

- project name/id;
- local project repository path;
- remote repository;
- authoritative branch;
- Executor logical session ID;
- Architect conversation/browser endpoint;
- worktree root;
- state root;
- Project Architect bootstrap path;
- Project Executor bootstrap path;
- optional project-specific validation adapters.

The generic runtime must not contain AFFOTECH repository names, branch names, business rules, tenant IDs or milestone semantics.

## Phase 2 — Project-scoped state and paths

Define deterministic namespaces such as:

```text
<orchestrator-root>/projects/<project-id>/state/
<orchestrator-root>/projects/<project-id>/worktrees/
<orchestrator-root>/projects/<project-id>/results/
```

Requirements:

- no path collisions across projects;
- task IDs cannot be consumed by another project;
- recovery reads only the selected project namespace;
- watcher instance locking is defined clearly per project or per runtime mode.

## Phase 3 — Generic runtime naming

Remove project-specific names from:

- constants;
- logger names where practical;
- class/function names where they imply AFFOTECH semantics;
- tests and fixtures;
- environment variable names;
- output/status text.

Do not rename aggressively if doing so increases regression risk. Behavior preservation is more important than cosmetic purity.

## Phase 4 — Bootstrap separation

Each project must have two project-owned bootstraps:

### Project Architect bootstrap

Must teach:

- final human authority;
- Architect-heavy / Executor-light principle;
- Orchestrator authority boundary;
- exact envelope format and field order;
- same-task HUMAN_REQUIRED continuation;
- Orchestrator-owned next task numbering;
- documentation disposition;
- non-regression/accepted-capability policy;
- project-specific governance reading order;
- rollover/handover expectations.

### Project Executor bootstrap

Must teach:

- narrow Executor role;
- repo/branch/worktree authority;
- protected areas and validation policy;
- result/evidence contract;
- no self-acceptance;
- no task-number allocation;
- no rerun because transport failed.

The universal runtime loads/uses these files but does not own their project/business content.

## Phase 5 — Protocol and schema versioning

Version independently:

- Orchestrator runtime version;
- `ORCHESTRATOR_PROTOCOL_VERSION`;
- durable state schema version;
- project profile schema version.

Rules:

- incompatible versions fail before mutation;
- migrations are explicit and testable;
- state migration must preserve exactly-once identities and completed task boundaries;
- an old Project Architect bootstrap must not silently speak an incompatible envelope protocol to a newer runtime.

## Phase 6 — `doctor` preflight

Add a read-only preflight command or equivalent API.

Suggested checks:

```text
repository reachable
remote identity matches profile
authoritative branch exists
local path valid
Executor logical session resolvable
Architect browser reachable
Architect conversation resolvable
Architect bootstrap present
Executor bootstrap present
state root writable
worktree root valid
no conflicting watcher ownership
protocol/profile/state versions compatible
no obvious secret in project profile
```

The command must not create project work or mutate the target repository.

## Phase 7 — Project initialization

Add an `init` workflow that generates a project skeleton without inventing governance.

Suggested output:

```text
projects/<project-id>/project.json
projects/<project-id>/PROJECT_ARCHITECT_BOOTSTRAP.md
projects/<project-id>/PROJECT_EXECUTOR_BOOTSTRAP.md
projects/<project-id>/state/
```

Generated bootstrap files should contain mandatory universal protocol sections plus clearly marked project-specific sections to be completed by the human/Project Architect.

## Phase 8 — Qualification parity

Run the complete qualification matrix against:

1. an AFFOTECH compatibility profile to prove behavior parity with the reference implementation;
2. a synthetic second project to prove isolation;
3. the first real non-AFFOTECH project to prove portability.

Do not call the runtime universal merely because configuration fields exist.

## Phase 9 — Operations documentation

Required manuals before public release:

- `NEW_PROJECT_SETUP.md`
- `RECOVERY_RUNBOOK.md`
- `QUALIFICATION.md`
- `UPGRADE_MIGRATION.md`
- `TROUBLESHOOTING.md`
- Architect protocol/bootstrapping guide;
- Executor role/bootstrapping guide;
- backup/restore/move-to-new-PC procedure;
- project decommission procedure.

## Known deferred maintenance boundary

The AFFOTECH reference implementation documented a deferred live-Executor rollover servicing reachability concern: the main flow may remain blocked in Executor observation while rollover servicing is scheduled after that observation returns.

Universalization rule:

- do not reopen the mature core because of this gap;
- preserve rollover as non-preemptive maintenance;
- qualify or improve safe-boundary servicing in a separate milestone if needed;
- rollover due must never halt authorized project work or synthesize project authority.

## Explicit non-goals for first universal release

Do not combine initial universalization with:

- multi-Executor parallel scheduling;
- distributed services/queues/databases;
- Linux/macOS portability unless separately authorized;
- order-insensitive protocol redesign;
- business-aware Orchestrator reasoning;
- autonomous acceptance of Executor work;
- replacement of Project Architect governance with generic runtime policy.

The first universal release should remain Windows-first if that is the qualified host architecture.

## Recommended first implementation milestone

`UNIVERSAL.PROJECT.PROFILE.EXTRACTION.1A`

Goal:

Parameterize project identity and runtime wiring while preserving state machine, envelope parsing, result-delivery, HUMAN_REQUIRED, pause and recovery semantics unchanged.

Minimum acceptance:

- project profile exists;
- AFFOTECH-equivalent profile reproduces current behavior in regression tests;
- synthetic second profile proves isolation;
- no project-specific identity remains in generic runtime logic except clearly marked temporary compatibility adapters;
- no workflow semantics are intentionally changed.
