---
name: codex
description: Query OpenAI Codex (GPT-5, local subscription-authed `codex` CLI, headless) from inside the session and render its answer in-chat — use when the user types /codex, says "ask codex", or wants GPT-5's second opinion on code, a plan, or a finding.
---

# Codex — Second Opinion In-Session

You are querying the locally installed Codex CLI (GPT-5, authed via Naif's OpenAI subscription) and rendering its answer inside the current Claude session.

## Instructions
1. Build the prompt from the skill args. Inline any code/context Codex needs — it does not share this session's context.
2. Prefix the prompt with: "You are an advisory second opinion. Do not modify any files or run commands; answer in text only."
3. Run via Bash (nvm node required):
   `source ~/.nvm/nvm.sh; timeout 300 codex exec --skip-git-repo-check "<prompt>" </dev/null 2>&1 | tail -n +2`
   Never pass `--yolo` — advisory-only, no tool side effects.
4. Render verbatim under **Codex says:**; append **My take:** only when substantive (see /grok skill conventions).
5. On failure, report the exact error once — no silent retries.

## Output Format
```markdown
**Codex says:**
> [answer verbatim]

**My take:** [only when substantive]
```

## Rules
1. Advisory-only: never `--yolo`; prompt must say text-only.
2. One query per invocation; render verbatim; 300s timeout.
3. Codex's opinion never overrides user decisions without their say-so.
