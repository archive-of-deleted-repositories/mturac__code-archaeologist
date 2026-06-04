# /timeline

**Usage:** `/timeline [file]`

Show the full chronological evolution of a file — every commit that touched it, major rewrites highlighted, and linked issues/PRs surfaced.

## Example

```
/timeline src/billing/invoice-generator.ts
```

**Output (example)**

```
EVOLUTION TIMELINE: src/billing/invoice-generator.ts
══════════════════════════════════════════════════════

Date         Author              Commit   +Lines  −Lines  Inflection  Links
──────────── ─────────────────── ──────── ─────── ─────── ─────────── ──────
2021-09-04   alice@co.com        a0b1c2   +312    −0      YES (birth) —
2022-02-18   alice@co.com        b2c3d4   +88     −12     no          #14
2022-07-30   bob@co.com          c4d5e6   +201    −198    YES         #31
2023-03-15   carol@co.com        d6e7f8   +15     −8      no          #55
2024-01-22   bob@co.com          e8f9a0   +6      −180    YES         #78
2024-09-10   dave@co.com         f0a1b2   +22     −4      no          —

Summary
───────
First commit:   2021-09-04
Last commit:    2024-09-10
Total commits:  6
Authors:        4
Major rewrites: 3 (flagged above)
Linked items:   #14, #31, #55, #78

Quiet periods
─────────────
- 2022-07-30 → 2023-03-15: 228 days with no changes
- 2024-01-22 → 2024-09-10: 231 days with no changes
```

## Notes
- Inflection = commit changed more than 50 lines total.
- Use `/excavate` to add a why-this deep-dive on each inflection point.
