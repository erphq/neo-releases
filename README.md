<div align="center">

<img src="assets/neo-logo.png" width="128" height="128" alt="Neo" />

# Neo

**A local-first agentic AI coding assistant for macOS.**

<br />

<a href="https://github.com/erphq/neo-releases/releases/latest/download/Neo_0.1.0_aarch64.dmg">
  <img src="https://img.shields.io/badge/Download%20for%20macOS-black?style=for-the-badge&logo=apple&logoColor=white&labelColor=black" alt="Download for macOS" height="44" />
</a>

<br /><br />

![macOS](https://img.shields.io/badge/macOS-10.13+-000?logo=apple&logoColor=white)
![Apple Silicon](https://img.shields.io/badge/Apple%20Silicon-M1%2FM2%2FM3%2FM4-000?logo=apple&logoColor=white)
![Notarized](https://img.shields.io/badge/Notarized-Apple%20Developer%20ID-success?logo=apple&logoColor=white)
[![Latest release](https://img.shields.io/github/v/release/erphq/neo-releases?label=latest&color=blue)](https://github.com/erphq/neo-releases/releases/latest)

[**What is Neo**](#what-is-neo) · [**Features**](#features) · [**Install**](#install) · [**Auto-updates**](#auto-updates) · [**Support**](#support--issues)

</div>

<br />

This repository hosts signed + notarized macOS builds of [**Neo**](https://github.com/erphq/neo). Source code lives in the main [erphq/neo](https://github.com/erphq/neo) repo — this one only holds release artifacts.

<br />

## What is Neo

Neo is an AI coding assistant that lives on your desktop. Open a folder, ask it to do something, and it reads your code, writes files, runs commands, and gets the job done — all on your machine.

No browser tab. No cloud sync. No subscription. Just a native Mac app that pairs with your editor.

> *"I know kung fu."* — You, after Neo writes your entire test suite.

<br />

## Features

- 🧠 **Works your whole codebase.** Reads files, searches across the project, edits, runs commands — end to end, not one file at a time.
- 🌐 **Searches the web when it needs to.** Grabs docs, pulls in examples, fetches pages.
- 💾 **Remembers what matters.** Keeps cross-session notes in a markdown file — architecture decisions, gotchas, preferences — so you don't repeat yourself.
- 🛡️ **Asks before risky stuff.** Anything that touches your disk or runs a command gets an inline approval prompt. Read-only actions just run.
- 🧩 **Extends with plugins.** Connect databases, GitHub, your own services — Neo auto-discovers and uses them.
- 🧭 **Thinks before it acts.** Big tasks start with a plan you can approve before anything runs.
- 🎨 **Looks how you want.** 14 themes out of the box (Dracula, Nord, Tokyo Night, Catppuccin, and more), custom fonts, light/dark auto-switch.
- 🚀 **Updates itself.** New versions install with one click from Settings. No re-downloading.
- 🍎 **Native Mac app.** Tiny download, no Electron bloat, proper traffic lights, Finder and Terminal shortcuts, one-click open in VS Code / Cursor / Zed / JetBrains / Sublime.

<br />

## Install

1. Click the download button above (or pick from [Releases](https://github.com/erphq/neo-releases/releases))
2. Open the `.dmg`, drag **Neo** into **Applications**
3. Launch from `/Applications/Neo.app`

No Gatekeeper prompt. No right-click → Open workaround. It just opens.

**First-run setup:** open Settings → paste an [OpenRouter](https://openrouter.ai) API key → open a folder → start building.

<details>
<summary>Why there's no Gatekeeper warning</summary>

The app is:
- **Code-signed** with a Developer ID Application certificate (Deskera Holdings Ltd., team `DMZC7LUBK8`)
- **Notarized** by Apple's notary service — both the `.app` bundle and the `.dmg` container
- **Stapled** — Gatekeeper verifies the notarization ticket offline, no internet needed on first launch

Verify the download yourself:

```bash
spctl --assess --type open --context context:primary-signature -vv ~/Downloads/Neo_*_aarch64.dmg
# → accepted
# → source=Notarized Developer ID
# → origin=Developer ID Application: Deskera Holdings Ltd. (DMZC7LUBK8)

xcrun stapler validate ~/Downloads/Neo_*_aarch64.dmg
# → The validate action worked!
```

</details>

<br />

## Auto-updates

Neo checks this repository silently on launch. When a new version is published, a green dot appears on the Settings icon in the titlebar.

To update, open **Settings → About → Download and install**. The app downloads, verifies the signature, installs, and relaunches itself.

You can also trigger a check manually from **Settings → About → Check for Updates**.

Update artifacts are signed with a dedicated ed25519 key (minisign). The app embeds the matching public key at build time — any tarball that doesn't verify is rejected before install.

<br />

## What's in a release

Every tag (`vX.Y.Z`) ships:

| Asset | Purpose |
| --- | --- |
| `Neo_X.Y.Z_aarch64.dmg` | macOS installer (what humans download) |
| `Neo_X.Y.Z_aarch64.app.tar.gz` | In-app auto-update payload |
| `Neo_X.Y.Z_aarch64.app.tar.gz.sig` | Minisign signature for the payload |
| `latest.json` | Update manifest consumed by the Tauri updater |

Current builds target **Apple Silicon only**. Intel builds can be added if there's demand — open an issue upstream.

<br />

## Support & issues

Bug reports, feature requests, and discussion: [erphq/neo/issues](https://github.com/erphq/neo/issues).

---

<div align="center">
<sub>Built with <a href="https://tauri.app">Tauri</a>. Signed by Apple. Distributed by humans who prefer not to dance around Gatekeeper.</sub>
</div>
