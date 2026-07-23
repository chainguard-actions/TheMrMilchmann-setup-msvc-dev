<!-- markdownlint-disable -->

# Hardening Report: TheMrMilchmann--setup-msvc-dev/v4.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **TheMrMilchmann--setup-msvc-dev/v4.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): `${{ env.WORKSPACE_ARCHIVE }}` is interpolated directly inside `run:` shell command strings in multiple steps. Any `${{ ... }}` expression inside a `run:` block is a script-injection risk because the value is substituted by the GitHub Actions template engine before the shell ever sees it, bypassing shell quoting. Offending lines include:
- Line 36: `run: tar --exclude='.git' --exclude=${{ env.WORKSPACE_ARCHIVE }} -cvf ${{ env.WORKSPACE_ARCHIVE }} .`
- Line 56: `tar -xvf ${{ env.WORKSPACE_ARCHIVE }}`
- Line 57: `rm ${{ env.WORKSPACE_ARCHIVE }}`
- Line 80: `tar -xvf ${{ env.WORKSPACE_ARCHIVE }}`
- Line 81: `rm ${{ env.WORKSPACE_ARCHIVE }}`
Fix: replace `${{ env.WORKSPACE_ARCHIVE }}` with the plain environment variable `$WORKSPACE_ARCHIVE` (set via the workflow `env:` block), which is already available as a shell variable without template interpolation.

Locations:

- `.github/workflows/ci.yaml:36`
- `.github/workflows/ci.yaml:56`
- `.github/workflows/ci.yaml:57`
- `.github/workflows/ci.yaml:80`
- `.github/workflows/ci.yaml:81`

### script-injection (severity: high)

Sub-rule (a): `${{ env.WORKSPACE_ARCHIVE }}` is interpolated directly inside `run:` shell command strings in multiple steps. Any `${{ ... }}` expression inside a `run:` block is a script-injection risk because the value is substituted by the GitHub Actions template engine before the shell ever sees it, bypassing shell quoting. Offending lines include:
- Line 35: `run: tar --exclude='.git' --exclude=${{ env.WORKSPACE_ARCHIVE }} -cvf ${{ env.WORKSPACE_ARCHIVE }} .`
- Line 56: `tar -xvf ${{ env.WORKSPACE_ARCHIVE }}`
- Line 57: `rm ${{ env.WORKSPACE_ARCHIVE }}`
- Line 80: `tar -xvf ${{ env.WORKSPACE_ARCHIVE }}`
- Line 81: `rm ${{ env.WORKSPACE_ARCHIVE }}`
- Line 107: `tar -xvf ${{ env.WORKSPACE_ARCHIVE }}`
- Line 108: `rm ${{ env.WORKSPACE_ARCHIVE }}`
Fix: replace `${{ env.WORKSPACE_ARCHIVE }}` with the plain environment variable `$WORKSPACE_ARCHIVE`, which is already available as a shell variable without template interpolation.

Locations:

- `.github/workflows/publish.yaml:35`
- `.github/workflows/publish.yaml:56`
- `.github/workflows/publish.yaml:57`
- `.github/workflows/publish.yaml:80`
- `.github/workflows/publish.yaml:81`
- `.github/workflows/publish.yaml:107`
- `.github/workflows/publish.yaml:108`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all script-injection findings in both workflow files by replacing `${{ env.WORKSPACE_ARCHIVE }}` template expressions inside `run:` shell blocks with the plain shell environment variable `"$WORKSPACE_ARCHIVE"` (double-quoted). Changes made:
- ci.yaml: Fixed 'Tar files' step (line 36) and two 'Untar' steps (lines 56-57, 80-81). File was rewritten entirely after corruption was detected during incremental edits.
- publish.yaml: Fixed 'Tar files' step (line 35) and three 'Untar' steps (lines 56-57, 80-81, 107-108).
The two remaining `${{ env.WORKSPACE_ARCHIVE }}` occurrences in both files are in the `path:` field of the `upload-artifact` action's `with:` block — these are YAML action inputs, not shell commands, and are not script injection risks.

