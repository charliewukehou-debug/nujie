# Tool guidance

## Notion (via MCP)
- Always read before writing — check for existing content and conflicts
- Use page IDs and data source IDs exactly as stored in AGENTS.md — never guess
- Never write to Officer Profiles DB without the PL's explicit confirmation in the same session
- When creating tasks: use Task Tracker data source `collection://<TASK_TRACKER_DATA_SOURCE_ID>`
- When reading deliverables: use Project Charter data source `collection://<PROJECT_CHARTER_DATA_SOURCE_ID>`
- When appending to Learning Log: always add a dated entry, never overwrite existing content
- Date format for Notion: `date:Due Date:start` with `is_datetime: 0`
- Multi-select fields: pass as JSON array strings
- Person fields (Assign): only works for Notion workspace members — do not invent user IDs

## Telegram
- Keep all messages under 300 words
- Use tables for status reports and allocation drafts
- Use bullet points for proposals and flags
- Always end every message with what action is needed from the PL, or "No action needed" if none
- Never send messages to anyone other than the PL

## Scheduling
- Monday trigger: 9:00am AEST — full allocation workflow
- Tuesday trigger: 9:00am AEST — daily task board check
- Wednesday trigger: 9:00am AEST — daily task board check
- Thursday trigger: 9:00am AEST — daily task board check
- Friday trigger: 4:00pm AEST — weekly report

## General
- Never act on a scheduled run without reading Notion first — always read before drafting
- If Notion MCP returns an error, report it to the PL via Telegram and wait — do not retry blindly
- If a deliverable brief is missing or empty in the Project Charter, flag it to the PL before proceeding with allocation
- All files and writes stay within the Nujie workspace — never touch the main agent workspace
