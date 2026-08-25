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
5. Changes the product repository only when the task explicitly says so.

The human should be able to tell Cursor only:

```text
execute 0001
```

When the task does not say to change the product repository, Cursor inspects it read-only.

## Human

The human:

1. Talks to ChatGPT.
2. Pulls ChatGPT changes locally.
3. Opens Cursor on the repo folder.
4. Commits and pushes Cursor's work.
5. Asks ChatGPT to review.

For the optional builder/product topology, the human also:

1. Creates the builder repository and sets git remotes (see [Existing-project initialization](examples/INIT_EXISTING_PROJECT.prompt.example.md)).
2. Connects/authorizes **both** the builder and the product in ChatGPT and in Cursor before cross-repo work. AIOS cannot do this automatically.
3. Commits and pushes product changes when a task changed the product.

## Files

| File | Owner | Purpose |
|------|-------|---------|
| `queue.md` | ChatGPT | Task state |
| `prompts/*.prompt.md` | ChatGPT | Task instructions |
| `prompts/*.response.md` | Cursor | Execution notes |
| `prompts/*.review.md` | ChatGPT | Review verdict |
| `suggestions/` | ChatGPT | Documented ideas before tasks |
| `memory/` | ChatGPT | Durable project knowledge |

In the builder/product topology these files live in the builder. See [Repository topologies](TOPOLOGY.md).
