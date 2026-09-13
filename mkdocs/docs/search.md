# Search page settings

A collection has no search page until its configuration asks for one. Three independent settings are
involved: `customRoutes` puts the page online, `searchConfig` shapes its facets, and
`homePageSettings.listSection.columns` shapes its results table.

!!! warning "One prerequisite comes first"
    dots-vue does not search the DTS endpoint: it queries an **Elasticsearch index**, built by
    [dots-cli-es](https://github.com/dots-suite/dots-cli-es). Which metadata becomes a facet, and
    under which canonical key, is decided there — the settings below rename, reorder and hide
    facets, they never invent one. See the
    [dots-cli-es documentation](https://dots-suite.github.io/dots-cli-es/) and its
    [Indexing](https://dots-suite.github.io/dots-cli-es/indexing/) page.

## Opening the page: `customRoutes`

```json
"customRoutes": [
  {
    "name": "Search",
    "path": "search",
    "compName": "SearchPage"
  }
]
```

| Key | Role |
|---|---|
| `path` | the URL segment — this entry makes the collection searchable at `/<collectionId>/search`. A segment no entry declares redirects to the home page rather than erroring. |
| `compName` | the component to mount. **`SearchPage` is case sensitive**, and is currently the only value the application can resolve. |
| `name` | the internal route name, used when building links. |

## Two families of facets

`searchConfig` holds two independent arrays:

- **`facets`** — metadata facets, values a reader ticks, such as an author;
- **`temporalFacets`** — date ranges, presented as sliders rather than lists.

In both, an entry is identified by `key`, the canonical metadata key as the indexing side writes it
(`dublinCore.creator`, `extensions.funder`). The one exception is `collections`, the facet that
narrows the search to a sub-collection.

**The logic is one of exclusion, not of selection.** A facet the API returns but the configuration
never mentions is still displayed, under its default label. Declaring an entry serves to do one of
three things: exclude it, rename it, or move it.

## Excluding: `"enabled": false`

```json
{
  "key": "dublinCore.publisher",
  "enabled": false
}
```

The facet disappears from the panel — and the saving goes beyond the display: its key is sent to the
search API as `excludeFacets` (or `excludeTemporalFacets`), which then **skips computing that
aggregation altogether**. Excluding a facet also lightens the query.

## Renaming: `label`

Without a `label`, a metadata facet is titled with its raw key — `dublinCore.creator` — which is
precise but hardly inviting. A temporal facet falls back on the label supplied by the API instead.

Renaming is purely a matter of display: it changes neither the key queried nor the values returned.

## Ordering: `order`

Facets are sorted by ascending `order`. **The two arrays do not treat an unnumbered facet the same
way:**

| | A facet left without `order` |
|---|---|
| `facets` | is pushed to the end of the panel |
| `temporalFacets` | keeps the position the API gave it; numbered facets are inserted relative to it |

The `collections` facet is a case apart: it always opens the panel, whatever number it is given.

## A complete example

```json
"searchConfig": {
  "facets": [
    { "key": "collections",            "label": "Volumes annuels", "enabled": true },
    { "key": "dublinCore.creator",     "label": "Auteurs", "enabled": true, "order": 3 },
    { "key": "dublinCore.contributor", "enabled": false },
    { "key": "dublinCore.publisher",   "enabled": false },
    { "key": "extensions.funder",      "enabled": false }
  ],
  "temporalFacets": [
    { "key": "dublinCore.created",  "label": "Promotion (période)", "enabled": true, "order": 1 },
    { "key": "dublinCore.coverage", "label": "Sujet (période)",     "enabled": true, "order": 2 },
    { "key": "dublinCore.issued",   "enabled": false }
  ]
}
```

## The results table: `columns`

`columns` decides what the reader sees of each result. It lives under
`homePageSettings.listSection`, not under `searchConfig`, because the same array also describes the
collection listing when `displayMode` is `list`: one declaration, two views.

**Left undeclared, the results table falls back to a single column, the title.**

```json
"listSection": {
  "columns": [
    { "key": "title",               "label": "Titre",  "width": "2fr" },
    { "key": "dublinCore.creator",  "label": "Auteur", "width": "1fr" },
    { "key": "dublinCore.created",  "label": "Date",   "type": "date", "width": "125px" },
    { "key": "dublinCore.coverage", "label": "Sujet",  "type": "date", "width": "125px" }
  ]
}
```

Columns appear in the order of the array. Each entry accepts four properties:

| Property | Effect |
|---|---|
| `key` | the metadata path to read, resolved case-insensitively. The only required property: an entry without it is dropped. |
| `label` | the column header. Without it, the raw key is displayed. |
| `type` | `string` unless stated. **`date`** is the one that changes behaviour on the search page: the value is checked against the temporal normalisation and displayed **only if both bounds of the period could be resolved**. An incomplete date leaves the cell empty rather than showing something misleading. `range` turns the column filter into a two-bound input. |
| `width` | a CSS grid track — a proportion (`2fr`) or a fixed size (`125px`). **If no column declares a width, all share the space equally.** |

## See it live

The [search page of the ENCPOS recipe](https://dots.chartes.psl.eu/cookbook/ENCPOS/search) is built
by the settings quoted above: open the filters to see the facets kept, their labels and their order,
and the four columns of the results table.
