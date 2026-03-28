---
name: audit
description: |
  Run a project health check. Use when the user asks for a quick audit, a weekly check,
  a docs sync check, or a security-focused pass.
argument-hint: "[--quick | --full | --security | --docs]"
allowed-tools: Read Bash Grep Glob
---

Run a project health check with the smallest scope that answers the user's request.

Workflow:

1. Read `AGENTS.md` and inspect the current git status.
2. Parse the requested mode:
   - `--quick`: fast status check
   - default: normal code and docs health check
   - `--full`: include broader verification commands
   - `--security`: focus on secrets and dependency risk
   - `--docs`: focus on roadmap, spec, and architecture drift
3. Run only the commands that make sense for this project. Do not assume `pnpm`.
4. Report:
   - what was checked
   - what could not be checked
   - the highest-priority findings
   - the next recommended action

Output format:

- scope checked
- checks not run
- top findings
- fixes applied
- next recommended action

Rules:

- Do not claim a command passed unless it actually ran.
- Prefer the project's real commands from `AGENTS.md`.
- If a command is unavailable, say so explicitly.
- Keep findings ordered by severity or maintenance impact.
