# AIOS

AIOS is a simple git + markdown workflow for building software with Claude.

You talk. Claude plans, implements, and reviews in separate steps. Progress lives in files in your **builder** repository, not only in chat.

No app. No server. No CLI. Just GitHub, Claude, and markdown.

## Start here

Stage 1 needs four Human setup steps, then one paste into Claude.

1. Create the product repository on GitHub (`owner/project`).
2. Create the matching builder repository (`owner/project-builder`).
3. Give Claude access to both repositories.
4. Copy the prompt below, fill in the two repo names, and paste it into Claude.

Claude then reads the protocol, initializes or resumes AIOS files in the builder, and continues the project. Claude does **not** create repositories, use hosted AIOS, or need Cursor for Stage 1.

### Copy this prompt into Claude

```text
Use https://github.com/shes-dev/aios-public as the AIOS protocol.

Product repo: <owner/project>
Builder repo: <owner/project-builder>

Read the protocol, initialize or resume AIOS in the builder, and continue this project strictly through AIOS.
You may act sequentially as Architect, Executor, and Reviewer, but keep task, response, review, and queue state as separate durable artifacts in the builder.
```

Replace the two `<owner/...>` lines, then paste the whole block.

## What Claude does after that

Using only Git files in the builder:

1. Reads `shes-dev/aios-public` protocol (`ROLES`, `BOOTSTRAP`, `TOPOLOGY`, `WORKFLOW`, `AGENTS`).
2. Treats the builder as the AIOS workspace and the product as implementation code.
3. If the builder is empty: initializes `queue.md`, `prompts/`, `suggestions/`, `memory/`, and records pairing under `memory/`.
4. If the builder already has AIOS state: resumes it. Does not overwrite queue, prompts, reviews, suggestions, or memory.
5. Works as Architect → Executor → Reviewer with separate `.prompt.md`, `.response.md`, and `.review.md` files.
6. Asks you only for real product decisions or risky/external actions.

## Deeper documentation

| Topic | Doc |
|-------|-----|
| Stage-1 handoff (repos already exist) | [protocol/BOOTSTRAP.md](protocol/BOOTSTRAP.md) |
| Roles | [protocol/ROLES.md](protocol/ROLES.md) |
| Topologies | [protocol/TOPOLOGY.md](protocol/TOPOLOGY.md) |
| Task loop | [protocol/WORKFLOW.md](protocol/WORKFLOW.md) |
| Agents / file ownership | [protocol/AGENTS.md](protocol/AGENTS.md) |
| Advanced: create builder from protocol | [protocol/examples/INIT_EXISTING_PROJECT.prompt.example.md](protocol/examples/INIT_EXISTING_PROJECT.prompt.example.md) |
| Operator guide | [protocol/README.md](protocol/README.md) |

## Manual ChatGPT + Cursor path

Still valid. Not required for Stage 1.

ChatGPT as Architect/Reviewer, Cursor as Executor. Same files and loop. Cursor must not edit `queue.md`.

## Files

| File | Writer | Purpose |
|------|--------|---------|
| `queue.md` | Architect; Reviewer after review | Task state |
| `prompts/*.prompt.md` | Architect | Task instructions |
| `prompts/*.response.md` | Executor | Work summary |
| `prompts/*.review.md` | Reviewer | Review verdict |
| `suggestions/` | Architect | Ideas before tasks |
| `memory/` | Architect | Pairing and decisions |

These live in the **builder**. The **product** holds application code. Builder Git files are the canonical Stage-1 persistence.

## License

MIT.
