---
name: pull-request
description: "Create a pull request with an accurate title and description derived from the branch's commits. Use when the user says /pull-request, /pr, asks to open a PR, create a pull request, or push and open a PR. Supports --draft and custom base branch as arguments."
---

# Pull Request

## Prerequisite: clean working tree
Before doing anything, run `git status`.

- If there are **uncommitted changes** → stop and tell the user to run `/commit` first.
  The PR description is generated from commits, so every change needs to be committed — and committed well — before opening the PR. The `/commit` skill ensures each commit follows Conventional Commits format, which directly feeds the PR summary.
- If the tree is clean → proceed.

## Context (gather at the start)
```bash
git branch --show-current                                  # current branch
gh pr view --json url,state 2>/dev/null || echo "no PR"   # existing PR?
git log main..HEAD --format="### %s%n%b"                  # commits vs base
git diff main...HEAD --stat                                # changed files
```
If a custom base branch was passed as argument, replace `main` with it.

## Arguments
- `--draft` → create as draft PR
- A branch name (not `--draft`) → use as base branch instead of `main`

---

## If PR already exists
1. Push any unpushed commits: `git push`
2. Done — no need to create a new PR. Show the existing PR URL.

---

## If no PR exists

### 1. Review ALL commits on this branch
Read every commit message (`git log main..HEAD`). These are your source of truth — they were written following Conventional Commits via the `/commit` skill, so they already encode what changed and why.

### 2. Auto-rename random branch (if needed)
- **Detect**: is the branch name a random/nonsensical phrase unrelated to the actual changes? (e.g. `feature/agile-chasing-clarke`, `feature/jolly-toasting-minsky`)
- **Skip if**: the branch already has a remote tracking branch (`git branch -vv`)
- **Generate**: from the commits, create a short kebab-case name (2–5 words) describing the primary change. Keep `feature/` prefix if it was there.
- **Rename**: `git branch -m <new-name>`

### 3. Push
```bash
git push -u origin <branch>
```

### 4. Generate PR title
Derive a short, imperative title from the commits — the same way a good conventional commit summary works. Think of it as the "merge commit" message for the whole branch.

### 5. Generate PR description
Use the commit messages as building blocks:
- **Summary**: 2–4 bullet points — what was done and why (distilled from commit bodies)
- **Changes**: key changes organized by area, if multiple areas were touched (derived from commit scopes/types)

Do NOT invent context that isn't in the commits. Do NOT add AI attribution.

### 6. Create the PR
```bash
gh pr create --title "Title here" --body "$(cat <<'EOF'
## Summary
- Point 1
- Point 2

## Changes
- Change 1
- Change 2
EOF
)" [--draft if requested]
```

## Deliverable
Return the PR URL once created.
