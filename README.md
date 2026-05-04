# Nujie
### An autonomous coordination agent for project teams

Most of the work in coordinating a team isn't the actual work.
It's chasing status updates, rebuilding context every week, and
manually figuring out who should do what — over and over again.

Nujie is an autonomous coordination agent that handles that layer
so you don't have to. You set the structure once. It runs from there.

Built inside a student consulting club at the University of Melbourne.
Named in loving memory of Anuj Sharma, VP of Strategy at MINT. (Thriving ans still very much alive)


---

## How it works

Nujie runs on three layers:

**Database layer** — stores everything the system needs. Built in
Notion, accessible via MCP.

**Agentic layer** — runs on a fixed Mon/Wed/Fri schedule via
OpenClaw. Reads the database, drafts actions, surfaces decisions.

**Human layer** — where all decisions live. The agent drafts.
You confirm. Nothing reaches the team without sign-off.

The layers are structurally separate by design. Swap the agent
model, the scheduler, or the database tool — the architecture holds.

---

## Workspace structure

```
🥬 Mint Project
│
├── 🔒 Command Centre          ← private, PL only
│   ├── Officer Profiles       ← quantified team metrics (DB)
│   ├── Weekly Dashboard       ← Monday review hub
│   ├── Agent Log              ← audit trail for all agent actions
│   ├── Learning Log           ← observations + pending profile updates
│   └── System Prompt          ← Claude Project instructions
│
└── 🎯 Task Board              ← shared with officers
    ├── Task Tracker           ← weekly tasks, status updates (DB)
    ├── Project Charter        ← all deliverables by week (DB)
    └── Meeting Log            ← client context, summaries (DB)
```

---

## The four databases

### Officer Profiles (private)
Fields: Name · Role · Skills · Work Style · Bandwidth % ·
On-time Rate % · Quality Rating (1–5) · Reliability ·
Tasks Completed · Flag

This is the allocation engine's source of truth. Private —
officers never see their own scores or each other's.

### Project Charter (shared, read-only)
Fields: Deliverable Name · Week · Due Date · Format ·
Required Skills · Complexity · Assigned To · Status ·
Brief · Client Context Tags

Required Skills options must match Officer Profiles Skills
exactly — this is the join condition the agent uses to match
tasks to people.

### Task Tracker (shared, officers edit)
Fields: Task · Assigned To · Status · Week · Due Date ·
Officer Note · Last Updated · Submitted On Time

Status flow: Not started → In progress → In Review → Done
Officers update this. The agent reads it on Wed and Fri.

### Meeting Log (shared)
Fields: Date · Meeting Type · Raw Notes · Summary ·
Key Decisions · Context Tags

Paste raw notes after each client meeting. Agent summarises
and syncs context tags back to the charter.

---

## The agent workflows

| File | Trigger | What it does |
|---|---|---|
| `monday_allocation.md` | Mon 8am | Reads charter + profiles, drafts assignments, sends to Telegram for approval |
| `wednesday_checkin.md` | Wed 2pm | Flags tasks with no update in 48h, posts to Notion |
| `friday_refinement.md` | Fri 6pm | Reads outcomes, proposes profile updates to Telegram |

All three prompts are in `/setup/`. Copy-paste into OpenClaw.

---

## Allocation logic

Default: split every deliverable into equal lanes — one per
person. Everyone does the same type of task divided by topic,
section, or slide.

Adjust down for lower-capacity members after drafting:
- Design-only members → visual assembly lane only
- Low-reliability members → smaller scope + 2-day buffer deadline
- High-commitment-elsewhere members → one bounded task max

All allocations are drafts. Nothing goes to the team without
explicit PL approval via Telegram.

---

## What makes it different

Allocation is based entirely on tracked metrics — calculated
from outcomes, not impressions. The system builds a picture of
how your team actually works and gets sharper every week without
you doing anything.

The agent also improves over time. Every submission writes back
into the profile layer via the Learning Log. Profiles update
with PL confirmation. The allocations compound.

---

## What you need

- [Notion](https://notion.so) (free)
- [OpenClaw](https://openclaw.ai) or any agentic scheduler
  with cron support
- Telegram bot token
- Notion MCP configured (`@notionhq/mcp`)

---

## Quickstart

**1. Duplicate the Notion template**
→ [Template link — coming soon]

**2. Fill in Officer Profiles**
Seed data: skills, work style, bandwidth estimate.
On-time rate starts at 100. Refines from week 1.

**3. Populate the Project Charter**
One row per deliverable. Required Skills must match
Officer Profiles Skills exactly.

**4. Add the three prompts to OpenClaw**
Copy from `/setup/`. Set Telegram bot token.
Configure Notion MCP with your database IDs.

**5. Approve the first Monday allocation**
Nujie sends a draft to Telegram. Review, adjust, confirm.
Nothing goes to the team until you do.

---

## Scaling it

**Larger organisations** — one agent per portfolio (projects,
events, media, sponsorships), each with its own database slice
and Mon/Wed/Fri rhythm. An orchestrating agent above them
aggregates to leadership via Telegram.

**Historical data** — MCP connections to Google Drive or other
storage let the agent reference past project outcomes when
making allocation decisions. Profiles compound across semesters,
not just within them.

**Passive status detection** — MCP connections to Canva, Figma,
Google Docs mean the agent can detect task progress without
officers manually updating status.

---

## Folder structure

```
nujie/
│
├── README.md
│
├── setup/
│   ├── monday_allocation.md
│   ├── wednesday_checkin.md
│   ├── friday_refinement.md
│   └── notion_schema.md
│
├── workspace/
│   ├── IDENTITY.md
│   ├── AGENTS.md
│   └── SOUL.md
│
└── notion-template/
    └── README.md
```

---

## Status

In testing. Open sourcing once stable.

Full write-up: [coming soon]
