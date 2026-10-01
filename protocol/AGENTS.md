# Agents

AIOS coordinates a Human, one Architect context, and external Executors through Git and Markdown in a **dedicated builder repository**.

Roles: [ROLES.md](ROLES.md). Topology: [TOPOLOGY.md](TOPOLOGY.md).

AIOS does not connect tools, launch agents, or require agent-to-agent messaging. The protocol is the artifact contract.

## Architect

The Architect:

1. Holds project/product intent and protocol context.
2. Talks with the Human; documents useful discussion.
3. Creates missing `prompts/`, `suggestions/`, and `memory/` when needed.
4. Decomposes work; writes `prompts/NNNN-slug.prompt.md`.
5. Owns `queue.md` (QMD lifecycle index).
6. Decides which independent tasks may proceed (including parallel Executors).
7. Receives `.response.md` evidence; performs or commissions review.
8. Writes/owns `prompts/NNNN-slug.review.md` (when acting as Reviewer).
9. Promotes accepted outcomes into durable knowledge / follow-up tasks.

ChatGPT is one current Architect example. The role is vendor-independent and is not a hosted AIOS runtime.

## Executor

The Executor:

1. Reads QMD only to find Active work.
2. Consumes one approved `prompts/NNNN-slug.prompt.md`.
3. Writes `prompts/NNNN-slug.response.md`.
4. Does not edit `queue.md` or write the review for that task.
5. Changes product repositories only when the task explicitly says so.

Cursor, Claude, Codex, or another capable tool may act as Executor. Independent Active tasks may run in parallel on different Executors.

```text
execute 0001
```

## Reviewer

The Reviewer (often the Architect in a distinct phase):

1. Judges the response against the prompt.
2. Writes `prompts/NNNN-slug.review.md`.
3. Updates `queue.md`.

```text
review 0001
```

## Parallel workers

Example under one Architect context:

```text
0394 -> Cursor
0395 -> Claude
0396 -> Codex
```

AIOS does not schedule or supervise those workers.

## Sequential single-agent staffing

One capable session may perform Architect, then Executor, then Reviewer with separate artifacts. Details: [ROLES.md](ROLES.md).

## Human

The Human owns vision and risky/external approvals; creates the dedicated builder; identifies product repo(s); grants tool access; starts bootstrap via the root README prompt.

## Files

| File | Writer | Purpose |
|------|--------|---------|
| `queue.md` | Architect; Reviewer after review | QMD lifecycle index |
| `prompts/*.prompt.md` | Architect | Approved task meaning |
| `prompts/*.response.md` | Executor | Evidence |
| `prompts/*.review.md` | Architect/Reviewer | Verdict |
| `suggestions/` | Architect | Pre-task thinking |
| `memory/` | Architect | Pairing and durable decisions |

These files live in the **builder**, never as the supported home inside a product repository.
