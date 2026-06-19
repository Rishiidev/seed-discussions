# Changelog

All notable changes documented here.
Format: [Keep a Changelog](https://keepachangelog.com/en/1.0.0/)

## [1.0.0] — 2026-06-19

### Added
- 9 trigger phrases: `seed discussions`, `seed my discussions`, `create discussion posts`, `start discussions`, `seed github discussions`, `discussion starter`, `populate discussions`, `setup discussions`, `warm up discussions`
- Project type detection: claude-skill, pwa, web-app, node-library, cli-tool, python-package, rust-crate, go-module, open-source fallback
- 5 template sets with all 5 discussion categories per set (Announcements, Ideas, Show and Tell, General, Q&A)
- Auto-enable Discussions if off via `gh api PATCH has_discussions=true`
- GraphQL-based discussion creation via `createDiscussion` mutation
- Pre-flight check: counts existing discussions, warns before adding to already-seeded repos
- Manual pin reminder in the report (GitHub GraphQL API doesn't expose `pinDiscussion`)
- Full report output with discussion numbers, URLs, and category mapping
