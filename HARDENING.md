<!-- markdownlint-disable -->

# Hardening Report: TheMrMilchmann--setup-msvc-dev/v2.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **TheMrMilchmann--setup-msvc-dev/v2.0.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files are pinned to mutable version tags rather than full 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved.

In `.github/workflows/ci.yml`:
- `actions/checkout@v4` (line 22)
- `actions/setup-node@v3` (line 24)
- `actions/upload-artifact@v3` (line 30)
- `actions/download-artifact@v3` (line 51, line 72)

In `.github/workflows/release.yml`:
- `actions/checkout@v4` (line 7, line 22, line 27)
- `actions/setup-node@v3` (line 9)

Only `GeekyEggo/delete-artifact@54ab544f12cdb7b71613a16a2b5a37a9ade990af` is correctly pinned to a SHA.

Locations:

- `.github/workflows/ci.yml:22`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:30`
- `.github/workflows/ci.yml:51`
- `.github/workflows/ci.yml:72`
- `.github/workflows/release.yml:7`
- `.github/workflows/release.yml:9`
- `.github/workflows/release.yml:22`
- `.github/workflows/release.yml:27`

### missing-permissions (severity: medium)

Neither `.github/workflows/ci.yml` nor `.github/workflows/release.yml` declares a top-level `permissions:` block, and no individual job within either file declares its own `permissions:` block. Without explicit permissions, workflows run with the repository's default token permissions (which may be `write-all` depending on org/repo settings), granting unnecessarily broad access. Each workflow should declare minimal required permissions (e.g. `permissions: read-all` at the top level, with specific write scopes added only where needed).

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/release.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references in both workflow files by replacing mutable version tags with full 40-character commit SHAs (preserving the tag as a comment). Added top-level permissions blocks: ci.yml gets `contents: read` (sufficient for checkout and artifact operations), release.yml gets `contents: write` (required for pushing commits and tags). The already-pinned GeekyEggo/delete-artifact reference was left unchanged.

