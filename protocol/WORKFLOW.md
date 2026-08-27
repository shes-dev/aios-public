# Workflow

AIOS is a file-based workflow for a Human and three roles: Architect, Executor, and Reviewer.

```text
Talk -> Document -> Task -> Execute -> Review -> Done
```

One Claude session may perform Architect, Executor, and Reviewer sequentially. ChatGPT + Cursor remains valid. See [Roles](ROLES.md).

## Queue

`queue.md` has five sections:

```text
# In Line
# Active
# Awaiting Review
# Completed
# Blocked
```

The Architect owns `queue.md`. The Reviewer updates it after a review.

The Executor does not edit `queue.md`.

## Bootstrap

The repo starts small.

The Architect creates runtime folders when the workflow needs them:

```text
prompts/
suggestions/
memory/
```

Do not ask the Human to create these folders manually.

Canonical stage-1 product/builder bootstrap: [Bootstrap](BOOTSTRAP.md) and [Repository topologies](TOPOLOGY.md). Stage 1 assumes the Human already created both repositories; Claude initializes or resumes the builder from the root README prompt.

Single-repository bootstrap remains valid: clone this protocol repo and start talking to the Architect.

Advanced empty-builder git sequence when a builder does **not** already exist: [Existing-project initialization](examples/INIT_EXISTING_PROJECT.prompt.example.md).

## Task Flow

ChatGPT as Architect/Reviewer and Cursor as Executor is one valid assignment:

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

The same artifacts and order apply when one Claude session plays every role. The Human tells that session to change roles (`document this` / `execute 0001` / `review 0001`) instead of switching products. Claude must still write a prompt, then a response, then a review.

In the builder/product topology:

- steps 4–11 use the **builder** repository for queue, prompts, responses, and reviews
- the Human must already have connected both builder and product in the tools that will act as Architect, Executor, and Reviewer
- the Executor modifies the product repository only when the task explicitly says so
- if the product changed, the Human also commits and pushes the product repository

## Files

```text
prompts/NNNN-slug.prompt.md
prompts/NNNN-slug.response.md
prompts/NNNN-slug.review.md
```

The prompt says what to do.

The response says what the Executor did.

The review says whether the Reviewer accepts it.

## Rework

If a task needs more work, the Architect creates a new task that refers to the review.

The Human still tells the Executor only:

```text
execute 0002
```

## Suggestions

Use suggestions when the discussion is useful but not ready for implementation.

```text
Discussion -> Document this -> Suggestion -> Promote it -> Task
```
