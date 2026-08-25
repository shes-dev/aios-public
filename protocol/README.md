# Operator Guide

## Basic Loop

1. Talk with ChatGPT.
2. Tell ChatGPT: `Document this`.
3. Tell ChatGPT to create a task.
4. Pull the repo locally.
5. Tell Cursor: `execute 0001`.
6. Commit and push Cursor's changes.
7. Tell ChatGPT: `review 0001`.
8. ChatGPT updates `queue.md`.

That loop is the same in both topologies. See [Repository topologies](TOPOLOGY.md).

## Bootstrap

ChatGPT creates missing runtime folders when needed:

```text
prompts/
suggestions/
memory/
```

For a **new** AIOS project, clone this protocol repository and use it as the single workspace. That is the default path in the root README.

For an **existing** product repository, the optional builder path is [Existing-project initialization](examples/INIT_EXISTING_PROJECT.prompt.example.md).

## Queue

ChatGPT manages `queue.md`.

Cursor does not edit it.

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
prompts/NNNN-slug.prompt.md    # written by ChatGPT
prompts/NNNN-slug.response.md  # written by Cursor
prompts/NNNN-slug.review.md    # written by ChatGPT
```

## Cursor Rule

Cursor executes the task and writes the response.

Cursor does not edit `queue.md`.

Cursor changes the product repository only when the task explicitly says so.

## ChatGPT Rule

ChatGPT plans, reviews, documents, creates missing folders, and moves the queue.

## Ownership

Builder (or the single AIOS repo) owns `queue.md`, `prompts/`, `suggestions/`, `memory/`, and project knowledge.

The product repository owns application, runtime, and deployment code.

`aios-public` stays generic protocol/template source.
