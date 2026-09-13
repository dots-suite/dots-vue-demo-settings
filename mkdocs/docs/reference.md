# Settings reference

Every key a configuration file may contain, with its accepted values, its default and what it
changes. Defaults come from the application's own `default.conf.json`; keys marked **no default**
exist only if a configuration file declares them.

Unless stated otherwise, a key may appear in `custom.conf.json` (under `genericConf`) or in any
collection file, and follows [the cascade](cascade.md).

## Identity

| Key | Values | Default | Effect |
|---|---|---|---|
| `collectionId` | string | **no default** | Ties the file to a collection of the DTS endpoint, **case-sensitively**, and names the directory holding its components and assets. The only mandatory key of a collection file. |

## Documents

| Key | Values | Default | Effect |
|---|---|---|---|
| `mediaTypeEndpoint` | `"tei"` \| `"html"` | `"tei"` | Format requested from the endpoint for a document. At this stage of development both are rendered with the application's default stylesheets — the TEI Consortium's, plus a few adjustments for the `theater` project, for `tei`; an ENC-PSL stylesheet for `html` — which a [collection stylesheet](styling.md#collection-stylesheets) can customise. |
| `collectionCustomCss` | file base name, or `false` (DoTS-vue default applies) | **no default** | Stylesheet applied to the documents of the collection — see [Styling and assets](styling.md). |

## Navigation bar

All under `homePageSettings.appNavBar`.

| Key | Values | Default | Effect |
|---|---|---|---|
| `collectionShortTitle` | string | `""` | Short name in the navigation bar and in the collection selector. Falls back to the collection identifier. |
| `appNavBarLogo.imgName` | file name, or a URL containing `https` | `"logo_dots_circle.svg"` | Left-hand logo. A file name is looked up in `assets/images/`; a URL is used as is. |
| `appNavBarLogo.href` | URL or path | `"/"` | Where that logo links to. |
| `appNavBarApiLogo.imgName` | file name, URL, or `""` | `""` | A second logo, on the right of the application navBar — typically your API endpoint. Empty means bare link without logo. |
| `appNavBarApiLogo.href` | URL or path | `"/"` | Where that second logo links to. |

## Collection header

All under `homePageSettings.pageHeader`.

| Key | Values | Default | Effect |
|---|---|---|---|
| `collectionAltTitle` | string | `"Titre"` | Title displayed instead of the collection's DTS title. Empty falls back to the DTS title. |
| `aboutButtonText` | string | `"about"` | Label of the About button. |
| `collectionBannerImg` | file name, or a URL starting with `https` | `"banner_default.png"` | Banner image — see [Styling and assets](styling.md). |

## Collection description

All under `homePageSettings.descriptionSection`.

| Key | Values | Default | Effect |
|---|---|---|---|
| `collectionDescription` | string | `""` | Plain-text description, displayed only when no component and no DTS description are available. |
| `customCollectionDescription.compName` | component name, without `.vue` | `"HomePageContent"` | A component rendered in place of the description — see [Collection pages](pages.md). |

## Collection listing

All under `homePageSettings.listSection`.

| Key | Values | Default | Effect |
|---|---|---|---|
| `displayMode` | `"toc"` \| `"card"` \| `"list"` \| `"mixed"` | `"toc"` | How members are listed: a tree (`toc`), paginated cards (`card`), a table (`list`), or cards at the first level then a tree below (`mixed`). Any other value falls back to `toc`. |
| `columns` | array of `{ key, label, type?, width? }` | `[{ "key": "title", "label": "Titre" }]` | Columns of the table. **On the home page it applies only when `displayMode` is `"list"`**; on the search page it always applies. Detailed in [Search page settings](search.md). |
| `cardCollectionPerPage` | integer | `3` | Page size of the card and table pagination. |
| `logo` | file name, URL, `.vue` component, or `""` | `"DotsLogo.vue"` | Thumbnail of the collection in listings and cards. The default is the application's own logo, a Vue component rather than an image file. Empty is an explicit opt-out. |
| `browseButtonText` | string | `"Browse the collection"` | Label of the button opening a collection (toc tree). |
| `openState` | boolean | `false` | When `true` and `displayMode` is `toc`, the first level of the tree is expanded on load. |
| `displaySort` | array of identifiers | **no default** | Manual ordering: the identifiers listed come first, in that order; everything else follows alphabetically. Without it, ordering is alphabetical. |

!!! note "`logo` accepts a component, not only an image"
    The resolver looks up the name among the images of `assets/images/`, then checks what it found:
    a Vue component is mounted as such, anything else is used as an image source. That is how the
    default `DotsLogo.vue` — shipped with the application — works, and a settings repository may do
    the same with a `.vue` file of its own.

## Footer

All under `footerSettings`.

| Key | Values | Default | Effect |
|---|---|---|---|
| `footerTitle` | string | `"Titre"` | Title at the bottom left. Empty falls back to the collection title. |
| `footerSubtitles` | array of strings | `[]` | One line each, under the title. |
| `footerDescription` | string | the DoTS-vue credit line | Text block in the right-hand column. Hidden when empty. |

## Table of contents

All under `tableOfContentsSettings`.

| Key | Values | Default | Effect |
|---|---|---|---|
| `tableOfContentDepth` | integer | `5` | How deep the citation tree is fetched and displayed. Raised automatically when `editByCiteType` requires a deeper level. |
| `editByLevel` | integer | `1` | Citation level from which a fragment is rendered as an edited text rather than as a sub-table of contents. |
| `editByCiteType` | array of `citeType` strings | `[]` | Takes precedence over `editByLevel`: a fragment whose `citeType` is listed is rendered as an edited text, whatever its level. |
| `countByCiteType` | array of `citeType` strings | `[]` | Counts descendants of these types and shows the count in the breadcrumb. |
| `displayTopToc` | boolean | `false` | Shows the table of contents above the document. |
| `displayLeftToc` | boolean | `false` | Shows the left-hand table of contents. |
| `leftTocFragmentIsDocument` | boolean | `false` | Restricts the left table of contents to the current fragment instead of the whole document. |

!!! warning "These three booleans are tested against `false`"
    `displayTopToc`, `displayLeftToc` and `leftTocFragmentIsDocument` are enabled by **any** value
    other than the boolean `false` — including the string `"false"`. Write real booleans.

## About page

| Key | Values | Default | Effect |
|---|---|---|---|
| `aboutPageSettings` | array of `{ tabName, compName }` | three placeholder tabs | Tabs of the About page, in order. A non-empty array is also what makes the About button appear. `null` entries are ignored, which is why several files in this repository end the array with one. See [Collection pages](pages.md). |

## Collections shown

| Key | Values | Default | Effect |
|---|---|---|---|
| `excludeCollectionIds` | array of identifiers | `[]` | Collections removed from listings, breadcrumbs and ancestors. Parent counts are recomputed accordingly. |

## Metadata panel

| Key | Values | Default | Effect |
|---|---|---|---|
| `namespaces` | `{ prefix: baseIRI }` | `dts`, `dct`, `schema`, `dots` | Vocabularies used to expand the JSON-LD metadata, then shorten the IRIs back into `prefix:term` form. Every key below is written in that shortened form. |
| `metadataDisplayOrder` | array of terms | `["dts:id", "dts:type", "dts:*", "dct:*", "schema:*", "dots:*"]` | Which metadata are displayed **and in which order**. Accepts an exact term, a whole-vocabulary wildcard `prefix:*`, or a dotted sub-key. A term listed explicitly is never removed by `excludeMetadata.fields`. |
| `metadataRename` | `{ term: the new label of your choice }` | `{"@id": "dts:id", "@type": "dts:type"}` | Labels shown to the reader. A term left out keeps its raw `prefix:term` form. |
| `excludeMetadata.onlyDeclared` | boolean | `false` | `true` displays only what `metadataDisplayOrder` names; `false` appends everything else afterwards. |
| `excludeMetadata.fields` | array of patterns | `[]` | Terms to drop among those not named explicitly — that is, what a wildcard collected, and the leftovers of the second pass. |
| `excludeMetadata.alwaysExclude` | array of patterns | `["dts:citationTrees"]` | Terms to drop **even if** named in `metadataDisplayOrder`. Evaluated first. |
| `metadataLogosMapping` | array of `{ name, ext, dynamic? }` | a set of repositories | Associates a URL fragment (`ext`) with a logo (`logo_<name>.svg`). `dynamic` accepts `"dots_api_base_url"` or `"dots_vue_self"`, computed at runtime. |
| `topBreadcrumbButtonLabel` | array of property paths | `["title"]` | How the label of a resource button is built in the breadcrumb. Several values are joined with `, `. |

!!! note "Wildcards have exactly one shape"
    `prefix:*` and nothing else. A partial pattern such as `dct:bibli*` is read as an ordinary term,
    matches nothing and fails silently. There is no pattern meaning “everything”: list the prefixes.

## Search page

| Key | Values | Default | Effect |
|---|---|---|---|
| `customRoutes` | array of `{ name, path, compName }` | **no default** | Extra pages. `compName` currently resolves `"SearchPage"` only. |
| `searchConfig.facets` | array of `{ key, label?, order?, enabled? }` | **no default** | Metadata facets. Exclusion-based: a facet not listed is still displayed. Exclusions are explicit, with `enabled` set to `false`. |
| `searchConfig.temporalFacets` | array of `{ key, label?, order?, enabled? }` | **no default** | Date-range facets, same rules with a different ordering behaviour. |

All three are detailed in [Search page settings](search.md).

## Read only at the top

| Key | Values | Default | Effect |
|---|---|---|---|
| `homePageSettings.favicon` | `.ico` file name | `"favicon.ico"` | Browser icon. Read **only from `custom.conf.json`**, and looked up **only in a `default/` directory** at the root of the repository. Declaring it in a collection file has no effect. |
