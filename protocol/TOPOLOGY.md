# Repository topologies

The canonical AIOS stage-1 topology is product + builder, with `aios-public` as protocol source.

Adopt, reuse, and create rules: [Bootstrap](BOOTSTRAP.md).

## Default: builder and product

```text
aios-public
    ↓ protocol source
<project>-builder
    ↓ AIOS workspace
<project>
    ↓ product implementation
```

`aios-public` stays generic. It is the protocol/template source, not the builder's primary origin, and not a place for consumer-specific knowledge.

The builder is the AIOS workspace. The product is implementation code. Reuse an existing matching builder; create `<product-repo-name>-builder` only when none exists and creation is authorized.

## Single repository

Still valid when protocol and product should share one `origin`. Not the Claude stage-1 default.

```text
aios-public clone
  queue.md + prompts + product code in one repository
```

ChatGPT, Cursor, or one Claude session, plus the Human, all work in that one repository.

The root README beginner path uses builder/product. This single-repository path remains valid for teams that want one shared `origin`.

## Ownership

The **builder** owns:

- `queue.md`
- `prompts/`
- `suggestions/`
- `memory/`
- project architecture and operating knowledge
- task prompts, Executor responses, and Architect reviews

The **product** repository owns:

- application, runtime, and deployment code
- implementation changes
- existing Git history and branches (never rewritten during adoption)

The Executor may change the product repository only when the active task explicitly says so.

## Builder remotes

After bootstrap, the builder remotes must be:

```text
origin       -> <BUILDER_REPOSITORY>
aios-public  -> <AIOS_PUBLIC_REPOSITORY>
```

Never leave the builder's `origin` pointing at `aios-public`.

If you cloned `aios-public` by mistake and want that working copy to become a builder, fix the remotes before any push:

```bash
git remote rename origin aios-public
git remote add origin https://github.com/<BUILDER_REPOSITORY>.git
git remote -v
git push -u origin main
```

Later protocol updates stay on the secondary remote. Do not retarget `origin`:

```bash
git fetch aios-public
git merge aios-public/main
```

## Who does what

These roles are the same in both topologies. Who plays them is defined in [Roles](ROLES.md). One Claude session may perform Architect, Executor, and Reviewer sequentially. ChatGPT + Cursor remains valid. The builder/product path adds extra Git and Human connection steps.

| Kind | Who | What |
|------|-----|------|
| Git operation | Human (or Executor when the task says so and creation is authorized) | Inspect first; reuse existing product/builder; create only if missing; set remotes; never rewrite product history |
| Human tool-connection | Human | Authorize/connect **both** the builder and the product in the tools that will act as Architect, Executor, and Reviewer before cross-repo work. AIOS cannot do this automatically. |
| Architect | Role | Plan, write task prompts, own `queue.md` |
| Executor | Role | Execute the active task, write `prompts/*.response.md`, do not edit `queue.md` |
| Reviewer | Role | Write `prompts/*.review.md`, then update `queue.md` |

## First task after a builder exists

1. Architect creates the first numbered task in the builder and updates builder `queue.md`.
2. Human tells the Executor `execute 0001` (or the applicable task id).
3. Executor changes the product repository only if that task says so.
4. Executor writes the response into the builder.
5. Reviewer writes the review and updates the builder queue.

Git sequence for an existing product and a **new** empty builder: [Existing-project initialization](examples/INIT_EXISTING_PROJECT.prompt.example.md). Run [Bootstrap](BOOTSTRAP.md) first so that sequence is used only when no matching builder exists.
