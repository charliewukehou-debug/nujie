## The Design Thinking Behind Nujie

This document is not a how-to guide. It is the full design thesis 
behind Nujie — the thinking that went into the architecture, the 
principles that shaped the decisions, and the vision for where 
this goes beyond its current implementation.

Nujie as it exists today is layer one. This document is the rest.

---

## Where it started

I was co-leading a consulting project. Six people, one client, 
eight weeks. Going in I expected the hard part to be the strategy.

The hard part was everything else.

Every week the same overhead repeated: figure out who was doing 
what, chase the people who hadn't started, reassemble context 
from the last client meeting, manually divide the deliverables 
across the team. None of it required judgment. It just required 
attention — and it was quietly consuming the attention that 
should have been going toward the actual work.

The frustration wasn't the volume. It was the type. I was 
spending time on things that shouldn't have required my time 
at all.

That distinction matters. The problem isn't that leaders are 
slow. It's that they're operating on the wrong layer. 
Coordination overhead isn't hard work. It's the wrong work. 
And every hour spent on it is an hour not spent on the 
thinking that actually moves things forward.

---

## The real bottleneck

The standard response to coordination overhead is better tools. 
A new project management app. A shared dashboard. A weekly 
standup.

These help at the margin. They don't solve the problem because 
they misidentify it.

The bottleneck isn't that information is hard to find. It's 
that information doesn't push anything forward on its own. 
You learn that someone is stronger at research than you 
initially thought. A team member's availability shifts. The 
client pivots their focus after a meeting. You absorb all of 
this. But none of it updates the system automatically. The 
charter stays the same. The allocations stay the same. The 
accumulated knowledge sits in your head, disconnected from 
the operational layer that is supposed to act on it.

Every week you start from scratch. Not because you forgot — 
because there was no mechanism to make the learning compound.

This is the problem Nujie is designed to solve.

---

## The core insight — making work legible to AI

AI agents are only as useful as what they can read.

Most human work is invisible to them. It lives in people's 
heads, in scattered files, in informal understandings between 
teammates. The meeting happened but the decisions weren't 
written down. The task was assigned verbally. The quality of 
the output was judged by feel. None of this is machine-readable. 
None of it can be acted on by an agent.

Before you can automate coordination, you have to solve a 
more fundamental problem: you have to make the work legible.

Nujie is, at its core, a translation layer. It converts the 
implicit, scattered way a team operates — who is doing what, 
how well, for whom, toward what deadline — into a structured 
form that an agent can parse, act on, and learn from.

The agent isn't the breakthrough. The translation schema is.

---

## The three-layer architecture

Once the work is legible, the architecture becomes obvious.

**Database layer — the translation schema**

This is where human work gets converted into something an 
agent can read. Not a filing system. A schema.

- Project charter: every deliverable mapped by week, format, 
  required skills, complexity, and brief
- Officer profiles: objective performance metrics calculated 
  from observed outcomes — bandwidth, on-time rate, quality 
  rating, reliability signal
- Task tracker: real-time status updated by the team, 
  readable by the agent at any point
- Meeting log: client context that accumulates over time 
  rather than resetting after each call

Every field is designed to be machine-readable. The profile 
metrics are numbers, not impressions. The charter briefs are 
structured, not freeform. The status options are a fixed 
taxonomy. The schema is the intelligence.

**Agentic layer — the coordination loop**

Once the work is legible, the loop can close itself.

On a fixed schedule — no manual trigger — the agent reads 
the database and acts. Monday: reads charter and officer 
profiles, drafts task assignments. Tuesday through Thursday, 
9am: checks task statuses, flags anything with no update, 
reports to the project leader. Friday: reads submission 
outcomes, proposes profile updates based on what it observed.

The agent connects to external tools through dedicated APIs — 
the Notion API for database reads and writes, the Telegram 
Bot API for summaries to the project leader, with additional 
connectors (calendars, meeting transcripts) slotted in as the 
system grows. Each connection is a declared tool. Adding a new 
integration never requires rebuilding the pipeline.

Critically, the agent improves over time. Every submission 
writes back into the profile layer. Every week the agent 
has a more accurate picture of the team. The allocations 
get sharper without anyone feeding it information manually.

**Human layer — the approval gate**

Every draft action sits with a human before it executes. 
The agent reads and proposes. The human confirms, adjusts, 
or overrides. Nothing reaches the team without explicit 
sign-off.

This constraint is non-negotiable. It is what keeps the 
system trustworthy. The moment an agent makes decisions 
unilaterally, you lose the ability to trust its outputs 
without verification — which brings back exactly the 
cognitive overhead you were trying to eliminate.

The human layer is not a limitation. It is the design.

---

## The fairness dimension

One consequence of this architecture that I did not fully 
anticipate: it makes task allocation easier to inspect.

In most teams, who gets which task is a function of who the 
manager likes, who is most visible, who asked most recently, 
or who the manager happens to think of first. It is subjective 
by default — even when the manager is actively trying to be 
fair. Blind spots are structural. They don't go away with 
good intentions.

Nujie does not remove judgment from allocation. It makes the
judgment more explicit. Instead of relying only on memory or
impression, the project leader can see the signals that shaped
the allocation: who submitted on time, who delivered strong
work, who has bandwidth this week, and where the system is
still uncertain.

The incentive structure this creates is subtle but important.
Officers should not have to manage perception as the main way
their work is recognised. Good performance should leave a trail
that can be noticed later, even when someone was quiet in the
meeting or not top of mind that week. The system does not make
allocation perfectly objective. It makes the reasoning easier
to challenge, correct, and improve.

Officer profiles are private. Team members never see their 
own scores or each other's. From their perspective they 
simply receive tasks matched to their strengths. The 
intelligence is invisible. That is intentional.

---

## The closed loop

The end state Nujie is designed toward is a team that 
operates as a closed loop — where the work generates the 
data that improves the system that coordinates the work.

Meetings get transcribed natively — Notion already does 
this. Transcripts feed the meeting log automatically. 
The agent reads the log and updates the charter's context 
tags without anyone summarising manually.

As MCP integrations get added for Canva, Figma, and Google Docs, 
task status updates become passive. The agent detects that 
a file was opened and marks the task in progress. It detects 
that a document was finalised and marks it submitted. The 
officer never has to report their own progress because the 
system already knows.

The onboarding cost for this is effectively zero. Officers 
don't download anything or create accounts. A notification 
points them to a filtered view in any browser. Their entire 
interaction with the system is a status tap once or twice 
a week — and eventually even that disappears.

You set the structure once. The system runs from there.

---

## On adaptability

The most important architectural decision in Nujie is one 
that is easy to overlook: the three layers are structurally 
independent.

The database layer does not know or care what agent reads 
from it. The agent does not know or care what database it 
reads from, as long as the schema is consistent. The human 
layer does not know or care how the agent produces its 
drafts.

This means any layer can be replaced without rebuilding 
the others. The agent model improves — point a new model 
at the same database. A better agentic framework emerges — 
swap the scheduler, keep the schema. The team moves to a 
different tool — migrate the data, keep the prompts.

This is the same principle that makes good software 
architecture durable: separation of concerns. It applies 
equally to AI systems. The organisations that build their 
AI infrastructure on entangled, platform-specific 
automations will find themselves rebuilding from scratch 
every time the tooling landscape shifts — which in AI 
right now means every few months.

The organisations that separate the data from the logic 
from the interface will compound instead.

---

## Scaling the architecture

Nujie as it exists today is a single agent coordinating 
a single team. The architecture is designed to scale 
differently.

In a larger organisation — a club running projects, events, 
media, and sponsorships simultaneously — a single agent 
trying to coordinate everything becomes the same bottleneck 
the system was built to eliminate. The answer is not a 
bigger agent. It is narrower agents, each with a defined 
scope, and an orchestrating layer above them that 
aggregates.

One agent owns the projects portfolio. It knows the project 
charters, the project officers, the client context. Another 
owns events. Another owns media. Each runs its own 
coordination cycle against its own database slice. Above 
them sits an orchestrating agent that reads the outputs of 
all three — a summary layer that surfaces cross-portfolio 
conflicts, resource clashes, and organisation-wide progress 
to leadership, without any of them needing to dig into 
the individual portfolios.

Because Nujie communicates through Telegram, the human 
layer is not inherently limited to one person. The same 
approval interface that currently sits with a single 
project leader could extend to a management layer — 
executives, presidents, senior leads — who want visibility 
into team progress without being pulled into operational 
detail. A weekly summary lands in a channel. A flag gets 
escalated. An allocation gets confirmed with a single 
reply. The agent doesn't change. Only the number of people 
it reports to does.

---

## The institutional memory problem

Every year most organisations reset.

New people join who don't know the history. Old processes 
get reinvented because nobody documented why the previous 
approach failed. Data accumulates in folders nobody opens 
because there is no system that makes it useful.

This is not a storage problem. The data exists. It is a 
legibility problem. Ten years of meeting notes, project 
outcomes, team compositions, client feedback — none of it 
is structured in a way that an agent can read and act on.

Nujie's profile refinement loop is the beginning of an 
answer. Every submission writes back into the profile 
layer. Every project cycle leaves behind a richer 
operational picture than the one before it. The next 
project leader inherits not just the tools but the 
accumulated read on how the team operates.

Extend that logic to the organisation level — with MCP 
connections to historical drives, with standardised 
schemas that persist across years rather than resetting 
each semester — and the compounding effect becomes 
significant. The allocation decisions in year three 
are informed by what worked in year one. The agent's 
picture of the organisation deepens with every cycle. 
New members onboard into a system that already knows 
what good looks like.

This is what a compounding organisation looks like. 
Not one that adds a new folder each year. One that learns.

---

## What Nujie is not

Nujie does not make strategic decisions. It does not tell 
you what the client needs, what your recommendations 
should be, or how to frame a solution. That work belongs 
entirely to humans.

Nujie does not evaluate performance subjectively. It tracks 
observable signals — on-time rate, quality rating, 
bandwidth — and proposes profile updates that a human 
confirms before any write happens.

Nujie is not a product. It is a pattern. The specific 
tools — Notion, OpenClaw, Telegram — are interchangeable. 
The pattern is not: a translation layer that makes work 
legible, an agentic loop that runs on that data, and a 
human gate that keeps decisions where they belong.

---

## The bigger shift

There is a lot of conversation right now about vibe coding — 
the idea that natural language prompts are enough to build 
software. That is true, and it matters. But it is a 
transitional observation, not the final one.

The more durable shift is this: the organisations that 
compound the most value from AI will not be the ones using 
the best models. They will be the ones that have designed 
the best structures around AI.

Vibe coding gets you a prototype. Agent orchestration gets 
you a system. The difference is not the quality of the 
output — it is whether the intelligence is embedded in a 
one-off interaction or in an architecture that runs, learns, 
and improves continuously without requiring human attention 
to keep it alive.

The productivity gains from a better writing tool are linear. 
The gains from restructuring how a team coordinates are 
compounding.

The future of AI in organisations is not about saving time. 
It is about saving time from what was never worth doing in 
the first place — and redirecting that reclaimed capacity 
toward the problems that actually require human judgment.

Nujie is not finished. It is not even close. But I hope it is built
on the right principle: AI's job is not to think for you, but to
make sure that when you do think, it is about something worth
thinking about.

---

*Built at the University of Melbourne, 2026.*
*Named in memory of Anuj Sharma, VP of Strategy at MINT.*
