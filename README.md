# best-practice-claude — Claude Code skill

A daily, automatic review of your **Claude setup** — it sweeps your global and
project `CLAUDE.md` files (and skill files) against the latest Anthropic / Claude
Code best practices and proposes concrete, numbered changes you approve or skip.

Runs at most **once per 24 hours**, triggered automatically by a prompt-submit
hook (no need to remember to run it).

## Install

```bash
# 1. the skill
mkdir -p ~/.claude/skills/best-practice-claude
for f in SKILL.md additional-resources.md; do
  curl -sf "https://raw.githubusercontent.com/mitchelfletcher11/best-practice-claude/main/$f" \
    -o "~/.claude/skills/best-practice-claude/$f"
done

# 2. the trigger script
mkdir -p ~/.claude/scripts
curl -sf https://raw.githubusercontent.com/mitchelfletcher11/best-practice-claude/main/check-best-practice.sh \
  -o ~/.claude/scripts/check-best-practice.sh
chmod +x ~/.claude/scripts/check-best-practice.sh
```

Then add the hook from **[SETUP.md](SETUP.md)** to your `~/.claude/settings.json`.
That's the one machine-specific step — once wired, prompting Claude reproduces
the author's exact behaviour.

> Pairs with **best-practice-skills** (reviews your *skills* the same way). They
> share one hook + one script — install either or both.
