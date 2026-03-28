---
name: deep-audit
description: |
  Run a deep project audit after a major milestone or before a release.
  Use for broad code, docs, and workflow consistency checks.
argument-hint: "[--no-fix]"
allowed-tools: Read Write Edit Bash Grep Glob
---

Run a deep audit of the project and produce a structured report.

Workflow:

1. Read `AGENTS.md`, roadmap files, active specs, and relevant architecture notes.
2. Inspect the repository state and identify the major modules and current milestone.
3. Check for drift across:
   - code vs roadmap
   - code vs active specs
   - code vs architecture docs
   - rules and commands vs actual project commands
4. If `--no-fix` is not present, fix small documentation drift directly.
5. Write a report under `docs/reports/` if that directory exists; otherwise summarize inline.

Report format:

- scope checked
- checks not run
- critical findings
- medium findings
- low findings
- fixes applied
- remaining risks
- recommended next step

Rules:

- Do not make broad code changes under the name of audit.
- Fix only small, high-confidence drift unless the user asked for full remediation.
