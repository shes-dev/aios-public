# Topology

AIOS can live in one repository or two. The loop does not change:

```text
Talk -> Document -> Task -> Cursor executes -> Review -> Done
```

Git and Markdown remain the coordination mechanism. ChatGPT remains Architect. Cursor remains Executor. Cursor does not edit `queue.md`.

## Single repository

The default. Protocol, queue, prompts, and product code share one `origin`.

Use this when you are starting a new project, or when you want planning history next to the product code.

See the root `README.md` Start section.

## Builder and product

Optional. Use this when a product repository already exists and you want AIOS planning, tasks, and reviews kept out of the product history.

```text
aios-public
    ↓ protocol/template source
<project>-builder
    ↓ approved tasks / project knowledge
<project>
```

| Repository | Role |
|------------|------|
| `<AIOS_PUBLIC_REPOSITORY>` | Generic protocol and template source. Not the builder's `origin`. Contains no consumer-specific knowledge. |
| `<BUILDER_REPOSITORY>` | Independent AIOS workspace. Canonical `origin`. Owns queue, prompts, suggestions, memory, responses, and reviews. |
| `<PRODUCT_REPOSITORY>` | Application, runtime, and deployment code. Unchanged until a task explicitly allows product edits. |

Single-repository AIOS remains valid. Do not treat builder/product separation as required.

## Ownership

| Path | Repository | Writer |
|------|------------|--------|
| `queue.md` | builder | ChatGPT only |
| `prompts/*.prompt.md` | builder | ChatGPT |
| `prompts/*.response.md` | builder | Cursor |
| `prompts/*.review.md` | builder | ChatGPT |
| `suggestions/` | builder | ChatGPT |
| `memory/` | builder | ChatGPT; Cursor only when a task says so |
| `protocol/` | builder, pulled from `aios-public` | Updated by merging the `aios-public` remote |
| application / runtime / deployment code | product | Cursor, and only when the active task says so |

`aios-public` stays generic. Consumer architecture, secrets, and product names belong in the builder `memory/` or the product repository, not in `aios-public`.

## Builder remotes

After bootstrap the builder remotes must be:

```text
origin       -> <BUILDER_REPOSITORY>
aios-public  -> <AIOS_PUBLIC_REPOSITORY>
```

Never leave the builder's canonical `origin` pointing at `aios-public`. Later protocol updates are:

```bash
git fetch aios-public
git merge aios-public/main
```

## Tool connection

AIOS cannot create GitHub repositories, attach ChatGPT connectors, or open Cursor folders by itself.

Before cross-repo work, the Human must:

1. Create or clone the builder with Git (see `protocol/examples/INIT_EXISTING_PROJECT.prompt.example.md`).
2. Connect **both** the builder and the product repositories in the Architect environment (ChatGPT with GitHub attached for each repo that must be read or written).
3. Open Cursor on the **builder** folder. Make the product repository available read-only or as a second workspace root.
4. Keep GitHub attached on Architect turns that read or write repo files.

## First task

1. Architect writes `prompts/NNNN-slug.prompt.md` in the builder and updates the builder `queue.md`.
2. Human tells Cursor `execute NNNN` (for example `execute 0001`).
3. Cursor executes from the builder. Cursor modifies the product repository only when that task says so. Cursor writes the response in the builder. Cursor does not edit `queue.md`.
4. Human commits and pushes the builder, and the product if it changed.
5. Human tells ChatGPT `review NNNN`. Architect writes the review and updates the builder queue.

## Step kinds

Every onboarding step is one of:

| Kind | Who | Examples |
|------|-----|----------|
| Git operation | Human (or Human-run CLI) | `gh repo create`, `git clone`, `git remote add`, `git pull`, `git push` |
| Tool connection | Human | Attach GitHub in ChatGPT; open Cursor on the builder; authorize both repos |
| Architect | ChatGPT | Create the first task; update `queue.md`; review |
| Executor | Cursor | Execute the task; write the response; inspect product read-only unless the task says otherwise |
