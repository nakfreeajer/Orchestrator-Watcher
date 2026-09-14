# Recovery Runbook

## Purpose

This runbook extracts recovery behavior proven or learned in the production reference implementation. It is intentionally project-neutral.

The permanent rule is:

> Recover the workflow state; do not recreate completed project work.

## State recovery table

### `NEXT_PROMPT_READY`

Expected behavior:

- verify the staged prompt and owned worktree;
- if discussion pause is inactive, allow only the production dispatcher to launch exactly one Executor;
- never allocate a second task merely because the watcher restarted.

### `EXECUTOR_RUNNING`

If the owned Executor child is alive:

- observe only;
- do not start another child;
- do not impose a wall-clock completion timeout as task authority.

If the child is dead and a usable result exists:

- transition to `RESULT_READY`;
- do not rerun Executor.

If the child is dead and no usable result exists:

- fail closed;
- preserve task identity;
- require recovery/human/Architect authority according to policy.

A stored PID is not sufficient proof of liveness. Always verify the current process.

### `RESULT_READY`

- resume or reconcile result delivery;
- if delivery may have occurred, reconcile first;
- never rerun the completed Executor merely because Architect delivery is uncertain.

### `ARCHITECT_RUNNING`

If result delivery is already confirmed:

- observe the Architect conversation;
- never resend the confirmed result;
- preserve the same reviewed task until one valid Architect decision is consumed.

### Deliberate `HUMAN_REQUIRED`

- remain resident;
- attach/re-attach to the same authoritative Architect conversation;
- ordinary discussion advances the observation baseline but creates no execution authority;
- valid same-task `EXECUTE` -> stage one next sequential task;
- valid same-task `HUMAN_REQUIRED` -> continue waiting;
- valid same-task `STOP` -> resident no-work/idle semantics;
- temporary attach/read failure -> disconnect safely, wait, retry, preserve state.

### Completed + confirmed + stale failure label

If durable facts show all of the following:

- current task completed;
- result exists;
- Architect delivery confirmed;
- no active Executor;
- no newer prompt staged;

then recover by those facts. A stale historical failure reason must not override stronger current evidence.

## Ambiguous-send rule

Never treat uncertainty as permission to repeat an external action.

For result/Architect delivery:

1. determine whether send was proven not attempted, attempted, confirmed or ambiguous;
2. proven pre-send failure may be retried after safe cleanup;
3. attempted/ambiguous send requires read-only reconciliation;
4. confirmed send is never repeated.

The same pattern should be reused for any future external mutation adapters.

## Composer cleanup rule

The Orchestrator may clear a browser composer only when it proves the current text is exactly its own known unsent payload.

Never clear merely because the composer is non-empty.

Never clear unrelated human-authored text.

Never clear after an attempted or ambiguous send solely to make retry convenient.

## Invalid Architect envelope

Machine authority classes:

- valid envelope -> process;
- opening marker present but envelope invalid -> fail closed;
- ordinary prose without attempted envelope -> discussion/non-authoritative observation.

Malformed machine authority must never be downgraded into ordinary discussion.

## Browser bridge failure

Reusable rules from production incidents:

- Playwright Sync bridge create/use/close must stay on one owning thread;
- startup timeout must also terminate the failed worker before reporting unavailable;
- dynamic UI locators must be reacquired after rerender;
- DOM existence is not actionability;
- browser transport failure must not mutate project workflow authority.

## Discussion pause recovery

Discussion pause is an overlay, not a workflow state.

On restart:

- reload/preserve the durable pause marker;
- do not silently clear it;
- underlying state remains truthful;
- running Executor may continue and finish;
- result delivery/new launch stays held until pause is explicitly cleared.

## Rollover failure

Rollover is maintenance.

If rollover fails:

- preserve workflow state;
- preserve completed task/result/delivery facts;
- keep rollover due for later when appropriate;
- do not synthesize `HUMAN_REQUIRED` for business reasons unless the Project Architect actually requested it;
- do not launch/relaunch project work as part of rollover recovery.

## Durable state encoding

Recovery tooling must tolerate the durable state encoding actually written by the runtime. A production incident showed that UTF-8 BOM can break a plain UTF-8 JSON read.

Rule:

- parsing uncertainty must fail before mutation;
- never mutate state using partially parsed or assumed content.

## Process safety

- never kill broad Codex process groups from stale metadata;
- ownership and current liveness must be proven;
- one watcher instance owns one canonical state namespace;
- one project/task has one writer authority.

## Recovery success condition

Recovery is successful when the system can truthfully continue from the last durable boundary without:

- rerunning completed work;
- duplicating a result delivery;
- creating a second Executor;
- inventing a new task ID;
- inventing human/business authority;
- losing a deliberate pause or human-decision boundary.
