# AIOStreams Seanime extensions

[Seanime](https://seanime.app) extensions for [AIOStreams](https://github.com/Viren070/AIOStreams), published to npm as `@aiostreams/seanime-extensions`:

| Extension | Folder | What it does |
| --- | --- | --- |
| AIOStreams | `src/extensions/aiostreams-plugin` | A plugin that lists AIOStreams' streams on an anime's page and plays them, with auto next episode |
| AIOStreams | `src/extensions/aiostreams-torrent-provider` | An anime torrent provider that searches through AIOStreams |
| Stremio Catalog | `src/extensions/stremio-custom-source` | A custom source that browses a Stremio addon's catalog |

Install them from an AIOStreams server: its configuration page links each one at `/seanime/<uuid>/<password>/extensions/<id>.json`, already set up for that config. The package itself isn't meant to be installed by hand.

## How a server uses the package

The build writes `dist/<id>/manifest.json`, each with its code inlined as `payload`. An AIOStreams server depends on the package, reads those manifests and fills in what depends on the server and the user before serving them:

- `manifestURI`, the URL it serves the manifest at, which Seanime checks for updates, and `website`
- `plugin.permissions.allow.networkAccess.allowedDomains`, the server's hostname
- the default of the `manifestUrl` field in `userConfig`, the user's Stremio manifest URL, setting `requiresConfig` to false
- for the custom source, `{{catalogId}}`, `{{catalogType}}` and `{{manifestUrl}}` in the payload, and its `id`, `name` and `icon`

So renaming or removing any of these is a breaking change for the server.

## Developing

```sh
pnpm install
pnpm typecheck
pnpm build
```

Seanime runs a plugin's `$ui.register` and `$app.on…` callbacks from their source text in fresh runtimes, without the bundle's top-level scope, so the build copies the helpers from `src/lib` into each of those callbacks.

## Releasing

[release-please](https://github.com/googleapis/release-please) keeps a release PR open from the conventional commits on `main`, bumping the package and each manifest's `version`. Merging it tags the release and publishes the package to npm with trusted publishing.

## Licence

AGPL-3.0-only, as in `LICENSE`.
