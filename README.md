# AIOS

AIOS is a simple way to work with ChatGPT and Cursor without losing the thinking.

The repo is the shared workspace:

```text
ChatGPT plans -> Cursor executes -> ChatGPT reviews -> queue.md moves forward
```

No app. No server. No CLI. Just git and markdown files.

## Start

This is the default path: one repository.

1. Clone this repo.
2. Open ChatGPT and connect it to this GitHub repo.
3. Open Cursor on the cloned repo folder.
4. Start by talking to ChatGPT.

When asking ChatGPT to read or write repo files, make sure the GitHub connector is attached to that message. If it is not attached, ChatGPT may not have repo access for that turn.

Tell ChatGPT:

```text
This repo uses AIOS. Read the README and protocol. We are starting a new project. Create anything missing when the workflow needs it.
```

## Existing project

If you already have a product repository and want AIOS planning kept separate from product code, use a builder repository. That path is optional. Single-repository AIOS remains valid.

```text
aios-public
    ↓ protocol/template source
<project>-builder
    ↓ approved tasks / project knowledge
<project>
```

Full copy/paste bootstrap, remotes, ownership, and first-task flow:

- [Existing-project initialization](protocol/examples/INIT_EXISTING_PROJECT.prompt.example.md)
- [Repository topologies](protocol/TOPOLOGY.md)

The human must connect both the builder and the product in ChatGPT and in Cursor. AIOS cannot do that automatically.

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

In the builder/product topology, the loop files live in the builder. Cursor changes the product only when the task says so.

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

In the optional builder/product topology, these files live in the builder. The product repository keeps application code.

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
