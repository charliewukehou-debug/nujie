# Heartbeat — Nujie

Route by current day in Australia/Sydney. Full rhythm and formats in `AGENTS.md` (Weekly rhythm). Follow that strictly; this file is just the tick checklist.

## Monday — Weekly allocation (9:00am AEST)
1. Read Learning Log (`<LEARNING_LOG_PAGE_ID>`) — note officer signals since last week.
2. Read Officer Profiles (`collection://<OFFICER_PROFILES_DATA_SOURCE_ID>`) — flag any rating changes.
3. Read Project Charter (`collection://<PROJECT_CHARTER_DATA_SOURCE_ID>`) for this week's deliverable.
4. If brief is vague, propose 2–3 breakdowns to the PL first (AGENTS.md Step 2).
5. Draft the 6-way split and send to the PL in the format from AGENTS.md Step 4.
6. **Stop.** Do not write to Notion until the PL replies "go" / "approved" / "yes" / "confirmed".
7. After Notion writes succeed, call `weekly-dashboard` `monday_refresh()` to populate the Command Centre Weekly Dashboard View for the new week.
8. Call `agent-log` `log_execution` (Workflow=`allocation`, Status=`ok`, Output=`[N] tasks written, [M] proposals raised`).

## Tue / Thu — Daily task check (9:00am AEST)
1. Read Task Tracker (`collection://<TASK_TRACKER_DATA_SOURCE_ID>`) — all active tasks this week.
2. For each task, classify: 🔴 Not started (flag) / 🟡 In progress / 🔵 In Review / ✅ Done.
3. Send daily summary per AGENTS.md format. End with: "No action needed from you unless flagged above. 🦞"
4. Do not nudge officers directly.
5. Call `agent-log` `log_execution` (Workflow=`daily-check`, Status=`ok`, Output=one-line).

## Wed — Meeting digest + Daily task check (9:00am AEST)
1. **First**, run `meeting-digest` skill:
   - Query Meeting Log (`collection://<MEETING_LOG_DATA_SOURCE_ID>`) for current ISO week (Mon–Sun, AEST), Status=Done. Read each match's page body.
   - Send the PL the Telegram digest: per-meeting summary + Action items + **Allocation impact** flags.
   - Append to Learning Log "Meeting digests" via `learning-log-writer`.
   - If no meeting → send `No meeting logged this week — skipping digest. 🦞` and skip Learning Log write.
   - Never edit Task Tracker. Allocation suggestions wait for the PL's "go" and apply next Monday.
2. **Then**, run the daily task check (same as Tue/Thu).
3. Call `weekly-dashboard` `wednesday_refresh(at_risk_list, meeting_flags)` — At-risk header, Officer status flags, Decisions needed, Wed actions log row.
4. Call `agent-log` `log_execution` (Workflow=`wednesday-tick`, Status=`ok`, Output=one-line).

## Friday — Weekly report (4:00pm AEST)
1. Compute on-time rate per officer (Done or In Review by Due Date).
2. Parse the PL's quality scores from Telegram this week (e.g. "Officer A 4, Officer C 3"). Update rolling average.
3. Read this week's Learning Log entries for flags.
4. For each metric proposal, call `learning-log-writer` `propose_profile_update` to append to "Pending profile updates awaiting the PL's confirmation".
5. Send Friday report per AGENTS.md.
6. **Stop.** Do not update officer profiles until the PL replies "go" / "confirmed" / "approved".
7. Call `weekly-dashboard` `friday_refresh(summary)` — Officer status, Friday actions log row, Notes append.
8. Call `agent-log` `log_execution` (Workflow=`weekly-report`, Status=`ok`, Output=one-line).

## Signal-strength rule (every tick)
- One-off comment → Learning Log only.
- Pattern across 2+ weeks → propose metric update.
- Explicit PL statement → propose immediately.
- Never write to profiles without explicit confirmation.

## Audit trail (every tick)
- Success → `agent-log` `log_execution` appends to Execution log table.
- Error → `agent-log` `log_error` appends to Error log table AND sends the PL a Telegram alert.

## If nothing needs attention
Reply `HEARTBEAT_OK`.
