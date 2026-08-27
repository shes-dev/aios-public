# Repository topologies

AIOS supports two topologies.

Single-repository AIOS is the default. Builder/product separation is optional.

## Single repository

Clone `aios-public` and use that clone as the project workspace.

```text
aios-public clone
  queue.md + prompts + product code in one repository
```

ChatGPT, Cursor, or one Claude session, plus the Human, all work in that one repository.

This is the path in the root README under **Start**.

## Builder and product

Use this when the product repository already exists and you want AIOS planning and history kept out of the product repo.

```text
aios-public
    ↓ protocol/template source
<project>-builder
    ↓ approved tasks / project knowledge
<project>
```

`aios-public` stays generic. It is the protocol/template source, not the builder's primary origin, and not a place for consumer-specific knowledge.

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

These roles are the same in both topologies. Who plays them is defined in [Roles](ROLES.md). One Claude session may perform Architect, Executor, and Reviewer sequentially. ChatGPT + Cursor remains valid. The optional two-repository path adds extra Git and Human connection steps.

| Kind | Who | What |
|------|-----|------|
| Git operation | Human (or Executor when the task says so) | Create/clone the builder, set remotes, fetch protocol, push builder `main`, inspect the product read-only |
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

See [Existing-project initialization](examples/INIT_EXISTING_PROJECT.prompt.example.md) for the copy/paste bootstrap.
