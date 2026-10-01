# Bootstrap

AIOS bootstrap initializes or resumes durable Git/Markdown state in a **builder** repository, with a separate **product** repository for implementation. `aios-public` is protocol source only.

The root README start prompt is **vendor-agnostic**: any capable Architect agent may run it. **Claude Code** is documented below as one concrete tested example (including sequential Architect → Executor → Reviewer in one session). Claude Code does not define AIOS.

Bootstrap does not require Cowork, hosted AIOS, AIOS MCP tools, AIOS connection status, the AIOS GitHub App, an artifact store, or any particular Executor vendor.

This file does not replace [Roles](ROLES.md). Single-repository AIOS remains valid; it is not the default product/builder topology.

## Handoff

```text
Human creates <project> and <project>-builder
        ↓
Human opens a capable Architect agent with access to both
        ↓
Human pastes the README start prompt
        ↓
Agent reads shes-dev/aios-public
        ↓
Agent initializes or resumes AIOS in the builder
        ↓
Agent reports state and asks what to build
        ↓
Architect -> Executor -> Reviewer (any valid staffing)
```

| Repository | Role |
|------------|------|
| `shes-dev/aios-public` | Protocol source only |
| `<owner>/<project>-builder` | Durable AIOS workspace (QMD, prompts, suggestions, memory) |
| `<owner>/<project>` | Product implementation |

Default branch name: `main`.

Builder remotes (when configured):

```text
origin       -> <BUILDER_REPOSITORY>
aios-public  -> shes-dev/aios-public
```

`aios-public` is never the builder's `origin`. Consumer knowledge does not belong in `aios-public`.

## Human setup

Create both repositories before the first bootstrap prompt. Private repositories are supported and are the recommended default example.

```bash
gh repo create <owner>/<project> --private
gh repo create <owner>/<project>-builder --private
```

Create them empty: no README, license, `.gitignore`, or template.

Then open your chosen Architect agent with normal direct Git/GitHub access to both repositories. Repository access is not provided by AIOS hosted state or the AIOS GitHub App. AIOS does not launch or connect workers.

## Initialize or resume

When the Architect agent receives the start prompt with both repos named:

1. **Work directly with the named builder and product Git repositories.** Do not search for substitutes. Do not create repositories.
2. **Read the canonical protocol first:** `ROLES.md`, `BOOTSTRAP.md`, `TOPOLOGY.md`, `WORKFLOW.md`, `AGENTS.md`.
3. **Do not use persistence outside builder Git files for this path.** Hosted AIOS state, AIOS MCP tools, connection-status state, artifact stores, and MCP memory are not canonical here.
4. **If the builder has no AIOS state**:
   - configure `main` and the protocol relationship as needed;
   - create missing `queue.md`, `prompts/`, `suggestions/`, and `memory/`;
   - write `memory/product-pairing.md` (or equivalent durable baseline) naming product, builder, and protocol source;
   - commit and push normal builder initialization when the Human's start prompt authorizes it;
   - do not modify the product;
   - stop before task `0001`, report the resulting state, and ask what the product is / what to build.
5. **If the builder already has AIOS state**:
   - resume it;
   - do **not** overwrite or reinitialize `queue.md`, `prompts/`, reviews, `suggestions/`, or `memory/`;
   - update pairing only if missing or clearly stale, without erasing durable history;
   - report current AIOS state and continue from the existing queue.
6. **Product history is sacred.** Never force-push, rewrite, or replace an existing product. Empty/new products stay empty until a real task explicitly authorizes implementation.
7. **Persistence is Git in the builder.** `queue.md` (QMD), task prompts, responses, reviews, suggestions, and memory are the durable organizational state.

## Ownership

Builder owns `queue.md`, `prompts/`, `suggestions/`, `memory/`, and all task/response/review files.

Product owns application, runtime, and deployment code.

Role staffing follows [Roles](ROLES.md). One session may play Architect, Executor, and Reviewer sequentially; multiple external Executors may take independent Active tasks. Role boundaries and artifacts remain mandatory.

## Human-only boundaries

The Human must:

1. Create the product repository (typical beginner path).
2. Create the matching builder repository.
3. Give the chosen tools access to both.
4. Paste the root README start prompt with both repo names filled in.
5. Make product decisions and approve risky/external operations.

The README prompt may explicitly authorize normal builder initialization commits and pushes so the Architect agent does not stop for a redundant approval before the first builder push.

Agents must still stop before force-push/history rewrite, deployment/publication, deleting repositories, exposing secrets, or other risky/irreversible/external actions. See [Roles](ROLES.md).

If the agent cannot access a named repo, ask only for normal Git/GitHub access to that repo. Do not redirect the beginner to hosted AIOS or create a differently named substitute.

## Expected successful new-builder state

After initialization, a new builder should contain or expose the equivalent of:

```text
queue.md
prompts/
suggestions/
memory/
memory/product-pairing.md
protocol/
```

Expected Git state:

- builder branch `main` exists and tracks `origin/main`;
- `origin` points to the builder;
- when configured, secondary `aios-public` points to `shes-dev/aios-public`;
- product repository remains untouched until a later task authorizes implementation.

## Tested example: Claude Code

Claude Code is a validated concrete path:

1. Human creates product + builder as above.
2. Human opens Claude Code with direct Git/GitHub access to both.
3. Human pastes the root README prompt (same vendor-agnostic text).
4. Claude Code may act sequentially as Architect → Executor → Reviewer while preserving separate artifacts.
5. Claude Code must not use hosted AIOS / MCP / artifact-store persistence as canonical state for this path.

This example does not privilege Claude as the conceptual architecture of AIOS.

## Advanced: adopt / create when repos are missing

Not part of the beginner path. Kept for operators who need discovery or creation.

Inspect first. Create only when missing **and** the Human has authorized repository creation. Never create a second copy of something that already exists.

| Product | Matching builder | Action |
|---------|------------------|--------|
| Exists | Exists | Adopt the product. Reuse the builder. Create neither. |
| Exists | Missing, creation authorized | Adopt the product. Create `<product-repo-name>-builder`. |
| Exists | Missing, not authorized | Adopt the product read-only. Stop for Human. |
| Missing, creation authorized | Matching builder | Reuse the builder. Create/initialize the product. |
| Missing, creation authorized | Missing, creation authorized | Create both. Pair them. |
| Missing, not authorized | Any | Stop. Do not invent the product. |

Matching-builder evidence (when the Human did not name the builder): Human identity -> `<product>-builder` name -> `memory/` pairing -> AIOS artifacts -> `aios-public` remote -> description. Ambiguous matches: stop and ask.

Empty-builder git sequence: [Existing-project initialization](examples/INIT_EXISTING_PROJECT.prompt.example.md).

## Stop (after init/resume)

Bootstrap handoff is done when:

- the named product and builder are in use;
- the builder has AIOS runtime files (new or resumed);
- pairing is recorded under builder `memory/`;
- product history was not rewritten;
- the Architect agent has reported state and asked the Human what to build before creating the first real task.

Then continue the knowledge lifecycle in [Workflow](WORKFLOW.md).
