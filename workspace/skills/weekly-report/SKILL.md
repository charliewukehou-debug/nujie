---
name: weekly-report
description: Wednesday status reports and Friday calibration summaries with signal strength rules
emoji: 📊
---

# Weekly report skill

## Wednesday status report format
Send to the PL via Telegram in this exact format:

**Wednesday check — Week [X]**

| Officer | Task | Status | Flag |
|---------|------|--------|------|
| Co-PL 1 | [task] | ✅ On track | — |
| Co-PL 2 | [task] | ✅ On track | — |
| Officer A | [task] | ✅ On track | — |
| Officer B | [task] | 🟡 No update | At-risk |
| Officer C | [task] | 🟡 No update | At-risk |
| Officer D | [task] | 🔴 Not started | Flag |

Status key:
- ✅ On track — task updated in Notion in last 48hrs
- 🟡 No update — no Notion activity in 48hrs
- 🔴 Not started — task not opened since assignment

Awaiting your direction before nudging any officer. 🦞

After sending the Wednesday Telegram, call `weekly-dashboard` `wednesday_refresh(at_risk_list, meeting_flags)` to update the dashboard, then call `agent-log` `log_execution` (Workflow=`weekly-report`, Action=`Wed status check week [X]`, Status=`ok`, Output=one-line).

---

## Friday calibration format
Send to the PL via Telegram in this exact format:

**Friday calibration — Week [X]**

**Officer A**
- Submitted: yes/no | On time: yes/no | Quality score: [PL's score]/5
- Rolling average: Quality [X.X] | On-time [X%]
- Proposed change: [none / specific metric update with reasoning]

**Officer B**
- Submitted: yes/no | On time: yes/no | Quality score: [PL's score]/5
- Rolling average: Quality [X.X] | On-time [X%]
- Proposed change: [none / specific metric update with reasoning]

**Officer C**
- Submitted: yes/no | On time: yes/no | Quality score: [PL's score]/5
- Rolling average: Quality [X.X] | On-time [X%]
- Proposed change: [none / specific metric update with reasoning]

**Officer D**
- Submitted: yes/no | On time: yes/no | Quality score: [PL's score]/5
- Rolling average: Quality [X.X] | On-time [X%]
- Proposed change: [none / specific metric update with reasoning]

Awaiting your confirmation before updating any officer profiles. 🦞

For each proposed metric change, call `learning-log-writer` `propose_profile_update(officer, proposed_change, reason)` BEFORE sending the Telegram, so the Pending profile updates table reflects what's awaiting the PL. Then after sending, call `weekly-dashboard` `friday_refresh(weekly_report_summary)` and `agent-log` `log_execution` (Workflow=`weekly-report`, Action=`Friday calibration week [X]`, Status=`ok`, Output=one-line).

---

## Signal strength rules
- One-off comment from the PL → log to Learning Log only, no profile change
- Same pattern observed across 2+ weeks → propose metric update
- Explicit PL statement e.g. "their quality has improved" → propose update immediately
- Never write to profiles without the PL typing "go", "confirmed", or "approved"
