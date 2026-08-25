# Example — Initialize AIOS for an existing project

This is the reusable bootstrap for an **existing** product repository plus a **separate** builder repository.

Single-repository AIOS is still valid. Use the root `README.md` Start section when protocol and product should share one repo. Use this example when you want product code isolated from AIOS planning history.

Replace every placeholder before running commands or creating a task:

| Placeholder | Meaning | Example shape |
|-------------|---------|---------------|
| `<PRODUCT_REPOSITORY>` | Existing product GitHub repo | `owner/my-app` |
| `<BUILDER_REPOSITORY>` | New builder GitHub repo | `owner/my-app-builder` |
| `<AIOS_PUBLIC_REPOSITORY>` | AIOS protocol source | `owner/aios-public` |
| `<OWNER>` | GitHub user or org | `owner` |
| `<BUILDER_NAME>` | Builder repo name only | `my-app-builder` |

Recommended builder name: `<product-repo-name>-builder`.

## Who does what

| Step | Kind | Actor |
|------|------|--------|
| Create the empty builder GitHub repo | Git operation | Human |
| Clone builder, add `aios-public` remote, pull protocol, push | Git operation | Human |
| Inspect remotes (`origin` = builder, `aios-public` = protocol source) | Git operation | Human, then Executor verifies |
| Attach GitHub for builder **and** product in ChatGPT | Tool connection | Human |
| Open Cursor on the builder; make the product repo available | Tool connection | Human |
| Create task `0001` and update builder `queue.md` | Architect | ChatGPT |
| Tell Cursor `execute 0001` | Human | Human |
| Inspect product read-only; write builder `memory/` baseline; do not change product behavior | Executor | Cursor |
| Write `prompts/0001-….response.md` in the builder | Executor | Cursor |
| Commit and push builder changes | Git operation | Human |
| `review 0001` and update builder `queue.md` | Architect | ChatGPT |

AIOS does not create repositories, attach connectors, or authorize tools automatically. Stop after bootstrap and wait for Architect review before any real product implementation.

## Expected remote topology

On the builder, after bootstrap:

```text
origin       -> <BUILDER_REPOSITORY>
aios-public  -> <AIOS_PUBLIC_REPOSITORY>
```

The product repository stays on its own `origin`. Do not rewrite product remotes during bootstrap. Do not point the builder's `origin` at `aios-public`.

## Human — Git operations

Create the builder as an **independent** Git repository. Do **not** add a README, license, or gitignore on GitHub (`gh repo create` without `--add-readme`), so the clone starts empty.

These commands work in macOS/Linux shells, Git Bash, and PowerShell. `<AIOS_PUBLIC_REPOSITORY>` is `owner/name` (for example `shes-dev/aios-public`).

### PowerShell / Git Bash / macOS / Linux

```powershell
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

`gh repo create` with a name and `--private` does not prompt. Do not pass `--add-readme`. Older GitHub CLI docs mentioned `--confirm`; current `gh` does not need it.

If the clone is empty, `git checkout -b main` may fail because there is not yet a commit. In that case, after `git fetch aios-public`, use this instead of `checkout -b` + `pull`:

```powershell
git checkout -B main aios-public/main
git push -u origin main
git remote -v
```

That still leaves `origin` on the builder and `aios-public` as a secondary remote. Shared history with the protocol source is useful; it does not make `aios-public` the canonical origin.

If GitHub already created a README commit on the builder, keep the `git pull aios-public main --allow-unrelated-histories --no-edit` form and resolve any README conflict by keeping both files or preferring the protocol README.

Confirm remotes before continuing:

```text
origin       https://github.com/<OWNER>/<BUILDER_NAME>.git
aios-public  https://github.com/<AIOS_PUBLIC_REPOSITORY>.git
```

SSH is fine if that is how you talk to GitHub. The remote **names** must still be `origin` (builder) and `aios-public` (protocol source).

Clone or open `<PRODUCT_REPOSITORY>` separately if you do not already have it. Do not push to the product during bootstrap.

### Later protocol updates

From the builder:

```powershell
git fetch aios-public
git merge aios-public/main
```

## Human — tool connection

Do this before asking ChatGPT or Cursor to work across repos. AIOS cannot do it for you.

1. In ChatGPT, connect GitHub and make sure both `<BUILDER_REPOSITORY>` and `<PRODUCT_REPOSITORY>` are authorized. Attach GitHub on every turn that must read or write repo files.
2. Open Cursor on the **builder** folder (`<BUILDER_NAME>`), not on `aios-public` and not on the product as the only root.
3. Make `<PRODUCT_REPOSITORY>` available to Cursor without making it the AIOS workspace: second workspace folder, adjacent clone, or GitHub read access. Bootstrap inspects it read-only.
4. Tell ChatGPT which GitHub remotes you created and that Cursor is opened on the builder.

## Architect — first task

In the **builder**, ChatGPT:

1. Creates `prompts/`, `suggestions/`, and `memory/` if they do not exist.
2. Writes `prompts/0001-existing-project-onboarding-bootstrap.prompt.md` using the prompt body below, with placeholders filled.
3. Puts `0001` in the Active section of `queue.md`.

Then the Human tells Cursor:

```text
execute 0001
```

Cursor may modify the product repository only when the task explicitly says so. This bootstrap task must **not** say so.

After Cursor writes the response, the Human commits and pushes the builder. The Human tells ChatGPT `review 0001`. ChatGPT writes the review and updates the builder `queue.md`.

---

# Task 0001 — Existing-project onboarding bootstrap

Copy from here down into `prompts/0001-existing-project-onboarding-bootstrap.prompt.md` after filling placeholders.

## Goal

Adopt `<PRODUCT_REPOSITORY>` into AIOS using builder `<BUILDER_REPOSITORY>` and protocol source `<AIOS_PUBLIC_REPOSITORY>`. Produce a durable adoption baseline in the builder. Do not change product behavior.

## Repositories

- Builder: `<BUILDER_REPOSITORY>` (this workspace; `origin`)
- Protocol source: `<AIOS_PUBLIC_REPOSITORY>` (remote `aios-public`)
- Product: `<PRODUCT_REPOSITORY>` (inspect only)

## Requirements

1. Verify builder remotes:
   - `origin` → `<BUILDER_REPOSITORY>`
   - `aios-public` → `<AIOS_PUBLIC_REPOSITORY>`
   - `origin` is not `<AIOS_PUBLIC_REPOSITORY>`
2. Inspect `<PRODUCT_REPOSITORY>` read-only (clone, second workspace root, or `gh repo view`). Do not commit, push, or change product files.
3. Create a durable adoption baseline under builder `memory/`, including at least:
   - product name, default branch, and apparent purpose;
   - builder/product/`aios-public` ownership split;
   - confirmed remotes;
   - that product behavior was not changed.
4. Do not add product-specific knowledge to `<AIOS_PUBLIC_REPOSITORY>`.
5. Do not implement product features.
6. Stop for Architect review. Do not start a product implementation task.

## Out Of Scope

- Product feature work
- Hosted AIOS, servers, CLIs, or automatic dispatch
- Automatic GitHub repo creation or connector authorization
- Editing `queue.md`

## Response

Cursor writes `prompts/0001-existing-project-onboarding-bootstrap.response.md` in the builder.

Cursor must not edit `queue.md`.
