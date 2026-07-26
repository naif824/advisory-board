---
name: deepseek
description: Query DeepSeek v4 Flash (via local Kilo CLI, headless) from inside the session and render its answer in-chat — use when the user types /deepseek or /ds, says "ask deepseek", or wants DeepSeek's second opinion.
---

# DeepSeek — Second Opinion In-Session

You are querying `deepseek/deepseek-v4-flash` through the local Kilo CLI and rendering its answer inside the current Claude session.

## Instructions
1. Build the prompt from the skill args. Inline any code/context DeepSeek needs — it does not share this session's context.
2. Prefix the prompt with: "You are an advisory second opinion. Do not modify any files or run commands; answer in text only."
3. Run via Bash (nvm node required):
   `source ~/.nvm/nvm.sh; timeout 300 kilo run -m deepseek/deepseek-v4-flash "<prompt>" 2>&1 | sed 's/\x1b\[[0-9;?]*[a-zA-Z]//g' | grep -vE '^\s*$|^> code'`
   Kilo's permission config allows tools — the text-only prompt prefix is the guard; if the output shows tool activity, stop and report it.
4. Render verbatim under **DeepSeek says:**; append **My take:** only when substantive.
5. On failure, report the exact error once — no silent retries.

## Output Format
```markdown
**DeepSeek says:**
> [answer verbatim]

**My take:** [only when substantive]
```

## Rules
1. Advisory-only: prompt must say text-only; abort and report if DeepSeek attempts tool use.
2. One query per invocation; render verbatim; 300s timeout.
3. DeepSeek's opinion never overrides user decisions without their say-so.
