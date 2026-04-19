# Neo Releases

Signed, notarized macOS builds of **[Neo](https://github.com/erphq/neo)** — a local-first AI coding assistant.

This repository contains only release artifacts (DMG installers, updater tarballs, manifests). Source lives in [erphq/neo](https://github.com/erphq/neo).

## Download

[**→ Latest release**](https://github.com/erphq/neo-releases/releases/latest)

> Current builds target **Apple Silicon (M1/M2/M3/M4)** on macOS 10.13+.
> Intel Mac builds aren't shipped yet — ping us if you need one.

## Install

1. Download the `.dmg`
2. Open it — drag `Neo` into `Applications`
3. Launch from `/Applications/Neo.app`

That's it. No Gatekeeper warning, no right-click → Open dance. The app is:

- **Code-signed** with a Developer ID Application certificate (Deskera Holdings Ltd., team `DMZC7LUBK8`)
- **Notarized** by Apple's notary service (both the `.app` and the `.dmg`)
- **Stapled** — Gatekeeper can verify the ticket offline on first launch

Verify for yourself:

```bash
spctl --assess --type open --context context:primary-signature -vv ~/Downloads/Neo_*_aarch64.dmg
# → accepted
# → source=Notarized Developer ID
# → origin=Developer ID Application: Deskera Holdings Ltd. (DMZC7LUBK8)

xcrun stapler validate ~/Downloads/Neo_*_aarch64.dmg
# → The validate action worked!
```

## Auto-updates

Installed Neo silently checks this repository for new versions on launch. When a new release is cut, the app shows a dot on the titlebar Settings icon. **Settings → About → Download and install** handles the rest — download, install, relaunch.

Manual check is also available at **Settings → About → Check for Updates**.

Update manifest (served publicly):

```
https://github.com/erphq/neo-releases/releases/latest/download/latest.json
```

Updater tarballs are signed with a separate ed25519 key (minisign). The app embeds the public key at build time — update artifacts that don't verify are rejected.

## Release layout

Each tagged release (`vX.Y.Z`) contains:

| Asset | Purpose |
| --- | --- |
| `Neo_X.Y.Z_aarch64.dmg` | User-facing installer |
| `Neo_X.Y.Z_aarch64.app.tar.gz` | In-app auto-update payload |
| `Neo_X.Y.Z_aarch64.app.tar.gz.sig` | Minisign signature for the payload |
| `latest.json` | Tauri updater manifest consumed by the app |

## Issues

Bug reports, feature requests, and discussion live in the source repo: [erphq/neo/issues](https://github.com/erphq/neo/issues).
