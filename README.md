# token-saver

Claude Code plugin to reduce token consumption. Designed for Salesforce / ticketing workflows where drafts are reviewed in the destination system, not in the console.

## What it does

- **Terse output style** — no preambles, no end-of-turn summaries, no progress narration, no draft echoes.
- **Salesforce / ticketing directive** — when a tool writes to SFDC (or any external case/ticket system), confirm with a one-line reference instead of pasting the draft back.
- **Tool hygiene** — no re-reading files after edits, no "let me verify" passes.

## Install at work (Windows)

1. Clone this repo somewhere stable:

   ```powershell
   git clone <your-remote-url> $env:USERPROFILE\token-saver
   ```

2. Install as a plugin from Claude Code:

   ```
   /plugin install C:\Users\<you>\token-saver
   ```

3. Activate the terse output style each session (or set it as default in `~/.claude/settings.json`):

   ```
   /output-style terse
   ```

4. (Optional, recommended) Copy `CLAUDE.md` to `~/.claude/CLAUDE.md` so the directives apply globally, not only when working inside this repo:

   ```powershell
   Copy-Item .\CLAUDE.md $env:USERPROFILE\.claude\CLAUDE.md
   ```

   If you already have a `~/.claude/CLAUDE.md`, append the contents instead of overwriting.

## Verifying it's working

After activation, you should see:

- One-line confirmations after tool calls instead of paragraphs.
- No "Here's what I just did:" summaries at end of turn.
- When a Salesforce skill writes a draft, you get something like `Draft staged on case 00123456` instead of the full draft content echoed back.

## Tuning

- If responses feel **too terse** (e.g. you can't tell what changed without reading the diff), edit `output-styles/terse.md` and soften the "no end-of-turn summary" rule.
- If a specific skill still echoes drafts despite the directive, that skill's own `SKILL.md` is overriding. You can either edit that skill directly or add a more specific rule in `CLAUDE.md`.

## What's intentionally NOT in v1

- **PostToolUse truncation hook** — would auto-truncate long Bash/Read outputs. Highest token savings but most maintenance. Add later if tool output (not assistant output) is the bottleneck.
- **Per-skill SKILL.md overrides** — out of scope for a portable plugin; do this at the skill level if needed.
