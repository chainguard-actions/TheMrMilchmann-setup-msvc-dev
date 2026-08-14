<!-- markdownlint-disable -->

# Hardening Report: TheMrMilchmann--setup-msvc-dev/v4.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **TheMrMilchmann--setup-msvc-dev/v4.0.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: `${{ env.WORKSPACE_ARCHIVE }}` is interpolated directly inside `run:` shell commands in multiple steps across both workflow files. Per the script-injection check, `env.*` flows through YAML template substitution before the shell sees it, so any `${{ ... }}` expression directly in a `run:` block is a finding regardless of which context it reads from. Offending lines include `run: tar -cvf ${{ env.WORKSPACE_ARCHIVE }} .`, `tar -xvf ${{ env.WORKSPACE_ARCHIVE }}`, and `rm ${{ env.WORKSPACE_ARCHIVE }}`. These should be replaced with the env-var shell form (e.g., `tar -cvf "$WORKSPACE_ARCHIVE" .`) so the value is never interpolated by the YAML template engine into the shell command string.

Locations:

- `.github/workflows/ci.yaml:35`
- `.github/workflows/ci.yaml:50`
- `.github/workflows/ci.yaml:51`
- `.github/workflows/ci.yaml:72`
- `.github/workflows/ci.yaml:73`
- `.github/workflows/publish.yaml:28`
- `.github/workflows/publish.yaml:50`
- `.github/workflows/publish.yaml:51`
- `.github/workflows/publish.yaml:72`
- `.github/workflows/publish.yaml:73`
- `.github/workflows/publish.yaml:91`
- `.github/workflows/publish.yaml:92`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Replaced all `${{ env.WORKSPACE_ARCHIVE }}` expressions inside `run:` shell commands with the shell env var form `"$WORKSPACE_ARCHIVE"` in both .github/workflows/ci.yaml and .github/workflows/publish.yaml. Affected steps: 'Tar files' (1 occurrence each file) and 'Untar' (2 occurrences per step × 2 steps in ci.yaml, 3 steps in publish.yaml). Remaining `${{ env.WORKSPACE_ARCHIVE }}` references in `with: path:` fields of upload-artifact steps are safe action inputs, not shell commands, and were left unchanged.

