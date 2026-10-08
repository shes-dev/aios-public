# Bootstrap

Bootstrap initializes or resumes durable Git/Markdown state in a **dedicated builder repository**. Product repositories hold implementation only. `aios-public` is protocol source.

The root README start prompt is vendor-agnostic. **Claude Code** is one concrete tested example below. Claude Code does not define AIOS.

Bootstrap does not require Cowork, hosted AIOS, AIOS MCP tools, the AIOS GitHub App, an artifact store, or any particular Executor vendor. AIOS does not launch workers.

## Topology (mandatory)

```text
one project / workstream  ->  one dedicated builder repo
one builder repo          ->  one or many product repos
```

```text
Human creates builder + names product repo(s)
        ↓
Human opens a capable Architect with access to builder + products
        ↓
Human pastes the README start prompt
        ↓
Architect reads shes-dev/aios-public
        ↓
Architect initializes or resumes AIOS in the builder
        ↓
Architect reports state and asks what to build
        ↓
prompt -> response -> review (external Executors as staffed)
```

| Repository | Role |
|------------|------|
| `shes-dev/aios-public` | Protocol source only |
| `<owner>/<project>-builder` | Sole AIOS organizational state |
| `<owner>/<product>` … | Implementation only |

Default branch: `main`.

Builder remotes (when configured):

```text
origin       -> <BUILDER_REPOSITORY>
aios-public  -> shes-dev/aios-public
```

`aios-public` is never the builder’s `origin`. Consumer knowledge does not belong in `aios-public`.

## Human setup

1. Create an empty dedicated builder repository.
2. Identify one or more product repositories the builder will govern.
3. Grant the Architect (and later Executors) access to the builder and relevant products.
4. Paste the root README prompt with builder and product repo names filled in.

```bash
gh repo create <owner>/<project>-builder --private
# product repos may already exist; create only when authorized and missing
```

Create the builder empty: no README, license, `.gitignore`, or template.

## Initialize or resume

When the Architect receives the start prompt:

1. **Work with the named builder and product Git repositories.** Do not invent substitutes. Do not create repositories unless the Human already authorized creation.
2. **Read the protocol first:** `ROLES.md`, `BOOTSTRAP.md`, `TOPOLOGY.md`, `WORKFLOW.md`, `AGENTS.md`.
3. **Persist only in builder Git files** for this path (not hosted AIOS / MCP artifact stores as canonical state).
4. **If the builder has no AIOS state**:
   - configure `main` and the protocol remote as needed;
   - create missing `queue.md`, `prompts/`, `suggestions/`, and `memory/`;
   - write `memory/product-pairing.md` naming builder, product repo(s), and protocol source;
   - commit and push when the start prompt authorizes it;
   - do not modify products;
   - stop before task `0001`, report state, ask what to build.
5. **If the builder already has AIOS state**: resume; do not overwrite queue, prompts, reviews, suggestions, or memory.
6. **Product history is sacred.** Never force-push or replace an existing product.
7. **Artifact contract:** Architect writes `.prompt.md` and owns QMD; Executors write `.response.md`; Architect/Reviewer writes `.review.md`.

## Ownership

Builder owns `queue.md`, `BLOCKED.md` / `COMPLETED.md` / `DELETED.md` (created when first needed), `prompts/`, `suggestions/`, `memory/`, and all task/response/review files.

Products own application, runtime, and deployment code.

## Human-only boundaries

The Human must create/select the builder, identify product repo(s), grant access, paste the start prompt, and approve risky/external operations.

The README prompt may authorize normal builder init commits/pushes. Agents must still stop before force-push, deploy/publish, deleting repos, exposing secrets, or other irreversible/external actions. See [ROLES.md](ROLES.md).

## Expected new-builder state

```text
queue.md
prompts/
suggestions/
memory/
memory/product-pairing.md
protocol/
```

- builder `main` tracks `origin/main`;
- `origin` points at the builder;
- secondary `aios-public` points at `shes-dev/aios-public` when configured;
- product repos remain untouched until a task authorizes implementation.

## Tested example: Claude Code

1. Human creates builder and identifies product repo(s).
2. Human opens Claude Code with Git/GitHub access to builder + products.
3. Human pastes the root README prompt.
4. Claude Code may act sequentially as Architect → Executor → Reviewer while preserving separate artifacts.
5. Do not use hosted AIOS / MCP persistence as canonical state for this path.

## Advanced: adopt / create when repos are missing

Not the beginner path. Inspect first. Create only when missing **and** authorized. Never create a second copy of something that exists.

| Product | Matching builder | Action |
|---------|------------------|--------|
| Exists | Exists | Adopt product(s). Reuse builder. Create neither. |
| Exists | Missing, creation authorized | Adopt product(s). Create `<name>-builder`. |
| Exists | Missing, not authorized | Stop for Human. |
| Missing, creation authorized | Matching builder | Reuse builder. Create/initialize product only if authorized. |
| Missing, not authorized | Any | Stop. |

Empty-builder git sequence: [Existing-project initialization](examples/INIT_EXISTING_PROJECT.prompt.example.md).

## Stop (after init/resume)

Done when the named builder has AIOS runtime files, pairing is under `memory/`, products were not rewritten, and the Architect asked what to build before task `0001`.

Then: [WORKFLOW.md](WORKFLOW.md).
