# Repository layout

A settings repository has two kinds of entry at its root: **configuration files** and **collection
directories**. Here is the demo repository, trimmed to what matters:

```
dots-vue-demo-settings/
├── custom.conf.json              default settings for this deployment
├── dots_cookbook.conf.json       the root collection of the DTS endpoint
│
├── encpos.conf.json              ┐
├── encpos_c2.conf.json           ├ the first-level collections, or projects
├── theater.conf.json             ┘
│
├── encpos_1849.conf.json         ┐
├── encpos_1971.conf.json         │
├── encpos_1972.conf.json         ├ deeper collections, inside a project
├── comedie.conf.json             │
├── moliere.conf.json             │
├── racine.conf.json              ┘
│
├── ENCPOS/                       and, when a collection needs components, a directory
│   ├── HomePageContent.vue
│   └── about1.vue … about5.vue
├── ENCPOS_1849/
│   └── assets/images/theses_1849.jpg
├── ENCPOS_c2/
├── dots_cookbook/
│   ├── HomePageContent.vue
│   ├── about1.vue … about4.vue
│   └── assets/
│       ├── css/dots_cookbook.customCss.css
│       └── images/
└── theater/
```

## Two naming rules

**Configuration file names are matched case-insensitively**, so the convention here is to write them
in lowercase: `ENCPOS_c2` is configured by `encpos_c2.conf.json`. What matters inside the file is the
`collectionId` value, which *is* compared case-sensitively.

**Directories keep the exact case of the collection id**: the components of `ENCPOS_c2` live in
`ENCPOS_c2/`, not `encpos_c2/`. The directory name is used to build an import path at runtime, so a
mismatch means the component is not found.

!!! warning "Case matters on the server, not always on your machine"
    macOS and Windows file systems are usually case-insensitive: a wrongly-cased directory works
    locally and breaks once deployed on Linux. Copy the id from the DTS endpoint rather than
    retyping it.

## A file and a directory are independent

Neither implies the other, and both are optional:

| | What it means |
|---|---|
| **A file, no directory** | The collection is configured but brings no component of its own. `comedie.conf.json`, `moliere.conf.json` and `racine.conf.json` are in this case: they adjust settings and inherit everything else. |
| **A directory, no file** | Possible, but the components are unreachable: nothing declares them. In practice every directory has a matching configuration file. |
| **A directory with assets only** | `ENCPOS_1849/` holds a single banner image, no Vue component. Perfectly valid. |

## What goes inside a collection directory

| Path | Role | Declared by |
|---|---|---|
| `about1.vue`, `about2.vue`, … | the tabs of the About page | `aboutPageSettings[].compName` |
| `HomePageContent.vue` | a free block on the collection home page | `homePageSettings.descriptionSection.customCollectionDescription.compName` |
| `assets/css/<collection_id>.customCss.css` | a stylesheet for the documents of the collection — the file name is matched **case-sensitively** | `collectionCustomCss` |
| `assets/images/` | banners, logos and thumbnails — any extension | the five settings below |

Every setting that names an image in that directory:

| Setting | What it shows |
|---|---|
| `homePageSettings.pageHeader.collectionBannerImg` | the banner at the top of the collection |
| `homePageSettings.listSection.logo` | the thumbnail of the collection in listings and cards |
| `homePageSettings.appNavBar.appNavBarLogo.imgName` | the left-hand logo of the navigation bar |
| `homePageSettings.appNavBar.appNavBarApiLogo.imgName` | a second logo, on the right of the application navBar (your API endpoint) |
| `metadataLogosMapping[].name` | a logo beside a metadata value, looked up as `logo_<name>.svg` or `.png` |

!!! note "The favicon is the exception"
    `homePageSettings.favicon` does **not** read from a collection directory: it is looked up in a
    `default/` directory at the root of the repository, and only from `custom.conf.json`. See
    [Styling and assets](styling.md).

Each of these is covered in [Collection pages](pages.md) and [Styling and assets](styling.md).

## Files that are not settings

A settings repository is a normal Git repository: a `README.md`, a `LICENSE.md` and a `CITATION.cff`
are welcome and ignored by the application. The `mkdocs/` directory of this very repository — the
documentation you are reading — is another example.
