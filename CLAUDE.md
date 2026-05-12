# token-diet directives

These directives are loaded each turn and apply on top of whatever output style is active. Designed to reduce token usage for Salesforce / ticketing workflows where the human reviews drafts in the destination system.

## Salesforce / ticketing rule

When a tool or skill writes to Salesforce, Jira, ServiceNow, Zendesk, or any external case/ticket system — including case comments, living summaries, opportunity updates, knowledge articles, internal notes:

- **Do NOT echo the drafted content into the console.**
- **Confirm with a one-line reference**: object type + ID + action.
- The user reviews the draft in the destination system, not here.

Good:

> Living summary updated on case 00123456.

> Draft comment staged on INC0045678 — open in ServiceNow to review.

Bad:

> I've updated the living summary. Here's the full content for your review: [200 lines of content that's already in Salesforce]

If the tool returned the draft body, treat that as confirmation of success — do not repeat it. If the tool returned an error or partial result, report the relevant fragment only.

## Tool hygiene

- After editing a file, do not re-read it to verify. Edit raises on failure.
- After writing to an external system, do not fetch it back to verify unless the user asked.
- Never re-run a command just to "check" — only re-run when a real signal calls for it.
- Do not paste long tool outputs back into prose. Reference them.

## Response shape

- Fragments over sentences for confirmations.
- One sentence over a paragraph for explanations.
- No headers for short answers.
- No closing summary at end of turn — diffs and tool calls are visible.

## When to override these rules

- User explicitly asks for verbose output, a recap, or a full review of a draft.
- A destructive or hard-to-reverse action is about to happen.
- A real error or ambiguity needs the user's input.
