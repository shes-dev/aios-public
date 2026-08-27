# AIOS

AIOS is a simple git + markdown workflow for building software with AI while preserving the thinking, execution, and review as durable project files.

For Stage 1, use **Claude Code**. Claude Code may act sequentially as Architect, Executor, and Reviewer, but the roles and artifacts stay separate.

Progress lives in your **builder** repository, not only in chat.

## Stage 1 — start here

The validated beginner path is:

```text
Human creates product repo + builder repo
        ↓
Human opens Claude Code with direct access to both
        ↓
Paste one prompt
        ↓
Claude Code initializes or resumes AIOS in the builder
        ↓
Claude asks what to build
```

Stage 1 does **not** require Cowork, hosted AIOS, AIOS MCP tools, the AIOS GitHub App, an artifact store, or Cursor.

### 1. Create the two repositories

Create an empty product repository and an empty builder repository. Private repositories are supported and are the recommended default example.

Using GitHub CLI:

```bash
gh repo create <owner>/<project> --private
gh repo create <owner>/<project>-builder --private
```

Do not add a README, license, `.gitignore`, or template during creation.

The topology is:

```text
shes-dev/aios-public
        ↓ protocol source
<owner>/<project>-builder
        ↓ AIOS workspace
<owner>/<project>
        ↓ implementation
```

### 2. Open Claude Code

Use **Claude Code** (desktop/GUI or local CLI) with normal direct Git/GitHub access to both repositories.

For private repositories, Claude Code must be able to read and write them through your normal Git/GitHub authorization. Stage 1 does not route repository access through AIOS hosted state or the AIOS GitHub App.

### 3. Paste this prompt into Claude Code

Replace the two repo names and paste the whole block:

```text
Use https://github.com/shes-dev/aios-public as the AIOS protocol.

Product repo: <owner/project>
Builder repo: <owner/project-builder>

Work directly with these repositories and their Git files. For this Stage-1 workflow, do not use hosted AIOS state, AIOS MCP tools, AIOS connection status, or AIOS artifact-store persistence.

Read the AIOS protocol first, especially:
- protocol/ROLES.md
- protocol/BOOTSTRAP.md
- protocol/TOPOLOGY.md
- protocol/WORKFLOW.md
- protocol/AGENTS.md

Then initialize or resume AIOS in the builder repository and continue strictly through the protocol.

You may act sequentially as Architect -> Executor -> Reviewer, but preserve the role boundaries and durable artifacts:
- Architect creates task prompts and manages queue.md.
- Executor executes only the Active task and writes the matching response.
- Reviewer writes the matching review and then updates queue.md.

Keep task, response, review, suggestions, memory, and queue state as Git files in the builder.

You are authorized to perform normal builder initialization, commits, and pushes to the builder repository. Do not modify the product repository yet. Do not force-push, rewrite history, deploy, publish, delete repositories, expose secrets, or perform other risky/external actions without asking me first.

If the builder is empty, initialize it from the AIOS protocol, record the product/builder pairing under memory/, commit and push the builder state, then stop before creating task 0001.
If the builder already contains AIOS state, resume it without overwriting queue, prompts, reviews, suggestions, or memory.

After initialization or resume, report the resulting AIOS state and ask me what we are building before creating task 0001.
```

That is the complete Stage-1 handoff.

## Expected result

For a new builder, Claude Code should leave you with:

```text
queue.md
prompts/
suggestions/
memory/
memory/product-pairing.md
protocol/
```

The builder should use `main`, with its canonical `origin` still pointing to the builder. When a protocol remote is configured, it should look like:

```text
origin       -> <owner>/<project>-builder
aios-public  -> shes-dev/aios-public
```

The product repository remains untouched until an approved AIOS task explicitly authorizes implementation.

For an existing AIOS builder, Claude Code resumes the existing queue, prompts, reviews, suggestions, and memory instead of reinitializing them.

## Then work normally

Once Claude asks what to build, the normal loop begins:

```text
Human + Architect discussion
        ↓
Task
        ↓
Executor
        ↓
Response
        ↓
Reviewer
        ↓
Review + queue update
```

One Claude Code session may perform all three AIOS roles sequentially. Separate task, response, and review files are still mandatory.

## Deeper documentation

| Topic | Doc |
|-------|-----|
| Stage-1 handoff | [protocol/BOOTSTRAP.md](protocol/BOOTSTRAP.md) |
| Roles | [protocol/ROLES.md](protocol/ROLES.md) |
| Topologies | [protocol/TOPOLOGY.md](protocol/TOPOLOGY.md) |
| Task loop | [protocol/WORKFLOW.md](protocol/WORKFLOW.md) |
| Agents / file ownership | [protocol/AGENTS.md](protocol/AGENTS.md) |
| Advanced builder initialization details | [protocol/examples/INIT_EXISTING_PROJECT.prompt.example.md](protocol/examples/INIT_EXISTING_PROJECT.prompt.example.md) |
| Operator guide | [protocol/README.md](protocol/README.md) |

## Other operating patterns

ChatGPT as Architect/Reviewer with Cursor as Executor remains valid. Hosted AIOS can also drive the same protocol files as an optional control plane.

Neither is required for the Stage-1 Claude Code path above.

## Files

| File | Writer | Purpose |
|------|--------|---------|
| `queue.md` | Architect; Reviewer after review | Task state |
| `prompts/*.prompt.md` | Architect | Task instructions |
| `prompts/*.response.md` | Executor | Work summary |
| `prompts/*.review.md` | Reviewer | Review verdict |
| `suggestions/` | Architect | Ideas before tasks |
| `memory/` | Architect | Pairing and durable decisions |

These live in the **builder**. The **product** holds implementation code. Builder Git files are the canonical Stage-1 persistence.

## License

MIT.
