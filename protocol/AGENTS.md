# Agents

AIOS coordinates a Human and three logical roles through git and markdown files.

Roles, sequential Claude, and the ChatGPT + Cursor assignment are defined in [Roles](ROLES.md).

AIOS does not connect tools to each other. It does not require a different AI product for each role.

## Architect

The Architect:

1. Talks with the Human.
2. Documents useful discussion.
3. Creates missing runtime folders when needed.
4. Creates task prompts in `prompts/`.
5. Manages `suggestions/` and `memory/`.
6. Owns `queue.md` state.

One Claude session may act as Architect. ChatGPT may act as Architect.

If `prompts/`, `suggestions/`, or `memory/` does not exist yet, the Architect creates it instead of asking the Human to do so.

## Executor

The Executor:

1. Reads `queue.md`.
2. Executes the current Active task.
3. Writes `prompts/NNNN-slug.response.md`.
4. Does not edit `queue.md`.
5. Changes the product repository only when the task explicitly says so.

One Claude session may act as Executor after it has finished Architect work for that task. Cursor may act as Executor.

The Human should be able to say only:

```text
execute 0001
```

When the task does not say to change the product repository, the Executor inspects it read-only.

## Reviewer

The Reviewer:

1. Reviews the execution against the task.
2. Writes `prompts/NNNN-slug.review.md`.
3. Updates `queue.md` according to the result.

One Claude session may act as Reviewer after it has finished Executor work for that task. ChatGPT may act as Reviewer.

The Human should be able to say only:

```text
review 0001
```

## Sequential Claude

One Claude session may perform Architect, then Executor, then Reviewer. That is a supported first-class mode.

It must still use separate phases and separate artifacts. It must not merge the task, the response, and the review into one undocumented step. Details: [Roles](ROLES.md).

## ChatGPT + Cursor

This assignment remains valid:

- ChatGPT is Architect and Reviewer.
- Cursor is Executor.
- Cursor does not edit `queue.md`.
- ChatGPT updates `queue.md` as Architect and as Reviewer.

## Human

The Human:

1. Talks to the Architect.
2. Pulls Architect changes locally when another tool must see them.
3. Tells the Executor to run the Active task.
4. Commits and pushes work when that is the Human's job in this setup.
5. Tells the Reviewer to review.

The Human retains authority over risky, destructive, and external-impact decisions. Claude in any role must stop and ask before those actions. See [Roles](ROLES.md).

For the default builder/product topology, the Human also:

1. Authorizes repository access or creation. Claude reuses existing product and matching builder first; it creates `<product>-builder` only when none exists and creation is authorized. See [Bootstrap](BOOTSTRAP.md).
2. Connects/authorizes **both** the builder and the product in the tools that will act as Architect, Executor, and Reviewer. AIOS cannot do this automatically.
3. Commits and pushes product changes when a task changed the product.
4. Creates a missing GitHub repository only when Claude stops with the smallest handoff from [Bootstrap](BOOTSTRAP.md).

## Files

| File | Writer | Purpose |
|------|--------|---------|
| `queue.md` | Architect; Reviewer after a review | Task state |
| `prompts/*.prompt.md` | Architect | Task instructions |
| `prompts/*.response.md` | Executor | Execution notes |
| `prompts/*.review.md` | Reviewer | Review verdict |
| `suggestions/` | Architect | Documented ideas before tasks |
| `memory/` | Architect | Durable project knowledge |

The Executor must not edit `queue.md`.

In the builder/product topology these files live in the builder. See [Repository topologies](TOPOLOGY.md).
