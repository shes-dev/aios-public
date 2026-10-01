# Roles

AIOS separates thinking, implementation, and review. Those are **roles**, not products.

Tools and vendors are replaceable. Valid staffing examples include:

- one capable agent sequentially playing Architect → Executor → Reviewer while preserving phase boundaries;
- ChatGPT as Architect/Reviewer + Cursor as Executor;
- ChatGPT as Architect/Reviewer + multiple external workers (Cursor / Claude / Codex) on independent tasks;
- mixed human/AI teams.

The Human keeps authority over risky, destructive, and external-impact decisions.

## Logical roles

| Role | Authority | Writes |
|------|-----------|--------|
| **Architect** | Defines work. Manages `suggestions/` and `memory/`. Owns QMD (`queue.md`) lifecycle state. | `prompts/*.prompt.md`, `suggestions/`, `memory/`, `queue.md` |
| **Executor** | Executes the current approved/Active task only. | `prompts/*.response.md` and the implementation the task allows |
| **Reviewer** | Judges the execution against the task. | `prompts/*.review.md`, then `queue.md` according to the result |

These remain separate logical phases even when the same agent session plays every role.

AIOS does not launch, schedule, route, connect, or monitor Executors. External workers consume approved tasks and return evidence through Git/Markdown artifacts.

## Sequential single-agent pattern

A single capable session may switch roles in order:

```text
Architect -> Executor -> Reviewer
```

Typical commands (same in any tool):

```text
document this
create a task
execute 0001
review 0001
```

While acting as **Architect**, the agent may talk, document, create missing `prompts/`, `suggestions/`, and `memory/` folders, write the task prompt, and update `queue.md`.

While acting as **Executor**, the agent executes only the Active task, writes the matching `.response.md`, and **must not** edit `queue.md`, rewrite the task, or write the review.

While acting as **Reviewer**, the agent reads the task and the response (and the diff), writes the matching `.review.md`, then updates `queue.md` (Completed, rework task, or Blocked).

Do not collapse task definition, execution, and review into one undocumented action. Each phase leaves its own durable file.

## ChatGPT + Cursor

Still valid. Do not treat any single vendor as required.

| Role | Usual assignment |
|------|------------------|
| Architect | ChatGPT |
| Executor | Cursor |
| Reviewer | ChatGPT |

Cursor does not edit `queue.md`. ChatGPT manages `queue.md` as Architect and Reviewer.

## Parallel external workers

Independent Active tasks may be executed concurrently by different external tools. The protocol invariant is unchanged: Architect defines approved work → Executor produces response evidence → Reviewer reviews → QMD/knowledge advances. Concurrency is a Human/tooling choice; AIOS does not orchestrate it.

## Artifacts

Keep these files distinct for every task:

```text
prompts/NNNN-slug.prompt.md     # Architect
prompts/NNNN-slug.response.md   # Executor
prompts/NNNN-slug.review.md     # Reviewer
queue.md                        # Architect; Reviewer after a review (QMD)
```

Executor does not write the prompt or the review for the task it is executing. Reviewer does not treat an execution as accepted without a review file.

## Human authority

The Human decides anything that is risky, destructive, or has impact outside the workspace. Agents in any role must stop and ask before:

- force-push, history rewrite, or hard reset of shared branches;
- creating, deleting, or transferring repositories unless the Human already authorized that step;
- deploying, publishing, or changing production/external systems;
- exposing or rotating secrets;
- other irreversible or externally visible actions.

Git remotes, tool connection, and commits/pushes remain Human operations unless the Human has already authorized the current session to do them. Manual Git courier steps between tools are one possible operating pattern, not an intrinsic requirement of AIOS.

## What this file is not

This file is the role contract only. Beginner onboarding copy lives in the root README. Bootstrap and topology live in [Bootstrap](BOOTSTRAP.md) and [Repository topologies](TOPOLOGY.md).
