# Creating a settings repository

A settings repository is plugged into dots-vue **at build time**, through an environment variable
holding a path relative to the application. Nothing is fetched over the network, and nothing is
installed: the application compiles your files together with its own.

## 1. Put the two repositories side by side

```bash
cd path/to/projects_folder
git clone https://github.com/dots-suite/dots-vue.git
git clone https://github.com/<your-org>/dots-vue-<yourname>-settings.git
```

```
projects_folder/
├── dots-vue/                    the application
└── dots-vue-myname-settings/    your repository
```

Any layout works as long as you can express one as a path relative to the other; siblings are the
simplest.

## 2. Declare the path

dots-vue reads the environment variable **`CUSTOM_SETTINGS_PATH`**, a path relative to the
application directory:

```bash
cd dots-vue
yarn
CUSTOM_SETTINGS_PATH=../dots-vue-myname-settings yarn serve
```

Without it, the application starts on its own defaults alone — a working but unconfigured front-end,
useful to see what the defaults look like.

The same variable applies to the production and staging builds:

```bash
CUSTOM_SETTINGS_PATH=../dots-vue-myname-settings yarn build:prod
```

## 3. Start with the deployment-wide file

Create `custom.conf.json` at the root of your repository. It carries what applies to the whole
deployment, before any collection speaks:

```json
{
  "genericConf": {
    "homePageSettings": {
      "appNavBar": {
        "collectionShortTitle": "My corpus"
      },
      "pageHeader": {
        "aboutButtonText": "About this corpus"
      }
    },
    "footerSettings": {
      "footerTitle": "My corpus"
    }
  }
}
```

!!! note "`custom.conf.json` has an outer `genericConf` key"
    It is merged over the application defaults, which use that same shape. The per-collection files
    described below do **not** have it — see [Configuration files](configuration.md).

Reload: the navigation bar and the footer already carry your wording.

## 4. Add a collection

Take a collection id from your DTS endpoint — for instance from
`https://dots.chartes.psl.eu/demo/api/dts/collection` — and create a file named after it, in
lowercase:

```json title="encpos.conf.json"
{
  "collectionId": "ENCPOS",
  "mediaTypeEndpoint": "tei",
  "homePageSettings": {
    "listSection": {
      "displayMode": "mixed"
    }
  }
}
```

`collectionId` is the only mandatory key: it is what ties the file to the collection served by the
endpoint, and **it must match the endpoint exactly, case included**.

## 5. Add components, if the collection needs any

Create a directory named exactly after the collection id, and put an About page in it:

```
ENCPOS/
└── about1.vue
```

then declare it:

```json
"aboutPageSettings": [
  { "tabName": "About this recipe", "compName": "about1" }
]
```

[Collection pages](pages.md) describes what such a component looks like.

## 6. Commit, then deploy

Your repository holds text and images only: no build, no dependency, nothing to publish to a
registry. Deployment consists in making it available next to dots-vue on the build machine — a clone,
a submodule or a CI checkout, as your infrastructure prefers.

## Next

Read [Configuration files](configuration.md) to see what each file may contain, then
[The settings cascade](cascade.md) to understand which value wins when several files speak about the
same thing.
