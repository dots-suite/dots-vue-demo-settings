# Styling and assets

Beyond text, a settings repository carries two kinds of file: a **stylesheet** shaping how the
documents of a collection are rendered, and **images** — banners, logos, thumbnails.

## Collection stylesheets

### Declaring one

`collectionCustomCss` names the stylesheet, without its `.customCss.css` suffix:

```json
"collectionCustomCss": "dots_cookbook"
```

By convention the value is a **collection id**, and names both the directory and the file:

```
dots_cookbook/
└── assets/
    └── css/
        └── dots_cookbook.customCss.css
```

!!! warning "Matched case-sensitively, and silently"
    The value is compared to the **file name** — `ENCPOS` finds `ENCPOS.customCss.css`, never
    `encpos.customCss.css`. A mismatch loads no stylesheet at all and raises no warning, so a
    collection whose documents suddenly look unstyled is worth checking here first.

Set it to `false` to state explicitly that a collection uses no stylesheet — that is what
`encpos.conf.json` does, so that ENCPOS documents keep the TEI Consortium rendering.

### How it is applied

The stylesheet is injected as a `<style>` element **appended at the end of `<head>`**, which is what
gives it the last word over the application's own rules. It is removed when the reader leaves the
collection, so two collections never bleed into each other.

!!! tip "A stylesheet can be borrowed"
    `collectionCustomCss` points at a file name, not at the current collection: several collections
    may name the same stylesheet. In this repository `encpos_1971.conf.json` borrows
    `dots_cookbook`'s.

### What to put in it

TEI and HTML documents are rendered from the publisher's markup, so the stylesheet is where you
decide what a `<speaker>`, a `<stage>` or a heading looks like.

The stylesheets of this repository follow a template in **three parts**, and we recommend keeping
it: a reader opening any of them knows at once where to look.

```css
/*=============================================================
 PART 1
 Updating the css color schema
 =============================================================*/
:root {
  --fill-color: #8f0e21;
}

/*=============================================================
 PART 2
 Updating the css of specific elements (HTML)
 =============================================================*/
.about > h1 {
  font-style: italic !important;
}

/*=============================================================
 PART 3
 Updating the css of the DTS API tei response
 =============================================================*/
.document-views > * {
  .row:not(.remove-bottom-padding):not(.bottom-toc) {
    & tei > div {
      border: 1px solid #d8d0be;
      background: url('../images/piece_fond.jpg') center top #fffbf5;
    }
  }
}
```

**Part 1 recolours the application** by overriding its CSS variables — `--fill-color` and its
companions — rather than restating colours rule by rule. One declaration changes buttons, active
states and accents at once.

**Part 2 adjusts the application's own elements** for this collection: a heading of the About page,
an icon, a button.

**Part 3 styles the documents themselves**, that is the markup returned by the DTS endpoint. Nesting
the rules inside `.document-views > * { .row:not(.remove-bottom-padding):not(.bottom-toc) { … } }`
confines them to the reading area, and the TEI elements are addressed from there. Note the relative
`url('../images/…')`: paths are resolved from the stylesheet, so a background image sits naturally in
the collection's own `assets/images/`.

!!! warning "A custom stylesheet is global once injected"
    The three parts are a convention, not a sandbox: nothing prevents a rule from reaching the whole
    interface. Keep document rules inside the Part 3 wrapper rather than writing a bare
    `.title { … }`, which would restyle every title of the application.

## Images

### Where they go

```
<collectionId>/
└── assets/
    └── images/
        ├── banner.jpg
        └── logo-enc-white.png
```

Any extension works. The settings below name a file in that directory:

| Setting | What it shows |
|---|---|
| `homePageSettings.pageHeader.collectionBannerImg` | the banner at the top of the collection |
| `homePageSettings.listSection.logo` | the thumbnail of the collection in listings and cards |
| `homePageSettings.appNavBar.appNavBarLogo.imgName` | the left-hand logo of the navigation bar |
| `homePageSettings.appNavBar.appNavBarApiLogo.imgName` | a second logo, on the right of the application navBar (your API endpoint) |

### How a file is found

Each of these follows the same descent, stopping at the first hit:

1. the **current collection**'s `assets/images/`;
2. the **root collection**'s, so a sub-collection inherits its parent's branding;
3. a **`default/assets/images/`** directory at the root of your repository, if you create one;
4. the application's own images.

Two shortcuts apply to logos and banners: a value containing `https` is used **as an external URL**
rather than looked up, and an **empty string** is an explicit opt-out — no image at all, as
`dots_cookbook.conf.json` does for its thumbnail.

### Metadata logos

`metadataLogosMapping` associates a URL fragment with a logo shown next to a metadata value — the
Wikidata mark beside an author link, for instance. The application ships a set of them; dropping a
file named `logo_<name>.svg` or `logo_<name>.png` into a collection's `assets/images/` **overrides**
the application's version for that name.

## The favicon

`homePageSettings.favicon` behaves unlike everything else, in two ways:

- it is read **only from `custom.conf.json`** — declaring it in a collection file has no effect;
- the file is looked up **only in a `default/` directory** at the root of your repository:

```
default/
└── my-corpus.ico
```

This repository has no such directory, so the browser falls back on the application's own favicon.
