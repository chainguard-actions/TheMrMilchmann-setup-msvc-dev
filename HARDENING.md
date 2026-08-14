<!-- markdownlint-disable -->

# Hardening Report: TheMrMilchmann--setup-msvc-dev/v3.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **TheMrMilchmann--setup-msvc-dev/v3.0.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks directly interpolate `${{ env.WORKSPACE_ARCHIVE }}` (an `env.*` context expression) into shell commands. Even though `env.WORKSPACE_ARCHIVE` is set to a constant string in this workflow, any `${{ ... }}` expression inside a `run:` block flows through YAML template substitution before the shell sees it, making it a script-injection risk. The offending lines are:
- `run: tar -cvf ${{ env.WORKSPACE_ARCHIVE }} .` (Tar files step)
- `tar -xvf ${{ env.WORKSPACE_ARCHIVE }}` and `rm ${{ env.WORKSPACE_ARCHIVE }}` (Untar steps in test-vcvarsall-invocation and test-path-exports jobs)
Fix: replace `${{ env.WORKSPACE_ARCHIVE }}` with the plain environment variable `$WORKSPACE_ARCHIVE` in all `run:` blocks.

Locations:

- `.github/workflows/ci.yaml:35`
- `.github/workflows/ci.yaml:60`
- `.github/workflows/ci.yaml:81`
- `.github/workflows/publish.yaml:31`
- `.github/workflows/publish.yaml:53`
- `.github/workflows/publish.yaml:74`
- `.github/workflows/publish.yaml:101`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Replaced all `${{ env.WORKSPACE_ARCHIVE }}` expressions in `run:` blocks with the plain shell variable `$WORKSPACE_ARCHIVE` in both .github/workflows/ci.yaml (3 locations: Tar files step, and two Untar steps) and .github/workflows/publish.yaml (4 locations: Tar files step, and three Untar steps across test-vcvarsall-invocation, test-path-exports, and publish jobs). The two remaining `${{ env.WORKSPACE_ARCHIVE }}` references in `path:` input fields of the upload-artifact action were intentionally left unchanged as they are not shell commands and pose no injection risk.

