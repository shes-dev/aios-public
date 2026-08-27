# Operator Guide

Roles: [Roles](ROLES.md). One Claude session may be Architect, Executor, and Reviewer sequentially. ChatGPT + Cursor remains valid.

## Basic Loop

ChatGPT + Cursor assignment:

1. Talk with ChatGPT.
2. Tell ChatGPT: `Document this`.
3. Tell ChatGPT to create a task.
4. Pull the repo locally.
5. Tell Cursor: `execute 0001`.
6. Commit and push Cursor's changes.
7. Tell ChatGPT: `review 0001`.
8. ChatGPT updates `queue.md`.

Claude-only assignment: tell one Claude session the same commands in order (`document this`, `execute 0001`, `review 0001`). Keep separate prompt, response, and review files.

That loop is the same in both topologies. Canonical stage-1 topology and adopt/reuse/create rules: [Repository topologies](TOPOLOGY.md), [Bootstrap](BOOTSTRAP.md).

## Bootstrap

Canonical stage-1 bootstrap is product + builder with reuse-before-create. See [Bootstrap](BOOTSTRAP.md).

The Architect creates missing runtime folders when needed:

```text
prompts/
suggestions/
memory/
```

For a **new** or **existing** project, follow [Bootstrap](BOOTSTRAP.md) before creating any GitHub repository. Single-repository clone of this protocol repo remains valid; it is not the Claude stage-1 default.

The git sequence for an existing product and a missing builder is [Existing-project initialization](examples/INIT_EXISTING_PROJECT.prompt.example.md).

## Queue

The Architect owns `queue.md`. The Reviewer updates it after a review.

The Executor does not edit it.

```text
# In Line
# Active
# Awaiting Review
# Completed
# Blocked
```

In the builder/product topology, this file lives in the builder.

## Task Files

```text
prompts/NNNN-slug.prompt.md    # written by Architect
prompts/NNNN-slug.response.md  # written by Executor
prompts/NNNN-slug.review.md    # written by Reviewer
```

## Executor Rule

The Executor executes the task and writes the response.

The Executor does not edit `queue.md`.

The Executor changes the product repository only when the task explicitly says so.

## Architect Rule

The Architect plans, documents, creates missing folders, writes tasks, and owns `queue.md` state.

## Reviewer Rule

The Reviewer writes the review and then updates `queue.md` according to the result.

## Ownership

Builder (or the single AIOS repo) owns `queue.md`, `prompts/`, `suggestions/`, `memory/`, and project knowledge.

The product repository owns application, runtime, and deployment code.

`aios-public` stays generic protocol/template source.
