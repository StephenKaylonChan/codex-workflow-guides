---
name: handoff
description: |
  Save structured session state before leaving or clearing context.
  Use when the user is stopping mid-task, switching context, or ending a long session.
allowed-tools: Read Write Edit Bash Grep Glob
---

Create a structured handoff so the next session can resume quickly.

Workflow:

1. Read `AGENTS.md`, git status, and recent commits.
2. Update `.agents/session-notes.md` with:
   - what was completed
   - what is still in progress
   - key decisions
   - modified files
   - exact next step
   - known risks or blockers
3. If roadmap or spec status clearly changed, sync them before finishing.
4. Summarize how the next session should resume.

Output format:

- completed
- in progress
- key decisions
- modified files
- exact next step
- known risks or blockers

Rules:

- Do not invent progress that is not reflected in files or commands.
- Do not auto-commit unless the user or project instructions explicitly require it.
- Prefer precise next steps over long narrative summaries.
- Keep the next step concrete enough that a new session can start immediately.
