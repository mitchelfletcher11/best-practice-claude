# Setup — best-practice-claude

> **You don't have to follow this by hand.** On first run the skill's **Step 0
> Preflight detects what's missing and offers to set it up for you** — with a
> security warning + explicit confirmation before writing the script or wiring the
> hook. This file is the manual / reference path.

Two machine-specific pieces beyond the skill file: the **trigger script** and the
**settings hook**. Both are portable (`$HOME`/`~` based) and carry no secrets.

## 1. Install the skill + script

See the [README](README.md) install block — it places:
- `~/.claude/skills/best-practice-claude/SKILL.md` (+ `additional-resources.md`)
- `~/.claude/scripts/check-best-practice.sh` (make it executable: `chmod +x`)

## 2. Wire the prompt-submit hook

Merge the `UserPromptSubmit` hook from **`settings.snippet.json`** into your
`~/.claude/settings.json` (under `hooks`). It is exactly:

```json
{
  "hooks": {
    "UserPromptSubmit": [
      { "hooks": [ { "type": "command", "command": "bash ~/.claude/scripts/check-best-practice.sh" } ] }
    ]
  }
}
```

If you already have `UserPromptSubmit` hooks, append this entry to the array.

## How it works

On every prompt, `check-best-practice.sh` checks
`~/.claude/skills/best-practice-claude/last-check.md`. If more than 24h have
passed (or it's never run), it prints `Before addressing this message, run:
/best-practice-claude`, which Claude then executes — sweeping your `CLAUDE.md`
files and proposing improvements. `last-check.md` is created automatically; you
don't ship or edit it.

## What it touches

It **reads** your global `~/.claude/CLAUDE.md`, your project `CLAUDE.md` files,
and your skill files, then proposes edits you approve. No credentials, no network
keys — just your local config.

> The same script also triggers **best-practice-skills** if you install it; one
> hook + one script covers both.
