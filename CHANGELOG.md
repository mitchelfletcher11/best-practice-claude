# Changelog

## v1.2.0 — 2026-06-14
Preflight now checks `curl` is installed before downloading the auto-run script (zero-state).

## v1.1.0 — 2026-06-14
- Self-bootstrapping **Step 0 Preflight**: detects the missing auto-run script + hook
  and offers to set them up — security warning + explicit confirmation before any
  script download or hook wiring; never installs executable code silently.
- **Bundled `live-sources.md`** so the skill is self-contained: Step 2 reads
  claude-api's copy if present, else the bundled one (no hidden cross-skill dependency).

## v1.0.0 — 2026-06-13
- Initial public release.
- Daily (24h-gated) automatic review of `CLAUDE.md` files + skills against
  current Claude/Anthropic best practices, proposed as numbered changes.
- Bundled `check-best-practice.sh` (the prompt-submit trigger) and a
  `settings.snippet.json` hook; setup documented in SETUP.md.
- Portable: all paths use `$HOME`/`~`.
