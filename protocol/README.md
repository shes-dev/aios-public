# Operator Guide

Roles: [ROLES.md](ROLES.md). Topology: [TOPOLOGY.md](TOPOLOGY.md).

One Architect context; external Executors (possibly parallel); dedicated builder mandatory. AIOS does not launch or connect workers.

## Basic loop

1. Talk with the Architect.
2. Architect writes Suggestion and/or `prompts/NNNN-slug.prompt.md` and updates QMD.
3. Hand Active prompts to Executors (`execute NNNN`).
4. Executors write matching `.response.md`.
5. Architect/Reviewer writes `.review.md` and updates `queue.md`.

Independent Active tasks may run on Cursor, Claude, and Codex at the same time.

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
# Blocked
```

## Task files

```text
prompts/NNNN-slug.prompt.md    # Architect
prompts/NNNN-slug.response.md  # Executor
prompts/NNNN-slug.review.md    # Architect/Reviewer
```

## Ownership

Builder owns `queue.md`, `prompts/`, `suggestions/`, `memory/`, and project knowledge.

Product repositories own application, runtime, and deployment code.

`aios-public` stays generic protocol/template source.
