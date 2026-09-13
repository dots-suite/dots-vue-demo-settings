# dots-vue-demo-settings

Configuration settings repo for [dots-vue](https://github.com/dots-suite/dots-vue) dedicated to the [demo project](https://dots.chartes.psl.eu/vue-demo/).

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE.md)
[![Documentation](https://img.shields.io/badge/docs-GitHub%20Pages-blue)](https://dots-suite.github.io/dots-vue-demo-settings/)

---

## Documentation

This repository doubles as the reference example for building a dots-vue settings repository. The
documentation site (MkDocs Material) lives in [`mkdocs/`](./mkdocs/) and is published to GitHub Pages
by [`.github/workflows/docs.yml`](./.github/workflows/docs.yml) on every push to `dev`.

📖 **https://dots-suite.github.io/dots-vue-demo-settings/**

Everyday commands:

```bash
make docs.install    # install the toolchain (once)
make docs.serve      # live-reload server on http://127.0.0.1:8000
make docs.build      # build in strict mode — exactly what the CI runs
make docs.clean      # remove the generated site
make help            # list every target
```

`make docs.build` runs `mkdocs build --strict`, which **fails** on any broken internal link or any
page missing from the nav. Run it before pushing.

The pinned `mkdocs/requirements.txt` is **generated**, not edited by hand. Edit
`mkdocs/requirements.in`, then `make docs.lock`.

---

## License

dots-vue-demo-settings is distributed under the [MIT License](./LICENSE.md).

## Citation

If you use dots-vue-demo-settings in academic work, please cite it as:

```
@software{boby_dots_vue_demo_settings_2026,
  author       = {Boby, Jean-Victor and Jolivet, Vincent and Pons, Philippe},
  title        = {dots-vue-demo-settings},
  year         = {2026},
  publisher    = {GitHub},
  institution  = {{École nationale des chartes}},
  url          = {https://github.com/dots-suite/dots-vue-demo-settings},
  note         = {Reference dots-vue configuration repository for the DoTS demonstration site}
}
```

You can also use the repository metadata from [CITATION.cff](./CITATION.cff).
