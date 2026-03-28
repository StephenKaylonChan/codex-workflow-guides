---
name: catchup
description: |
  Rebuild project context after a cleared context or a new session.
  Use when the user asks to continue, catch up, or restore working context.
allowed-tools: Read Bash Glob
---

Rebuild enough context to continue work safely.

Workflow:

1. Read `AGENTS.md`.
2. Read `.agents/session-notes.md` if it exists.
3. Read `docs/roadmap/README.md` if it exists.
4. Read active spec files if they exist.
5. Inspect git status and recent commits.
6. Summarize:
   - current branch and repo state
   - current roadmap phase
   - active spec or task
   - modified files
   - the most likely next step

Output format:

- current state
- active phase
- active slice or spec
- modified files
- next step

Rules:

- Keep the summary short and execution-oriented.
- Do not restate the whole project if only one active slice matters.
- End with a single recommended next action.
