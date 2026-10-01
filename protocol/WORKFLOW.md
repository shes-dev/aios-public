# Workflow

AIOS is a file-based protocol for a Human and three logical roles: Architect, Executor, and Reviewer.

The canonical knowledge lifecycle is:

```text
Discussion
  ↓
Document
  ↓
Suggestion
  ↓
Promote
  ↓
Task
  ↓
Implementation
  ↓
Review
  ↓
Knowledge
```

A shorter operational shorthand for approved work is Talk → Document → Task → Execute → Review → Done. Do not reduce AIOS to a task manager: Suggestions are first-class pre-task knowledge.

Role staffing is by example, not by vendor. See [Roles](ROLES.md).

## QMD (operational queue)

**QMD** is the human-readable Markdown operational board backed by `queue.md` (and related lifecycle archives). It is not a hosted runtime service.

`queue.md` keeps the live states:

```text
# In Line
# Active
# Awaiting Review
# Blocked
```

Accepted/completed history should be moved out of the live queue into `COMPLETED.md`; stale, paused, or long-horizon prepared work may be moved to `BACKLOG.md` when a builder uses one. Keep only a short pointer to those archives in `queue.md` when helpful.

The Architect owns `queue.md`. The Reviewer updates it after a review.

The Executor does not edit `queue.md`, `COMPLETED.md`, or `BACKLOG.md` during normal execution.

Independent Active tasks may run in parallel on different external workers. AIOS does not launch or schedule those workers.

## Protocol invariant vs tooling

**Invariant:** Architect defines approved work → external Executor consumes it → response/evidence is produced → Reviewer reviews → canonical state/knowledge advances.

**Tooling examples:** Git pull/push, opening Cursor/Claude/Codex/ChatGPT, or other transport between people and tools. Manual courier steps are optional mechanics when tools do not share a workspace — not the definition of AIOS.

## Bootstrap

The repo starts small.

The Architect creates runtime folders when the workflow needs them:

```text
prompts/
suggestions/
memory/
```

Do not ask the Human to create these folders manually.

Canonical product/builder bootstrap: [Bootstrap](BOOTSTRAP.md) and [Repository topologies](TOPOLOGY.md). The Human typically creates both repositories; an Architect agent initializes or resumes the builder from the root README prompt.

Single-repository bootstrap remains valid: clone this protocol repo and start talking to the Architect.

Advanced empty-builder git sequence when a builder does **not** already exist: [Existing-project initialization](examples/INIT_EXISTING_PROJECT.prompt.example.md).

## Task Flow

Example assignment — ChatGPT as Architect/Reviewer and Cursor as Executor:

1. Human talks with ChatGPT.
2. Human says: `Document this`.
3. ChatGPT writes a suggestion or task.
4. ChatGPT adds the task to `queue.md`.
5. When tools do not share the workspace, the Human (or authorized agent) syncs Git so the Executor can see the update.
6. Human opens Cursor on the repo folder.
7. Human tells Cursor: `execute 0001`.
8. Cursor does the work and writes a response file.
9. Sync the response back to shared Git when needed.
10. Human tells ChatGPT: `review 0001`.
11. ChatGPT writes the review and updates `queue.md`.
12. If accepted, ChatGPT removes the task from operational queue state and appends its final summary to `COMPLETED.md`.

The same artifacts and order apply when one agent session plays every role, or when multiple Executors take independent Active tasks in parallel. Preserve separate prompt, response, and review files.

In the builder/product topology:

- queue, prompts, responses, and reviews live in the **builder**
- tools that act across repos need access to both builder and product
- the Executor modifies the product repository only when the task explicitly says so
- if the product changed, those commits must land in the product repository

## Files

```text
prompts/NNNN-slug.prompt.md
prompts/NNNN-slug.response.md
prompts/NNNN-slug.review.md
```

The prompt says what to do.

The response says what the Executor did.

The review says whether the Reviewer accepts it.

## Rework

If a task needs more work, the Architect creates a new task that refers to the review.

The Human still tells the Executor only:

```text
execute 0002
```

## Suggestions

Use suggestions when the discussion is useful but not ready for implementation.

```text
Discussion -> Document this -> Suggestion -> Promote it -> Task
```
