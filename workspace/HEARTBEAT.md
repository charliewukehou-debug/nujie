# Heartbeat — Nujie

Route by current day in Australia/Sydney. Full rhythm and formats in `AGENTS.md` (Weekly rhythm). Follow that strictly; this file is just the tick checklist.

## Monday — Weekly allocation (9:00am AEST)
1. Read Learning Log (`<LEARNING_LOG_PAGE_ID>`) — note officer signals since last week.
2. Read Officer Profiles (`collection://<OFFICER_PROFILES_DATA_SOURCE_ID>`) — flag any rating changes.
3. Read Project Charter (`collection://<PROJECT_CHARTER_DATA_SOURCE_ID>`) for this week's deliverable.
4. If brief is vague, propose 2–3 breakdowns to the PL first (AGENTS.md Step 2).
5. Draft the 6-way split and send to the PL in the format from AGENTS.md Step 4.
6. **Stop.** Do not write to Notion until the PL replies "go" / "approved" / "yes" / "confirmed".

## Tue / Wed / Thu — Daily task check (9:00am AEST)
1. Read Task Tracker (`collection://<TASK_TRACKER_DATA_SOURCE_ID>`) — all active tasks this week.
2. For each task, classify: 🔴 Not started (flag) / 🟡 In progress / 🔵 In Review / ✅ Done.
3. Send daily summary per AGENTS.md format. End with: "No action needed from you unless flagged above. 🦞"
4. Do not nudge officers directly.

## Friday — Weekly report (4:00pm AEST)
1. Compute on-time rate per officer (Done or In Review by Due Date).
2. Parse the PL's quality scores from Telegram this week (e.g. "Officer A 4, Officer C 3"). Update rolling average.
3. Read this week's Learning Log entries for flags.
4. Send Friday report per AGENTS.md Step 3.
5. **Stop.** Do not update officer profiles until the PL replies "go" / "confirmed" / "approved".

## Signal-strength rule (every tick)
- One-off comment → Learning Log only.
- Pattern across 2+ weeks → propose metric update.
- Explicit PL statement → propose immediately.
- Never write to profiles without explicit confirmation.

## If nothing needs attention
Reply `HEARTBEAT_OK`.
