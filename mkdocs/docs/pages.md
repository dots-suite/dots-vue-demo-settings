# Collection pages

Two kinds of Vue component may live in a collection directory: the **tabs of the About page**, and a
**free block on the home page**. Both are ordinary single-file components, written by you and
compiled with the application.

!!! info "These pages belong to projects"
    dots-vue gives a home page and an About page to a **project** — a first-level collection such as
    ENCPOS or theater. Its sub-collections are reached from within those pages.

## About pages

### Declaring the tabs

`aboutPageSettings` is an array: one entry per tab, in the order the reader sees them.

```json
"aboutPageSettings": [
  { "tabName": "Metadata settings", "compName": "about1" },
  { "tabName": "Segmentation",      "compName": "about2" },
  { "tabName": "Overview",          "compName": "about3" }
]
```

`tabName` is the label of the tab in the front-end; `compName` is the file to mount, **without the `.vue`
extension**. The names `about1`, `about2`… are a convention, not a requirement — but keeping the
number aligned with the position spares the next reader a puzzle.

!!! warning "Renumbering is a three-step move"
    Inserting a tab means renaming the files, updating `compName` in the configuration and, if you
    follow the convention, renaming the component's own `name:` option. Forgetting one leaves a tab
    pointing at nothing.

### How a component is found

The application looks for `compName` in three places, in order:

1. **the directory of the collection that declares the tab** — `ENCPOS_c2/about1.vue` for
   `ENCPOS_c2`;
2. **the directory of the root collection** — `ENCPOS/about1.vue`, so a collection may declare tabs
   without keeping a directory of its own;
3. **the application's own About components**, as a last resort — and it only ships `about1`,
   `about2` and `about3`, so a `compName` of `about4` with no matching file anywhere fails.

!!! warning "One level, no sub-directories"
    About components are looked up **directly inside the collection directory**.
    `ENCPOS/about/about1.vue` is not found; `ENCPOS/about1.vue` is.

### Writing one

The application provides the tab strip and the surrounding layout; the component provides the
content. The house shape is a title and a body:

```vue
<template>
  <article class="about">
    <div class="title app-width-padding">Collection, document, fragment</div>
    <div class="about-content app-width-padding">
      <h2>Thesis abstract as a document</h2>
      <p>
        In this case, the publisher wishes to emphasize the semantic structuring of the corpus…
      </p>
    </div>
  </article>
</template>
<script>
export default {
  name: 'AboutFirstSection',

  setup () {
  }
}
</script>
<style scoped>
</style>
```

The two `app-width-padding` classes align the block with the rest of the page. `<style scoped>` keeps
whatever you add to that component alone.

!!! tip "Plain HTML is usually enough"
    An About page is prose. Reach for `ref`, `computed` or a `<script setup>` only when the content
    genuinely varies — the existing examples in this repository are almost entirely static markup.

## The home-page block

`homePageSettings.descriptionSection` offers two ways of describing a project on its home page:

```json
"descriptionSection": {
  "collectionDescription": "A short paragraph, written straight in the configuration.",
  "customCollectionDescription": { "compName": "HomePageContent" }
}
```

`collectionDescription` takes plain text. When a paragraph is not enough — links, images, a table,
several sections — point `customCollectionDescription.compName` at a component instead, by
convention `HomePageContent.vue`, placed in the collection directory:

```
ENCPOS/
└── HomePageContent.vue
```

In this repository, `ENCPOS`, `ENCPOS_c2`, `dots_cookbook` and `theater` each carry one — four
projects, four home pages.

## What these components may not do

They are compiled into the application, so they can do a great deal — but they are **not** the place
for application logic. Fetching from the DTS endpoint, altering the metadata panel or adding routes all
belong to configuration keys, not to component code: see the
[Settings reference](reference.md). A component that queries the API on its own will drift the day
the application changes how it does so.
