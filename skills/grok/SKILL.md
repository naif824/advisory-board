---
name: grok
description: Query Grok (local subscription-authed `grok` CLI, headless) from inside the session and render its answer in-chat — use when the user types /grok, says "ask grok", or wants a second model's opinion on code, a plan, or a finding.
---

# Grok — Second Opinion In-Session

You are querying the locally installed Grok CLI (`~/.local/bin/grok`, authed via Naif's SuperGrok subscription) and rendering its answer inside the current Claude session.

## Context
**Skill request:** grok — query the local Grok CLI (subscription-authed, headless `grok -p`) from inside a Claude Code session and render the answer in-chat, optionally with Claude's commentary on disagreements.

## Instructions
1. Build the prompt from the skill args (everything after `/grok`). If the args reference code, a file, or a finding from the conversation, inline the relevant snippet/context into the prompt so Grok gets a self-contained question — Grok does not share this session's context.
2. Prefix the prompt with: "You are an advisory second opinion. Do not modify any files or run commands; answer in text only." — Grok is advisory-only in this workflow (one-pen doctrine: Claude executes, Grok opines).
3. Run via Bash from the relevant project directory (or cwd):
   `timeout 300 grok -p "<prompt>" --output-format plain`
   Never pass `--always-approve` — headless tool auto-approval could let Grok execute or edit, which this workflow forbids.
4. Render the response in the final message under a **Grok says:** heading, quoted verbatim (trim CLI noise/spinner lines only).
5. If the question was about something Claude has a position on (a review, a plan, a diagnosis), append a short **My take:** section — agreements in one line, disagreements called out specifically with reasoning. Skip this section entirely for neutral factual queries.
6. On failure (non-zero exit, timeout, auth error): report the exact error and suggest checking `grok` login state (`grok` interactively) — do not retry more than once.

## Output Format
```markdown
**Grok says:**
> [Grok's answer verbatim]

**My take:** [only when substantive — agreement one-liner or specific disagreements]
```

## Rules
1. Advisory-only: never let Grok write, edit, or execute — no `--always-approve`, and the prompt must say text-only.
2. One query per invocation; don't re-ask or chain Grok calls unless the user asks.
3. Always render Grok's answer verbatim — no paraphrasing it into Claude's voice.
4. Timeout 300s; a hung call is reported, not silently retried.
5. Grok's opinions never override user decisions or Claude's committed findings without the user's say-so.
