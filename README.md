# TramLLM-Code-Build

Public CI for [TramLLM-Code](https://github.com/HDQuanDev/TramLLM-Code) (private).
GitHub Actions is free for public repositories on standard runners, so the
Linux and Windows release builds run here instead of spending the private
repo's Pro minutes. No source code lives in this repo — workflows only.

## How it works

1. A `v*` tag is pushed to the private repo.
2. `.github/workflows/dispatch-release.yml` there calls this repo's
   `release.yml` with the tag (one 1-minute job).
3. `release.yml` verifies the tag (same `ci.yml` as the private repo),
   builds Linux (AppImage/deb/rpm) and Windows (NSIS) installers, and
   uploads them to the draft release in the private repo.
4. The maintainer machine adds the signed macOS packages and promotes
   stable, as before.

## Secrets

`PRIVATE_REPO_TOKEN` — a fine-grained personal access token scoped to
`HDQuanDev/TramLLM-Code` (Contents: read and write) and to this repo
(Actions: read and write). It is used to check out the private repo and
to create/upload the draft release. Rotate it before it expires.
