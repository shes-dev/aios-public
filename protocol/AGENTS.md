# Agents

AIOS does not connect ChatGPT and Cursor directly.

They work through git and markdown files.

## ChatGPT

ChatGPT is the planner and reviewer.

ChatGPT:

1. Talks with the human.
2. Documents useful discussion.
3. Creates missing runtime folders when needed.
4. Creates task prompts in `prompts/`.
5. Updates `queue.md`.
6. Reviews Cursor responses.
7. Moves accepted tasks to Completed.

ChatGPT is the only agent that should update `queue.md` during normal work.

If `prompts/`, `suggestions/`, or `memory/` does not exist yet, ChatGPT creates it instead of asking the human to do so.

## Cursor

Cursor is the executor.

Cursor:

1. Reads `queue.md`.
2. Executes the first Active task.
3. Writes `prompts/NNNN-slug.response.md`.
4. Does not edit `queue.md`.

The human should be able to tell Cursor only:

```text
execute 0001
```

## Human

The human:

1. Talks to ChatGPT.
2. Pulls ChatGPT changes locally.
3. Opens Cursor on the repo folder (the builder folder in a two-repo setup).
4. Commits and pushes Cursor's work.
5. Asks ChatGPT to review.
6. Connects GitHub, ChatGPT, and Cursor to each repository that must be used. AIOS cannot do this automatically.

## Files

| File | Owner | Purpose |
|------|-------|---------|
| `queue.md` | ChatGPT | Task state |
| `prompts/*.prompt.md` | ChatGPT | Task instructions |
| `prompts/*.response.md` | Cursor | Execution notes |
| `prompts/*.review.md` | ChatGPT | Review verdict |
| `suggestions/` | ChatGPT | Documented ideas before tasks |
| `memory/` | ChatGPT | Durable project knowledge |

This ownership does not change in a builder/product setup. The files above live in the **builder**. Product application code lives in the product repository. Cursor edits the product only when a task explicitly says so.

`aios-public` is the generic protocol source. Consumer-specific knowledge belongs in the builder (`memory/`, prompts, reviews) or in the product repository, not in `aios-public`.

Optional topology, remotes, and Human tool-connection steps: [Topology](TOPOLOGY.md). Existing-product bootstrap: [Initialize AIOS for an existing project](examples/INIT_EXISTING_PROJECT.prompt.example.md).
