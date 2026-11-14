# Nexo catalog

The official, public catalog of [egdev](https://github.com/egdev6) desktop apps for **Nexo**, the hub that discovers, opens and updates them from a single tray icon.

Nexo downloads this catalog on startup, validates it and caches it. Adding an app, a new version or a news item here reaches every Nexo install **without releasing a new version of Nexo**.

## Endpoints

| File | URL | Readers |
| --- | --- | --- |
| `v2/catalog.json` | `https://egdev6.github.io/nexo-catalog/v2/catalog.json` | Current Nexo (preferred) |
| `catalog.json` | `https://egdev6.github.io/nexo-catalog/catalog.json` | Schema v1 fallback |

Both files describe the same apps. v2 adds an optional Windows detection hint per app; v1 must never contain v2-only fields. Never replace the v1 file with v2 content.

## Rules

- **Allowlist:** only apps listed here appear in Nexo as official apps. Everything in this repository is public: never add private repositories, private links, tokens or unreleased material.
- **Format:** `catalog.schema.json` is the structural schema. Nexo also applies domain validation (unique lowercase `[a-z0-9-]` ids, HTTPS URLs without credentials, SemVer versions, RFC 3339 timestamps with an explicit zone, nonblank text). Unknown fields are rejected, so a new field needs a new schema version and a Nexo release.
- **Detection hints (v2):** `displayName` and `publisher` must be the exact values written by the app's NSIS installer in `HKCU\Software\Microsoft\Windows\CurrentVersion\Uninstall`, and `executableRelativePath` is relative to its `InstallLocation`. Only add a hint after confirming those values on a real install; without a hint Nexo shows the installation state as unknown.
- **News:** each app's `news` entries feed the Novedades popup. Nexo shows unseen entries from the last 30 days, latest first.
- **Icons:** card icons live in `icons/` and are served from GitHub Pages.

## Adding or updating an app

1. Edit `v2/catalog.json` and mirror the change in `catalog.json` (without `detection`).
2. Check that every URL is public: installer, `latest.json`, links and icon.
3. Open a pull request; the `Validate catalog` workflow checks both files against the schema.
4. After merging, GitHub Pages publishes the change within minutes and Nexo picks it up on its next start.
