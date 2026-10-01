# Roles

AIOS separates thinking, implementation, and review as **roles**, not products.

Canonical staffing for the public architecture:

- one **Architect** owns decomposition, task definition, QMD lifecycle, and review feedback;
- multiple **external Executors** (Cursor, Claude, Codex, …) may consume independent Active tasks in parallel;
- the **Human** owns vision and risky/external approvals.

ChatGPT is one current Architect example. A single capable session may still play Architect → Executor → Reviewer sequentially when that is the staffing choice. The role/artifact contract does not change.

## Logical roles

| Role | Authority | Writes |
|------|-----------|--------|
| **Architect** | Holds intent; decomposes work; decides which tasks proceed; owns QMD; receives responses; performs/commissions review; promotes knowledge; may coordinate multiple independent Executors | `prompts/*.prompt.md`, `prompts/*.review.md` (when acting as Reviewer), `suggestions/`, `memory/`, `queue.md` |
| **Executor** | Executes one approved/Active task; does not edit QMD or write the review for that task | `prompts/*.response.md` and product changes the task allows |
| **Reviewer** | Judges execution against the prompt (often the same person/session as Architect, as a distinct phase) | `prompts/*.review.md`, then `queue.md` |

AIOS does not launch, schedule, route, or monitor Executors. Coordination is Architect decisions + Git/Markdown artifacts + QMD + external execution + review.

## Artifact triplet

```text
prompts/NNNN-slug.prompt.md     # Architect — approved work
prompts/NNNN-slug.response.md   # Executor — evidence
prompts/NNNN-slug.review.md     # Architect/Reviewer — verdict
queue.md                        # Architect/Reviewer — lifecycle index only
```

Task meaning lives in the prompt, not in QMD.

## Parallel Executors

Independent Active tasks may run concurrently:

```text
0394 -> Cursor
0395 -> Claude
0396 -> Codex
```

Each Executor reads only its prompt and writes only its response. Agents need not message each other.

## Sequential single-agent pattern

Still valid as staffing:

```text
Architect -> Executor -> Reviewer
```

Typical commands:

```text
document this
create a task
execute 0001
review 0001
```

Do not collapse prompt, response, and review into one undocumented action.

## Human authority

The Human decides risky, destructive, or externally visible actions. Agents must stop and ask before force-push, history rewrite, repo create/delete (unless already authorized), deploy/publish, secrets exposure, or other irreversible work.

Manual Git courier steps between tools are optional mechanics, not the protocol.

## What this file is not

Role contract only. Onboarding: root README. Topology: [TOPOLOGY.md](TOPOLOGY.md). Bootstrap: [BOOTSTRAP.md](BOOTSTRAP.md).
