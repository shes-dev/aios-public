# Roles

AIOS separates thinking, implementation, and review. Those are **roles**, not products.

One Claude session may perform all three roles **sequentially**. ChatGPT as Architect/Reviewer and Cursor as Executor remains a valid assignment.

The Human keeps authority over risky, destructive, and external-impact decisions.

## Logical roles

| Role | Authority | Writes |
|------|-----------|--------|
| **Architect** | Defines work. Manages `suggestions/` and `memory/`. Owns `queue.md` state. | `prompts/*.prompt.md`, `suggestions/`, `memory/`, `queue.md` |
| **Executor** | Executes the current approved/Active task only. | `prompts/*.response.md` and the implementation the task allows |
| **Reviewer** | Judges the execution against the task. | `prompts/*.review.md`, then `queue.md` according to the result |

These remain separate logical phases even when the same Claude session plays every role.

## Sequential Claude

A single Claude session may switch roles in order:

```text
Architect -> Executor -> Reviewer
```

The Human (or Claude, after stating the role change) uses the same commands:

```text
document this
create a task
execute 0001
review 0001
```

While acting as **Architect**, Claude may talk, document, create missing `prompts/`, `suggestions/`, and `memory/` folders, write the task prompt, and update `queue.md`.

While acting as **Executor**, Claude executes only the Active task, writes the matching `.response.md`, and **must not** edit `queue.md`, rewrite the task, or write the review.

While acting as **Reviewer**, Claude reads the task and the response (and the diff), writes the matching `.review.md`, then updates `queue.md` (Completed, rework task, or Blocked).

Claude must not collapse task definition, execution, and review into one undocumented action. Each phase leaves its own durable file.

## ChatGPT + Cursor

Still valid. Do not treat Claude-only as required.

| Role | Usual assignment |
|------|------------------|
| Architect | ChatGPT |
| Executor | Cursor |
| Reviewer | ChatGPT |

Cursor does not edit `queue.md`. ChatGPT manages `queue.md` as Architect and Reviewer.

## Artifacts

Keep these files distinct for every task:

```text
prompts/NNNN-slug.prompt.md     # Architect
prompts/NNNN-slug.response.md   # Executor
prompts/NNNN-slug.review.md     # Reviewer
queue.md                        # Architect; Reviewer after a review
```

Executor does not write the prompt or the review for the task it is executing. Reviewer does not treat an execution as accepted without a review file.

## Human authority

The Human decides anything that is risky, destructive, or has impact outside the workspace. Claude (in any role) must stop and ask before:

- force-push, history rewrite, or hard reset of shared branches;
- creating, deleting, or transferring repositories unless the Human already authorized that step;
- deploying, publishing, or changing production/external systems;
- exposing or rotating secrets;
- other irreversible or externally visible actions.

Git remotes, tool connection, and commits/pushes remain Human operations unless the Human has already authorized the current session to do them.

## What this file is not

This file is the role contract only. Onboarding copy, repository bootstrap, and topology are documented elsewhere and are unchanged by this contract.
