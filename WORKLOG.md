# Advisory Board — Work Log

Append-only, Riyadh-timestamped. Newest entries at the bottom.

## [2026-07-26 15:15 +03] Repo created + published

Packaged the five in-session second-opinion skills (`/grok`, `/codex`, `/muse`, `/deepseek`, `/kiro`) into `skills/` with a README explaining the one-pen / advisory-only model. Pushed public to `naif824/advisory-board`. Each skill shells out to a locally-installed CLI in headless mode on an existing subscription; answers render verbatim, Claude adds a take only on disagreement.

## [2026-07-26 15:15 +03] Added `/ab` — whole-board fan-out

New `skills/ab/SKILL.md`: `/ab <question>` fans a single question out to all five members **in parallel**, renders each answer verbatim under its own heading, then a **Board synthesis** (agreement / divergence by name / recommendation). Subset form `/ab grok,codex <q>`. Fault-tolerant — one member failing shows `(failed: …)` and never aborts the board. Kiro bills AWS credits per call, noted in the skill. README table updated with the `/ab` row.

## [2026-07-26 15:15 +03] Live test — all five green

Ran `/ab "most underrated productivity habit for a software engineer?"`. All five responded; strong consensus (write-it-down to kill context-reconstruction cost) at different task moments. Cosmetic-only observations: Codex/Muse print a banner block (trimmed by `tail -n +2`); Muse emits a harmless `Model metadata not found` stderr warning but answers correctly. No functional issues — `/ab` confirmed working end-to-end.
