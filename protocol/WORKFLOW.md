# Workflow

AIOS is a file-based workflow for ChatGPT, Cursor, and a human.

```text
Talk -> Document -> Task -> Cursor executes -> Review -> Done
```

## Queue

`queue.md` has five sections:

```text
# In Line
# Active
# Awaiting Review
# Completed
# Blocked
```

ChatGPT manages the queue.

Cursor does not edit `queue.md`.

## Bootstrap

The repo starts small.

ChatGPT creates runtime folders when the workflow needs them:

```text
prompts/
suggestions/
memory/
```

Do not ask the human to create these folders manually.

## Task Flow

1. Human talks with ChatGPT.
2. Human says: `Document this`.
3. ChatGPT writes a suggestion or task.
4. ChatGPT adds the task to `queue.md`.
5. Human pulls the repo locally.
6. Human opens Cursor on the repo folder.
7. Human tells Cursor: `execute 0001`.
8. Cursor does the work and writes a response file.
9. Human commits and pushes Cursor's changes.
10. Human tells ChatGPT: `review 0001`.
11. ChatGPT writes the review and updates `queue.md`.
12. If accepted, ChatGPT moves the task to Completed.

## Files

```text
prompts/NNNN-slug.prompt.md
prompts/NNNN-slug.response.md
prompts/NNNN-slug.review.md
```

The prompt says what to do.

The response says what Cursor did.

The review says whether ChatGPT accepts it.

## Rework

If a task needs more work, ChatGPT creates a new task that refers to the review.

The human still tells Cursor only:

```text
execute 0002
```

## Suggestions

Use suggestions when the discussion is useful but not ready for implementation.

```text
Discussion -> Document this -> Suggestion -> Promote it -> Task
```
