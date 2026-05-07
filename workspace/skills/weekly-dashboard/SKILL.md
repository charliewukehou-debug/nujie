---
name: weekly-dashboard
description: Updates Command Centre Weekly Dashboard View on Mon/Wed/Fri ticks — officer status table, at-risk flags, OpenClaw actions log
emoji: 📊
---

# Weekly dashboard skill

Maintains the **Weekly Dashboard View** page in the Command Centre.

- Notion page id: `<WEEKLY_DASHBOARD_PAGE_ID>`
- Timezone: Australia/Sydney
- Week number: project Weeks 1–8. Read from Project Charter `collection://<PROJECT_CHARTER_DATA_SOURCE_ID>`, fall back to Task Tracker `collection://<TASK_TRACKER_DATA_SOURCE_ID>`.

This skill is called by sibling skills (`allocation`, `meeting-digest`, `weekly-report`) at their respective ticks. It writes to Notion only.

## Page sections (already pre-built — never recreate)
1. **This week at a glance** — three header lines: Current week, Deliverables due this week, At-risk tasks.
2. **Officer status** table — columns: Officer | Task this week | Status | Internal deadline | Flag. Rows in order: Officer A, Officer B, Officer C, Officer D, Co-PL 2, Co-PL 1.
3. **Decisions needed this week** — bulleted list.
4. **OpenClaw actions log (this week)** table — columns: Day | Action | Result. Rows: Monday, Wednesday, Friday.
5. **Notes** — free-form.

---

## Operation: `monday_refresh()`
Called by `allocation` after the PL approves the week's allocation.

1. Update **This week at a glance**:
   - Current week: `Week N`
   - Deliverables due this week: pull from Project Charter for the week
   - At-risk tasks: `—`
2. Replace each row in **Officer status**:
   - Task this week: from Task Tracker
   - Status: `🟢 Assigned`
   - Internal deadline: only filled for low-reliability and visual-specialist members (actual due date − 2 days). Others: `—`
   - Flag: `—`
3. Clear **Decisions needed** bullets, except any unresolved ones carried over from last week.
4. **OpenClaw actions log** — Monday row:
   - Action: `Allocation trigger`
   - Result: `Drafted + approved + N tasks written`
5. **OpenClaw actions log** — wipe Wednesday and Friday rows: Result = `—`.

### Format example — Officer status row (Monday)
| Officer A | Slide 4 — competitor benchmarking | 🟢 Assigned | — | — |
| Officer C | Section 2 research synthesis | 🟢 Assigned | 2026-05-13 | — |

---

## Operation: `wednesday_refresh(at_risk_list, meeting_flags)`
Called after `meeting-digest` + Wednesday daily task check.

1. Update **At-risk tasks** header line: comma-separated officer labels whose tasks are 🔴 Not started or 🟡 No update >48hrs. If none: `—`.
2. Update each row in **Officer status** — Status column flips to 🟢 / 🟡 / 🔴 using the same classification rules as `weekly-report` Wednesday format:
   - `✅ On track` — task updated in Notion in last 48hrs
   - `🟡 No update` — no Notion activity in 48hrs
   - `🔴 Not started` — task not opened since assignment
3. Append bullets to **Decisions needed** for any calls the PL must make (allocation impact flags from meeting-digest, at-risk escalations, etc.).
4. **OpenClaw actions log** — Wednesday row:
   - Action: `At-risk flag + meeting digest`
   - Result: `[N at-risk officers], [M meeting flags]`

### Format example — Wednesday actions log row
| Wednesday | At-risk flag + meeting digest | 2 at-risk officers, 1 meeting flag |

---

## Operation: `friday_refresh(weekly_report_summary)`
Called at end of Friday calibration.

1. Update each row in **Officer status** — Status column updates to `✅ Submitted` / `❌ Late` / etc. based on Friday calibration data.
2. **OpenClaw actions log** — Friday row:
   - Action: `Profile refinement proposal`
   - Result: `[N proposals raised, awaiting the PL]` or `No proposals this week`
3. Append a one-line summary to **Notes**:
   - Format: `Week N closed — on-time rate avg X%, quality avg Y.`

### Format example — Notes append
> Week 4 closed — on-time rate avg 83%, quality avg 3.6.

---

## Hard rules
- **Notion only.** Never send Telegram messages from this skill — that's the calling skill's job.
- **Append / update existing tables.** Never recreate, replace, or delete the page structure.
- **Propagate errors.** If a Notion write fails, raise it back to the calling skill so it can route through `agent-log`'s `log_error`. Do not swallow exceptions.
- **Log every successful refresh** to the Agent log Execution table via the `agent-log` skill:
  - Workflow: `weekly-dashboard`
  - Action: `[monday|wednesday|friday]_refresh week N`
  - Status: `ok`
  - Output: one-line summary of what changed
