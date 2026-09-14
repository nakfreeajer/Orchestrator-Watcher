# New Project Setup

This document is the mandatory setup path for adding a new project to Orchestrator Watcher.

Do not copy another project's profile or bootstrap blindly. Every project must receive its own identities, paths, governance, Architect bootstrap, Executor bootstrap, state namespace, and qualification evidence.

## 1. Prerequisites

Required on the initial supported host:

- Windows;
- Git;
- Python runtime required by Orchestrator Watcher;
- Codex CLI with a usable logical Executor session;
- supported Chromium/Brave browser for the Project Architect conversation;
- GitHub repository access for the target project;
- a project repository with an authoritative branch;
- a Project Architect conversation controlled by the final human authority.

## 2. Create the project profile

Copy:

`templates/project-profile.example.json`

into the future configured-project location and fill every required value.

Never place passwords, cookies, OAuth tokens, API keys, or private customer data in the project profile.

At minimum configure:

- unique project ID;
- project name;
- local repository path;
- remote repository identity;
- authoritative branch;
- unique state directory;
- unique worktree root;
- Executor logical session ID;
- Project Architect browser endpoint;
- Project Architect conversation ID;
- Architect bootstrap path;
- Executor bootstrap path;
- protocol version.

## 3. Create the Project Architect bootstrap

Start from:

`templates/PROJECT_ARCHITECT_BOOTSTRAP.md`

This is mandatory.

A Project Architect that does not understand the Orchestrator protocol can break synchronization even when the runtime is healthy.

Customize the bootstrap with:

- final human authority;
- project/repository/branch identity;
- project purpose;
- architecture/governance reading order;
- protected areas;
- validation rules;
- current accepted baseline;
- classification rules;
- the exact canonical envelope;
- HUMAN_REQUIRED continuation behavior;
- documentation rules;
- handover/rollover expectations;
- non-regression rules.

The Project Architect must explicitly know that it is the thinker/decision-maker, not the repository Executor.

## 4. Create the Project Executor bootstrap

Start from:

`templates/PROJECT_EXECUTOR_BOOTSTRAP.md`

Customize only project-specific repository, governance, validation, and protected-area context.

The Executor bootstrap must not grant architecture or business authority.

## 5. Verify project governance

The project itself should have durable governance documents appropriate to its complexity. At minimum the Architect bootstrap must identify the authoritative sources for:

- current project state;
- architecture;
- protected areas;
- validation rules;
- decisions;
- handover/restart state;
- closed/non-regression capabilities when used.

The Orchestrator must not become the source of truth for project business policy.

## 6. Create isolated runtime namespaces

Every project must have unique:

- state directory;
- worktree root;
- task sequence;
- result directory;
- Architect conversation identity;
- Executor session identity.

Check that no path collides with another configured project.

## 7. Verify the Executor session

Confirm the configured Codex logical session exists and is the intended session for this project.

Do not reuse another project's logical Executor session unless that reuse is explicitly designed and qualified.

## 8. Verify the Architect browser/conversation

Confirm:

- the browser endpoint is reachable;
- the configured conversation exists;
- the conversation is the intended Project Architect;
- the Project Architect bootstrap has been delivered;
- the Architect can explain the canonical envelope and task-ID rule correctly.

## 9. Run read-only preflight

Before any project mutation, a future `doctor` command should verify:

```text
PROFILE_VALID
PROTOCOL_VERSION_MATCH
PROJECT_REPOSITORY_REACHABLE
AUTHORITATIVE_BRANCH_EXISTS
LOCAL_PATH_VALID
STATE_NAMESPACE_UNIQUE
WORKTREE_NAMESPACE_UNIQUE
EXECUTOR_SESSION_VALID
ARCHITECT_BROWSER_REACHABLE
ARCHITECT_CONVERSATION_VALID
ARCHITECT_BOOTSTRAP_PRESENT
EXECUTOR_BOOTSTRAP_PRESENT
NO_SECOND_WATCHER
```

Until the doctor command exists, perform equivalent checks manually/read-only.

## 10. Run a synthetic non-business task

The first task must not alter real business data.

Use a harmless repository/read-only or controlled fixture task that proves:

- task allocation;
- one Executor launch;
- result capture;
- result delivery;
- Architect observation;
- valid envelope parsing;
- next-task staging without duplicate execution.

## 11. Qualify HUMAN_REQUIRED

Run a controlled decision boundary proving:

- Architect returns `HUMAN_REQUIRED`;
- watcher remains resident;
- human and Architect can discuss naturally;
- ordinary prose does not trigger format recovery;
- Architect later emits a same-task envelope automatically;
- Orchestrator allocates one next task;
- no manual task-number intervention is required.

## 12. Qualify restart/recovery

Prove at least:

- restart with no active task;
- restart with a staged task;
- restart with a completed captured result;
- confirmed result is not resent;
- completed project work is not rerun because transport failed.

## 13. Run the first real bounded project milestone

Only after deterministic qualification should the project use Orchestrator Watcher for a real milestone.

The first real milestone should be narrow, reversible when practical, and independently verifiable.

## 14. Production-ready checklist

A project should not be called production-qualified until all required items are proven:

```text
PROFILE_VALID
ARCHITECT_BOOTSTRAP_VALID
EXECUTOR_BOOTSTRAP_VALID
PROTOCOL_VERSION_MATCH
ENVELOPE_ROUND_TRIP_PASS
SYNTHETIC_EXECUTOR_PASS
EXACTLY_ONCE_RESULT_PASS
RESTART_RECOVERY_PASS
HUMAN_REQUIRED_PASS
DISCUSSION_CONTINUATION_PASS
NEXT_TASK_SINGLE_LAUNCH_PASS
PROJECT_ISOLATION_PASS
FIRST_REAL_MILESTONE_PASS
```

## 15. Ongoing operation

After qualification:

- preserve one Project Architect as project authority;
- keep Executor prompts narrow;
- update project governance after accepted milestones when required;
- do not reopen accepted infrastructure without direct regression evidence;
- never treat transport failure as permission to rerun completed project work;
- keep profile/runtime protocol versions synchronized.

## 16. Migration, replacement, and decommissioning

Future operational documentation must define safe procedures for:

- moving a project to another workstation;
- replacing an Architect conversation;
- replacing an Executor logical session;
- upgrading runtime/state/profile schema versions;
- backing up/restoring durable state;
- recovering from corrupted local state without rerunning completed work;
- removing a project configuration permanently.

These operations are authority-sensitive and must fail closed when durable task identity is uncertain.
