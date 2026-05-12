---
name: allocation
description: Monday task allocation logic with per-person rules and approval gate
emoji: 📋
---

# Allocation skill

Use this every Monday when drafting the week's task allocation.

## Default approach — 6-way split
Divide every deliverable into 6 equal lanes, one per person. Everyone does the same type of task divided by topic, section, or slide. This applies to research weeks and execution weeks equally.

## Per-person rules

**Co-PL 1 & Co-PL 2** — always own:
- Strategic framing and problem definition
- Synthesis across research lanes
- All client-facing communications
- Final quality check on all outputs

**Officer A — high-stretch profile** — stretch them:
- Assign tasks slightly outside their comfort zone
- Best on independent end-to-end deliverables
- Do not co-own tasks with them
- Proactive — give them the most ambitious sixth

**Officer B — limited-bandwidth profile** — keep it bounded:
- This project is not their top priority — they have higher-priority commitments elsewhere
- Maximum ONE task per week
- Give them the most clearly scoped and interesting sixth
- High quality when engaged — keep tasks intellectually stimulating

**Officer C — low-reliability profile** — frame with presentation angle:
- Frame tasks as "you'll present this at the mid-review" where possible
- Always set internal deadline 2 days before actual due date
- Motivated by talking and presenting — use mid-review weeks as a carrot
- Build in a review buffer

**Officer D — visual-specialist profile** — visual only:
- If week is research or writing heavy: swap their lane for a visual or design task (digital audit, asset sourcing, slide formatting)
- If week is PPT: they take their sixth scoped to visual assembly only
- Never assign anything requiring judgment or written output
- Always set internal deadline 2 days before actual due date
- Always review output before it goes anywhere

## Deadline rules
- Officer C internal deadline = actual due date minus 2 days
- Officer D internal deadline = actual due date minus 2 days
- All others = actual due date

## Approval gate — non-negotiable
Every allocation draft must be sent to the PL via Telegram before anything is written to Notion or communicated to officers. Wait for explicit confirmation ("go", "approved", "yes"). Silence = wait. Never assume approval.

## After approval — closing steps
Once tasks are written to Notion:
1. Call `weekly-dashboard` `monday_refresh()` to populate the Command Centre Weekly Dashboard View — Officer status table, Deliverables, Monday actions log row.
2. If the Monday meeting raised observations or context shifts, route them to the Learning Log via `learning-log-writer` (`append_officer_observation` / `append_allocation_pattern` / `append_client_context` / `propose_profile_update` as appropriate).
3. Call `agent-log` `log_execution` with Workflow=`allocation`, Action=`Week [X] allocation`, Status=`ok`, Output=`[N] tasks written, [M] proposals raised`.

On any error during this flow, call `agent-log` `log_error` and stop — do not proceed past the failed step.

## Format for the PL's approval message
Send as a Telegram message in this format:

**Week [X] allocation draft**

- Co-PL 1: [task] — [one-line rationale]
- Co-PL 2: [task] — [one-line rationale]
- Officer A: [task] — [one-line rationale]
- Officer B: [task] — [one-line rationale]
- Officer C: [task] — [one-line rationale] (internal deadline: [date])
- Officer D: [task] — [one-line rationale] (internal deadline: [date])

Awaiting your approval before writing to Notion. 🦞
