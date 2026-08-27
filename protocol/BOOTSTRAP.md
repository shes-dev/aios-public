# Bootstrap

Stage-1 AIOS is validated with **Claude Code**.

The Human creates the product and builder repositories first, gives Claude Code direct Git/GitHub access to both, and then pastes the root README prompt. Claude Code initializes or resumes AIOS using Git files in the builder.

Stage 1 does not require Cowork, hosted AIOS, AIOS MCP tools, AIOS connection status, the AIOS GitHub App, an artifact store, or Cursor.

This file does not replace [Roles](ROLES.md). Single-repository AIOS remains valid; it is not the Stage-1 default.

## Stage-1 handoff

```text
Human creates <project> and <project>-builder
        ↓
Human opens Claude Code with direct access to both
        ↓
Human pastes the README start prompt
        ↓
Claude Code reads shes-dev/aios-public
        ↓
Claude Code initializes or resumes AIOS in the builder
        ↓
Claude Code reports state and asks what to build
        ↓
Architect -> Executor -> Reviewer
```

| Repository | Role |
|------------|------|
| `shes-dev/aios-public` | Protocol source only |
| `<owner>/<project>-builder` | Durable AIOS workspace |
| `<owner>/<project>` | Product implementation |

Default branch name: `main`.

Builder remotes (when configured):

```text
origin       -> <BUILDER_REPOSITORY>
aios-public  -> shes-dev/aios-public
```

`aios-public` is never the builder's `origin`. Consumer knowledge does not belong in `aios-public`.

## Human setup

Create both repositories before the first Claude Code prompt. Private repositories are supported and are the recommended default example.

```bash
gh repo create <owner>/<project> --private
gh repo create <owner>/<project>-builder --private
```

Create them empty: no README, license, `.gitignore`, or template.

Then open Claude Code with normal direct Git/GitHub access to both repositories. Repository access for Stage 1 is not provided by AIOS hosted state or the AIOS GitHub App.

## Initialize or resume

When Claude Code receives the Stage-1 prompt with both repos named:

1. **Work directly with the named builder and product Git repositories.** Do not search for substitutes. Do not create repositories.
2. **Read the canonical protocol first:** `ROLES.md`, `BOOTSTRAP.md`, `TOPOLOGY.md`, `WORKFLOW.md`, `AGENTS.md`.
3. **Do not use Stage-1 persistence outside builder Git files.** Hosted AIOS state, AIOS MCP tools, connection-status state, artifact stores, and MCP memory are not canonical Stage-1 state.
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
7. **Persistence is Git in the builder.** `queue.md`, task prompts, responses, reviews, suggestions, and memory are the durable organizational state.

## Ownership

Builder owns `queue.md`, `prompts/`, `suggestions/`, `memory/`, and all task/response/review files.

Product owns application, runtime, and deployment code.

One Claude Code session may play Architect, Executor, and Reviewer sequentially. The role boundaries and artifacts remain mandatory.

## Human-only boundaries (Stage 1)

The Human must:

1. Create the product repository.
2. Create the matching builder repository.
3. Give Claude Code direct access to both.
4. Paste the root README start prompt with both repo names filled in.
5. Make product decisions and approve risky/external operations.

The README prompt may explicitly authorize normal builder initialization commits and pushes so Claude Code does not stop for a redundant approval before the first builder push.

Claude Code must still stop before force-push/history rewrite, deployment/publication, deleting repositories, exposing secrets, or other risky/irreversible/external actions. See [Roles](ROLES.md).

If Claude Code cannot access a named repo, ask only for normal Git/GitHub access to that repo. Do not redirect the beginner to hosted AIOS or create a differently named substitute.

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

## Advanced: adopt / create when repos are missing

Not part of the Stage-1 beginner path. Kept for operators who need discovery or creation.

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

## Stop (after Stage-1 init/resume)

Bootstrap handoff is done when:

- the named product and builder are in use;
- the builder has AIOS runtime files (new or resumed);
- pairing is recorded under builder `memory/`;
- product history was not rewritten;
- Claude Code has reported state and asked the Human what to build before creating the first real task.

Then: Talk -> Document -> Task -> Execute -> Review -> Done.
