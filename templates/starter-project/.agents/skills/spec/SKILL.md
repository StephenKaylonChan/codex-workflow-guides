---
name: spec
description: |
  Turn a multi-step discussion into a durable implementation spec.
  Use when a feature will span multiple commits, sessions, or decision points.
argument-hint: "[feature-name]"
allowed-tools: Read Write Edit Bash Glob Grep
---

Create or update a spec file under `docs/specs/`.

Workflow:

1. Determine the spec filename from the argument or the current topic.
2. Create `docs/specs/` if it does not exist.
3. Write or update a spec with these sections:
   - problem
   - scope
   - out of scope
   - affected modules or files
   - implementation approach
   - validation plan
   - open questions
4. If the user has already made decisions, write them clearly instead of preserving ambiguity.
5. End with the recommended first implementation slice.

Output format:

- spec file
- scope summary
- open questions
- first implementation slice

Rules:

- A spec is an implementation entry point, not a meeting transcript.
- Keep it actionable.
- If critical decisions are still unresolved, label them as open questions.
- Use stable section names so later sessions can scan the spec quickly.
