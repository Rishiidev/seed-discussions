# 💬 seed-discussions — 5 tailored GitHub Discussion posts in 60 seconds

<div align="center">

**One command seeds your GitHub Discussions with real, project-specific content — not lorem ipsum.**

[![Stars](https://img.shields.io/github/stars/Rishiidev/seed-discussions?style=for-the-badge&color=gold)](https://github.com/Rishiidev/seed-discussions/stargazers)
[![CI](https://github.com/Rishiidev/seed-discussions/actions/workflows/validate.yml/badge.svg)](https://github.com/Rishiidev/seed-discussions/actions)
[![License](https://img.shields.io/github/license/Rishiidev/seed-discussions?style=for-the-badge)](LICENSE)
[![Version](https://img.shields.io/badge/version-1.0.0-blue?style=for-the-badge)](https://github.com/Rishiidev/seed-discussions/releases)

*Works in Claude Code · Requires `gh` CLI · MIT License*

</div>

> Say "seed discussions" in Claude Code and get 5 tailored GitHub Discussion posts created in your repo — Roadmap, voting thread, Show & Tell prompt, tips guide, and Q&A starter — all filled with real content from your README.

---

## The Problem

You just launched a repo. Discussions is enabled but completely empty. Visitors see a ghost town — no threads, no activity, nothing to engage with.

Starting from zero is harder than joining a conversation. Most repos stay silent forever not because nobody cares, but because nobody wanted to post first.

## The Fix

```
seed discussions
```

seed-discussions reads your README, detects your project type (Claude skill, web app, library, CLI tool, etc.), and generates 5 real discussion posts — each filled with content specific to your project, not a generic template.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✓ Discussions: enabled
✓ Project type detected: claude-skill
✓ Posts created: 5

  #1  Roadmap: what's coming and what will never...  → Announcements
  #2  Which trigger phrase feels most natural?       → Ideas
  #3  Show your result — paste what it generated    → Show and Tell
  #4  Tips for getting the best output              → General
  #5  Troubleshooting: common errors and fixes      → Q&A

One manual step — pin the Roadmap post → Click ⋯ → "Pin discussion"
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Install

| Platform | Command |
|----------|---------|
| **Claude Code** | `/plugin install github:Rishiidev/seed-discussions` |

---

<div align="center">
<b>If this saves you from a silent launch, star it — takes 2 seconds.</b><br>
<a href="https://github.com/Rishiidev/seed-discussions">⭐ Star on GitHub</a>
</div>

---

## How It Works

| Step | What happens |
|------|-------------|
| **Detect** | Reads README, SKILL.md, package.json, Cargo.toml, go.mod — whichever exists |
| **Classify** | Assigns project type: claude-skill, web-app, pwa, node-library, cli-tool, python-package, rust-crate, go-module |
| **Generate** | Picks the right template set, fills every post with real project content |
| **Enable** | Turns on Discussions via `gh api PATCH` if not already active |
| **Create** | Posts all 5 via GitHub's GraphQL `createDiscussion` mutation |
| **Report** | Returns every URL and reminds you to pin the Roadmap post manually |

## Trigger Phrases

All of these activate the skill:

| Phrase |
|--------|
| `seed discussions` |
| `seed my discussions` |
| `create discussion posts` |
| `start discussions` |
| `seed github discussions` |
| `discussion starter` |
| `populate discussions` |
| `setup discussions` |
| `warm up discussions` |
| `add discussions` |

## What Gets Created

| # | Category | Post type |
|---|----------|-----------|
| 1 | Announcements | Roadmap — what's coming, what will never be added |
| 2 | Ideas | Voting thread — trigger phrases, feature requests, or API design |
| 3 | Show and Tell | Community sharing prompt — seeded with a real example |
| 4 | General | Tips guide — specific to your project type |
| 5 | Q&A | Troubleshooting — real failure modes, real fixes |

## Project Types Supported

| Type | Detected when |
|------|--------------|
| Claude skill | `SKILL.md` exists or `.claude-plugin/` directory exists |
| Web app | `package.json` has react, vue, svelte, next, or nuxt |
| PWA | Web app + `vite-plugin-pwa` or `manifest` in code |
| Node library | `package.json` with `main`/`exports`, no UI framework |
| CLI tool | `package.json` has `bin`, or Cargo `[[bin]]`, or dominant `.sh` files |
| Python package | `pyproject.toml` or `setup.py` exists |
| Rust crate | `Cargo.toml` exists |
| Go module | `go.mod` exists |
| Open source | Fallback for everything else |

## Requirements

- `gh` CLI authenticated (`gh auth login`)
- Write access to the target repo

## What Never Gets Touched

| Protected | Why |
|-----------|-----|
| Existing discussions | Never overwrites — counts first, asks if 3+ already exist |
| Repo code / files | Read-only access to README and config files |
| Pinned discussions | GitHub's API doesn't support pinning — always manual |
| Your gh auth token | Never stored, only used in the active session |

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=Rishiidev/seed-discussions&type=Date)](https://star-history.com/#Rishiidev/seed-discussions&Date)

## License

MIT — see [LICENSE](LICENSE)

---

<div align="center">
<b>Found seed-discussions useful? A ⭐ star helps others find it.</b><br>
<a href="https://github.com/Rishiidev/seed-discussions">⭐ Star this repo</a>
</div>
