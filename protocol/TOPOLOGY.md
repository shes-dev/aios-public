# Repository topologies

The public AIOS topology is mandatory:

```text
one project / workstream  ->  one dedicated builder repo
one builder repo          ->  one or many product repos
```

`aios-public` is protocol source only. Organizational state never lives inside a product repository.

## Builder and product(s)

```text
aios-public
    ↓ protocol source
<project>-builder
    ↓ one canonical AIOS state (QMD + artifacts)
<product-a> ... <product-n>
    ↓ implementation only
```

```text
                         BUILDER REPO

                         Architect
                             |
                 prompts/NNNN-slug.prompt.md
                      /      |      \
                 Cursor    Claude   Codex
                    \        |       /
                 prompts/NNNN-slug.response.md
                             |
                 prompts/NNNN-slug.review.md
                             |
                      queue.md / QMD

           product repo A   product repo B   product repo C
```

The Human typically creates the builder and names the product repo(s). An Architect agent initializes or resumes the builder. Details: [Bootstrap](BOOTSTRAP.md).

`aios-public` stays generic. It is not the builder’s primary origin and not a place for consumer-specific knowledge.

## Ownership

The **builder** owns:

- `queue.md` (QMD: live work only)
- `BLOCKED.md`, `COMPLETED.md`, `DELETED.md` (task IDs moved out of QMD)
- `prompts/` (`.prompt.md` / `.response.md` / `.review.md`)
- `suggestions/` (ideas that may be promoted to tasks)
- `memory/` (how we work and product facts; never promoted)
- cross-repo decisions and evidence

Each **product** repository owns:

- application, runtime, and deployment code
- implementation changes
- existing Git history (never rewritten during adoption)

The Executor may change a product repository only when the active task explicitly says so.

## Builder remotes

After bootstrap:

```text
origin       -> <BUILDER_REPOSITORY>
aios-public  -> <AIOS_PUBLIC_REPOSITORY>
```

Never leave the builder’s `origin` pointing at `aios-public`.

If you cloned `aios-public` by mistake and want that working copy to become a builder, fix the remotes before any push:

```bash
git remote rename origin aios-public
git remote add origin https://github.com/<BUILDER_REPOSITORY>.git
git remote -v
git push -u origin main
```

Later protocol updates:

```bash
git fetch aios-public
git merge aios-public/main
```

## Who does what

Roles: [Roles](ROLES.md). AIOS does not launch or connect tools.

| Kind | Who | What |
|------|-----|------|
| Git / access | Human | Create builder; authorize tools on builder + product repo(s); never rewrite product history |
| Architect | Role | Decompose work; write `.prompt.md`; own QMD; write/own `.review.md`; coordinate independent Executors |
| Executor | Role | Consume one Active prompt; write matching `.response.md`; do not edit `queue.md` |
| Reviewer | Role | Often the Architect; writes `.review.md` and updates QMD |

## First tasks after a builder exists

1. Architect creates numbered `.prompt.md` files in the builder and updates `queue.md`.
2. Independent Active tasks may go to different external Executors in parallel (e.g. Cursor / Claude / Codex).
3. Each Executor changes product repos only if its task says so, and writes its `.response.md` in the builder.
4. Architect/Reviewer writes each `.review.md`, then moves the completed task from QMD to `COMPLETED.md`.

Empty-builder git sequence: [Existing-project initialization](examples/INIT_EXISTING_PROJECT.prompt.example.md). Run [Bootstrap](BOOTSTRAP.md) first.
