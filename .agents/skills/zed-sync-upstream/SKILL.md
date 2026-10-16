---
name: zed-sync-upstream
description: Sync the fork's `main` branch and rebase the `risotech` branch onto upstream Zed (`https://github.com/zed-industries/zed`). Use this whenever the user asks to sync, update, or rebase `main` and/or `risotech` with `upstream`.
---

# Zed Sync Upstream

In the `zed-ai-crew` repository (`origin`: `Riso-application-factory/zed-ai-crew`), branch roles are strictly separated per [`GLOSSARY.md`](../../../GLOSSARY.md):

- `upstream/main` (`https://github.com/zed-industries/zed.git`): canonical upstream Zed history.
- `main` (on `origin`): mirrors `upstream/main` 100% with **no** Crew-specific commits.
- `risotech` (on `origin`): the primary branch ("Nhánh chính") for Zed AI Crew, carrying all Crew commits rebased directly on top of `upstream/main`.

## When to use

Use this skill whenever the user asks to:
- Rebase `main` and `risotech` with upstream Zed
- Sync `origin` branches (`main`, `risotech`) with `upstream/main`
- Pull the latest upstream Zed changes into the fork

## Pre-flight Checks

1. **Verify working tree is clean**:
   ```sh
   git status --porcelain
   ```
   If there are uncommitted changes, stop and ask the user whether to stash or commit them before proceeding.

2. **Verify remotes**:
   ```sh
   git remote -v
   ```
   Ensure `upstream` points to `https://github.com/zed-industries/zed.git` (or `.git` equivalent). If `upstream` is missing, add it:
   ```sh
   git remote add upstream https://github.com/zed-industries/zed.git
   ```

3. **Fetch latest refs**:
   ```sh
   git --no-pager fetch upstream main
   git --no-pager fetch origin main risotech
   ```

## Procedure

### 1. Audit commits before modifying refs

Check if local `main` or `origin/main` accidentally received any Crew commits that are not yet on `risotech`:

```sh
git --no-pager log --oneline upstream/main..main
git --no-pager log --oneline risotech..main
```

- If `risotech..main` is **empty**, all Crew commits are already on `risotech`, so `main` can safely be reset to `upstream/main`.
- If `risotech..main` is **not empty**, stop and inform the user (or cherry-pick/rebase those commits onto `risotech` first) so no commits are lost when resetting `main`.

### 2. Sync `main` with `upstream/main`

Reset local `main` to match `upstream/main` exactly and push to `origin`:

```sh
git checkout main
git reset --hard upstream/main
git push origin main
```

*(Note: If `origin/main` had diverged due to stray commits that are already accounted for on `risotech`, use `git push --force-with-lease origin main`.)*

### 3. Rebase `risotech` onto `upstream/main`

Ensure local `risotech` has all commits from `origin/risotech`, then rebase onto `upstream/main`:

```sh
git checkout risotech
git --no-pager log --oneline risotech..origin/risotech
```

If `origin/risotech` has commits not in local `risotech` (and local hasn't diverged), fast-forward first (`git merge --ff-only origin/risotech`). Then run the rebase:

```sh
git rebase upstream/main
```

### 4. Handle Rebase Conflicts (if any)

If `git rebase upstream/main` stops due to conflicts:
- Inspect conflicted files with `git status`.
- If conflicts are trivial (e.g., `.rules`, `Cargo.lock`, or docs), resolve them carefully while preserving both upstream Zed changes and Crew additions.
- If conflicts are non-trivial or ambiguous, stop and ask the user before continuing.
- When resolved, continue non-interactively:
  ```sh
  git add <resolved_files>
  GIT_EDITOR=true git rebase --continue
  ```

### 5. Push `risotech` to `origin`

Once the rebase completes cleanly, force-push with lease to `origin`:

```sh
git push --force-with-lease origin risotech
```

### 6. Verify Final Topology

Confirm that `main` matches `upstream/main` and `risotech` sits cleanly on top of `upstream/main`:

```sh
git rev-list --left-right --count upstream/main...main
# Expected: 0	0

git rev-list --left-right --count upstream/main...risotech
# Expected: 0	<number_of_crew_commits>
```

## Final Report

Report to the user:
- The latest `upstream/main` commit SHA and subject synced to `main` and `origin/main`.
- The number of Crew commits rebased onto `risotech` and pushed to `origin/risotech`.
- Output of the `git rev-list --left-right --count` verification checks.
