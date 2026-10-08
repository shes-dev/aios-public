# Operator Guide

Roles: [ROLES.md](ROLES.md). Topology: [TOPOLOGY.md](TOPOLOGY.md).

One Architect context; external Executors (possibly parallel); dedicated builder mandatory. AIOS does not launch or connect workers.

## Basic loop

1. Talk with the Architect.
2. Architect writes Suggestion and/or `prompts/NNNN-slug.prompt.md` and updates QMD.
3. Hand Active prompts to Executors (`execute NNNN`).
4. Executors write matching `.response.md`.
5. Architect/Reviewer writes `.review.md`, removes the task from `queue.md`, and appends its ID to `COMPLETED.md`.

Independent Active tasks may run on Cursor, Claude, and Codex at the same time:

```text
QMD
0371 → Cursor
0372 → Claude
0373 → Codex
```

## Bootstrap

Create a dedicated builder; identify product repo(s); paste the root README prompt. Architect initializes or resumes **in the builder**. See [BOOTSTRAP.md](BOOTSTRAP.md).

Architect creates missing folders when needed:

```text
prompts/
suggestions/
memory/
```

## QMD / Queue

**QMD** (`queue.md`) is the lifecycle/status index — not the source of task meaning.

Architect owns it. Reviewer updates after review. Executor does not edit it.

```text
# In Line
# Active
# Awaiting Review
```

QMD lists live work only. Blocked, completed, and deleted task IDs move to `BLOCKED.md`, `COMPLETED.md`, and `DELETED.md`. A task is complete once its `.prompt.md`, `.response.md`, and `.review.md` all exist.

## Suggestions vs Memory

- `suggestions/` — documented ideas that may be promoted to tasks.
- `memory/` — how we work on this project and facts about the product; never promoted to a task.

## Task files

```text
prompts/NNNN-slug.prompt.md    # Architect
prompts/NNNN-slug.response.md  # Executor
prompts/NNNN-slug.review.md    # Architect/Reviewer
```

## Ownership

Builder owns `queue.md`, `BLOCKED.md` / `COMPLETED.md` / `DELETED.md`, `prompts/`, `suggestions/`, `memory/`, and project knowledge.

Product repositories own application, runtime, and deployment code.

`aios-public` stays generic protocol/template source.
