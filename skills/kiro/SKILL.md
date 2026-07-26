---
name: kiro
description: Query Kiro (AWS `kiro-cli`, headless non-interactive) from inside the session and render its answer in-chat — use when the user types /kiro, says "ask kiro", or wants Kiro's second opinion (strong on AWS/spec-driven engineering questions).
---

# Kiro — Second Opinion In-Session

You are querying the local AWS Kiro CLI non-interactively and rendering its answer inside the current Claude session.

## Instructions
1. Build the prompt from the skill args. Inline any code/context Kiro needs — it does not share this session's context.
2. Prefix the prompt with: "You are an advisory second opinion. Do not modify any files or run commands; answer in text only."
3. Run via Bash:
   `timeout 300 kiro-cli chat --no-interactive "<prompt>" 2>/dev/null | sed 's/\x1b\[[0-9;?]*[a-zA-Z]//g;s/\x1b\][^\x07]*\x07//g' | grep -vE '^\s*$|Credits:'`
   Never pass `-a`/`--trust-all-tools` — advisory-only, no tool side effects.
4. Render verbatim under **Kiro says:**; append **My take:** only when substantive.
5. Kiro burns AWS credits per query (~0.03/short query) — keep prompts focused; on failure report the exact error once.

## Output Format
```markdown
**Kiro says:**
> [answer verbatim]

**My take:** [only when substantive]
```

## Rules
1. Advisory-only: never `-a`; prompt must say text-only.
2. One query per invocation; render verbatim; 300s timeout.
3. Kiro's opinion never overrides user decisions without their say-so.
