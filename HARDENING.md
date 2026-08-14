<!-- markdownlint-disable -->

# Hardening Report: TheMrMilchmann--setup-msvc-dev/v3.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **TheMrMilchmann--setup-msvc-dev/v3.0.1** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in ci.yml are pinned to mutable version tags instead of full 40-character commit SHAs, making the workflow vulnerable to supply-chain attacks if the upstream action tag is moved. Unpinned references: `actions/checkout@v4` (line 24), `actions/setup-node@v4` (line 26), `actions/upload-artifact@v4` (line 36), `actions/download-artifact@v4` (lines 46, 70). Note: `GeekyEggo/delete-artifact` is correctly SHA-pinned.

Locations:

- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:26`
- `.github/workflows/ci.yml:36`
- `.github/workflows/ci.yml:46`
- `.github/workflows/ci.yml:70`

### unpinned-uses (severity: high)

Multiple `uses:` references in publish.yml are pinned to mutable version tags instead of full 40-character commit SHAs. Unpinned references: `actions/checkout@v4` (lines 18, 89, 95), `actions/setup-node@v4` (lines 21, 85), `actions/upload-artifact@v4` (line 31), `actions/download-artifact@v4` (lines 41, 65, 80). Note: `GeekyEggo/delete-artifact` is correctly SHA-pinned.

Locations:

- `.github/workflows/publish.yml:18`
- `.github/workflows/publish.yml:21`
- `.github/workflows/publish.yml:31`
- `.github/workflows/publish.yml:41`
- `.github/workflows/publish.yml:65`
- `.github/workflows/publish.yml:80`
- `.github/workflows/publish.yml:85`
- `.github/workflows/publish.yml:89`
- `.github/workflows/publish.yml:95`

### script-injection (severity: high)

Rule (a) violation: `${{ env.WORKSPACE_ARCHIVE }}` is interpolated directly inside `run:` shell command strings. Any `${{ ... }}` expression inside a run: block is a script-injection risk because the value is substituted by the template engine before the shell ever sees it, bypassing shell quoting. Offending lines: `run: tar -cvf ${{ env.WORKSPACE_ARCHIVE }} .` and `tar -xvf ${{ env.WORKSPACE_ARCHIVE }}` / `rm ${{ env.WORKSPACE_ARCHIVE }}` in the Untar steps. Fix: set the value as an env var and reference it as `"$WORKSPACE_ARCHIVE"` in the shell.

Locations:

- `.github/workflows/ci.yml:33`
- `.github/workflows/ci.yml:52`
- `.github/workflows/ci.yml:53`
- `.github/workflows/ci.yml:76`
- `.github/workflows/ci.yml:77`

### script-injection (severity: high)

Rule (a) violation: `${{ env.WORKSPACE_ARCHIVE }}` is interpolated directly inside `run:` shell command strings in publish.yml. Offending lines: `run: tar -cvf ${{ env.WORKSPACE_ARCHIVE }} .` and `tar -xvf ${{ env.WORKSPACE_ARCHIVE }}` / `rm ${{ env.WORKSPACE_ARCHIVE }}` in multiple Untar steps. Fix: reference the value via an env var (`"$WORKSPACE_ARCHIVE"`) instead of using the expression directly in the shell command.

Locations:

- `.github/workflows/publish.yml:28`
- `.github/workflows/publish.yml:46`
- `.github/workflows/publish.yml:47`
- `.github/workflows/publish.yml:70`
- `.github/workflows/publish.yml:71`
- `.github/workflows/publish.yml:84`
- `.github/workflows/publish.yml:85`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` block and none of its jobs (build, test-vcvarsall-invocation, test-path-exports, cleanup) define job-level `permissions:`. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g., write access to contents). A top-level `permissions: {}` or minimal per-job permissions should be added.

Locations:

- `.github/workflows/ci.yml:1`

### missing-permissions (severity: medium)

publish.yml has no top-level `permissions:` block. While the `publish` job correctly declares `permissions: contents: write`, the remaining jobs (build, test-vcvarsall-invocation, test-path-exports, cleanup) have no `permissions:` key and therefore inherit the repository's default token permissions. A top-level `permissions: {}` with per-job overrides, or explicit permissions on every job, should be used.

Locations:

- `.github/workflows/publish.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed both .github/workflows/ci.yml and .github/workflows/publish.yml:

1. unpinned-uses: Pinned all mutable tag references to full 40-char SHAs with tag comments:
   - actions/checkout@v4 → @34e114876b0b11c390a56381ad16ebd13914f8d5 # v4
   - actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020 # v4
   - actions/upload-artifact@v4 → @ea165f8d65b6e75b540449e92b4886f43607fa02 # v4
   - actions/download-artifact@v4 → @d3f86a106a0bac45b974a628896c90dbdf5c8093 # v4
   GeekyEggo/delete-artifact was already SHA-pinned and left unchanged.

2. script-injection: Moved all `${{ env.WORKSPACE_ARCHIVE }}` expressions from run: shell strings into step-level env: blocks, referencing them as "$WORKSPACE_ARCHIVE" in the shell. Applied to all Tar and Untar steps in both files.

3. missing-permissions: Added `permissions: {}` top-level block to both ci.yml and publish.yml. The publish job in publish.yml retains its existing `permissions: contents: write` job-level override.

