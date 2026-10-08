# AIOS

AIOS (**AI Operating System**) is a **vendor-agnostic protocol** for humans and AI agents collaborating on long-lived engineering work. It preserves thinking, execution evidence, review, and knowledge as durable Git and Markdown artifacts — not as disposable chat.

> **AI agents are workers. The protocol is the organization.**

AIOS does not launch, schedule, route, or monitor agents. The protocol is the artifact contract and durable state. External Executors consume approved tasks and return evidence through Git.

## The engineering loop

The concrete core of AIOS is a durable artifact triplet in a **dedicated builder repository**:

```text
Architect
  -> prompts/NNNN-slug.prompt.md
  -> Executor (Cursor / Claude / Codex / …)
  -> prompts/NNNN-slug.response.md
  -> Architect / Reviewer
  -> prompts/NNNN-slug.review.md
  -> next task / knowledge
```

In one line:

```text
TASK -> IMPLEMENT -> REVIEW -> KNOWLEDGE
```

| Artifact | Writer | Meaning |
|----------|--------|---------|
| `.prompt.md` | Architect | What is approved to do |
| `.response.md` | Executor | What was done / evidence |
| `.review.md` | Architect / Reviewer | Verdict and feedback |

Different prompt artifacts can be **Active at the same time** with different Executors:

```text
QMD
0371 → Cursor
0372 → Claude
0373 → Codex
```

Each Executor consumes only its approved prompt and writes the matching response. Executors do not talk to each other through AIOS. The Architect closes each loop through the matching review.

```text
                         BUILDER REPO
                    (one canonical AIOS state)

                         Architect
                             |
                     writes task artifact
                             |
                 prompts/NNNN-slug.prompt.md
                      /      |      \
                 Cursor    Claude   Codex
                    \        |       /
                 prompts/NNNN-slug.response.md
                             |
                      Architect review
                             |
                 prompts/NNNN-slug.review.md
                             |
                      queue.md / QMD
                         (lifecycle index)

           product repo A   product repo B   product repo C
                 ^                 ^                ^
                 +------ implementation work ------+
```

## Mandatory topology

```text
one project / workstream  ->  one dedicated builder repo
one builder repo          ->  one or many product repos
```

The **builder** is the single home for organizational state: `queue.md`, task prompt/response/review artifacts, suggestions, memory, and cross-repo decisions. **Product** repositories hold implementation only — never AIOS organizational memory.

Reason: implementation may span many repositories; task and organizational knowledge need one canonical home independent of product code layout.

Embedding AIOS state inside a product repository is **not** a supported public topology.

## Architect

The Architect is not merely “the agent that writes prompts.” The Architect:

- holds project/product intent and protocol context;
- decomposes work into bounded tasks;
- creates `.prompt.md` artifacts;
- decides which independent tasks may proceed;
- owns QMD (`queue.md`) lifecycle state;
- receives Executor responses/evidence;
- performs or commissions independent review;
- writes/owns the durable `.review.md` feedback/verdict;
- decides follow-up work and promotes accepted outcomes into durable knowledge;
- can coordinate multiple Executors concurrently when tasks are independent.

ChatGPT is one current Architect example. The role is vendor-independent. The Architect is not a hosted AIOS runtime.

## QMD — lifecycle index

**QMD** / `queue.md` is the human-readable **lifecycle/status index** over durable task artifacts. It is not a hosted service and **not** the source of task meaning.

- Task meaning → `.prompt.md`
- Implementation evidence → `.response.md`
- Review verdict → `.review.md`
- Live status (In Line / Active / Awaiting Review) → `queue.md`

Architect / Reviewer own QMD transitions. Executors do not edit `queue.md`.

### Keep QMD lean

QMD lists **live work only**. Terminal and parked tasks would make it grow forever, so they move out to designated files in the builder:

| File | Holds |
|------|-------|
| `BLOCKED.md` | Task IDs that cannot proceed, with the reason |
| `COMPLETED.md` | Task IDs whose triplet is finished |
| `DELETED.md` | Task IDs dropped without completion |

A task is **complete once it has all three artifacts**: `.prompt.md`, `.response.md`, and `.review.md`. There is no reason to keep tracking it in QMD: the Reviewer removes it from `queue.md` and appends its ID to `COMPLETED.md`. The artifacts themselves remain the record.

## Builder artifact layout

Canonical public paths (flat `prompts/` files):

```text
my-project-builder/
  queue.md
  BLOCKED.md      (when needed)
  COMPLETED.md    (when needed)
  DELETED.md      (when needed)
  prompts/
    0001-fix-auth.prompt.md
    0001-fix-auth.response.md
    0001-fix-auth.review.md
    0002-draft-copy.prompt.md
    0002-draft-copy.response.md
    0002-draft-copy.review.md
  suggestions/
  memory/
```

- `queue.md` — which tasks are live and in which lifecycle state
- `BLOCKED.md` / `COMPLETED.md` / `DELETED.md` — task IDs moved out of QMD
- `prompts/NNNN-slug.prompt.md` — Architect’s approved task
- `prompts/NNNN-slug.response.md` — Executor’s evidence
- `prompts/NNNN-slug.review.md` — Architect/Reviewer verdict
- `suggestions/` — documented ideas that may be promoted to tasks
- `memory/` — how we work on this project, and facts about the product; never promoted to tasks

## Suggestions vs Memory

Both are durable Markdown the Architect writes in the builder. They differ in where they can lead.

| | Suggestion | Memory |
|---|------------|--------|
| What it is | An idea, proposal, or finding documented now | General metadata on how to work on the project, and facts about the product |
| Examples | “Add SSO to the admin app”, “Split the billing service” | Builder/product pairing, conventions, past decisions, domain glossary |
| Can become a task? | **Yes.** It may be promoted to a `.prompt.md` | **No.** It is never promoted |
| Lifecycle | Open → promoted (to task NNNN) or declined | None; updated in place as things change |
| Lives in | `suggestions/` | `memory/` |

Rule of thumb: if it might turn into work, it is a Suggestion. If it is context every future task should know, it is Memory.

Suggestions record ideas before they become engineering tasks:

```text
Discussion -> Suggestion -> promoted Task
                              |
                              v
                     prompt -> response -> review
                              |
                              v
                           Knowledge
```

Keep Suggestions first-class. Do not let that broader lifecycle obscure the prompt → response → review contract above.

## Try AIOS

1. Create a **dedicated builder** repository (empty; private is fine).
2. Identify the **product repository or repositories** this builder will govern.
3. Give a capable **Architect** access to the builder and those product repos (and to this protocol).
4. Initialize builder state (paste below).
5. Discuss the first goal with the Architect.
6. Let the Architect create the first `.prompt.md` and register it on QMD.
7. Hand Active task prompts to one or more **external** Executors (Cursor, Claude, Codex, …).
8. Return `.response.md` evidence for Architect review (`.review.md` + QMD update).

Bootstrap prompt:

```text
Use https://github.com/shes-dev/aios-public as the AIOS protocol.

Builder repo: <owner/project-builder>
Product repo(s): <owner/project>[, <owner/other-product>]

Read the protocol first, especially:
- protocol/ROLES.md
- protocol/BOOTSTRAP.md
- protocol/TOPOLOGY.md
- protocol/WORKFLOW.md
- protocol/AGENTS.md

Initialize or resume AIOS in the dedicated builder using Git/Markdown artifacts only.
Preserve the artifact contract:
- Architect writes prompts/NNNN-slug.prompt.md and owns queue.md (QMD lifecycle index).
- External Executors write matching .response.md only; they do not edit queue.md.
- Architect/Reviewer writes matching .review.md, then updates queue.md.
- queue.md lists live work only (In Line / Active / Awaiting Review). Blocked,
  completed and deleted task IDs move to BLOCKED.md / COMPLETED.md / DELETED.md.
  A task is complete once its prompt, response and review all exist.
- suggestions/ holds ideas that may be promoted to tasks; memory/ holds how we
  work and product facts, and is never promoted to a task.

One builder may span one or many product repos. Do not embed AIOS state in a product repo.
Do not assume AIOS launches or controls workers.

Authorize normal builder initialization commits/pushes when the builder is empty.
Do not modify product repos yet. Stop and ask before force-push, history rewrite,
deploy, publish, delete repos, expose secrets, or other risky/external actions.

If the builder is empty: initialize from the protocol, record builder/product pairing
under memory/, commit and push, then stop before task 0001 and ask what we are building.
If the builder already has AIOS state: resume without overwriting queue, prompts,
reviews, suggestions, or memory.
```

Successful first-run **builder** state:

```text
queue.md
prompts/
suggestions/
memory/
```

Product repos stay untouched until an approved task authorizes implementation.

More detail: [protocol/TOPOLOGY.md](protocol/TOPOLOGY.md), [protocol/BOOTSTRAP.md](protocol/BOOTSTRAP.md).

### Tested example: Claude Code

Claude Code is one concrete tested path (including sequential Architect → Executor → Reviewer in one session). See [protocol/BOOTSTRAP.md](protocol/BOOTSTRAP.md). It does not define AIOS.

## Human authority

The Human owns vision, product decisions, and approvals for risky or external actions. Manual Git courier steps between tools are optional mechanics when workspaces are not shared — not the definition of AIOS.

## Deeper documentation

| Topic | Doc |
|-------|-----|
| Roles / Architect | [protocol/ROLES.md](protocol/ROLES.md) |
| Workflow | [protocol/WORKFLOW.md](protocol/WORKFLOW.md) |
| Agents / ownership | [protocol/AGENTS.md](protocol/AGENTS.md) |
| Bootstrap | [protocol/BOOTSTRAP.md](protocol/BOOTSTRAP.md) |
| Topology | [protocol/TOPOLOGY.md](protocol/TOPOLOGY.md) |
| Operator guide | [protocol/README.md](protocol/README.md) |
| Empty-builder git sequence | [protocol/examples/INIT_EXISTING_PROJECT.prompt.example.md](protocol/examples/INIT_EXISTING_PROJECT.prompt.example.md) |

## License

MIT.
