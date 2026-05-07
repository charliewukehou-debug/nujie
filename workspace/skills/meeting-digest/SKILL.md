---
name: meeting-digest
description: Wednesday meeting summary that surfaces action items and flags allocation impacts
emoji: 📝
---

# Meeting digest skill

Use this every Wednesday morning, called from the Wednesday branch of HEARTBEAT.md. It runs **before** the daily task check so any allocation flags surface in time for the PL's response cycle.

## Inputs to read from Notion

**Meeting Log** — `collection://<MEETING_LOG_DATA_SOURCE_ID>`
- Schema: Name (title), Date (date), Type (select: "Client call" / "Internal sync" / "PL check-in" / "Presentation"), Status (Scheduled / Done / Cancelled), Attendees (people), Action Items (text), Tags (multi-select).
- Filter: Date in current ISO week (Mon–Sun, Australia/Sydney) AND Status = "Done".
- For every matching entry, also fetch the page body. Action Items field is short; full notes / pasted transcripts often live in the body.
- If multiple matches, summarise each in turn in the order they occurred.

**Task Tracker** — `collection://<TASK_TRACKER_DATA_SOURCE_ID>`
- Read this week's active tasks so the digest can cross-reference what was discussed against what is currently allocated.

**Officer profiles** — `collection://<OFFICER_PROFILES_DATA_SOURCE_ID>`
- Read only if the meeting touched on a specific officer's bandwidth, performance, or context.

## Steps

1. Resolve the current ISO week number (Australia/Sydney) and Mon–Sun date range.
2. Query Meeting Log with the filter above. If zero results, jump to the **Empty week** branch.
3. For each matching meeting, fetch its page body. Extract Action Items from the field and from the body.
4. Pull this week's Task Tracker rows. For each meeting, identify any topic that maps to an existing task or to an officer currently allocated.
5. Identify allocation impacts. An allocation impact is anything that would change next Monday's lanes: officer bandwidth changes (exam clash, travel, illness), client direction shifts (new pillar, scope cut, pivot), deadline moves, or quality concerns raised by the PLs in-meeting.
6. Compose the Telegram digest in the format below. Send to the PL's chatId `<TELEGRAM_CHAT_ID>` via the existing Telegram delivery.
7. Append a dated entry to the Learning Log via the `learning-log-writer` skill (page id `<LEARNING_LOG_PAGE_ID>`, section "Meeting digests").
8. Append an Execution log row via the `agent-log` skill.

## Telegram digest format

Send to chatId `<TELEGRAM_CHAT_ID>` in this exact format:

**Wednesday meeting digest — Week [X]**

**[Meeting Name]** — [Type] — [Date]
[3–5 sentence summary, no fluff.]

Action items:
- [item]
- [item]
- [item]

(repeat the block above for each meeting, in chronological order)

**Allocation impact**
- [officer or pillar] — [one-line implication and suggested next-Monday adjustment]
- [officer or pillar] — [one-line implication and suggested next-Monday adjustment]

(if nothing impacts allocation, replace the bullets with a single line: `No allocation impact this week.`)

Awaiting your direction before changing any task allocations. 🦞

## Learning Log entry format

Append under the "Meeting digests" section of page `<LEARNING_LOG_PAGE_ID>` via `learning-log-writer`. One entry per meeting:

- Date: [YYYY-MM-DD]
- Meeting: [Name] ([Type])
- Summary: [3–5 sentence summary, same as Telegram]
- Action items: [bullet list]
- Allocation flags raised: [bullet list, or "none"]

## Agent log entry

Append one Execution log row via the `agent-log` skill:
- Workflow: `meeting-digest`
- Action: `Wed week [X] meeting digest`
- Status: `ok`
- Output: `Summarised [N] meetings, [M] allocation flags raised`

## Empty week branch

If the Meeting Log query returns zero rows for the current ISO week:
1. Send a single Telegram line to the PL: `No meeting logged this week — skipping digest. 🦞`
2. Append one Execution log row via `agent-log`:
   - Workflow: `meeting-digest`
   - Action: `Wed week [X] meeting digest`
   - Status: `skipped`
   - Output: `No Status=Done meetings in current ISO week`
3. Do not write to the Learning Log.

## Hard rules — non-negotiable

- **Never write to Task Tracker.** Allocation changes are SUGGESTIONS only. The PL must reply "go" / "approved" / "yes" before any allocation edit happens, and that edit happens via the existing `allocation` skill on the next Monday.
- **Never write to Officer Profiles directly.** Any officer-related signal goes via the signal-strength rule in `weekly-report/SKILL.md`:
  - One-off → Learning Log only.
  - Pattern across 2+ weeks → propose update.
  - Explicit PL statement → propose immediately.
- **Never ask the PL open questions.** Binary approval only. Silence = wait.
- **Always end the Telegram digest with 🦞.**

## Error handling

On any error (Notion API failure, missing IDs, permission errors, Telegram send failure):
1. Append one row to the Agent Log **Error log** table via the `agent-log` skill, including the error message and the step it occurred on.
2. Send the PL a short Telegram alert: `meeting-digest failed: [one-line error]. Check Agent Log → Error log.`
3. Do not retry automatically. Do not write partial output to the Learning Log.
