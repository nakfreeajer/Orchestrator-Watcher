# Qualification Matrix

## Purpose

A universal Orchestrator should not be declared production-ready merely because the runtime starts or a few unit tests pass.

This matrix extracts the qualification boundaries learned from the production reference implementation and turns them into reusable release gates.

## Layer 1 — Project profile validation

Required checks:

- project ID present and unique;
- local repository path exists;
- remote repository matches configured project identity;
- authoritative branch exists;
- state/worktree roots are project-isolated;
- Executor logical session identity is present and resolvable;
- Architect conversation/browser endpoint is present and resolvable;
- Project Architect bootstrap exists;
- Project Executor bootstrap exists;
- protocol/profile/state schema versions are supported;
- no secret/cookie/token is stored in the profile.

Expected result:

`PROFILE_VALID`

## Layer 2 — Architect protocol qualification

Required checks:

- fresh Architect bootstrap teaches exact envelope syntax;
- canonical field order is preserved;
- completed `taskId` is used by Architect;
- Orchestrator owns next task allocation;
- ordinary prose is non-authoritative;
- invalid attempted envelope fails closed;
- `EXECUTE`, `HUMAN_REQUIRED` and `STOP` are all parsed deterministically;
- documentation disposition is recognized.

Expected result:

`ARCHITECT_PROTOCOL_PASS`

## Layer 3 — Executor qualification

Required checks:

- exactly one bounded Executor child launches;
- task-owned worktree is used;
- Executor receives the intended bounded prompt;
- Executor cannot allocate task IDs;
- result path is durable;
- non-empty result is captured;
- child exit/result determines completion;
- no duplicate writer is launched.

Expected result:

`EXECUTOR_PATH_PASS`

## Layer 4 — Result transport qualification

Required checks:

- result payload identity/hash is durable;
- confirmed Architect delivery is never repeated;
- ambiguous delivery reconciles before retry;
- completed Executor work is not rerun because delivery is uncertain;
- restart from `RESULT_READY` resumes transport rather than execution.

Expected result:

`EXACTLY_ONCE_RESULT_PASS`

## Layer 5 — Architect decision qualification

Required checks:

- one completed Architect response is consumed once;
- `EXECUTE` stages exactly one next sequential task;
- repeated observation of the same Architect response cannot allocate another task;
- `HUMAN_REQUIRED` preserves the same completed task;
- `STOP` creates no new work.

Expected result:

`ARCHITECT_DECISION_PASS`

## Layer 6 — Resident HUMAN_REQUIRED qualification

Required checks:

- watcher remains resident;
- no Executor launches while authority is missing;
- ordinary human/Architect discussion is tolerated;
- ordinary prose does not trigger envelope-format recovery;
- observation baseline advances after discussion;
- temporary browser attach/read failure preserves state and retries;
- later same-task `EXECUTE` launches exactly one next task;
- repeated `HUMAN_REQUIRED` continues waiting;
- `STOP` returns to resident no-work behavior.

Expected result:

`HUMAN_REQUIRED_PASS`

## Layer 7 — Discussion-pause qualification

Required checks:

- pause is durable across restart;
- running Executor is not killed;
- completed result may become `RESULT_READY` but is held locally;
- `NEXT_PROMPT_READY` is not launched while paused;
- Architect conversation receives no new Orchestrator result/bootstrap traffic while paused;
- resume clears only the overlay;
- underlying HUMAN_REQUIRED remains truthful;
- resume never invents project authority.

Expected result:

`DISCUSSION_PAUSE_PASS`

## Layer 8 — Restart/recovery qualification

Exercise restart from at least:

- `NEXT_PROMPT_READY`;
- live `EXECUTOR_RUNNING`;
- dead child with result;
- dead child without result;
- `RESULT_READY`;
- `ARCHITECT_RUNNING` after confirmed delivery;
- deliberate `HUMAN_REQUIRED`;
- active discussion pause.

Assert:

- no completed task reruns;
- no confirmed result resends;
- no duplicate Executor launches;
- no task number is reused;
- no human/business authority is synthesized.

Expected result:

`RESTART_RECOVERY_PASS`

## Layer 9 — Browser bridge qualification

Required checks:

- create/use/close thread ownership is consistent;
- delayed startup within budget succeeds;
- failed startup proves worker termination;
- dynamic composer rerender is handled by reacquisition;
- exact unsent-payload cleanup does not touch unrelated human text;
- pre-send and ambiguous-send failures follow different retry rules;
- conversation identity can be discovered dynamically after rollover.

Expected result:

`BROWSER_BRIDGE_PASS`

## Layer 10 — Cross-project isolation

Use at least two profiles, one of which may be synthetic.

Required checks:

- state roots do not collide;
- task/result/worktree paths do not collide;
- Executor session identity does not leak across projects;
- Architect conversation identity does not leak across projects;
- project-specific bootstrap/governance stays outside generic core;
- task numbering is isolated according to configured project namespace;
- one project's recovery cannot consume another project's envelope/result.

Expected result:

`PROJECT_ISOLATION_PASS`

## Layer 11 — Real browser-mediated smoke path

For every newly onboarded real project, perform one bounded, low-risk real milestone through the complete path:

`Architect -> Orchestrator -> Executor -> result -> Architect verification -> next decision`

Where practical, also exercise one genuine `HUMAN_REQUIRED` continuation before declaring the project's remote human-decision integration production-ready.

Expected result:

`FIRST_REAL_MILESTONE_PASS`

## Production readiness

A project is production-qualified only when all applicable gates above pass and any explicitly deferred capability is documented as such.

Suggested status block:

```text
PROFILE_VALID=PASS
ARCHITECT_PROTOCOL_PASS=PASS
EXECUTOR_PATH_PASS=PASS
EXACTLY_ONCE_RESULT_PASS=PASS
ARCHITECT_DECISION_PASS=PASS
HUMAN_REQUIRED_PASS=PASS
DISCUSSION_PAUSE_PASS=PASS
RESTART_RECOVERY_PASS=PASS
BROWSER_BRIDGE_PASS=PASS
PROJECT_ISOLATION_PASS=PASS
FIRST_REAL_MILESTONE_PASS=PASS
```

A deferred maintenance feature such as conversation rollover may remain separately qualified, but it must never be allowed to own or halt project workflow authority merely because maintenance is due.
