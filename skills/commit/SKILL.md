---
name: commit
description: "Create high-quality git commits: review/stage intended changes, split into logical commits, and write clean Conventional Commit messages. Use this skill whenever the user says /commit, asks to commit, wants to craft a commit message, stage changes, or split work into multiple commits. Never add Co-Authored-By or any AI attribution."
---

# Commit work

## Goal
Make commits that are easy to review and safe to ship:
- Only intended changes are included
- Commits are logically scoped (split when needed)
- Messages describe what changed and why
- **Never** include `Co-Authored-By`, AI attribution, or any non-human trailer

## Workflow

1. **Inspect the working tree before staging**
   - `git status`
   - `git diff` (unstaged) — or `git diff --stat` if there are many changes

2. **Decide commit boundaries** (split if needed)
   - Split by: feature vs refactor, backend vs frontend, formatting vs logic, tests vs prod code, dependency bumps vs behavior changes
   - If changes are mixed in one file, plan to use patch staging

3. **Stage only what belongs in the next commit**
   - Prefer patch staging for mixed changes: `git add -p`
   - To unstage: `git restore --staged -p` or `git restore --staged <path>`

4. **Review what will actually be committed**
   - `git diff --cached`
   - Sanity checks: no secrets or tokens, no debug logging, no unrelated formatting churn

5. **Describe the staged change in 1–2 sentences** (before writing the message)
   - "What changed?" + "Why?"
   - If you can't describe it cleanly → the commit is too big or mixed → go back to step 2

6. **Write the commit message** using Conventional Commits (required)
   - Format: see `references/commit-message-template.md`
   - Always use a heredoc to avoid escaping issues:
     ```bash
     git commit -m "$(cat <<'EOF'
     type(scope): short summary

     Body explaining what and why.
     EOF
     )"
     ```
   - **Never** add `Co-Authored-By`, `Signed-off-by`, or any AI/tool attribution in trailers

7. **Run the fastest meaningful check** (if available)
   - Lint or unit tests only — **never build**
   - Skip if the repo has no fast check configured

8. **Repeat** for the next commit until the working tree is clean

## Deliverable
- The final commit message(s)
- A short summary per commit (what/why)
- Commands used to stage/review (at minimum: `git diff --cached`)
