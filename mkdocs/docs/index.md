# DoTS-Vue settings

[dots-vue](https://github.com/dots-suite/dots-vue) is a front-end for browsing and searching TEI
corpora published through a [DoTS](https://github.com/dots-suite/dots) endpoint. The application
itself holds no editorial choice: what a deployment looks like, which metadata it shows, how its
collections are listed, whether it offers a search page — all of it comes from a **settings
repository**, kept outside the application and plugged in at build time.

!!! info "Three words, used precisely throughout this site"
    A **deployment** is one dots-vue build paired with one settings repository — what a visitor
    reaches at a single address. A **project** is a first-level collection of the corpus: ENCPOS and
    theater are two projects, unrelated to one another, and a deployment may serve several of them.
    A **collection** is any node of the DTS tree, at any depth.

This site documents how such a repository is built, using
[dots-vue-demo-settings](https://github.com/dots-suite/dots-vue-demo-settings) — the settings of the
[DoTS Cookbook](https://dots.chartes.psl.eu/cookbook/) — as its worked example.

## Why settings live outside the application

One dots-vue codebase serves many deployments. Separating settings from code means a deployment can
change its labels, its colours, its metadata panel or its home page without touching — or
redeploying — the application, and without any deployment inheriting another's choices.

```
     dots-vue                          your-deployment-settings
     (the application)                 (this kind of repository)
            │                                    │
            │  build-time alias `confs`          │
            └──────────────┬─────────────────────┘
                           ▼
                   the deployed front-end
```

## What a settings repository contains

| | |
|---|---|
| **`*.conf.json`** at the root | one file per collection, plus `custom.conf.json` for deployment-wide settings |
| **Directories named after collection ids** | the Vue components and assets belonging to that collection: About pages, a custom home-page block, stylesheets, images |

Nothing else is required. A deployment that only wants to relabel a few things can consist of a single
`custom.conf.json`.

## Where to start

<div class="grid cards" markdown>

- **[Creating a settings repository](creating.md)** — from an empty repository to a working deployment.
- **[Repository layout](layout.md)** — what goes where, and the naming rules that matter.
- **[Configuration files](configuration.md)** — `custom.conf.json` and the per-collection files.
- **[The settings cascade](cascade.md)** — how five layers of settings are merged into one.
- **[Settings reference](reference.md)** — every key, its accepted values and its effect.

</div>

## Related projects

| Project | Repository | Documentation |
|---|---|---|
| **DoTS** — DTS-compliant publication API | [dots-suite/dots](https://github.com/dots-suite/dots) | [dots_documentation](https://dots-suite.github.io/dots_documentation/) |
| **dots-vue** — the front-end configured here | [dots-suite/dots-vue](https://github.com/dots-suite/dots-vue) | — |
| **dots-cli-es** — indexing CLI and search API | [dots-suite/dots-cli-es](https://github.com/dots-suite/dots-cli-es) | [DoTS Search API](https://dots-suite.github.io/dots-cli-es/) |
