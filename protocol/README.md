# Operator Guide

## Topologies

Single-repository AIOS is the default. Builder/product separation is optional.

Read [Topology](TOPOLOGY.md) for ownership, remotes, and tool connection.

To adopt an existing product repo, follow [Initialize AIOS for an existing project](examples/INIT_EXISTING_PROJECT.prompt.example.md) instead of duplicating those commands here.

## Basic Loop

1. Talk with ChatGPT.
2. Tell ChatGPT: `Document this`.
3. Tell ChatGPT to create a task.
4. Pull the repo locally.
5. Tell Cursor: `execute 0001`.
6. Commit and push Cursor's changes.
7. Tell ChatGPT: `review 0001`.
8. ChatGPT updates `queue.md`.

## Bootstrap

ChatGPT creates missing runtime folders when needed:

```text
prompts/
suggestions/
memory/
```

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

## Task Files

```text
prompts/NNNN-slug.prompt.md    # written by ChatGPT
prompts/NNNN-slug.response.md  # written by Cursor
prompts/NNNN-slug.review.md    # written by ChatGPT
```

## Cursor Rule

Cursor executes the task and writes the response.

That is all.

In a builder/product setup, Cursor is opened on the builder. Cursor modifies the product repository only when the active task says so.

## ChatGPT Rule

ChatGPT plans, reviews, documents, creates missing folders, and moves the queue.

In a builder/product setup, ChatGPT owns the builder `queue.md` and writes prompts, reviews, and memory in the builder. `aios-public` stays generic.

## Tool connection

Connecting GitHub, ChatGPT, and Cursor is a Human step. AIOS cannot authorize those tools.

For two repositories, the Human must authorize both the builder and the product in the Architect environment and open Cursor on the builder with the product available for inspection.
