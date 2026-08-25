# AIOS

AIOS is a simple way to work with ChatGPT and Cursor without losing the thinking.

The repo is the shared workspace:

```text
ChatGPT plans -> Cursor executes -> ChatGPT reviews -> queue.md moves forward
```

No app. No server. No CLI. Just git and markdown files.

AIOS supports two setups. Both use the same loop. **Single-repository** is the default. A separate **builder** repository is optional when you already have a product repo and want planning history kept out of it. See [Topology](protocol/TOPOLOGY.md).

## Start

Use this path for a new project, or when protocol and product should share one GitHub repository.

1. Clone this repo.
2. Open ChatGPT and connect it to this GitHub repo.
3. Open Cursor on the cloned repo folder.
4. Start by talking to ChatGPT.

When asking ChatGPT to read or write repo files, make sure the GitHub connector is attached to that message. If it is not attached, ChatGPT may not have repo access for that turn.

Tell ChatGPT:

```text
This repo uses AIOS. Read the README and protocol. We are starting a new project. Create anything missing when the workflow needs it.
```

## Existing product

Use this path when `<PRODUCT_REPOSITORY>` already exists and you want a dedicated `<PRODUCT_REPOSITORY>-builder` workspace.

That is a Human Git setup plus Human tool connection, then the normal Architect/Executor loop. AIOS cannot create the builder repo or attach ChatGPT/Cursor for you.

Follow [Initialize AIOS for an existing project](protocol/examples/INIT_EXISTING_PROJECT.prompt.example.md). In short:

1. Create an independent builder repo. Keep `origin` on the builder. Add `aios-public` as a secondary remote and pull this protocol into the builder.
2. Connect **both** the builder and the product repositories in ChatGPT and in Cursor. Open Cursor on the **builder**.
3. Ask ChatGPT (Architect) to create the first builder task and update builder `queue.md`.
4. Tell Cursor `execute 0001`. Cursor inspects the product read-only, writes builder files, and does not change product behavior during bootstrap.
5. Commit, push, and tell ChatGPT `review 0001`.

Do not start product implementation until that review passes.

## The Loop

1. Talk with ChatGPT.
2. Make sure GitHub is attached when repo changes are needed.
3. Tell ChatGPT: **Document this**.
4. Tell ChatGPT to create a task.
5. Pull the changes locally.
6. Tell Cursor: **execute 0001**.
7. Cursor writes a response file.
8. Commit and push Cursor's work.
9. Make sure GitHub is attached again.
10. Tell ChatGPT: **review 0001**.
11. ChatGPT writes the review and updates `queue.md`.
12. When accepted, ChatGPT moves the task to Completed.

## Files

The repo starts small.

ChatGPT creates runtime folders when needed.

| File | Owner | Purpose |
|------|-------|---------|
| `queue.md` | ChatGPT | Task state |
| `prompts/*.prompt.md` | ChatGPT | Task instructions |
| `prompts/*.response.md` | Cursor | Work summary |
| `prompts/*.review.md` | ChatGPT | Review verdict |
| `suggestions/` | ChatGPT | Ideas before tasks |
| `memory/` | ChatGPT | Decisions worth keeping |

If `prompts/`, `suggestions/`, or `memory/` does not exist yet, ChatGPT creates it.

In a builder/product setup these files live in the builder. See [Topology](protocol/TOPOLOGY.md).

## Rule

Cursor executes tasks.

ChatGPT manages `queue.md`.

Cursor must not move tasks between queue sections.

## Example

You tell ChatGPT:

```text
Let's think about adding a simple onboarding flow.
```

After the discussion, make sure GitHub is attached and say:

```text
Document this and create a task.
```

ChatGPT creates the needed files:

```text
suggestions/0001-onboarding-flow.prompt.md
prompts/0001-onboarding-flow.prompt.md
queue.md
```

You pull locally:

```bash
git pull
```

Tell Cursor:

```text
execute 0001
```

Cursor does the work and writes:

```text
prompts/0001-onboarding-flow.response.md
```

Commit and push Cursor's work.

Then make sure GitHub is attached and tell ChatGPT:

```text
review 0001
```

ChatGPT writes the review and updates `queue.md`.

## License

MIT.
