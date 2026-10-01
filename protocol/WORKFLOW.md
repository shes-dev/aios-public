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

## Suggestions (secondary context)

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
# Blocked
```

Accepted history moves to `COMPLETED.md` when used; paused work may live in `BACKLOG.md`. QMD is **not** the store of task meaning — that is the `.prompt.md`.

Architect owns `queue.md`. Reviewer updates it after review. Executor never edits `queue.md`, `COMPLETED.md`, or `BACKLOG.md` during normal execution.

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
6. Architect/Reviewer writes `prompts/NNNN-slug.review.md` and updates QMD.
7. Accepted outcomes advance knowledge; follow-ups become new prompts.

When tools do not share a workspace, sync Git (or another transport) so each role sees the artifacts. That transport is not AIOS orchestration.

## Rework

If review rejects or requests more work, the Architect creates a new prompt that refers to the review. The Executor still hears only:

```text
execute NNNN
```
