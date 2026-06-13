---
name: best-practice-claude
description: >
  Checks Anthropic's latest updates across all official resources — models, Claude Code releases,
  API changes, SDK updates, and documentation. Triggered automatically on every user message via
  UserPromptSubmit hook, gated to once per 24 hours. Also invokable directly. Surfaces
  project-specific, implementable changes as a numbered proposal list.
allowed-tools: WebFetch WebSearch Read Write Bash
---

# Best Practice: Claude

Checks all Anthropic resources for updates since the last run and surfaces concrete, project-specific changes with numbered proposals.

---

## Step 1 — Check cadence

Read `~/.claude/skills/best-practice-claude/last-check.md`. Compare the stored ISO 8601 timestamp against the current time.

- If the file does not exist, or more than 24 hours have elapsed since the stored timestamp: proceed to Step 2.
- If less than 24 hours have elapsed: print "Last checked [TIMESTAMP] — [N] hours ago. No check needed." and stop.

---

## Step 2 — Collect resource URLs

Read both source files in full:

1. `~/.claude/skills/claude-api/shared/live-sources.md` — extract every URL and its extraction prompt from every table in the document
2. `~/.claude/skills/best-practice-claude/additional-resources.md` — extract every URL and its extraction prompt

Compile a single deduplicated list of all URLs with their extraction prompts.

---

## Step 3 — Fetch and filter

WebFetch every URL from Step 2. Apply each URL's extraction prompt when fetching.

**404 fallback:** For any URL that returned 404, run `WebSearch [topic] site:platform.claude.com` to locate the current URL, then WebFetch the found URL. If the correct URL differs from what is in the source file, update `~/.claude/skills/best-practice-claude/additional-resources.md` with the corrected URL before continuing.

Filter findings: keep only content that is dated AFTER the timestamp stored in `last-check.md`. Discard anything older.

---

## Step 4 — Read project context

Read:
- The global `~/.claude/CLAUDE.md`
- All project CLAUDE.md files — run `find "$HOME" -maxdepth 3 -name 'CLAUDE.md' -not -path "$HOME/.claude/*"` to locate them, then read each one found
- All skill files at `~/.claude/skills/*/SKILL.md`

Extract: which models are referenced, which tools are listed, which hooks or settings are configured, which skills reference which Anthropic features, and which project-specific patterns or constraints exist across all projects. Include `best-practice-claude` and `best-practice-skills` themselves in this review.

---

## Step 5 — Assess impact

For each new or changed finding from Step 3, determine whether it has a concrete impact on the project context found in Step 4:

- Does a new model or parameter affect a skill or CLAUDE.md pattern in use in this project?
- Does a Claude Code change affect a hook, slash command, or setting referenced in this project?
- Does a deprecated field appear in any skill or rule in this project?

Discard any finding with no concrete connection to the project's actual setup. Do not surface generic Anthropic news with no project relevance.

---

## Step 6 — Output

Print to chat:

```
## Best Practice: Claude — [CURRENT DATE]
Last check: [PREVIOUS TIMESTAMP] ([N] hours/days ago)

Sources checked: [N] · Changes found: [N] · Affecting this project: [N]

What changed:
- [Item] — [source] — [date]
- ...

## Proposed changes

1. 🔄 [skill-name or file] — [section] — [specific text to change]
   Reason: [why, grounded in the specific Anthropic update]

2. ➕ [skill-name or file] — [section] — [specific text to add]
   Reason: ...

3. ✅ [skill-name] — no change needed
   Reason: ...

4. ❌ [skill-name or file] — [section] — [specific text to remove]
   Reason: ...
```

Emoji key: ✅ Keep · 🔄 Update · ➕ Add · ❌ Remove

After the numbered list ask: **"Apply all, apply selected (list numbers), or skip?"**

If no relevant changes were found: print "No relevant Anthropic updates affecting this project since [last check timestamp]." and proceed directly to Step 7.

---

## Step 7 — Update timestamp

Write the current ISO 8601 timestamp to `~/.claude/skills/best-practice-claude/last-check.md`. Do this regardless of whether changes were found or applied.

---

## Final Step — Write observation

Append a timestamped entry to `~/.claude/skills/best-practice-claude/observations.md`:

```markdown
## [ISO 8601 timestamp]
[One sentence: what changed was found, what was applied, any source fetch failures, overall quality of proposals]
```

If `observations.md` does not exist, create it with this frontmatter first:

```markdown
---
last-reviewed: 1970-01-01T00:00:00Z
---
```
