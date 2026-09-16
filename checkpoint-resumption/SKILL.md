---
name: checkpoint-resumption
description: >-
  Maintains an incremental `.agent/.progress.md` checkpoint ledger during
  long-running tasks to prevent re-reading context upon interruptions. Activate
  during multi-step refactors, batch file processing, long multi-file operations,
  or any task with more than ~5 sequential steps where interruption would be costly.
---

# Checkpoint & State Resumption

Use this skill during multi-step refactors, batch processing, or long multi-file operations.

## Protocol

### 1. State Tracking File
- Maintain a local state file at `.agent/.progress.md`.
- Create it at the start of the task if it does not exist.

### 2. Step Lifecycle
- **Before executing step N:** Read `.agent/.progress.md` to identify completed tasks.
- **After executing step N:** Write a single markdown bullet updating the state:
  ```markdown
  - [x] Step name: status (Target: `path/to/file`, Summary: 1 sentence)
  ```

### 3. Recovery Execution
- If interrupted, read `.agent/.progress.md` immediately upon resumption.
- Bypass all completed `[x]` steps without re-indexing source files or repeating completed calls.
- Resume from the first `[ ]` incomplete step.

## Progress File Format

```markdown
# Task: <short task name>
Started: <ISO timestamp>

## Steps
- [x] Step 1 name: done (Target: `src/foo.ts`, Summary: what was done)
- [x] Step 2 name: done (Target: `src/bar.ts`, Summary: what was done)
- [ ] Step 3 name: pending
- [ ] Step 4 name: pending
```

## Rules

- Always write the bullet **immediately after** completing a step — not in batches.
- Keep summaries to **one sentence** to avoid bloating the ledger.
- Never skip writing to `.progress.md` even if the step was trivial.
- On task completion, append a final line: `## ✅ Task Complete` with a timestamp.
