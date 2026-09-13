# The settings cascade

A collection is almost never configured by a single file. Its settings are assembled from up to five
layers, each overriding the previous one. Understanding that order is what lets a deployment state a
rule once and refine it where needed, instead of repeating it in every file.

## The five layers

```
  1  default.conf.json         the application's own defaults  (in dots-vue)
             ↓
  2  custom.conf.json          deployment-wide, your repository
             ↓
  3  the root collection       the top of the DTS tree
             ↓
  4  the project               a first-level collection: ENCPOS, theater
             ↓
  5  a sub-collection          when its documents are read
                               ex: year 1849 (ENCPOS_1849) of the ENCPOS thesis collection
```

The first two produce what the application calls **`genericConf`**: they are merged once, at startup.
The last three are the `*.conf.json` files of your repository, matched by their `collectionId` and
applied in turn.

A key absent from a layer is simply inherited from the one above. This is why
`encpos_1971.conf.json` can be four lines long: everything it does not mention comes from `ENCPOS`,
and before that from your `custom.conf.json`.

## Objects merge, arrays replace

This is the rule to remember.

**Objects are merged deeply.** Setting `homePageSettings.listSection.displayMode` in a collection
file leaves `cardCollectionPerPage`, `logo` and the rest of `listSection` untouched.

**Arrays are replaced wholesale.** Declaring `aboutPageSettings` in a collection means *these tabs
and no others* — the parent's tabs do not show through. The same goes for `columns`, `displaySort`,
`footerSubtitles`, `excludeCollectionIds` and every other array.

```json title="parent: encpos.conf.json"
"aboutPageSettings": [
  { "tabName": "Metadata settings", "compName": "about1" },
  { "tabName": "Segmentation",      "compName": "about2" }
]
```

```json title="child: encpos_c2.conf.json"
"aboutPageSettings": [
  { "tabName": "Segmentation", "compName": "about1" }
]
```

The child shows **one** tab, not three. That is deliberate: partial array merging by position would
produce nonsense as soon as two files listed things in a different order.

## Layer 3, the root collection

The root collection is the top of the DTS tree — `dots_cookbook` for the endpoint of this
repository, whose members are the projects `theater`, `ENCPOS` and `ENCPOS_c2`. Its configuration
file, matched by `collectionId`, is the natural place for everything the corpus shares: the
navigation bar, the footer, the metadata panel, the vocabularies.

## Layer 4, the project

The **project** is a first-level collection — ENCPOS, theater — coherent in itself and unrelated to
its siblings. It is the level a reader lands on: dots-vue gives a home page and an About page to a
project, not to each of its sub-collections, which are reached from within those pages.

This is therefore the layer that carries a project's identity: its title, its banner, its About
tabs, the way its members are listed. When a file matches it, it is merged on top of the root; when
none does, the root's settings are carried down, minus a few identity fields (the short title, the
alternative title and the About button label are reset) so that a project does not silently display
the name of the deployment that hosts it.

## Layer 5, a sub-collection

Deeper collections have configuration files too, and they are merged last — but they refine **how
their contents behave**, not how they are presented as a destination: a different
`mediaTypeEndpoint`, another stylesheet, a different citation depth.

In this repository, `encpos_1972.conf.json` is exactly that: it does not give the 1972 volume a home
page of its own, it decides that the documents of that volume are served as HTML.

## A worked example

One key, `mediaTypeEndpoint`, travels the whole cascade in this repository. The DTS endpoint of the
Cookbook has `dots_cookbook` for root collection, with `theater`, `ENCPOS` and `ENCPOS_c2` as its
projects:

| Layer | File | Value | Effect |
|---|---|---|---|
| 1 | `default.conf.json` (dots-vue) | `"tei"` | the application's default |
| 2 | `custom.conf.json` | `"html"` | **the deployment turns it over**: everything is served as HTML unless stated otherwise |
| 3 | `dots_cookbook.conf.json` | — | the root collection says nothing, and inherits HTML |
| 4 | `encpos.conf.json` | `"tei"` | **the project turns it back**: ENCPOS is a TEI corpus |
| 5 | `encpos_1972.conf.json` | `"html"` | the 1972 volume alone departs from its project again |

Four files, four answers to the same question, and the last one to speak about a given collection
wins. A reader opening a document of `ENCPOS_1972` gets HTML; one opening a document of
`ENCPOS_1971`, which says nothing, gets TEI inherited from its project; one opening a document of
`ENCPOS_c2`, a project that says nothing either, gets HTML inherited from the deployment.

!!! tip "Reading a value from the bottom up"
    To know where a setting comes from, walk the layers upwards from the collection and stop at the
    first file that mentions it. If none does, the value is the application's default.

## Two things to watch

**A file with no `collectionId` is never selected.** The pivot is that value, not the file name: a
file can be called anything, but if `collectionId` does not match an identifier from the endpoint,
nothing in it will ever apply.

**`collectionId` is matched case-sensitively.** `ENCPOS_c2` and `encpos_c2` are different
collections as far as the endpoint is concerned; copy the identifier rather than retyping it.

**Some settings are read only at the top.** `homePageSettings.favicon` is read from `genericConf`
alone: overriding it in a collection file has no effect. It is noted as such in the
[Settings reference](reference.md).
