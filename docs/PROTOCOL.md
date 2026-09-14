# Protocol

## Protocol version

Initial protocol identifier:

```text
ORCHESTRATOR_PROTOCOL_VERSION=1
```

The runtime, Project Architect bootstrap, and project profile must agree on the protocol version.

## Canonical Architect envelope

The machine-authoritative Project Architect response is:

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

For protocol v1, field order is significant.

The Project Architect uses the completed task ID. The Orchestrator allocates the next sequential task ID.

## Classification

- `ACCEPTED` — evidence independently supports acceptance.
- `BLOCKED` — a specific blocker prevents safe completion/continuation.
- `INCONCLUSIVE` — evidence is insufficient or contradictory.
- `NO_NEW_REPORT` — no new Executor report exists to review.

Executor PASS is not automatic Architect acceptance.

## Actions

### EXECUTE

Exactly one bounded next Executor action is authorized.

Requirements:

- include the complete prompt between `promptBegin` and `promptEnd`;
- keep scope narrow;
- preserve project invariants;
- do not allocate a new task ID manually.

### HUMAN_REQUIRED

Human/business authority is genuinely missing.

For a deliberate Project Architect decision boundary:

- execution pauses;
- the watcher remains resident;
- the same completed task remains authoritative;
- natural human/Architect discussion may span multiple turns;
- ordinary discussion does not need an envelope;
- the Architect must not invent authority;
- once sufficient human authority exists, the Architect must automatically emit a valid envelope for the same completed task in that same response;
- a later `EXECUTE` lets the Orchestrator allocate exactly one next task.

### STOP

No currently authorized next work exists.

STOP is not permission to destroy durable state or rerun completed work.

## Ordinary discussion

Ordinary human/Architect prose is not machine authority.

The literal opening marker `<ORCHESTRATOR_RESULT>` means the response is attempting machine authority. An attempted malformed envelope must fail closed; it must not be silently reinterpreted as discussion.

## Documentation disposition

- `NOT_REQUIRED` — no documentation closure gate.
- `REQUIRED` — accepted work requires a bounded documentation closure before ordinary advancement.
- `COMPLETE` — required documentation has already been synchronized and verified.

Project-specific document ownership belongs to the Project Architect, not the generic Orchestrator.

## Exactly-once principles

- completed project work is never rerun because transport failed;
- confirmed result delivery is never resent;
- ambiguous attempted delivery reconciles read-only before retry;
- stale process metadata is not sufficient proof of active execution;
- recovery uses current durable facts rather than old error labels.

## Discussion pause

Discussion pause is a transport overlay and is separate from HUMAN_REQUIRED.

A pause may hold new result delivery, IDLE contact, or next-task launch while preserving the truthful underlying workflow state.

Pause/resume controls do not create project authority.

## Rollover

Architect conversation rollover is session maintenance only.

Rollover must never:

- invent a project decision;
- rerun completed Executor work;
- resend confirmed results;
- renumber completed tasks;
- replace a HUMAN_REQUIRED decision;
- become a second workflow engine.

A rollover failure should leave rollover pending for later while preserving project state.
