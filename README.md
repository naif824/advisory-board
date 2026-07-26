# Advisory Board

**Query other AI models for a second opinion from inside a Claude Code session — without leaving the chat.**

Claude stays the executor (one pen). Each board member is *advisory only*: it answers in text,
never edits files or runs commands. Claude renders the answer verbatim and adds its own take when
it disagrees. Every model runs through its own locally-installed CLI in headless one-shot mode,
using your existing subscription — no per-token API cost.

## The board

| Skill | Model | Under the hood |
|---|---|---|
| `/grok <q>` | Grok (SuperGrok) | `grok -p` |
| `/codex <q>` | GPT-5 (OpenAI) | `codex exec --skip-git-repo-check` |
| `/muse <q>` | Muse Spark 1.1 (Meta) | `codex exec --model muse-spark-1.1 -c model_provider=meta` |
| `/deepseek <q>` | DeepSeek v4 Flash | `kilo run -m deepseek/deepseek-v4-flash` |
| `/kiro <q>` | Kiro (AWS) | `kiro-cli chat --no-interactive` |

## How it works

1. You type `/grok <question>` (or `ask grok …`) in any Claude Code session.
2. Claude inlines whatever context the model needs — the board member can't see your chat.
3. Claude shells out to that model's CLI in headless mode and captures the answer.
4. The answer is rendered verbatim under a **"<Model> says:"** heading, with a short **"My take:"**
   only when Claude substantively agrees or disagrees.

## Guardrails (every skill)

- **Advisory only** — no yolo / trust-all-tools / auto-approve flags; the prompt itself forbids file
  edits and command execution. One pen stays with Claude.
- **One query per invocation**, rendered verbatim, 300s timeout, failures reported (not silently retried).
- A board member's opinion never overrides your decisions or Claude's committed findings without your say-so.

## Install

Drop each folder under `~/.claude/skills/` (Claude Code auto-discovers them; no restart needed).
Each model's CLI must already be installed and authenticated on the machine.

## Requirements

- `grok`, `codex`, `kilo`, `kiro-cli` installed and logged in via their respective subscriptions.
- `/muse` reads its Meta API key at runtime from a local integration config — adjust the path in
  `skills/muse/SKILL.md` to your environment.
