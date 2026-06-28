---
name: transmute-ship
description: >-
  Ship a finished change to the Transmute app: the branch -> commit -> PR ->
  squash-merge cycle. Use WHENEVER a change is verified and ready to land —
  triggers include "ship it", "commit and PR", "open a PR", "merge it", or
  finishing any edit to index.html (the workflow CLAUDE.md mandates: branch,
  commit, push, gh pr create, gh pr merge --squash --delete-branch). Captures
  the exact commands, the PR-body shape, and the gotchas so the cycle is fast,
  consistent, and token-cheap instead of re-derived each time.
---

# Shipping a Transmute change

Repo `lennoxlewis2/transmute-app`. Identity `lennoxlewis2` / `lewiscurtis2@hotmail.co.uk`.
Run the **whole cycle automatically without asking** (CLAUDE.md rule).

## Before you ship
1. **Verify first** with the `transmute-verify` skill (eval-based; screenshots hang). Never ship on a diff read alone.
2. **Revert any temporary `.claude/launch.json` port configs** you added for the preview — only the original `transmute-app` (port 3000) config should remain.
3. **Stage only what you changed** — almost always just `index.html` (single-file app). Touch `sw.js` / `manifest.json` / `privacy.html` / icons only if you intentionally edited them.

## The cycle (one shot)
```bash
git checkout main -q && git pull -q
git checkout -b <type>/<kebab-slug>          # feat/ fix/ ux/ etc.
git add index.html
git commit -q -m "$(cat <<'EOF'
<type>: <concise summary>

<1–4 lines: what changed and why. Plain, no fluff.>

Co-Authored-By: Claude Opus 4.8 <noreply@anthropic.com>
EOF
)"
git push -u origin <branch>
gh pr create --title "<type>: <summary>" --body "$(cat <<'EOF'
<short What/Why>

## Verification
- <bullet: what you exercised via eval, what state changed> ✓
- No console errors ✓

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
gh pr merge <#> --squash --delete-branch
```

## Gotchas (don't be surprised)
- **"Warning: 2 uncommitted changes"** on `gh pr create` is EXPECTED and fine — they're the pre-existing `D sr-tracker-flat_36.zip` and untracked `audio/test.mp3` from the repo's start state. **Never stage them.**
- `gh pr merge --squash --delete-branch` **auto-returns you to `main`** and fast-forwards — no manual checkout after.
- Keep PR bodies **tight**: What/Why + a few verification bullets. Don't write essays; the diff carries the detail.
- After merging, **update memory** (`MEMORY.md` + the relevant `project_*.md`) if the change is a new feature or a non-obvious decision worth recalling.
- `git commit` interactive flags aren't available here; always use the heredoc form for multi-line messages.

## One PR per logical change
Bundle tightly-related edits; split unrelated ones into separate PRs (e.g. a UI redesign and an i18n refactor ship separately). This convo's history (PRs #58–#74) is the model.
