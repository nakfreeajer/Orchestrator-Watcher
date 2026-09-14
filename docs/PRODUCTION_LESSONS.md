# Production Lessons

## Purpose

These lessons were extracted from the production stabilization history of the AFFOTECH Local Orchestrator. They are preserved here because they apply to any browser-mediated Architect/Executor orchestration system.

The goal is not to copy AFFOTECH history into the universal product. The goal is to avoid repeating the same classes of failure.

## 1. Browser object ownership includes thread ownership

A Playwright Sync object is not ordinary shared data.

Production failure occurred when a bridge was created/used/closed across different threads.

Permanent lesson:

- create, use and close a browser bridge on the same owning thread;
- shutdown should signal/join the owner rather than close the bridge from another thread;
- tests should verify creator/use/close thread identity, not just method return values.

## 2. Startup timeout is protocol behavior

An unrealistically short startup wait can turn a healthy delayed browser attachment into a false failure and leave an orphan worker that later becomes active.

Permanent lesson:

- a bounded startup timeout must be realistic;
- timeout/failure must prove worker termination;
- `start()` must not return failure while the supposedly failed worker can later become authoritative.

## 3. DOM presence is not actionability

Dynamic UIs can leave stale or hidden elements in the DOM after rerender.

Permanent lesson:

- require visible/actionable controls;
- reacquire the composer after rerender;
- do not trust `count() > 0` as proof that a control is current;
- browser mutation and verification stages should resolve the current live element independently.

## 4. Pre-send and post-send failure are different authority classes

A proven failure before any send is different from a failure after send may have happened.

Permanent lesson:

- proven pre-send failure may be retried safely after exact cleanup;
- attempted or ambiguous send must reconcile read-only before retry;
- never convert ambiguity into a blind duplicate send.

## 5. Cleanup requires ownership proof

A production failure left a full Orchestrator payload in the human composer even though it was unsent.

Permanent lesson:

- clear a composer only when normalized current text exactly matches the known Orchestrator-owned payload;
- never clear unrelated human text;
- never use a non-empty composer as cleanup authority.

## 6. Malformed machine authority must fail closed

A malformed Architect envelope was once mistaken for ordinary discussion because the strict parser failed to extract a task ID.

Permanent lesson:

- detect intent to provide machine authority separately from successful parsing;
- literal `<ORCHESTRATOR_RESULT>` opening marker means machine authority is being attempted;
- invalid attempted authority must fail closed;
- ordinary prose remains non-authoritative discussion.

## 7. Machine protocols are literal contracts

A semantically correct envelope failed because fields appeared in a different order from the parser contract.

Permanent lesson:

- document exact syntax and field order;
- Project Architect bootstrap must carry that contract;
- parser/producer compatibility should be a deterministic test;
- any future order-insensitive protocol is a versioned protocol change, not an informal assumption.

## 8. Recovery tooling must match durable encoding

A one-time recovery failed because state contained a UTF-8 BOM while the reader expected plain UTF-8.

Permanent lesson:

- fail before mutation when state parsing is uncertain;
- qualify recovery against the actual durable file encoding;
- state migration/compatibility belongs in versioned runtime logic rather than ad-hoc operator assumptions.

## 9. Stored process metadata is not process liveness

A recorded PID survived after the Executor had exited.

Permanent lesson:

- PID/state metadata is historical until current liveness is proven;
- never kill broad process groups based only on a stored PID;
- operator/status displays should distinguish recorded identity from live ownership.

## 10. Unit tests do not equal production proof for browser-mediated orchestration

Many defects appeared only in the real composition of:

`completed task -> human boundary -> real ChatGPT DOM -> Architect response -> envelope consumption -> next task staging`

Permanent lesson:

- deterministic tests are necessary;
- at least one real browser-mediated smoke path is required before calling a new integration production-ready;
- when direct regression evidence exists, reopen only the smallest affected boundary rather than re-auditing the whole system.

## 11. Completed work must be preserved before transport repair

The most damaging recovery mistake would be to rerun completed project mutation simply because the watcher, browser or delivery channel failed.

Permanent lesson:

- workflow recovery and project execution are different concerns;
- exactly-once project work is more important than making the transport look clean;
- current durable facts should dominate stale historical error labels.

## 12. Human discussion must not become execution authority

Ordinary human/Architect conversation is expected, especially during `HUMAN_REQUIRED`.

Permanent lesson:

- natural language discussion does not authorize Executor work;
- only a valid machine envelope does;
- the Orchestrator should observe, not interpret, project/business semantics.

## 13. Session rollover is maintenance

Conversation memory pressure is an operational concern, not a project-governance concern.

Permanent lesson:

- rollover may be due, but workflow continues until a safe maintenance boundary;
- rollover failure must not create business authority, rerun tasks or resend results;
- Project Architect handover must preserve current authoritative state and protocol behavior.

## 14. Keep the Orchestrator boring

The reference system became more reliable when the central loop stayed simple:

`recover -> run one task -> capture one result -> deliver once -> wait -> stage one next action`

Every helper exists to protect that loop. New features should be rejected when they introduce a second source of workflow authority without a proven need.
