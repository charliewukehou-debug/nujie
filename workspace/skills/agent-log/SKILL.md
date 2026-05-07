---
name: agent-log
description: Audit trail writer — appends to Command Centre Agent log Execution table and routes errors to the Error log table
emoji: 🤖
---

# Agent log skill

Utility skill. Every other Nujie skill MUST call this at the end of its run — `log_execution` on success, `log_error` on failure. Never call this skill from itself.

## Target

- Notion page id: `<AGENT_LOG_PAGE_ID>` ("🤖 Agent log", inside Command Centre)
- Page contains two pre-built tables. Append rows to the correct one. Leave the existing em-dash placeholder row untouched.
  - **Execution log** — columns: Date | Time | Workflow | Action | Status | Output
  - **Error log** — columns: Date | Workflow | Error | Resolution

Append-only. Never edit or delete existing rows.

## Timezone

Australia/Sydney (AEST). Format Date as ISO `YYYY-MM-DD`. Format Time as 24h `HH:MM`.

## Operations

### `log_execution(workflow, action, status, output)`

Append one row to the **Execution log** table.

- `workflow` — skill name, e.g. `allocation`, `meeting-digest`, `weekly-report`
- `action` — one-line description of what was done, e.g. `Drafted Week 7 allocation`
- `status` — one of: `ok` | `skipped` | `partial`
- `output` — one-line summary of what was produced or written, e.g. `Sent draft to the PL; awaiting approval`

Row format:

| Date | Time | Workflow | Action | Status | Output |
|------|------|----------|--------|--------|--------|
| YYYY-MM-DD | HH:MM | [workflow] | [action] | [status] | [output] |

Output MUST be a single line. Multi-line summaries belong in the calling skill's own artefacts, not here.

### `log_error(workflow, error)`

Append one row to the **Error log** table, then send a Telegram alert.

- `workflow` — skill name
- `error` — short error message. Truncate to ~200 chars. Full stack stays in OpenClaw run logs at `~/.openclaw/cron/runs/{jobId}.jsonl`.

Row format:

| Date | Workflow | Error | Resolution |
|------|----------|-------|------------|
| YYYY-MM-DD | [workflow] | [error] | (blank) |

Resolution column is left blank for the PL to fill in manually.

Then send a Telegram message to the PL (chatId `<TELEGRAM_CHAT_ID>`) in this exact format:

`⚠️ [workflow] failed: [error]. Check Agent Log → Error log. 🦞`

## Failure of agent-log itself

If writing to the Agent log page fails, do NOT recurse into `log_error`. Send a Telegram alert to the PL (chatId `<TELEGRAM_CHAT_ID>`) only, in this exact format:

`⚠️ agent-log write failed: [error]. Run logs at ~/.openclaw/cron/runs/. 🦞`

## Hard rules

- Every Nujie skill calls `log_execution` once at the end of a successful run.
- Every Nujie skill calls `log_error` instead if anything throws.
- The agent-log skill never calls itself.
- Output field is always one line.
- Append-only. No edits, no deletes.
