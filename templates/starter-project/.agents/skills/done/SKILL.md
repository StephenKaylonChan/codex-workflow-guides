---
name: done
description: |
  Close out a completed feature slice by verifying code quality and syncing project state.
  Use when a task is finished and ready for review or commit.
argument-hint: "[what was finished]"
allowed-tools: Read Write Edit Bash Glob Grep
---

Close out a completed slice before commit.

Workflow:

1. Identify the slice that was just completed.
2. Summarize verification status:
   - what ran
   - what did not run
   - key boundary checks
   - remaining risk
3. Ensure roadmap or spec status is updated if applicable.
4. Ensure session notes reflect the new state if the work is not fully complete.
5. Produce a final close-out summary with:
   - completion status
   - verification status
   - review status
   - synced docs
   - next recommended action

Output format:

- completion status
- verification status
- review status
- synced docs
- remaining risk
- next recommended action

Rules:

- Do not mark a task done if verification is still unclear.
- Do not skip roadmap or spec updates when the slice changes project state.
- If review did not run, state that explicitly instead of implying completion.
