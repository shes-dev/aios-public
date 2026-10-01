# AIOS

AIOS is a **vendor-agnostic protocol** for humans and AI agents collaborating on long-lived work. It preserves thinking, execution evidence, review, and knowledge as durable Git and Markdown artifacts — not as disposable chat.

> **AI agents are workers. The protocol is the organization.**

Roles are logical contracts (Architect, Executor, Reviewer, Human). Tools and vendors are replaceable. AIOS does not launch, schedule, route, connect, or monitor agents. External workers consume approved tasks and return evidence through the same files.

## Knowledge lifecycle

AIOS is not only a task board. The canonical flow is:

```text
Discussion
  ↓
Document
  ↓
Suggestion
  ↓
Promote
  ↓
Task
  ↓
Implementation
  ↓
Review
  ↓
Knowledge
```

**Suggestions** capture product and organizational thinking before implementation. Only promoted work becomes **Tasks**. Accepted outcomes become durable **Knowledge**.

## QMD — the operational board

**QMD** is the human-readable Markdown operational queue / task board. It is backed by canonical files such as `queue.md` and the matching task artifacts under `prompts/`. QMD is not a hosted runtime service.

Illustrative board (independent tasks, different external workers):

```text
# In Line
0003  document pricing edge cases          → Architect

# Active
0001  fix auth redirect                    → Cursor
0002  draft onboarding copy                → Claude
0004  explore billing API shape            → Codex

# Awaiting Review
0005  add health endpoint                  → (response ready)

# Blocked
```

**Ownership:** Architect and Reviewer own lifecycle state on the board. Executors execute Active tasks and write responses; they do not arbitrarily rewrite queue state.

## Protocol invariant vs tooling

**Invariant (always):**

```text
Architect defines approved work
  → external Executor consumes it
  → response / evidence is produced
  → Reviewer reviews
  → canonical state and knowledge advance
```

**Transport / tooling (examples, not the protocol):** Git pull/push, opening Cursor / Claude / Codex / ChatGPT, pasting a bootstrap prompt, or any other way you move files between people and tools.

Independent approved tasks may run **in parallel** on different external workers. AIOS does not orchestrate that concurrency; the board and artifacts make it visible.

## Supported role assignments (examples)

The invariant is the role/artifact contract, not the vendor. Valid examples:

| Pattern | Architect / Reviewer | Executor(s) |
|---------|----------------------|-------------|
| One capable agent, sequential phases | Same session, phase boundaries preserved | Same session after Architect phase |
| Split tools | ChatGPT | Cursor |
| Parallel workers | ChatGPT | Cursor + Claude + Codex on independent tasks |
| Mixed teams | Human + AI | Human + AI |

Claude-only sequential operation remains a supported pattern. It is not the definition of AIOS.

## Try AIOS

About one minute to start:

1. Create (or reuse) a **builder** repository for AIOS state and a **product** repository for implementation. Private repos are fine.
2. Open any capable Architect agent with access to both (and to this protocol).
3. Paste:

```text
Use https://github.com/shes-dev/aios-public as the AIOS protocol.

Product repo: <owner/project>
Builder repo: <owner/project-builder>

Read the protocol first, especially:
- protocol/ROLES.md
- protocol/BOOTSTRAP.md
- protocol/TOPOLOGY.md
- protocol/WORKFLOW.md
- protocol/AGENTS.md

Initialize or resume AIOS in the builder using Git/Markdown artifacts only.
Preserve role boundaries and durable files:
- Architect creates task prompts and owns queue.md (QMD).
- Executor runs only Active tasks and writes matching responses.
- Reviewer writes matching reviews, then updates queue.md.

You may staff roles with any capable tools (including one agent sequentially).
Do not assume AIOS launches or controls workers.

Authorize normal builder initialization commits/pushes when the builder is empty.
Do not modify the product yet. Stop and ask before force-push, history rewrite,
deploy, publish, delete repos, expose secrets, or other risky/external actions.

If the builder is empty: initialize from the protocol, record product/builder pairing
under memory/, commit and push, then stop before task 0001 and ask what we are building.
If the builder already has AIOS state: resume without overwriting queue, prompts,
reviews, suggestions, or memory.
```

Successful first-run state in the **builder** looks like:

```text
queue.md
prompts/
suggestions/
memory/
```

The **product** holds implementation and stays untouched until an approved task authorizes changes.

Topology detail (product + builder + protocol source): [protocol/TOPOLOGY.md](protocol/TOPOLOGY.md). Full bootstrap notes: [protocol/BOOTSTRAP.md](protocol/BOOTSTRAP.md).

### Tested example: Claude Code

Claude Code is one concrete, tested bootstrap path (one session may act Architect → Executor → Reviewer sequentially). See [protocol/BOOTSTRAP.md](protocol/BOOTSTRAP.md) for the Claude-specific checklist. It does not define AIOS; any capable Architect agent can run the prompt above.

## Human authority

The Human owns vision, product decisions, and approvals for risky or external actions (force-push, deploy, publish, secrets, irreversible changes). The Human is not inherently required to shuttle every artifact between tools — that is one possible operating pattern when tools do not share a workspace.

## Files

| File | Writer | Purpose |
|------|--------|---------|
| `queue.md` (QMD) | Architect; Reviewer after review | Operational task board |
| `prompts/*.prompt.md` | Architect | Task instructions |
| `prompts/*.response.md` | Executor | Work / evidence summary |
| `prompts/*.review.md` | Reviewer | Review verdict |
| `suggestions/` | Architect | Ideas before tasks |
| `memory/` | Architect | Pairing and durable decisions |

These live in the **builder**. Builder Git files are the canonical organizational state for this protocol path.

## Deeper documentation

| Topic | Doc |
|-------|-----|
| Roles | [protocol/ROLES.md](protocol/ROLES.md) |
| Task / knowledge loop | [protocol/WORKFLOW.md](protocol/WORKFLOW.md) |
| Agents / file ownership | [protocol/AGENTS.md](protocol/AGENTS.md) |
| Bootstrap | [protocol/BOOTSTRAP.md](protocol/BOOTSTRAP.md) |
| Topologies | [protocol/TOPOLOGY.md](protocol/TOPOLOGY.md) |
| Operator guide | [protocol/README.md](protocol/README.md) |
| Advanced empty-builder git sequence | [protocol/examples/INIT_EXISTING_PROJECT.prompt.example.md](protocol/examples/INIT_EXISTING_PROJECT.prompt.example.md) |

## License

MIT.
