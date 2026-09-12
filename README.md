# CyberChef

[![Deploy](https://github.com/Aryaman73/cyberchef/actions/workflows/deploy.yml/badge.svg)](https://github.com/Aryaman73/cyberchef/actions/workflows/deploy.yml)
[![Build & Test](https://github.com/Aryaman73/cyberchef/actions/workflows/master.yml/badge.svg)](https://github.com/Aryaman73/cyberchef/actions/workflows/master.yml)
[![](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](./LICENSE)

A personal, self-hosted build of [CyberChef](https://github.com/gchq/CyberChef) — GCHQ's
"Cyber Swiss Army Knife" for encryption, encoding, compression and data analysis.

**Live at <https://aryamans.me/cyberchef>**

This README covers only what is specific to this fork. For what CyberChef *is*, how the
recipe/operations model works, the feature list, deep-linking syntax and example recipes,
read the **[upstream README](https://github.com/gchq/CyberChef/blob/master/README.md)**.
The [upstream wiki](https://github.com/gchq/CyberChef/wiki) is still the reference for
writing operations and for the deeper technical write-ups, and this fork's in-app help
links point there.

## Differences from upstream

| | |
| --- | --- |
| **Deployment** | Built and published to GitHub Pages by [`deploy.yml`](.github/workflows/deploy.yml) on every push to `master`. This replaces upstream's `gh-pages` branch deploy, along with the Google Analytics snippet and the `sitemap.xml` that were specific to `gchq.github.io`. |
| **Default theme** | Dark, for visitors with no stored preference. Set in both `src/web/index.js` (`defaultOptions`) and the inline bootstrap in `src/web/html/index.html` — the latter paints the preloader before the bundle loads, so without it the first paint flashes light. An existing stored preference always wins. |
| **Links** | "Raise an issue", the "Fork me on GitHub" banner, the changelog links and the shareable "link to reproduce" point at this repo and this site. Wiki links, the Gitter badge and the Download modal's advice to prefer the official build deliberately still point upstream — this deployment is exactly the kind of unofficial build that warning is about. |
| **Workflows** | `master.yml` is build-and-test only. Upstream's two CLA automation workflows are removed. |

Everything else tracks upstream. The full list of diverging files is:

```
.github/workflows/deploy.yml          (new)
.github/workflows/master.yml
.github/workflows/cla-label.yml       (removed)
.github/workflows/cla-close-stale.yml (removed)
README.md
src/web/index.js
src/web/html/index.html
src/web/App.mjs
src/web/waiters/ControlsWaiter.mjs
src/web/static/structuredData.json
src/web/static/sitemap.mjs
src/core/config/scripts/newMinorVersion.mjs
```

## Deployment

Pushing to `master` runs [`deploy.yml`](.github/workflows/deploy.yml), which runs
`grunt prod` and publishes `build/prod/` to GitHub Pages via `actions/deploy-pages`.
A full run takes about four minutes.

Two things this depends on, both repository settings rather than code:

- **Settings → Pages → Source** must be **GitHub Actions**. With it unset, the build
  succeeds and the deploy step fails with a 404.
- **The site path is the repository name.** `aryamans.me/cyberchef` works because the repo
  is named `cyberchef` and `aryamans.me` is the custom domain on the
  [`aryaman73.github.io`](https://github.com/Aryaman73/aryaman73.github.io) user site;
  project sites inherit it. Renaming the repo moves the site.

The build needs no configuration to live under a sub-path — webpack is set to
`output.publicPath: ""`, so every asset, worker and lazily-loaded module chunk is
referenced relative to the page.

## Local development

**Prerequisites:** Node.js `v24` (see [`.nvmrc`](./.nvmrc); [nvm](https://github.com/nvm-sh/nvm)
will pick it up automatically).

```bash
git clone https://github.com/Aryaman73/cyberchef.git
cd cyberchef
npm install
```

| Command | Description |
| --- | --- |
| `npm start` | Development server with live reload at `http://localhost:8080`. |
| `npm run build` | Production build into `build/prod`. |
| `npm test` | Node.js and operation test suites. |
| `npm run testui` | Browser (UI) tests. |
| `npm run lint` | Check against the linting rules. |
| `npm run newop` | Scaffold a new operation via the interactive quickstart script. |

If a build hits an out-of-memory error, raise Node's heap with `npm run setheapsize`.

There is also a [`Dockerfile`](./Dockerfile) if you would rather not install the toolchain:

```bash
docker build --tag cyberchef --ulimit nofile=10000 .
docker run -it -p 8080:8080 cyberchef
```

## Staying current with upstream

```bash
git remote add upstream https://github.com/gchq/CyberChef.git
git fetch upstream
git merge upstream/master
```

Conflicts will be confined to the diverging files listed above — in practice
`src/web/html/index.html` and `.github/workflows/` are the ones that actually collide.
Merging to `master` triggers a deploy, so run `npm test` and `npm run build` first.

## Security

Vulnerabilities in CyberChef itself should be reported to the upstream maintainers, not
raised as an issue here — [`SECURITY.md`](./SECURITY.md) has the address. This fork only
adds deployment configuration and the small changes listed above, so anything specific to
*this* deployment can go in [the issues](https://github.com/Aryaman73/cyberchef/issues).

## Licence

CyberChef is released under the [Apache 2.0 Licence](https://www.apache.org/licenses/LICENSE-2.0)
and is covered by [Crown Copyright](https://www.nationalarchives.gov.uk/information-management/re-using-public-sector-information/uk-government-licensing-framework/crown-copyright/).
That applies to this fork too — see [`LICENSE`](./LICENSE).
