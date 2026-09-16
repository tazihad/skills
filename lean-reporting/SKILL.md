---
name: lean-reporting
description: >-
  Enforces strict length limits, concise tool-call receipts, and exception-only
  verbose logs. Activate to suppress conversational fluff, prevent oversized
  output tokens, and keep responses tight and scannable.
---

# Lean Reporting & Output Constraints

Use this skill to suppress conversational fluff and prevent oversized output tokens.

## Output Standards

### 1. Tool Invocation Feedback
- Output single-line receipts:
  ```
  [ACTION]: <tool_name> | [TARGET]: <file/resource> | [STATUS]: OK/FAIL
  ```
- Suppress full stdout/stderr dumps unless exit code != 0.

### 2. Response Caps
- Hard limit: Default summary responses must stay under 150 words.
- Disallow long-form narrative explanations, introductory meta-statements, or conversational bookends.
- No openers like "Sure!", "Great!", "I'll now proceed to..."
- No closers like "Let me know if you need anything else!"

### 3. Exception Handling
- Expand output to detailed diagnostic traces **only** when an unrecoverable failure occurs.
- For all other cases: keep it silent and compact.
