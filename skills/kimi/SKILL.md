---
name: kimi
description: Query Kimi K3 (Moonshot's 2.8T flagship, 1M context, via paid Moonshot API) for a second opinion from inside the session and render its answer in-chat — use when the user types /kimi, says "ask kimi", or wants K3's take. Also joins the /ab advisory board.
---

# Kimi K3 — Second Opinion In-Session

You are querying Moonshot's Kimi K3 (2.8T MoE, 1M context, reasoning model) via the official Kimi Code CLI and rendering its answer inside the current Claude session. Unlike the subscription board members, this one **bills per token** from Naif's Moonshot balance.

## Instructions
1. Build the prompt from the skill args. Inline any code/context K3 needs — it does not share this session's context.
2. Prefix the prompt with: "You are an advisory second opinion. Do not modify any files or run commands; answer in text only."
3. Run via the Kimi Code CLI (official Moonshot tool, OAuth auth):
   `~/.kimi-code/bin/kimi --prompt "<prompt>" --model moonshotai/kimi-k3 2>&1 | grep -vE '^\s*$|^→|^✱|^✗'`
   K3 always reasons; keep prompts focused to avoid unnecessary token burn (and billing).
4. Render the response verbatim under a **Kimi K3 says:** heading; append **My take:** only when substantive (agreements one-liner or specific disagreements).
5. On failure (auth error, network error, or billing issue): report the exact error once. To check account status or re-authenticate, run: `~/.kimi-code/bin/kimi login`.

## Output Format
```markdown
**Kimi K3 says:**
> [answer verbatim]

**My take:** [only when substantive]
```

## Rules
1. Advisory-only: K3 never edits files or runs commands; the prompt says text-only.
2. **Costs real money** (~$3/1M in, $15/1M out from Naif's balance) — one query per invocation, don't loop or re-ask without the user's say-so.
3. Render verbatim; the synthesis/take is the only place you speak.
4. K3's opinion never overrides the user's decisions or Claude's committed findings without their say-so.
