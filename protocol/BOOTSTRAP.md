# Bootstrap

Stage-1 AIOS assumes the Human has already created the product and builder repositories and given Claude access to both.

The root README beginner path is: paste one short prompt with those two repo names. Claude does not create GitHub repositories, use hosted AIOS, or use any persistence outside builder Git files.

This file does not replace [Roles](ROLES.md). Single-repository AIOS remains valid; it is not the Stage-1 default.

## Stage-1 handoff

```text
Human creates <project> and <project>-builder
        ↓
Human opens Claude with access to them
        ↓
Human pastes the README start prompt
        ↓
Claude reads shes-dev/aios-public protocol
        ↓
Claude initializes or resumes AIOS in the builder
        ↓
Claude works Architect -> Executor -> Reviewer
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

## Initialize or resume

When Claude receives the Stage-1 prompt with both repos named:

1. **Open the named builder and product.** Do not search for substitutes. Do not create repositories.
2. **If the builder has no AIOS state** (no usable `queue.md` / `prompts/` / `memory/`):
   - create missing `queue.md`, `prompts/`, `suggestions/`, `memory/` as needed;
   - pull or copy protocol reference as needed without making `aios-public` the builder origin;
   - write `memory/product-pairing.md` (and/or `memory/adoption-baseline.md`) naming product, builder, and protocol source;
   - begin as Architect.
3. **If the builder already has AIOS state**:
   - resume it;
   - do **not** overwrite or reinitialize `queue.md`, `prompts/`, reviews, `suggestions/`, or `memory/`;
   - update pairing only if missing or clearly stale, without erasing durable history;
   - continue the queue from its current sections.
4. **Product history is sacred.** Never force-push, rewrite, or replace an existing product. Empty/new products stay empty until a later task implements something.
5. **Persistence is Git in the builder.** No hosted UI store, extension artifact store, MCP memory, or PAT/device-flow auth is required for Stage 1.

## Ownership

Builder owns `queue.md`, `prompts/`, `suggestions/`, `memory/`, and all task/response/review files.

Product owns application, runtime, and deployment code.

## Human-only boundaries (Stage 1)

The Human must:

1. Create the product repository.
2. Create the matching builder repository.
3. Grant Claude access to both.
4. Paste the README start prompt (with the two repo names filled in).

Claude must stop and ask before risky/external actions (force-push, deploy, secrets, deleting repos, etc.). See [Roles](ROLES.md).

If Claude cannot access a named repo, ask only for access to **that** repo. Do not invent a differently named substitute.

## Advanced: adopt / create when repos are missing

Not part of the Stage-1 beginner path. Kept for operators who still need discovery or creation.

Inspect first. Create only when missing **and** the Human has authorized repository creation. Never create a second copy of something that already exists.

| Product | Matching builder | Action |
|---------|------------------|--------|
| Exists | Exists | Adopt the product. Reuse the builder. Create neither. |
| Exists | Missing, creation authorized | Adopt the product. Create `<product-repo-name>-builder`. |
| Exists | Missing, not authorized | Adopt the product read-only. Stop for Human. |
| Missing, creation authorized | Matching builder | Reuse the builder. Create/initialize the product. |
| Missing, creation authorized | Missing, creation authorized | Create both. Pair them. |
| Missing, not authorized | Any | Stop. Do not invent the product. |

Matching-builder evidence (when the Human did not name the builder): Human identity → `<product>-builder` name → `memory/` pairing → AIOS artifacts → `aios-public` remote → description. Ambiguous matches: stop and ask.

Empty-builder git sequence: [Existing-project initialization](examples/INIT_EXISTING_PROJECT.prompt.example.md).

## Stop (after Stage-1 init/resume)

Bootstrap handoff is done when:

- the named product and builder are in use;
- the builder has AIOS runtime files (new or resumed);
- pairing is recorded under builder `memory/`;
- product history was not rewritten.

Then: Talk -> Document -> Task -> Execute -> Review -> Done.
