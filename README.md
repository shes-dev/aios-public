# AIOS

AIOS is a **vendor-agnostic protocol** for humans and AI agents collaborating on long-lived engineering work. It preserves thinking, execution evidence, review, and knowledge as durable Git and Markdown artifacts — not as disposable chat.

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

| Artifact | Writer | Meaning |
|----------|--------|---------|
| `.prompt.md` | Architect | What is approved to do |
| `.response.md` | Executor | What was done / evidence |
| `.review.md` | Architect / Reviewer | Verdict and feedback |

Different prompt artifacts can be **Active at the same time** with different Executors:

```text
# Active (illustrative)
0394  fix auth redirect     → Cursor
0395  draft onboarding copy → Claude
0396  explore billing API   → Codex
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
- Status (In Line / Active / Awaiting Review / Blocked) → `queue.md`

Architect / Reviewer own QMD transitions. Executors do not edit `queue.md`.

## Builder artifact layout

Canonical public paths (flat `prompts/` files):

```text
my-project-builder/
  queue.md
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
- `prompts/NNNN-slug.prompt.md` — Architect’s approved task
- `prompts/NNNN-slug.response.md` — Executor’s evidence
- `prompts/NNNN-slug.review.md` — Architect/Reviewer verdict
- `suggestions/` — pre-task product/organizational thinking
- `memory/` — durable pairing and decisions

## Suggestions (before engineering work)

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
