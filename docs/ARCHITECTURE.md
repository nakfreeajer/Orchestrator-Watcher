# Architecture

## System model

Orchestrator Watcher has four authority layers:

```text
Final Human Authority
        |
        v
Project Architect
        |
        v
Orchestrator Watcher
        |
        v
Executor
```

The layers are intentionally asymmetric.

## Human

The human owns:

- final product/business authority;
- risk acceptance;
- priority decisions that genuinely require human judgment;
- permission to cross protected governance boundaries.

## Project Architect

The Project Architect owns:

- project-wide reasoning;
- architecture and roadmap;
- business-policy interpretation;
- milestone selection;
- narrowing uncertainty before Executor dispatch;
- complete bounded Executor prompts;
- independent verification of Executor evidence;
- ACCEPTED/BLOCKED/INCONCLUSIVE/NO_NEW_REPORT classification;
- project documentation governance;
- deciding when HUMAN_REQUIRED authority has been resolved.

The Project Architect must understand the Orchestrator protocol through a mandatory project bootstrap.

## Orchestrator Watcher

The Orchestrator owns mechanics, not project meaning:

- durable workflow state;
- task identity and sequencing;
- one-at-a-time dispatch by default;
- Executor child lifecycle;
- isolated task worktrees;
- result capture;
- exactly-once result delivery/reconciliation;
- Architect response observation;
- canonical envelope parsing;
- resident HUMAN_REQUIRED waiting;
- discussion-pause transport;
- browser/session maintenance;
- recovery and duplicate protection;
- privacy-safe runtime logging.

The Orchestrator must not:

- decide product architecture;
- infer business authority from ordinary discussion;
- accept project milestones independently;
- broaden Executor scope;
- invent the next roadmap milestone;
- treat session maintenance as workflow authority.

## Executor

The Executor owns bounded repository work only:

- audit requested scope;
- implement authorized mutation;
- run specified tests/validation;
- commit/push only when the task authorizes it;
- report exact evidence or blockers;
- stop.

The Executor does not self-accept and does not allocate task IDs.

## Three-layer universal implementation

The reusable system should separate:

### Generic runtime core

Project-neutral state machine, transport, recovery, browser bridge, logging, envelope parser, worktree management, and Executor lifecycle.

### Project profile

Data/configuration only, for example:

- project ID/name;
- local repository path;
- remote repository;
- authoritative branch;
- Executor logical session ID;
- Architect conversation ID;
- Architect browser endpoint;
- state/worktree roots;
- Architect bootstrap path;
- Executor bootstrap path;
- optional validation adapters.

### Project governance

Owned by the project itself:

- architecture;
- roadmap;
- protected areas;
- business permissions;
- documentation rules;
- validation policy;
- standing HUMAN AUTHORITY CONTINUATION rule;
- project-specific context.

## Project isolation

Every project must have isolated:

- durable state;
- task sequence;
- worktree namespace;
- result files;
- Executor session identity;
- Architect conversation identity;
- project profile;
- project bootstraps.

No project may consume another project's pending result, task number, worktree, conversation, or session by accident.

## Versioned contracts

The universal system should version at least:

- runtime version;
- state-schema version;
- Orchestrator protocol version;
- project-profile schema version.

Incompatible versions must fail closed rather than silently reinterpret durable authority.

## Operating-system scope

Initial universalization target: **project-universal on the currently qualified Windows host architecture**.

Windows/macOS/Linux portability is a separate adapter concern and must not force redesign of the proven workflow core.
