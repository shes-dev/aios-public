# Example — Initialize AIOS for an existing product repository

This is a reusable git example, not a live task, and not the adopt/create decision table.

**Run [Bootstrap](../BOOTSTRAP.md) first.** Use this sequence only when the product already exists, no matching builder exists, and the Human has authorized builder creation. If a matching builder already exists, reuse it; do not run Step 1.

Copy it into a builder as `prompts/0001-init-existing-project.prompt.md` after replacing every placeholder. Keep `aios-public` generic: do not add consumer-specific names or knowledge to the protocol repository.

## Placeholders

Replace all of:

- `<PRODUCT_REPOSITORY>` — existing GitHub product repo, for example `owner/app`
- `<BUILDER_REPOSITORY>` — new builder repo, for example `owner/app-builder`
- `<AIOS_PUBLIC_REPOSITORY>` — AIOS protocol source, for example `shes-dev/aios-public`
- `<OWNER>` — GitHub owner for the builder
- `<BUILDER_NAME>` — builder repository name, usually `<product>-builder`

## Goal

Adopt an existing product into AIOS using a separate builder repository.

Assign Architect and Executor per [Roles](../ROLES.md). ChatGPT as Architect and Cursor as Executor remains valid. One Claude session may perform both sequentially. The product must stay unchanged during this initialization.

## Topology

```text
aios-public
    ↓ protocol/template source
<project>-builder
    ↓ approved tasks / project knowledge
<project>
```

Single-repository AIOS remains valid. This example is the existing-product git path for the canonical builder/product topology.

## Ownership

- Builder owns `queue.md`, `prompts/`, `suggestions/`, `memory/`, project knowledge, task prompts, Executor responses, and Architect reviews.
- Product owns application, runtime, and deployment code.
- `<AIOS_PUBLIC_REPOSITORY>` remains generic protocol/template source. Do not store consumer-specific knowledge there.

Expected builder remotes when this task is done:

```text
origin       -> <BUILDER_REPOSITORY>
aios-public  -> <AIOS_PUBLIC_REPOSITORY>
```

## Requirements

1. Reuse a matching builder if one exists. Create an independent builder repository only when [Bootstrap](../BOOTSTRAP.md) says none exists and creation is authorized.
2. Keep `origin` pointed at the builder.
3. Add the AIOS protocol source under a secondary remote named `aios-public` (or equivalent).
4. Never leave the builder's canonical `origin` pointing at `aios-public`.
5. Inspect the product repository read-only. Do not change product behavior.
6. Write a durable adoption baseline under builder `memory/`.
7. Stop for Architect review before any real product implementation.

## Out of scope

- Product features or behavior changes
- Automatic repository creation by Architect or Executor
- Automatic tool connector authorization
- Hosted AIOS, CLI, servers, or other machinery beyond git and markdown

---

## Step 1 — Create an empty builder (only if none exists)

**Kind:** Git operation (Human), and only after [Bootstrap](../BOOTSTRAP.md) says the matching builder is missing.

Create the builder **empty**. Do not use `--add-readme`, a template, a license file, or any other initial commit. An initial commit on the builder will conflict when the protocol history is pulled.

GitHub CLI (`gh`) plus git, PowerShell:

```powershell
gh repo create <OWNER>/<BUILDER_NAME> --private --description "AIOS builder workspace for <PRODUCT_REPOSITORY>" --confirm
git clone https://github.com/<OWNER>/<BUILDER_NAME>.git
cd <BUILDER_NAME>
git remote add aios-public https://github.com/<AIOS_PUBLIC_REPOSITORY>.git
git fetch aios-public
git checkout -b main
git pull aios-public main --allow-unrelated-histories --no-edit
git push -u origin main
git remote -v
```

The same commands in bash / zsh / Git Bash:

```bash
gh repo create <OWNER>/<BUILDER_NAME> --private --description "AIOS builder workspace for <PRODUCT_REPOSITORY>"
git clone https://github.com/<OWNER>/<BUILDER_NAME>.git
cd <BUILDER_NAME>
git remote add aios-public https://github.com/<AIOS_PUBLIC_REPOSITORY>.git
git fetch aios-public
git checkout -b main
git pull aios-public main --allow-unrelated-histories --no-edit
git push -u origin main
git remote -v
```

Notes:

- `--confirm` is accepted by current `gh` but deprecated. Passing `--private` already skips the interactive prompt.
- If `main` already exists, use `git checkout main` instead of `git checkout -b main`.
- If the clone has no commits yet and `git checkout -b main` fails, after `git fetch aios-public` run `git checkout -B main aios-public/main`, then `git push -u origin main`.
- `--no-edit` avoids stopping for a merge-commit message.
- SSH remotes are fine. The remote **names** must still be `origin` (builder) and `aios-public` (protocol source).
- If `git pull` asks how to reconcile divergent branches, add `--no-rebase`. That should not happen on an empty builder.

`git remote -v` must look like:

```text
aios-public  https://github.com/<AIOS_PUBLIC_REPOSITORY>.git (fetch)
aios-public  https://github.com/<AIOS_PUBLIC_REPOSITORY>.git (push)
origin       https://github.com/<OWNER>/<BUILDER_NAME>.git (fetch)
origin       https://github.com/<OWNER>/<BUILDER_NAME>.git (push)
```

If `origin` points at `<AIOS_PUBLIC_REPOSITORY>`, stop and fix remotes before pushing. See `protocol/TOPOLOGY.md`.

Do not run `git clone https://github.com/<AIOS_PUBLIC_REPOSITORY>.git` and then treat that clone as the builder. That leaves `origin` on the protocol source.

Later protocol updates from the builder:

```bash
git fetch aios-public
git merge aios-public/main
```

## Step 2 — Inspect the product read-only

**Kind:** Git operation (Human or Executor)

Clone or fetch `<PRODUCT_REPOSITORY>` separately. Do not push to it. Do not commit in it. Do not change application behavior.

Record only what is needed for the adoption baseline: default branch, layout, how the app is built/run, and obvious constraints.

## Step 3 — Connect Architect and Executor tools

**Kind:** Human tool-connection / authorization

AIOS cannot connect Architect or Executor tools for you.

Before any cross-repo task:

1. Connect/authorize **both** `<BUILDER_REPOSITORY>` and `<PRODUCT_REPOSITORY>` in the tools that will act as Architect, Executor, and Reviewer.
2. If Cursor is the Executor, open the builder folder and add/open the product folder in the same workspace. If one Claude session is playing every role, give that session access to both repositories.

Until both tools can see both repositories, do not start product implementation tasks.

## Step 4 — Adoption baseline

**Kind:** Executor, in the builder only

Create durable notes under builder `memory/`, for example `memory/adoption-baseline.md`.

Include:

- product and builder repository names
- builder remotes (`origin` and `aios-public`)
- protocol source used
- product default branch and a short layout summary
- confirmation that the product was not modified

Do not write this baseline into `<AIOS_PUBLIC_REPOSITORY>`.

## Step 5 — First numbered AIOS task

After initialization files exist in the builder:

1. **Architect:** create the first numbered task in the builder (`prompts/0001-*.prompt.md`) and update builder `queue.md`.
2. **Human:** pull the builder locally if needed, then tell the Executor `execute 0001` (or the applicable task id).
3. **Executor:** do the work. Modify the product repository only when that task explicitly says so. Write `prompts/0001-*.response.md` in the builder. Do not edit `queue.md`.
4. **Human:** commit and push the builder (and the product, if the task changed it).
5. **Reviewer:** review, write `prompts/0001-*.review.md`, and update builder `queue.md`.

This is the normal loop: `Talk -> Document -> Task -> Execute -> Review -> Done`.

## Step 6 — Stop

**Kind:** Executor must stop here

Do not start real product implementation in this initialization task.

Hand back to Architect review with:

- builder `origin` on `<BUILDER_REPOSITORY>`
- `aios-public` remote on `<AIOS_PUBLIC_REPOSITORY>`
- adoption baseline in builder `memory/`
- product repository unchanged

Architect decides the first product-implementation task after that review.
