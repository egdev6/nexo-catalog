# Nexo catalog

The official, public catalog of [egdev](https://github.com/egdev6) desktop apps for **Nexo**, the hub that discovers, opens and updates them from a single tray icon.

Nexo downloads this catalog on startup, validates it and caches it. Adding an app, a new version or a news item here reaches every Nexo install **without releasing a new version of Nexo**.

## Endpoints

| File | URL | Readers |
| --- | --- | --- |
| `v4/catalog.json` | `https://egdev6.github.io/nexo-catalog/v4/catalog.json` | Nexo 0.2.0 and later (per-platform entries) |
| `v3/catalog.json` | `https://egdev6.github.io/nexo-catalog/v3/catalog.json` | Nexo 0.1.x (enables installs; Windows only) |
| `v2/catalog.json` | `https://egdev6.github.io/nexo-catalog/v2/catalog.json` | Nexo before v3 |
| `catalog.json` | `https://egdev6.github.io/nexo-catalog/catalog.json` | Schema v1 fallback |

All files describe the same apps. v2 adds an optional Windows detection hint per app; v3 adds the app's updater public key and restricts installer and `latest.json` URLs to `egdev6` GitHub release assets; v4 moves the installer, `latest.json`, version, release date, key and detection hint into per-platform entries. Older files must never contain newer fields. Never replace an older file with newer content.

## Rules

- **Allowlist:** only apps listed here appear in Nexo as official apps. Everything in this repository is public: never add private repositories, private links, tokens or unreleased material.
- **Format:** `catalog.schema.json` is the structural schema. Nexo also applies domain validation (unique lowercase `[a-z0-9-]` ids, HTTPS URLs without credentials, SemVer versions, RFC 3339 timestamps with an explicit zone, nonblank text). Unknown fields are rejected, so a new field needs a new schema version and a Nexo release.
- **Detection hints (v2):** `displayName` and `publisher` must be the exact values written by the app's NSIS installer in `HKCU\Software\Microsoft\Windows\CurrentVersion\Uninstall`, and `executableRelativePath` is relative to its `InstallLocation`. Only add a hint after confirming those values on a real install; without a hint Nexo shows the installation state as unknown.
- **Updater key (v3):** `updaterPublicKey` is copied exactly from the app's `src-tauri/tauri.conf.json` `plugins.updater.pubkey` at the released tag. A keyed app asserts that `installerUrl` is a per-user Tauri NSIS `_x64-setup.exe` (`bundle.windows.nsis.installMode: "currentUser"`) with a `.sig` signed by that key. Nexo verifies the signature before running anything; a wrong key makes installs fail closed. `installerUrl` and `latestJsonUrl` must be `https://github.com/egdev6/<repo>/releases/download/<tag>/<asset>`.
- **Platforms (v4):** `platforms` maps Tauri updater targets (`windows-x86_64`, `darwin-aarch64`, `darwin-x86_64`, `linux-x86_64`, …) to that platform's `installerUrl`, `latestJsonUrl`, `version`, `releasedAt`, optional `updaterPublicKey` and optional `detection` (Windows keys only, same fields as the v2 hint). Nexo shows an app, its news and its updates only when it has an entry for the platform Nexo runs on. v3 and older files only describe the `windows-x86_64` build.
- **News:** each app's `news` entries feed the Novedades popup. Nexo shows unseen entries from the last 30 days, latest first.
- **Icons:** card icons live in `icons/` and are served from GitHub Pages.
- **Banners:** each app may have `banners/<id>.webp` (the app's GitHub README banner, 1280×320 WebP, 4:1). Nexo loads it by id from `https://egdev6.github.io/nexo-catalog/banners/<id>.webp` for the catalog card and shows a placeholder when it is missing; no catalog field is involved.

## Adding or updating an app

1. Edit `v4/catalog.json`, then mirror the app's `windows-x86_64` entry in `v3/catalog.json` (flat fields), `v2/catalog.json` (without `updaterPublicKey`, keeping v2 URLs) and `catalog.json` (also without `detection`). Apps without a `windows-x86_64` entry appear only in v4.
2. Check that every URL is public: installer, `.sig`, `latest.json`, links and icon; confirm the installer's signature verifies with the key.
3. Open a pull request; the `Validate catalog` workflow checks all files against the schema.
4. After merging, GitHub Pages publishes the change within minutes and Nexo picks it up on its next start.
