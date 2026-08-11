# token-saver

Claude Code plugin to reduce token consumption. Designed for Salesforce / ticketing workflows where drafts are reviewed in the destination system, not in the console.

## What it does

- **Terse output style** — no preambles, no end-of-turn summaries, no progress narration, no draft echoes.
- **Salesforce / ticketing directive** — when a tool writes to SFDC (or any external case/ticket system), confirm with a one-line reference instead of pasting the draft back.
- **Tool hygiene** — no speculative re-checks, no pasting raw tool output back into prose, prefer Edit over Write.

## Install

This repo is a self-hosted plugin marketplace (one plugin: itself). From Claude Code:

```
/plugin marketplace add brumley-agents/token-saver
/plugin install token-saver@token-saver
```

That's it — no manual file copying. The Salesforce/ticketing directive loads automatically via a `SessionStart` hook (`hooks/hooks.json` + `hooks/sfdc-rules.md`), and the terse output style is discovered automatically from `output-styles/terse.md`.

The terse style isn't on by default — it's deliberately scoped that way, since it changes *all* responses, not just the Salesforce write path. Turn it on per-session with:

```
/output-style terse
```

or set it as your default in `~/.claude/settings.json`:

```json
{ "outputStyle": "terse" }
```

### Note on `CLAUDE.md`

The root `CLAUDE.md` in this repo is **not** loaded by the plugin system (Claude Code plugins don't auto-load a root `CLAUDE.md` — only skills/agents/hooks/output-styles). It's kept here only for reference and for anyone who prefers the old manual-import path (`@` importing it directly from `~/.claude/CLAUDE.md`). `hooks/sfdc-rules.md` is the canonical, always-loaded copy once this is installed as a plugin — keep the two in sync if you edit the directive.

## Measured savings

Numbers below are pulled from real files on disk, not estimated — using ~4 chars/token as the conversion.

**Fixed cost per session:** `hooks/sfdc-rules.md` (2,751 bytes) + `output-styles/terse.md` (3,187 bytes) ≈ 1,480 tokens. This loads into context every turn (cached after the first turn in a session via prompt caching, so it's a one-time hit per session, not a per-turn one).

**Per-invocation savings**, from real case artifacts this plugin's rules suppress from being echoed:

| Suppressed content | Size on disk | ≈ tokens saved |
|---|---|---|
| Living summary echo | 2,097 bytes | ~520 |
| aifind report echo (typical case) | 4,372 bytes | ~1,090 |
| aifind report echo (large case) | 12,237 bytes | ~3,060 |

A single suppressed aifind report echo pays for the entire fixed cost of loading both directive files for the session — in one turn. That's before counting anything `terse.md` saves across the rest of the session (no preambles, no closers, no progress narration, no end-of-turn recaps).

**What this doesn't cover:** these are content-size savings confirmed against real artifacts, not a transcript-verified study of whether every skill obeys the directive on every turn. If a specific skill's own `SKILL.md` overrides it and still echoes content, see Tuning below.

## Verifying it's working

After activation, you should see:

- One-line confirmations after tool calls instead of paragraphs.
- No "Here's what I just did:" summaries at end of turn.
- When a Salesforce skill writes a draft, you get something like `Draft staged on case 00123456` instead of the full draft content echoed back.

## Tuning

- If responses feel **too terse** (e.g. you can't tell what changed without reading the diff), edit `output-styles/terse.md` and soften the "no end-of-turn summary" rule.
- If a specific skill still echoes drafts despite the directive, that skill's own `SKILL.md` is overriding. You can either edit that skill directly or add a more specific rule in `hooks/sfdc-rules.md`.

## What's intentionally NOT in v1

- **PostToolUse truncation hook** — would auto-truncate long Bash/Read outputs. Highest token savings but most maintenance. Add later if tool output (not assistant output) is the bottleneck.
- **Per-skill SKILL.md overrides** — out of scope for a portable plugin; do this at the skill level if needed.
