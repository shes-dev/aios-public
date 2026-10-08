# Workflow

AIOS is a file-based protocol centered on a **dedicated builder repository** and a durable artifact triplet.

## Engineering loop (primary)

```text
Architect
  -> prompts/NNNN-slug.prompt.md
  -> external Executor
  -> prompts/NNNN-slug.response.md
  -> Architect / Reviewer
  -> prompts/NNNN-slug.review.md
  -> next task / knowledge
```

Independent Active prompts may run in parallel on different Executors. AIOS does not launch those workers.

## Suggestions vs Memory

- **Suggestion** (`suggestions/`): an idea, proposal, or finding documented now that **may be promoted to a task** later. Its outcome is recorded: open, promoted (to task NNNN), or declined.
- **Memory** (`memory/`): general metadata on how to work on the project, and facts about the product (pairing, conventions, decisions, glossary). It is reference context, updated in place, and **never promoted to a task**.

If it might turn into work, write a Suggestion. If every future task should know it, write Memory.

Before engineering work:

```text
Discussion -> Suggestion -> promoted Task
                              |
                              v
                     prompt -> response -> review
                              |
                              v
                           Knowledge
```

Suggestions are first-class pre-task thinking. They must not replace the prompt/response/review contract in onboarding.

## QMD (lifecycle index)

**QMD** / `queue.md` tracks live status only:

```text
# In Line
# Active
# Awaiting Review
```

Keep QMD lean. Anything that is no longer live moves to a designated file in the builder, so QMD never grows with history:

| File | Holds | Moved there when |
|------|-------|------------------|
| `BLOCKED.md` | Task ID + reason | The task cannot proceed |
| `COMPLETED.md` | Task ID | The triplet is finished |
| `DELETED.md` | Task ID + reason | The task is dropped without completion |

A task is **complete once it has all three artifacts**: `prompts/NNNN-slug.prompt.md`, `prompts/NNNN-slug.response.md`, and `prompts/NNNN-slug.review.md`. After writing the review, the Reviewer removes the ID from `queue.md` and appends it to `COMPLETED.md`. Completed tasks are not tracked in QMD; the artifacts are the record. A blocked task returns to QMD when it can proceed.

QMD is **not** the store of task meaning — that is the `.prompt.md`.

Architect owns `queue.md` and the three archive files. Reviewer updates them after review. Executor never edits them during normal execution.

## Topology invariant

```text
one project / workstream  ->  one dedicated builder repo
one builder repo          ->  one or many product repos
```

Builder holds organizational artifacts. Product repos hold implementation. See [TOPOLOGY.md](TOPOLOGY.md).

## Bootstrap

Architect creates runtime folders when needed:

```text
prompts/
suggestions/
memory/
```

Canonical bootstrap: [BOOTSTRAP.md](BOOTSTRAP.md). Empty-builder git sequence: [Existing-project initialization](examples/INIT_EXISTING_PROJECT.prompt.example.md).

## Task flow (example staffing)

ChatGPT as Architect/Reviewer; Cursor / Claude / Codex as Executors:

1. Human discusses goals with the Architect.
2. Architect may write a Suggestion, then promote a Task.
3. Architect writes `prompts/NNNN-slug.prompt.md` and registers the id on QMD.
4. Independent Active tasks may be handed to different external Executors.
5. Each Executor writes `prompts/NNNN-slug.response.md` (and product changes only if authorized).
6. Architect/Reviewer writes `prompts/NNNN-slug.review.md`, removes the ID from QMD, and appends it to `COMPLETED.md`.
7. Accepted outcomes advance knowledge; follow-ups become new prompts.

When tools do not share a workspace, sync Git (or another transport) so each role sees the artifacts. That transport is not AIOS orchestration.

## Rework

A Rework verdict still completes the reviewed task: its triplet exists, so it moves to `COMPLETED.md`. The Architect creates a new prompt that refers to the review. The Executor still hears only:

```text
execute NNNN
```
