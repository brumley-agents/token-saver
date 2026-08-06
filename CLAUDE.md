# token-diet directives

These directives are loaded each turn regardless of output style. They cover Salesforce / ticketing specifics that a general-purpose output style can't know. General terseness (response shape, tool-use hygiene, when to break terseness) lives in `output-styles/terse.md` — activate it each session; these directives don't repeat it.

## Salesforce write rule (drafts and living summaries)

When a skill or tool **writes** to Salesforce — specifically private draft emails, case comments, or living summary field updates (Problem, Cause, Validation, Solution):

- **Do NOT echo the drafted content into the console.**
- **Confirm with a one-line reference**: object type + case number + action.
- The user reviews the draft in Salesforce, not here.

Good:

> Living summary updated on case 00123456.

> Draft email staged on case 00123456 — review in Salesforce to send.

Bad:

> I've updated the living summary. Here's the full content for your review: [content that's already in Salesforce]

If the tool returned the draft body as confirmation of success — do not repeat it. If the tool returned an error or partial result, report the relevant fragment only.

## Salesforce read rule (investigation pulls)

When a skill pulls data **from** Salesforce to investigate a case — email history, case metadata, attachments, org details — do not dump the raw API output into prose. The skill is responsible for presenting the result. Reference counts or key fields only if a summary is needed.

## sfdc-aifind output rule

After `sfdc-aifind` completes — whether run inline via the Skill tool or as a background subagent via the Agent tool — confirm with **one line only**:

> aifind complete — Case XXXXXXXX. Report at `downloads/case-XXXXXXXX/aifind_report.md`.

**Do not echo the report body, re-summarize findings, or produce a "Case Summary" block.** The report is saved to file; the engineer reads it there. This overrides any "display inline" instruction in the skill's own SKILL.md.

If the file write failed (permissions denied), add one sentence: "File write was blocked — full report is in the subagent output above." Nothing more.

## Jira and external ticket generation rule

Jira issues, Confluence pages, and other tickets with no pre-creation review step in their own system are **not** covered by the silent-confirmation rule in `terse.md` Mode 1 — that rule is for systems where the human already reviews the write in its destination (Salesforce drafts, case comments). For Jira/Confluence: **always show the full generated content for user review before creating.** These are not auto-committed; the user must verify before the record exists. After creation, confirm with one line as usual.
