---
description: Cut a versioned release of alph-cli (bump version, run tests, commit, tag, push)
allowed-tools: [Bash, Read, Edit, Glob]
---

Cut a release of alph-cli. Follow these steps exactly.

## Required input

Ask the user for the new version string if not already provided (e.g. `0.1.2`).

## Steps

### 1. Confirm working directory and clean state

```bash
cd /Users/cpettet/git/chasemp/AlpheusCEF/alph-cli
git status
git diff --stat
```

Stop and report if there are uncommitted changes unrelated to the release.

### 2. Run the full test suite

```bash
cd /Users/cpettet/git/chasemp/AlpheusCEF/alph-cli
poetry run pytest tests/ -q
poetry run mypy
poetry run ruff check src/ tests/
```

All three must pass. Fix any failures before continuing — do not skip.

### 3. Bump the version in pyproject.toml

Edit the `version = "..."` line under `[project]` in `pyproject.toml`.
Confirm the new value matches the requested version exactly.

### 4. Commit the version bump

```bash
cd /Users/cpettet/git/chasemp/AlpheusCEF/alph-cli
git add pyproject.toml
git commit -m "chore: bump version to <VERSION>"
git push origin main
```

### 5. Tag the release

```bash
cd /Users/cpettet/git/chasemp/AlpheusCEF/alph-cli
git tag v<VERSION>
git push origin v<VERSION>
```

This triggers the `release.yml` workflow which:
- Runs tests again on CI
- Builds the sdist
- Creates the GitHub release with the sdist attached
- Updates the Homebrew formula in `homebrew-tap` (if `HOMEBREW_TAP_TOKEN` is set)

### 6. Verify the workflow

```bash
cd /Users/cpettet/git/chasemp/AlpheusCEF/alph-cli
gh run list --limit 3
```

Wait for the release workflow to complete. Check for failures with `gh run view`.

### 7. If HOMEBREW_TAP_TOKEN is not set (formula update skipped)

Run manually from the homebrew-tap repo:

```bash
cd /Users/cpettet/git/chasemp/AlpheusCEF/homebrew-tap
./scripts/update-formula.sh <VERSION>
git add Formula/alph.rb
git commit -m "update alph formula to v<VERSION>"
git push origin main
```

> The script downloads the tarball to a tempfile before hashing. Do NOT use `curl | shasum`
> directly — piped curl can produce a truncated byte stream and a wrong SHA256.

### 8. Confirm Homebrew reinstall works

```bash
brew update && brew reinstall alph
alph --help
alph-mcp --help
```

## Output

Report:
- Version bumped to: `<VERSION>`
- Tag pushed: `v<VERSION>`
- CI workflow URL (from `gh run list`)
- Whether formula was auto-updated or needs manual update
