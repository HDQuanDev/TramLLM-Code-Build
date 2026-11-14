# TramLLM-Code-Build

Public build and release pipeline for [TramLLM Code](https://tramllm.com).
The app is public, so its installers are built and published here; the
source code lives in the private repo
[TramLLM-Code](https://github.com/HDQuanDev/TramLLM-Code) and is not
public. No source code lives in this repo — workflows only.

## How it works

1. A `v*` tag is pushed to the private repo.
2. `.github/workflows/dispatch-release.yml` there calls this repo's
   `release.yml` with the tag.
3. `release.yml` verifies the tag (same `ci.yml` as the private repo),
   builds Linux (AppImage/deb/rpm) and Windows (NSIS) installers, and
   uploads them to the draft release in this public repo — the app is
   public, only its source code stays private.
4. The macOS build runs on a self-hosted runner on the maintainer's Mac
   and uploads to the same draft (`draft` job attaches release notes from
   `ui/CHANGELOG.md`).
5. The `publish` job runs on the self-hosted Mac runner via
   `node scripts/desktop-release.mjs sync <tag>`: verifies checksums and macOS
   signatures, signs updater artifacts with `~/.tauri/tramllm-code.key`, uploads
   `latest.json`, and promotes the release to `latest`.

## Secrets

- `PRIVATE_REPO_TOKEN` — a fine-grained personal access token scoped to
  `HDQuanDev/TramLLM-Code` (Contents: read and write) and to this repo
  (Actions: read and write, Contents: write). It is used to check out the
  private repo and to create/upload/publish the release in this repo. Rotate it
  before it expires.
- `TAURI_SIGNING_PRIVATE_KEY_PASSWORD` *(optional, if `~/.tauri/tramllm-code.key`
  has a passphrase)* — password used by the self-hosted Mac `publish` job to
  unlock the updater signing key when generating `latest.json`.
