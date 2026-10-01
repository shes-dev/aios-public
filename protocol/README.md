# Operator Guide

Roles: [Roles](ROLES.md). Staff by example: sequential single-agent phases, ChatGPT + Cursor, or ChatGPT + multiple external workers. AIOS does not launch or connect those workers.

## Basic Loop

Example — ChatGPT + Cursor:

1. Talk with ChatGPT.
2. Tell ChatGPT: `Document this`.
3. Tell ChatGPT to create a task.
4. Sync Git when the Executor needs the update (optional if tools share the workspace).
5. Tell Cursor: `execute 0001`.
6. Sync the Executor response back when needed.
7. Tell ChatGPT: `review 0001`.
8. ChatGPT updates `queue.md`.

Sequential single-agent assignment: tell one capable session the same commands in order (`document this`, `execute 0001`, `review 0001`). Keep separate prompt, response, and review files.

Independent Active tasks may run in parallel on different Executors. Bootstrap: [Bootstrap](BOOTSTRAP.md). Topology: [Repository topologies](TOPOLOGY.md). Knowledge lifecycle: [Workflow](WORKFLOW.md).

## Bootstrap

Human creates product + builder (typical), grants a capable Architect agent access, pastes the root README prompt. The agent initializes or resumes AIOS **in the builder** using Git files only. See [Bootstrap](BOOTSTRAP.md).

The Architect creates missing runtime folders when needed:

```text
prompts/
suggestions/
memory/
```

If those already exist with AIOS state, resume them. Do not overwrite.

Advanced create/adopt when repos are missing, and the empty-builder git sequence, are documented in [Bootstrap](BOOTSTRAP.md) and [Existing-project initialization](examples/INIT_EXISTING_PROJECT.prompt.example.md). They are not the beginner path.

## QMD / Queue

**QMD** is the human-readable Markdown operational board backed by `queue.md`. Not a hosted service.

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
