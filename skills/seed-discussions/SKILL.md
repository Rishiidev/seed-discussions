---
name: seed-discussions
description: Seeds 5 tailored GitHub Discussion posts in under 60 seconds — detects project type, generates real content from your README, enables Discussions if off, creates all posts in one shot.
triggers:
  - seed discussions
  - seed my discussions
  - create discussion posts
  - start discussions
  - seed github discussions
  - discussion starter
  - populate discussions
  - setup discussions
  - warm up discussions
  - add discussions
---

# Seed Discussions

One command. Five tailored discussion posts. Discussions enabled automatically if they aren't already.

---

## STEP 0 — Collect Inputs

Ask in ONE message. Do not proceed until you have the answers:

```
1. GitHub repo URL or owner/repo?
   (Skip if you can detect it from: git remote -v in current directory)
2. Enable Discussions if currently off? (Default: yes)
```

If the user says just "seed discussions" with no repo in the message, run `git remote -v` and detect the repo from the remote URL. Confirm with the user before proceeding. If there's no remote, ask for the URL.

---

## STEP 1 — Pre-flight

```bash
# 1. Auth check
gh auth status

# 2. Verify repo + check Discussions state
gh repo view <owner>/<repo> --json name,hasDiscussionsEnabled,description,url 2>&1

# 3. Get repo ID + existing discussion count + category IDs
gh api graphql -f query='{
  repository(owner:"<owner>", name:"<repo>") {
    id
    discussions(first: 1) { totalCount }
    discussionCategories(first: 10) {
      nodes { id name }
    }
  }
}'
```

**Decision logic — act on these, do not skip:**
- Auth fails → stop. Tell user to run `gh auth login` then re-trigger the skill.
- Repo not found → stop. Ask the user to double-check the URL.
- Discussions already has 3+ posts → ask: *"This repo already has discussions. Add more anyway, or skip?"*
- Discussions disabled + user said yes (default) → enable in Step 2.

Save the repo GraphQL ID and category IDs. You'll need them in Step 6.

---

## STEP 2 — Enable Discussions (if needed)

```bash
gh api repos/<owner>/<repo> --method PATCH --field has_discussions=true
```

Confirm with a quick `gh repo view <owner>/<repo> --json hasDiscussionsEnabled` after patching.

---

## STEP 3 — Read the Project

Fetch these files via `gh api repos/<owner>/<repo>/contents/<path>` and decode the base64 content. Read as many as exist — don't skip if a file is missing, just move to the next:

| Priority | File | What to extract |
|----------|------|-----------------|
| 1 | `README.md` | Project name, value prop, key features (top 3), install command, target user |
| 2 | `skills/*/SKILL.md` | Trigger phrases, what it does, platforms |
| 3 | `package.json` | name, description, dependencies (detect framework) |
| 4 | `metadata.json` | name, description |
| 5 | `.claude-plugin/plugin.json` | name, description |
| 6 | `Cargo.toml` | name, description |
| 7 | `go.mod` | module name |
| 8 | `pyproject.toml` or `setup.py` | name, description |

Build this map before writing any post content:

| Field | Source |
|-------|--------|
| Project name | README h1 or package name |
| One-line value prop | README opening paragraph or description field |
| Key features (top 3) | README features section |
| Install / trigger command | README install section or SKILL.md triggers |
| Target user | Infer from the tool's purpose |
| Measurable benefit | Any number in the README (e.g. "4 min", "80%", "5x faster") |

---

## STEP 4 — Detect Project Type

Assign ONE type based on files found. First match wins:

| Type | Signal |
|------|--------|
| `claude-skill` | `SKILL.md` exists anywhere OR `.claude-plugin/` directory exists |
| `pwa` | `package.json` has react/vue/svelte AND `manifest` in code OR `vite-plugin-pwa` |
| `web-app` | `package.json` has react/vue/svelte/next/nuxt/remix |
| `node-library` | `package.json` exists, no major UI framework, has `main` or `exports` |
| `cli-tool` | `package.json` has `bin` field OR `Cargo.toml` has `[[bin]]` OR `.sh` files dominate |
| `python-package` | `pyproject.toml` or `setup.py` or `requirements.txt` exists |
| `rust-crate` | `Cargo.toml` exists |
| `go-module` | `go.mod` exists |
| `open-source` | Default fallback |

---

## STEP 5 — Generate All 5 Posts

Use the template set matching the detected type. Fill EVERY placeholder with real content from Step 3. **Never leave angle-bracket placeholders in the final output.** Review each post before creation — a post that could apply to any project is a failed post.

The 5 posts map to GitHub's default discussion categories:
- Post 1 → **Announcements** (pinnable roadmap)
- Post 2 → **Ideas** (voting / requests)
- Post 3 → **Show and Tell** (community sharing)
- Post 4 → **General** (tips / how-to)
- Post 5 → **Q&A** (troubleshooting)

---

### Template Set A — Claude Skill / Plugin

**Post 1 — Announcements: Roadmap**

Title: `Roadmap: what's coming and what will never be added`

Body:
```
## What's on the radar

**Likely coming:**
- [3 plausible next features derived from the SKILL.md and README — be specific, e.g. "Support for multi-file repos where SKILL.md lives in a subdirectory"]

**Under consideration:**
- [1-2 larger ideas that fit the skill's purpose]

---

## What will never be added

| Feature | Why not |
|---------|---------|
| [feature that would break the skill's core value] | [specific reason tied to this project] |
| Auto-run without confirmation on destructive steps | Too risky — always pauses before irreversible actions |
| Storing API keys or session data | Everything stays local in your Claude session |

---

Drop ideas in the [Ideas](link) category — voting happens there.
```

**Post 2 — Ideas: Trigger phrase voting**

Title: `Which trigger phrase feels most natural to you?`

Body:
```
[Project name] activates on these phrases — which one do you actually use?

| Trigger | When you'd use it |
|---------|-------------------|
[list all trigger phrases from SKILL.md, one per row, add a short note for each]

**Comment with:**
- Which phrase you use most
- Any phrase NOT listed that feels more natural to you

Most-upvoted additions make it into the next release.
```

**Post 3 — Show and Tell: Share your result**

Title: `Show your result — paste what [project name] generated for your repo`

Body:
```
Ran [project name] on something? Drop the repo URL and what you got here.

**Format:**
\`\`\`
Repo:
Project type detected:
Time taken:
Best thing it generated:
\`\`\`

---

I'll start: just ran it on [the skill's own repo URL] — [write a genuine seed entry based on what this skill actually does].
```

**Post 4 — General: Tips for best output**

Title: `Tips for getting the best output from [project name]`

Body:
```
[Write 4-6 concrete tips based on the skill's actual steps. Each tip should reference a real decision point or step in the SKILL.md. Format as a tips table, then a gotchas table.]

## Tips

| Tip | Why it helps |
|-----|-------------|
[4-6 rows, each one specific to this skill]

## Common gotchas

| Gotcha | How to avoid it |
|--------|----------------|
[3-4 rows based on real failure modes in this skill]
```

**Post 5 — Q&A: Troubleshooting**

Title: `Troubleshooting: common errors and how to fix them`

Body:
```
[Write 5 specific troubleshooting entries based on real failure modes in the skill — e.g. auth errors, missing files, API limits. Format: ## Error/symptom → **Cause:** → **Fix:**. End with: "Post your specific error below and I'll help."]
```

---

### Template Set B — Web App / PWA

**Post 1 — Announcements: Roadmap**

Title: `Roadmap: what's next for [project name]`

Body: Same structure as A-Post1. Derive 3 next features from README functionality gaps and obvious user needs.

**Post 2 — Ideas: Feature voting**

Title: `Which feature should we build next? Vote here`

Body:
```
React with 👍 on features you want most:

| # | Feature | What it would do |
|---|---------|-----------------|
[6-8 plausible features derived from the app's purpose and current feature gaps from README]

Or drop your own idea in a comment.
```

**Post 3 — Show and Tell: Share your setup**

Title: `How do you use [project name]? Share your setup`

Body:
```
[Write a seed entry based on the app's specific purpose. For a finance tracker: share your category structure. For a productivity app: share your workflow. Make it specific to this app's domain.]

Drop yours below — repo/screenshot/config welcome.
```

**Post 4 — General: Device/install tips** (PWA) or **Tips and tricks** (non-PWA)

For PWA:
Title: `Install tips by device and browser`

Body:
```
[Write device-specific PWA install steps: iOS Safari, Android Chrome, desktop Chrome/Edge. Based on app's actual PWA support from README.]

## Verified installs

| Device | Browser | Works? | Notes |
|--------|---------|--------|-------|
[populate with known working combos based on README]
```

For non-PWA:
Title: `Tips and tricks that aren't in the docs`

**Post 5 — Q&A: Data / privacy questions**

For localStorage/local apps:
Title: `Where is my data stored? Backup, export, and privacy FAQ`

Body:
```
**Q: Where does [project name] store my data?**
[Answer based on README — localStorage, IndexedDB, file system, etc.]

**Q: What happens if I clear my browser data?**
[Answer specifically based on this app's storage mechanism]

**Q: How do I back up my data?**
[Answer based on any export feature mentioned in README, or honest "not yet supported — workarounds below"]

**Q: Can [project name] see my data?**
[Answer based on README privacy stance — local-only, no cloud, etc.]

**Q: How do I move data to a new device?**
[Answer with whatever mechanism exists]

---
Post your data or privacy question below.
```

---

### Template Set C — Library (Node / Python / Rust / Go)

**Post 1 — Announcements: Roadmap + breaking changes policy**

Title: `Roadmap and our breaking changes policy`

Body:
```
## What's coming

[3 next features derived from README and likely library evolution]

## Breaking changes policy

- **Major version** bumps when we break public API
- **Minor version** for new features (backwards compatible)
- **Patch** for bug fixes only
- Deprecation warnings ship at least one minor version before removal
- Migration guides published with every major bump

---

Tracking upcoming breaking changes in the [Issues tab](link).
```

**Post 2 — Ideas: API design feedback**

Title: `API design feedback — what would make this easier to use?`

Body:
```
[Ask 3-4 specific questions about the library's API based on README examples. E.g. "Should we support async/await natively or keep the callback pattern?", "Should the config object be required or optional?". Make questions specific to the actual API surface.]
```

**Post 3 — Show and Tell: What did you build?**

Title: `Show what you built with [project name]`

Body:
```
Built something with [project name]? Show it here — repo, snippet, or description.

[Write a genuine seed entry: a typical integration pattern from the README, or a real-looking example project.]
```

**Post 4 — General: Integration patterns**

Title: `Integration patterns and gotchas`

Body:
```
[Write 4-5 integration tips based on the library's actual API from README. Cover: initialization, error handling, TypeScript usage if applicable, performance considerations. Format as a table.]
```

**Post 5 — Q&A: Version compatibility**

Title: `Version compatibility and migration guide`

Body:
```
## Compatibility matrix

[Write based on README peer deps, Node/Python/Rust/Go version requirements]

| [Library] version | [Runtime] version | Status |
|---|---|---|

## Migrating from older versions

[Write any known migration notes from README changelog or CHANGELOG.md, or "No breaking changes yet — check back here when we ship v2."]

---
Post your compatibility question below.
```

---

### Template Set D — CLI Tool

**Post 1 — Announcements: Roadmap**

Same structure. Derive new commands/flags from README.

**Post 2 — Ideas: New command suggestions**

Title: `What command or flag should we add next?`

Body:
```
React with 👍 to vote:

| Command/flag | What it would do | When you'd need it |
|---|---|---|
[5-6 plausible additions based on what the CLI currently does]

Or suggest your own below.
```

**Post 3 — Show and Tell: Share your workflow**

Title: `Share your workflow — how do you use [project name] day to day?`

Body:
```
[Seed with a real workflow example based on README use cases. E.g. for a CI tool: show the pipeline config. For a dev tool: show the alias/script you use daily.]
```

**Post 4 — General: Shell aliases and config tips**

Title: `Shell aliases, config tips, and productivity shortcuts`

Body:
```
[Write 4-5 tips: useful aliases for common commands, config file location/structure, shell completion setup if available, combining with other tools via pipes. All based on the CLI's actual commands from README.]
```

**Post 5 — Q&A: Common errors**

Title: `Common errors and how to fix them`

Body:
```
[Write 5 specific error scenarios based on the CLI's actual commands from README. Format: ## Error message or symptom → **Cause:** → **Fix:**. End with: "Post your error below."]
```

---

### Template Set E — Open Source (fallback)

**Post 1 — Roadmap** — Standard structure, derive from README.

**Post 2 — Ideas: Contribution opportunities**

Title: `Good first contributions — what would you work on?`

Body:
```
[3-4 specific contribution opportunities derived from README gaps or obvious missing pieces. Each with: what to build, why it helps, which files to touch. Link to CONTRIBUTING.md if it exists.]
```

**Post 3 — Show and Tell**

Title: `How are you using [project name]? Share your use case`

**Post 4 — General**

Title: `Getting started tips that aren't in the README`

**Post 5 — Q&A**

Title: `Ask anything — questions, clarifications, edge cases`

---

## STEP 6 — Create All 5 Discussions

Using the repo ID and category IDs from Step 1, create each post:

```bash
gh api graphql -f query='mutation {
  createDiscussion(input: {
    repositoryId: "<repo-id>"
    categoryId: "<category-id>"
    title: "<title>"
    body: "<body>"
  }) {
    discussion { number url title }
  }
}'
```

Category mapping:
- Post 1 (Roadmap) → **Announcements** category ID
- Post 2 (Voting/Ideas) → **Ideas** category ID
- Post 3 (Community sharing) → **Show and Tell** category ID
- Post 4 (Tips/How-to) → **General** category ID
- Post 5 (Troubleshooting) → **Q&A** category ID

Create all 5. If a category doesn't exist, skip that post and note it in the report.

---

## STEP 7 — Report Results

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✓ Discussions: <enabled / was already on>
✓ Project type detected: <type>
✓ Posts created: 5

  #N  Roadmap                   → Announcements   <url>
  #N  <title>                   → Ideas           <url>
  #N  <title>                   → Show and Tell   <url>
  #N  <title>                   → General         <url>
  #N  <title>                   → Q&A             <url>

One manual step — pin the Roadmap post:
  → <roadmap url>
  → Click ⋯ → "Pin discussion"
  (GitHub's GraphQL API doesn't expose pinDiscussion yet)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Rules

- **Never create generic content.** Every post must use the project's real name, real features, real trigger phrases, real commands. A post that could apply to any project is a failed post.
- **Never overwrite existing discussions.** Count first (Step 1). If 3+ exist, ask before adding more.
- **Always fill placeholders.** Review each generated post — no `<placeholder>` text in the final output.
- **Project type detection is mandatory.** Always read the repo before picking a template set.
- **The pin is manual.** GitHub's GraphQL API does not expose a pinDiscussion mutation. Always remind the user to pin the Roadmap post manually.
