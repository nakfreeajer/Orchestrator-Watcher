# Reference Implementation Extraction

## Purpose

This document records the reusable engineering knowledge extracted from the production AFFOTECH Local Orchestrator into Orchestrator Watcher.

The legacy reference repository is:

`nakfreeajer/affotech-agent-orchestrator`

The accepted reference runtime/source checkpoint used for this extraction is:

`57a3a914b35ed0c715aaaf0267ed0bd36a39cc78` — resident human-decision continuation.

The later legacy repository documentation records that implementation as a mature reference implementation and identifies universalization as the next phase.

This file is provenance and design authority for extraction. It is not permission to copy project-specific AFFOTECH identity into the universal runtime.

## What is worth preserving

The useful part of the legacy system is the workflow contract and the recovery discipline, not the AFFOTECH constants.

Protected universal invariants:

1. `recover where stopped -> run one task -> capture one result -> deliver once -> wait -> stage one next action -> repeat`.
2. One visible Executor child at a time by default.
3. One writer per project/task.
4. Durable task identity and sequential task allocation owned by the Orchestrator.
5. Persistent logical Executor session with short-lived bounded task processes.
6. Orchestrator-owned isolated task worktrees.
7. Executor completion is determined by process/result evidence, not an arbitrary wall-clock timeout.
8. Result delivery is exactly-once with read-only reconciliation before retry.
9. Completed project work is never rerun merely because transport, browser control, or rollover failed.
10. Project Architect independently verifies Executor evidence and owns project meaning.
11. Final human authority remains above Architect and Orchestrator.
12. Machine authority is an explicit `ORCHESTRATOR_RESULT`; ordinary discussion is not executable authority.
13. Deliberate `HUMAN_REQUIRED` can remain resident while human and Architect discuss naturally.
14. Once human authority is sufficient, the Architect emits a same-task envelope; the Orchestrator allocates the next task ID.
15. Discussion pause is a transport overlay, not a replacement workflow state.
16. Architect conversation rollover is maintenance only and never business/workflow authority.
17. Recovery uses current durable facts rather than stale historical error labels.
18. Ambiguous external actions reconcile read-only before retry.
19. Durable privacy-safe runtime logging is mandatory.
20. Project-specific governance remains in the project, not in generic Orchestrator code.

## Canonical state model extracted from production

Normal durable states:

- `IDLE`
- `NEXT_PROMPT_READY`
- `EXECUTOR_RUNNING`
- `RESULT_READY`
- `ARCHITECT_RUNNING`
- `HUMAN_REQUIRED`

Exceptional retained state:

- `EXECUTOR_CRASHED`

There should not be a synthetic `PAUSED` workflow state. Human discussion pause is a durable overlay on the truthful workflow state.

Only the production dispatcher may perform the actual `NEXT_PROMPT_READY -> EXECUTOR_RUNNING` launch transition. Recovery, parsing, rollover, pause and transport helpers may restore or inspect state but must not create alternate launch authority.

## Canonical machine authority

The production reference contract is:

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

Current protocol field order is significant.

The Project Architect uses the completed task ID. The Orchestrator creates the next sequential task ID after an `EXECUTE` decision.

Three input classes must remain distinct:

1. valid machine authority -> process;
2. intended but invalid machine authority -> fail closed;
3. ordinary discussion -> observe without execution authority.

Presence of the literal opening marker `<ORCHESTRATOR_RESULT>` indicates attempted machine authority. An invalid attempted envelope must never be silently treated as ordinary prose.

## Resident human-decision contract

The deliberate business/governance wait is identified by the project-side Architect decision that produced `HUMAN_REQUIRED`.

Universal semantics:

- project execution pauses;
- watcher remains resident;
- no Executor launches while authority is missing;
- completed task/result remain preserved;
- ordinary human/Architect discussion is expected;
- ordinary discussion does not trigger format recovery;
- a later valid envelope for the same completed task is consumed normally;
- later `EXECUTE` stages exactly one next task;
- later `HUMAN_REQUIRED` keeps waiting;
- later `STOP` means no currently authorized work and returns to resident no-work semantics;
- temporary browser attach/read failure preserves state and retries supervision rather than killing the watcher.

The Orchestrator never interprets the business discussion itself. The Project Architect decides when human authority is sufficient.

## Exactly-once delivery and recovery

Useful production rules:

- confirmed result delivery is never resent;
- an ambiguous attempted send is reconciled before retry;
- inability to prove delivery is not proof that delivery did not occur;
- a completed Executor task is never rerun because result transport failed;
- stale composer cleanup is permitted only when the Orchestrator proves the text is its own exact known payload;
- unrelated human-authored composer text is never cleared;
- completed-confirmed recovery should use durable facts, not stale historical failure labels.

## Discussion pause

The reference implementation supports explicit human discussion pause separately from `HUMAN_REQUIRED`.

Semantics worth preserving:

- do not kill a running Executor;
- allow it to finish;
- hold `RESULT_READY` locally instead of delivering while paused;
- hold `NEXT_PROMPT_READY` instead of launching while paused;
- suppress new idle/bootstrap contact while paused;
- preserve the underlying workflow state;
- resume only clears the pause overlay and never synthesizes project authority.

The exact local/remote control mechanism should be adapter/configuration-level rather than embedded project policy.

## Architect browser/session lessons

The reference system uses Playwright for ChatGPT Architect browser control.

Reusable rules:

- browser automation objects are thread-affine; create/use/close on the same owning thread;
- dynamic DOM presence is not enough: controls must be current, visible and actionable;
- after rerender, reacquire the composer rather than trusting a stale locator;
- distinguish proven pre-send failure from attempted/ambiguous send;
- startup failure must prove worker termination, not merely stop waiting;
- rollover must dynamically capture the new conversation identity rather than rely on a hardcoded ID.

## Rollover contract

Rollover is session maintenance only.

A safe rollover may:

1. request a complete handover from the old Project Architect;
2. open a fresh authenticated Architect conversation;
3. submit the handover;
4. verify the new conversation identity;
5. persist that identity;
6. close the old page;
7. resume the same durable workflow state.

Rollover must never:

- launch project work;
- regenerate Executor results;
- renumber completed tasks;
- resend confirmed work;
- invent business authority;
- convert maintenance failure into a different workflow decision.

The reference implementation still had a deferred live-Executor rollover reachability gap. Universalization must preserve the non-preemptive rule and treat any improvement as a separate maintenance milestone.

## What must NOT be copied into the universal core

The legacy source contains project-specific identity such as:

- AFFOTECH project paths and repository names;
- AFFOTECH branch identity;
- AFFOTECH Executor logical session ID;
- Architect conversation ID;
- workstation-specific Windows paths;
- AFFOTECH-specific bootstrap filename/content;
- project-specific logger/module naming;
- legacy relay repository/path assumptions;
- AFFOTECH-specific validation/browser assumptions in tests.

These belong in project profile, project bootstrap, adapters or fixtures.

## Extraction rule

Universalization is an extraction/configuration exercise, not a redesign.

The mature workflow semantics above should remain stable while project identity is parameterized and moved out of the generic runtime.
