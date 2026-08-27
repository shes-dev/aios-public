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

That loop is the same in both topologies. Stage-1 beginner path: [Bootstrap](BOOTSTRAP.md). Topology: [Repository topologies](TOPOLOGY.md).

## Bootstrap

Stage 1: Human creates product + builder, grants Claude access, pastes the root README prompt. Claude initializes or resumes AIOS **in the builder** using Git files only. See [Bootstrap](BOOTSTRAP.md).

The Architect creates missing runtime folders when needed:

```text
prompts/
suggestions/
memory/
```

If those already exist with AIOS state, resume them. Do not overwrite.

Advanced create/adopt when repos are missing, and the empty-builder git sequence, are documented in [Bootstrap](BOOTSTRAP.md) and [Existing-project initialization](examples/INIT_EXISTING_PROJECT.prompt.example.md). They are not the beginner path.

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
