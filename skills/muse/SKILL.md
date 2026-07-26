---
name: muse
description: Query Meta's Muse Spark 1.1 (via Codex CLI + Meta provider, headless) from inside the session and render its answer in-chat — use when the user types /muse, says "ask muse", or wants Meta's model as a second opinion.
---

# Muse — Second Opinion In-Session

You are querying Meta's `muse-spark-1.1` through the local Codex CLI (Meta provider; API key from the MetaModelAPI integration) and rendering its answer inside the current Claude session.

## Instructions
1. Build the prompt from the skill args. Inline any code/context Muse needs — it does not share this session's context.
2. Prefix the prompt with: "You are an advisory second opinion. Do not modify any files or run commands; answer in text only."
3. Run via Bash (key loaded at runtime from the integration config — never hardcode it):
   `source ~/.nvm/nvm.sh; META_API_KEY=$(python3 -c "import json;print(json.load(open('/home/ft/Admin/integrations/MetaModelAPI/config.json'))['api_key'])") timeout 300 codex exec --skip-git-repo-check --model muse-spark-1.1 -c model_provider=meta "<prompt>" </dev/null 2>&1 | tail -n +2`
   Never pass `--yolo` — advisory-only.
4. Render verbatim under **Muse says:**; append **My take:** only when substantive. Note: Muse answers behind the Codex persona; the model is still muse-spark-1.1.
5. Heavy-reasoning questions need room — per the integration README, the model wants max_tokens ≥ 1500; keep prompts focused. On failure, report the exact error once.

## Output Format
```markdown
**Muse says:**
> [answer verbatim]

**My take:** [only when substantive]
```

## Rules
1. Advisory-only: never `--yolo`; prompt must say text-only.
2. API key always read from `/home/ft/Admin/integrations/MetaModelAPI/config.json` at runtime.
3. One query per invocation; render verbatim; 300s timeout.
