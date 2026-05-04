# Nujie
### An autonomous coordination agent for project teams

Most of the work in coordinating a team isn't the actual work.
It's chasing status updates, rebuilding context every week, and
manually figuring out who should do what — over and over again.

Nujie is an autonomous coordination agent that handles that layer
so you don't have to. You set the structure once. It runs from there.

Built inside a student consulting club at the University of Melbourne.
Named in loving memory of Anuj Sharma, VP of Strategy at MINT. (Thriving and still very much alive)


---

## How it works

Nujie runs on three layers:

**Database layer** — stores everything the system needs. Built in
Notion, accessible via MCP.

**Agentic layer** — runs on a fixed weekly schedule via OpenClaw:
allocation on Monday, daily check-ins Tue–Thu, weekly report on
Friday. Reads the database, drafts actions, surfaces decisions.

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
Officers update this. The agent reads it daily Tue–Thu and again
on Fri.

### Meeting Log (shared)
Fields: Date · Meeting Type · Raw Notes · Summary ·
Key Decisions · Context Tags

Paste raw notes after each client meeting. Agent summarises
and syncs context tags back to the charter.

---

## The agent workflows

The cron just wakes the agent with a one-line ping. The actual
routing logic lives in `workspace/HEARTBEAT.md` (which day is it?)
and `workspace/AGENTS.md` (full rhythm + formats). This keeps the
scheduler dumb and the agent's behaviour version-controlled.

| Trigger (cron) | Wake message | What the agent does |
|---|---|---|
| Mon–Thu 9am AEST | `Check the HEARTBEAT.md and run your scheduled tasks for today.` | **Mon:** drafts the 6-way allocation, sends to Telegram for approval. **Tue/Wed/Thu:** reads Task Tracker, sends daily status summary. |
| Fri 4pm AEST | `Run the Friday calibration report.` | Compiles on-time + quality, proposes profile updates to Telegram. |

The cron config template lives at `setup/cron_jobs.example.json` —
drop it into `~/.openclaw/cron/jobs.json` and replace the
Telegram placeholders.

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

**4. Wire up OpenClaw**
Copy `setup/cron_jobs.example.json` into `~/.openclaw/cron/jobs.json`,
fill in your Telegram account ID + chat ID, and configure the
Notion MCP with your database IDs. The agent's behaviour comes
from `workspace/*.md` — no per-day prompt files needed.

**5. Approve the first Monday allocation**
Nujie sends a draft to Telegram. Review, adjust, confirm.
Nothing goes to the team until you do.

---

## Scaling it

**Larger organisations** — one agent per portfolio (projects,
events, media, sponsorships), each with its own database slice
and weekly rhythm (Mon allocation + daily checks + Fri report).
An orchestrating agent above them aggregates to leadership via
Telegram.

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
├── VISION.md
│
├── setup/
│   └── cron_jobs.example.json
│
├── workspace/
│   ├── AGENTS.md
│   ├── HEARTBEAT.md
│   ├── IDENTITY.md
│   ├── SOUL.md
│   └── TOOLS.md
│
└── notion-template/
    └── README.md
```

---

