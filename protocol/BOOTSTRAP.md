# Bootstrap

This is the canonical AIOS stage-1 product/builder contract for Claude.

It does not replace [Roles](ROLES.md). It does not rewrite the root README. Single-repository AIOS remains valid; it is not this stage-1 default.

## Default topology

```text
aios-public
    ↓ protocol source
<project>-builder
    ↓ AIOS workspace
<project>
    ↓ product implementation
```

| Repository | Role | Canonical `origin` |
|------------|------|--------------------|
| `<AIOS_PUBLIC_REPOSITORY>` | Generic protocol/template source | its own GitHub repo |
| `<BUILDER_REPOSITORY>` | AIOS workspace: queue, prompts, responses, reviews, suggestions, memory | the builder |
| `<PRODUCT_REPOSITORY>` | Application, runtime, and deployment code | the product |

`aios-public` is never the builder's `origin`. Consumer knowledge does not belong in `aios-public`.

Default GitHub branch name is `main`.

## Decision rule: reuse before create

Inspect first. Create only when missing **and** the Human has authorized repository creation. Never create a second copy of something that already exists.

| Product | Matching builder | Action |
|---------|------------------|--------|
| Exists | Exists | Adopt the product. Reuse the builder. Create neither. |
| Exists | Missing, creation authorized | Adopt the product. Create `<product-repo-name>-builder`. |
| Exists | Missing, not authorized | Adopt the product read-only. Stop for the Human handoff below. Do not invent a differently named builder. |
| Missing, creation authorized | Exists and pairs to this intended project | Reuse the builder. Create/initialize the product. |
| Missing, creation authorized | Missing, creation authorized | Create/initialize the product. Create `<product-repo-name>-builder`. Pair them. |
| Missing, not authorized | Any | Stop for the Human handoff. Do not create a builder for a product that is not identified yet. |

If two builders look like a match, stop and ask the Human. Do not create a third.

## Adopt product, never replace it

When a product repository already exists:

- Use it. Do not create a replacement with a similar name.
- Do not reinitialize, squash, or rewrite its history.
- Do not force-push, delete branches, or change its `origin`.
- Keep existing branches. Adoption is fetch/clone, not a new root.
- Do not copy product history into `aios-public`.
- Do not change product behavior during bootstrap. Implementation waits for a later task that says so.

When no product exists and creation is authorized, initialize a normal product repository. The product does **not** start from `aios-public`. Only the builder pulls protocol from `aios-public`.

## Recognize a matching builder

Use evidence in this order. One strong match is enough. Conflicting matches: stop and ask.

1. **Human identity** — the Human named a builder URL or `owner/name`.
2. **Repository name** — same owner as the product, name `<product-repo-name>-builder`.
3. **Durable pairing** — builder `memory/product-pairing.md` or `memory/adoption-baseline.md` names this product.
4. **AIOS artifacts** — `queue.md` with the five protocol sections, plus `prompts/` and/or `protocol/`.
5. **Protocol relationship** — git remote `aios-public` (or equivalent) pointing at `<AIOS_PUBLIC_REPOSITORY>`, and `origin` pointing at the builder itself.
6. **Description** — GitHub description or README stating it is the AIOS builder for this product.

Do **not** treat `<AIOS_PUBLIC_REPOSITORY>` as a consumer builder.

If a candidate already has AIOS artifacts, reuse it. Do not reinitialize it. Do not pull `aios-public` in a way that overwrites existing queue, prompts, or memory.

## Create a builder (only if missing and authorized)

Recommended name: `<product-repo-name>-builder`.

Create it **empty** (no README, license, or template commit). Keep `origin` on the builder. Add `aios-public` as a secondary remote and pull protocol `main`. Exact git example: [Existing-project initialization](examples/INIT_EXISTING_PROJECT.prompt.example.md).

After create or reuse, builder remotes must be:

```text
origin       -> <BUILDER_REPOSITORY>
aios-public  -> <AIOS_PUBLIC_REPOSITORY>
```

Later protocol updates:

```bash
git fetch aios-public
git merge aios-public/main
```

Do not retarget `origin` to `aios-public`. Do not force-push the product to "fix" a builder setup.

## Ownership after bootstrap

Builder owns `queue.md`, `prompts/`, `suggestions/`, `memory/`, and all task/response/review files.

Product owns application, runtime, and deployment code.

Record pairing in the builder, not in `aios-public`. Create or update `memory/product-pairing.md` with at least:

- product repository (`owner/name`)
- builder repository (`owner/name`)
- protocol source (`aios-public` remote)
- product and builder default branches
- whether each was adopted/reused or created in this bootstrap
- confirmation that product history was not rewritten

## Authorization and smallest Human handoff

Creating or granting access to GitHub repositories is a Human operation unless this session already has that authority.

Claude (any role) must stop and ask before creating, deleting, or transferring repositories. See [Roles](ROLES.md).

When Claude **cannot** create or access a required repo, ask for the smallest missing step, then wait. Do not start a parallel repo under a new name.

**Missing repo, no create access:**

```text
I cannot create GitHub repositories from this session.

Please create this empty repository (no README) and grant this session access:
  <OWNER>/<REPO_NAME>

Tell me when it exists. I will adopt/reuse it. I will not create a second copy.
```

**Repo exists, no access:**

```text
I found <OWNER>/<REPO_NAME> but this session cannot access it.

Please grant access (or confirm a different repo). I will not create a duplicate under another name.
```

After the Human confirms, re-run the decision table from the top. Still reuse-before-create.

## Stop

Bootstrap is done when:

- product identity is stable (adopted or created, history intact);
- exactly one matching builder is in use (`origin` on the builder, `aios-public` as protocol source);
- pairing is recorded in builder `memory/`;
- no product implementation has started unless a later task says so.

Then the normal loop begins: Talk -> Document -> Task -> Execute -> Review -> Done.
