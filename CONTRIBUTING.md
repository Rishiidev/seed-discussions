# Contributing

## What's welcome

- **New project type templates** — if the current 5 types don't cover your project (e.g. mobile app, browser extension, desktop app), add a Template Set F with all 5 post templates
- **Better discussion content** — if an existing template produces generic output for your project type, replace it with something more specific
- **New trigger phrases** — add them to `skills/seed-discussions/SKILL.md` under `triggers:` in the frontmatter
- **Step logic fixes** — if project type detection misclassifies your repo, improve the signal table in Step 4

## How to test

1. Paste the full `SKILL.md` into Claude Code
2. Say "seed discussions" and point it at a test repo
3. Check: does it detect the project type correctly? Does the content reference real project details?
4. Required: paste a before/after in your PR — the old output vs. the new output for the same repo

## PR format

Every PR needs a before/after example:
```
Before: [paste the discussion title/body that was generated with the old template]
After:  [paste the discussion title/body generated with your improved template]
Repo tested: [owner/repo]
```

## What must not change

- The 7-step flow order (pre-flight → read → detect → generate → create → report)
- The manual pin reminder in Step 7 — GitHub's API doesn't support it; this note protects users from expecting it to work automatically
- The "never leave placeholders" rule — every generated post must use real project data

## Good first contribution

See [open issues](https://github.com/Rishiidev/seed-discussions/issues) labeled `good first issue`.
