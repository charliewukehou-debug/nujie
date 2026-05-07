---
name: learning-log-writer
description: Centralised appender for the Command Centre Learning Log — officer observations, allocation patterns, client context, profile-update proposals, meeting digests
emoji: 🧠
---

# Learning log writer skill

Single writer for the "🧠 Learning Log" Notion page. All sibling skills (allocation, weekly-report, meeting-digest) route Learning Log appends through this skill.

## Target page
- Notion page id: `<LEARNING_LOG_PAGE_ID>`
- Title: "🧠 Learning Log" (inside Command Centre)

## Existing sections (do not rename, do not recreate)
1. Officer observations — one sub-section per officer (Officer A, Officer B, Officer C, Officer D, Co-PL 1, Co-PL 2). Each holds a `Date | Observation | Action taken` table.
2. Allocation pattern notes — `Date | Pattern observed | Applied from` table.
3. Client and project context updates — `Date | Update | Source` table.
4. Pending profile updates awaiting the PL's confirmation — `Date | Officer | Proposed change | Reason | Status` table.
5. Meeting digests — free-form dated entries. Create on first use only if missing, directly above "Pending profile updates awaiting the PL's confirmation".

## Date format
Human-readable, matching existing entries: `4 May 2026`. Timezone Australia/Sydney.

## Hard rules
- Append-only. Never edit or delete existing rows or sections.
- If the target table or section is missing, call `agent-log` `log_error` and abort. The only exception is creating "Meeting digests" on first use.
- Never write to Officer Profiles. Profile changes are surfaced via `propose_profile_update` and require the PL's "go" / "approved" / "yes" / "confirmed".
- After every successful append, call `agent-log` `log_execution`: Workflow = `learning-log-writer`, Action = `[operation_name] for [target]`, Status = `ok`, Output = one-line summary.
- On any Notion error, call `agent-log` `log_error` and re-raise so the calling skill can also handle it.

---

## Operations

### `append_officer_observation(officer, observation, action_taken)`
- **officer**: one of `Officer A` / `Officer B` / `Officer C` / `Officer D` / `Co-PL 1` / `Co-PL 2`
- **observation**: short factual note
- **action_taken**: what Nujie or the PL did about it (or `None` if logged for pattern tracking)
- **Target**: Officer observations → `[officer]` sub-section → `Date | Observation | Action taken` table
- **Row appended**: `today | observation | action_taken`
- **Use case**: weekly-report or meeting-digest spots a one-off comment about that officer.

### `append_allocation_pattern(pattern, applied_from)`
- **pattern**: 1–3 sentence observation about how splits should change going forward
- **applied_from**: e.g. `Week 7 allocation`, `Client context`, `Allocation principles`
- **Target**: Allocation pattern notes → `Date | Pattern observed | Applied from` table
- **Row appended**: `today | pattern | applied_from`

### `append_client_context(update, source)`
- **update**: 1–2 sentence statement about client/project context
- **source**: e.g. `the PL`, `Client meeting Week 4`
- **Target**: Client and project context updates → `Date | Update | Source` table
- **Row appended**: `today | update | source`

### `propose_profile_update(officer, proposed_change, reason)`
- **officer**: one of `Officer A` / `Officer B` / `Officer C` / `Officer D` / `Co-PL 1` / `Co-PL 2`
- **proposed_change**: specific metric change (e.g. `Officer A quality 3 → 4`)
- **reason**: short justification rooted in observed signal
- **Target**: Pending profile updates awaiting the PL's confirmation → `Date | Officer | Proposed change | Reason | Status` table
- **Row appended**: `today | officer | proposed_change | reason | Awaiting the PL`
- This is the ONLY way Nujie surfaces metric proposals. Never edit Officer Profiles directly.
- After appending, the calling skill (typically weekly-report) sends the PL a Telegram message naming the proposal.

### `append_meeting_digest(meeting_name, date, summary, action_items, allocation_flags)`
- **meeting_name**: from Meeting Log Name field
- **date**: ISO date of the meeting
- **summary**: 3–5 sentence paragraph
- **action_items**: bulleted list (string) extracted from Meeting Log Action Items field + page body
- **allocation_flags**: bulleted list (string) of any task-allocation impacts surfaced. If none, write `No allocation impact this week.`
- **Target**: Meeting digests section, free-form dated block
- **Block appended** (in order):
  - H3 heading: `[meeting_name] — [date formatted as "4 May 2026"]`
  - Paragraph: summary
  - Bold label `Action items` followed by action_items
  - Bold label `Allocation flags` followed by allocation_flags
- If "Meeting digests" section does not exist, create it once as an H2 heading directly above "Pending profile updates awaiting the PL's confirmation", then append the block. Log this section creation via `agent-log` `log_execution` with Action = `created Meeting digests section`.
