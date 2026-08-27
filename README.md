# AIOS

AIOS is a simple git + markdown workflow for building software with AI.

You talk. Claude plans, implements, and reviews in separate steps. Progress lives in files in a builder repository, not only in chat.

No app. No server. No CLI. Just GitHub, Claude, and markdown.

## Start here

You need:

1. A GitHub account.
2. Claude with access to GitHub (or the ability to create/open repositories you authorize).
3. Either an idea for a new project, or an existing product repository to adopt.

### Copy this prompt into Claude

```text
You are running AIOS from https://github.com/shes-dev/aios-public.

Read these protocol files first and follow them:
- protocol/ROLES.md
- protocol/BOOTSTRAP.md
- protocol/TOPOLOGY.md
- protocol/WORKFLOW.md
- protocol/AGENTS.md

What I want:
<describe the new product to build, OR paste the existing product GitHub URL / owner/name>

Do this next:
1. Follow reuse-before-create. Adopt an existing product if I named one. Reuse a matching builder if one exists. Create a product and/or <product>-builder only when missing and I authorize creation. Never replace or rewrite an existing product's history.
2. Keep aios-public as protocol source only. The builder's origin must be the builder. Do not put consumer knowledge into aios-public.
3. Record durable baseline/pairing state in the builder under memory/.
4. Then begin the AIOS loop as Architect: talk with me, document useful decisions, create the first task in the builder queue when ready.
5. Keep Architect, Executor, and Reviewer as separate phases with separate prompt/response/review files even if you perform all three roles in this session.
6. Stop and ask me before creating repositories, force-pushing, deploying, or any other risky/external action.

Start by confirming what you understood and what product/builder you will adopt, reuse, or create.
```

Replace the `<describe…>` line with your project. Paste the whole block into Claude.

Claude will adopt or create the product and builder from the protocol rules. You do not need to choose a path first.

## Deeper documentation

Use these when you want the details behind the prompt:

| Topic | Doc |
|-------|-----|
| Roles (Architect / Executor / Reviewer) | [protocol/ROLES.md](protocol/ROLES.md) |
| Adopt / reuse / create bootstrap | [protocol/BOOTSTRAP.md](protocol/BOOTSTRAP.md) |
| Repository topologies | [protocol/TOPOLOGY.md](protocol/TOPOLOGY.md) |
| Task loop and queue | [protocol/WORKFLOW.md](protocol/WORKFLOW.md) |
| Agents and file ownership | [protocol/AGENTS.md](protocol/AGENTS.md) |
| Existing product + new builder git commands | [protocol/examples/INIT_EXISTING_PROJECT.prompt.example.md](protocol/examples/INIT_EXISTING_PROJECT.prompt.example.md) |
| Operator guide | [protocol/README.md](protocol/README.md) |

## Manual ChatGPT + Cursor path

Still valid. Not required for the Claude start above.

In that assignment, ChatGPT is Architect/Reviewer and Cursor is Executor. Same files and loop:

```text
Talk -> Document -> Task -> Execute -> Review -> Done
```

Typical commands:

```text
Document this
execute 0001
review 0001
```

Cursor must not edit `queue.md`. ChatGPT owns queue updates as Architect and Reviewer.

Open the builder folder in Cursor. Connect both builder and product in ChatGPT and Cursor before cross-repo work. AIOS cannot authorize tools for you.

## Files

AIOS files live in the **builder** (or in a single-repo workspace if you choose that topology):

| File | Writer | Purpose |
|------|--------|---------|
| `queue.md` | Architect; Reviewer after review | Task state |
| `prompts/*.prompt.md` | Architect | Task instructions |
| `prompts/*.response.md` | Executor | Work summary |
| `prompts/*.review.md` | Reviewer | Review verdict |
| `suggestions/` | Architect | Ideas before tasks |
| `memory/` | Architect | Decisions and pairing worth keeping |

The **product** repository keeps application, runtime, and deployment code. The Executor changes it only when a task says so.

## License

MIT.
