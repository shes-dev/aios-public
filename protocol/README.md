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

## ChatGPT Rule

ChatGPT plans, reviews, documents, creates missing folders, and moves the queue.
