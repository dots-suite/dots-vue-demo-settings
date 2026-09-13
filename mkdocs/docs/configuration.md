# Configuration files

Every `*.conf.json` file sits at the **root** of the repository — never inside a collection
directory. There are two kinds, and they do not have the same shape.

## `custom.conf.json`, the deployment-wide file

One per repository. It overrides the application's own defaults, and therefore reuses their shape:
everything goes under an outer **`genericConf`** key.

```json title="custom.conf.json"
{
  "genericConf": {
    "homePageSettings": {
      "appNavBar": {
        "collectionShortTitle": "DoTS Cookbook",
        "appNavBarLogo": { "imgName": "logo-enc-white.png", "href": "/" }
      },
      "pageHeader": {
        "aboutButtonText": "About the Cookbook"
      }
    },
    "footerSettings": {
      "footerTitle": "DoTS Cookbook"
    },
    "namespaces": {
      "dts":    "https://dtsapi.org/v1.0#",
      "dct":    "http://purl.org/dc/terms/",
      "schema": "https://schema.org/"
    }
  },
  "excludeCollectionIds": []
}
```

Use it for what is true of the whole deployment: branding, footer, vocabularies, the default metadata
panel. Anything a single collection wants differently goes in its own file.

## `<collection_id>.conf.json`, one per collection

The file name is matched **case-insensitively** — `ENCPOS_c2` is found by `encpos_c2.conf.json` as
well as by `ENCPOS_c2.conf.json`. What is compared case-sensitively is the `collectionId` value
inside.

No `genericConf` wrapper: the keys sit at the top level, next to `collectionId`.

```json title="encpos.conf.json"
{
  "collectionId": "ENCPOS",
  "mediaTypeEndpoint": "tei",
  "homePageSettings": {
    "pageHeader": {
      "collectionAltTitle": "Publishing a periodical: thesis abstracts (case 1)"
    },
    "listSection": {
      "displayMode": "mixed",
      "cardCollectionPerPage": 5
    }
  },
  "aboutPageSettings": [
    { "tabName": "Segmentation", "compName": "about1" }
  ],
  "collectionCustomCss": false
}
```

### `collectionId` is the pivot

It is **the only mandatory key**, and it is what ties the file to a collection served by the DTS
endpoint. Everything else follows from it:

- the configuration is selected by comparing `collectionId` with the identifier of the collection
  being browsed — **case-sensitively**;
- the same value names the directory holding that collection's components and assets.

A file whose `collectionId` matches nothing is loaded and never used. A file with no `collectionId`
at all is never selected, with the exception of `custom.conf.json`, which has its own loading
process.

### Naming the file

The application finds configuration files by globbing the repository root, and resolves them by file
name **case-insensitively**. The convention in this repository is a lowercase file name and the real
casing in the value:

```
encpos_c2.conf.json     →  "collectionId": "ENCPOS_c2"  →  directory ENCPOS_c2/
```

Follow it: a lowercase file name is easy to type, while the two places that must carry the exact
casing — the value and the directory — are the ones the application compares literally.

## Which file should carry what

| Put it in | When |
|---|---|
| `custom.conf.json` | it is true of the whole deployment: branding, footer, namespaces, the default metadata panel |
| the root collection's file | it is true of the corpus served by the endpoint, rather than of the deployment that presents it |
| a project's file | it gives a first-level collection its identity: title, banner, About tabs, listing mode |
| a deeper collection's file | it refines how the contents of a sub-collection behave: one volume served as HTML, another stylesheet |

The [settings cascade](cascade.md) explains how these files are merged, and
[Settings reference](reference.md) lists what may appear in them.

## Files are JSON, with what that implies

No comments, no trailing commas. A malformed file breaks the build rather than being ignored, which
is the good outcome: the alternative would be a silently unconfigured collection.
