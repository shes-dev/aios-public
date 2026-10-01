# Agents

AIOS coordinates a Human and three logical roles through git and markdown files.

Roles and staffing examples are defined in [Roles](ROLES.md).

AIOS does not connect tools to each other, launch agents, or require a different AI product for each role. Execution is external: workers consume approved tasks and return evidence through durable artifacts.

## Architect

The Architect:

1. Talks with the Human.
2. Documents useful discussion.
3. Creates missing runtime folders when needed.
4. Creates task prompts in `prompts/`.
5. Manages `suggestions/` and `memory/`.
6. Owns `queue.md` (QMD) state.

ChatGPT, Claude, Cursor, or another capable agent may act as Architect.

If `prompts/`, `suggestions/`, or `memory/` does not exist yet, the Architect creates it instead of asking the Human to do so.

## Executor

The Executor:

1. Reads `queue.md`.
2. Executes the current Active task.
3. Writes `prompts/NNNN-slug.response.md`.
4. Does not edit `queue.md`.
5. Changes the product repository only when the task explicitly says so.

Cursor, Claude, Codex, or another capable agent may act as Executor. Independent Active tasks may be taken by different Executors in parallel.

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

ChatGPT, Claude, or another capable agent may act as Reviewer.

The Human should be able to say only:

```text
review 0001
```

## Sequential single-agent pattern

One capable session may perform Architect, then Executor, then Reviewer. That is a supported pattern.

It must still use separate phases and separate artifacts. It must not merge the task, the response, and the review into one undocumented step. Details: [Roles](ROLES.md).

## ChatGPT + Cursor

This assignment remains valid:

- ChatGPT is Architect and Reviewer.
- Cursor is Executor.
- Cursor does not edit `queue.md`.
- ChatGPT updates `queue.md` as Architect and as Reviewer.

## Parallel workers

ChatGPT (or another Architect/Reviewer) may keep QMD while Cursor, Claude, Codex, and others execute independent Active tasks concurrently. AIOS does not schedule or supervise those workers.

## Human

The Human:

1. Owns vision, product decisions, and approvals for risky/external actions.
2. Talks to the Architect.
3. Ensures Executors can see approved artifacts (shared workspace, Git sync, or another transport — mechanics vary).
4. Tells the Executor to run the Active task (or authorizes a worker that already has access).
5. Tells the Reviewer to review.

The Human is not inherently required to shuttle every file between tools; that is one operating pattern when tools do not share state.

For the default builder/product topology, the Human also typically:

1. Creates the product and matching builder repositories before the first bootstrap.
2. Grants the chosen tools access to both.
3. Pastes the root README start prompt with both repo names filled in.
4. Ensures product commits land when a task changed the product.

An Architect agent initializes or resumes AIOS in the builder. See [Bootstrap](BOOTSTRAP.md).

## Files

| File | Writer | Purpose |
|------|--------|---------|
| `queue.md` | Architect; Reviewer after a review | QMD / task state |
| `prompts/*.prompt.md` | Architect | Task instructions |
| `prompts/*.response.md` | Executor | Execution notes |
| `prompts/*.review.md` | Reviewer | Review verdict |
| `suggestions/` | Architect | Documented ideas before tasks |
| `memory/` | Architect | Durable project knowledge |

The Executor must not edit `queue.md`.

In the builder/product topology these files live in the builder. See [Repository topologies](TOPOLOGY.md).
