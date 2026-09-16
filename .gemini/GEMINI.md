# Global Agent Rules

## Model Allocation Strategy

Always follow this model allocation strategy when spawning subagents or choosing
a model for any task:

### Model Tiers (Version-Agnostic)

Always use the **latest available version** of each tier. If a newer version is
available (e.g., Gemini 3.9, 4.0 or Claude Opus 5, 6, etc.), prefer it over
older versions. The tier roles never change — only the version numbers evolve.

| Tier | Current Model | Role | Cost |
|---|---|---|---|
| **flash-low** | latest `gemini-*-flash-low` | Mechanical / read-only tasks | 💚 Cheapest |
| **flash-medium** | latest `gemini-*-flash-medium` | Primary coding & implementation | 🟡 Moderate |
| **opus-thinking** | latest `claude-opus-*-thinking` | Thinking, planning, review | 🆓 Free |

> **Version rule:** Always pick the highest available version number for each tier.
> For Gemini flash: prefer `gemini-3.9-flash-low` over `gemini-3.8-flash-low`.
> For Claude Opus: prefer `claude-opus-5-thinking` over `claude-opus-4-6-thinking`.
> The suffix (`-low`, `-medium`, `-high`, `-thinking`) defines the tier role.

---

### Fallback Chain

If a model from a higher tier is unavailable, fall back in this order:

```
claude-opus-*-thinking  →  (unavailable?)  →  latest gemini-*-flash-low
```

> When falling back from `claude-opus-*-thinking` to `gemini-*-flash-low`,
> the task still gets done — just with less reasoning depth. Accept this
> trade-off rather than blocking.

---

### Quick Reference

```
THINKING / PLANNING / REVIEW / DESIGN    → latest claude-opus-*-thinking   (free, use liberally)
  └─ fallback if unavailable             → latest gemini-*-flash-low

MAIN CODING / IMPLEMENTATION / EDITING   → latest gemini-*-flash-medium    (moderate cost)
SIMPLE / MECHANICAL / READ-ONLY TASKS    → latest gemini-*-flash-low       (cheapest)
```

---

### Rules

1. **`latest gemini-*-flash-low`** — Use for fast, mechanical, no-thinking tasks:
   - Reading files, grepping, listing directories
   - Running single commands
   - Simple lookups, formatting, copying values
   - Single-line edits

2. **`latest gemini-*-flash-medium`** — Use for all primary coding work:
   - Writing and editing code
   - Implementing features, fixing bugs
   - Writing tests, refactoring
   - Any task where output quality matters but deep reasoning isn't needed

3. **`latest claude-opus-*-thinking`** — Use freely for all thinking-heavy tasks (it's free):
   - Architecture and system design
   - Code review and design review
   - Planning what subagents to spawn and how
   - Root-cause debugging analysis
   - Security and risk assessment
   - Summarizing findings and writing reports
   - Any task where reasoning quality matters more than output volume
   - **Fallback:** If unavailable, use `latest gemini-*-flash-low` instead

---

### Subagent Orchestration Pattern

When spawning multiple subagents for a task:

```
[Plan / Design]       → latest claude-opus-*-thinking  (fallback: gemini-*-flash-low)
[Gather Context]      → latest gemini-*-flash-low
[Implement]           → latest gemini-*-flash-medium
[Review / QA]         → latest claude-opus-*-thinking  (fallback: gemini-*-flash-low)
[Apply Fixes]         → latest gemini-*-flash-medium
```

---

### Anti-Patterns to Avoid

- Do NOT use `flash-low` for complex implementation — it will produce shallow results
- Do NOT use `flash-medium` for trivial file reads — wastes tokens
- Do NOT use `claude-opus` as the primary code writer — it is a thinker, not a typist
- Do NOT avoid `claude-opus` out of cost fear — it is FREE, use it for all reviews and planning
- Do NOT stay on an older version when a newer one is available
- Do NOT block or error when `claude-opus` is unavailable — fall back to `gemini-*-flash-low`
