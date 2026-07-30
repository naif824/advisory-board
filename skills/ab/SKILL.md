---
name: ab
description: Query the whole Advisory Board at once — fan a question out to Grok, GPT-5 (Codex), Muse, DeepSeek, Kiro, and Kimi K3, and render all answers together. Use when the user types /ab, says "ask the board" / "advisory board", or wants multiple models' second opinions in one shot.
---

# Advisory Board — Fan-Out Second Opinions

You are querying several locally-installed AI CLIs at once (advisory-only) and rendering all their answers together in the current Claude session. This is the batch form of the single-model skills (`/grok`, `/codex`, `/muse`, `/deepseek`, `/kiro`, `/kimi`).

## Instructions
1. Parse the args. If they begin with a comma/space-separated list of member names (e.g. `grok,codex describe X`), query only that subset; otherwise query **all six**: grok, codex, muse, deepseek, kiro, kimi.
2. Build one shared prompt from the question. Inline any code/context the board needs — the members do not share this session's context. Prefix it with: "You are an advisory second opinion. Do not modify any files or run commands; answer in text only."
3. Run the selected members **in parallel** — issue their Bash calls together in a single message so they execute concurrently. Never pass yolo / auto-approve / trust-all-tools flags. Per-member commands:
   - **grok:** `timeout 300 grok -p "<prompt>" --output-format plain`
   - **codex:** `source ~/.nvm/nvm.sh; timeout 300 codex exec --skip-git-repo-check "<prompt>" </dev/null 2>&1 | tail -n +2`
   - **muse:** `source ~/.nvm/nvm.sh; META_API_KEY=$(python3 -c "import json;print(json.load(open('/home/ft/Admin/integrations/MetaModelAPI/config.json'))['api_key'])") timeout 300 codex exec --skip-git-repo-check --model muse-spark-1.1 -c model_provider=meta "<prompt>" </dev/null 2>&1 | tail -n +2`
   - **deepseek:** `source ~/.nvm/nvm.sh; timeout 300 kilo run -m deepseek/deepseek-v4-flash "<prompt>" 2>&1 | sed 's/\x1b\[[0-9;?]*[a-zA-Z]//g' | grep -vE '^\s*$|^> code'`
   - **kiro:** `timeout 300 kiro-cli chat --no-interactive "<prompt>" 2>/dev/null | sed 's/\x1b\[[0-9;?]*[a-zA-Z]//g;s/\x1b\][^\x07]*\x07//g' | grep -vE '^\s*$|Credits:'`
   - **kimi:** `timeout 300 ~/.kimi-code/bin/kimi --prompt "<prompt>" --model moonshotai/kimi-k3 2>&1 | sed 's/\x1b\[[0-9;?]*[a-zA-Z]//g' | grep -vE '^\s*$|^→|^✱|^✗'`
4. Render each member's reply verbatim under its own `### <Model> says:` heading (trim CLI banner/spinner noise only). If a member errors or times out, show a one-line `_(failed: <reason>)_` for it and keep the rest — never abort the whole board for one failure.
5. Finish with a **Board synthesis:** — where the members agree, where they diverge (name who said what), and your own recommendation. Keep it short; the value is the contrast between views.

## Output Format
```markdown
### Grok says:
> ...
### Codex (GPT-5) says:
> ...
### Muse says:
> ...
### DeepSeek says:
> ...
### Kiro says:
> ...
### Kimi K3 says:
> ...

**Board synthesis:** consensus in one line; disagreements called out by name; your recommendation.
```

## Rules
1. Advisory-only: no member may edit files or run commands; never pass yolo/trust flags; the prompt says text-only.
2. Run members concurrently; one failure never blocks the others.
3. Render every answer verbatim — no paraphrasing into Claude's voice; the synthesis is the only place you speak.
4. 300s timeout per member. Kiro bills AWS credits per call; Kimi bills Moonshot balance — respect that when the board is queried repeatedly.
5. The board's opinions never override the user's decisions or Claude's committed findings without the user's say-so.
