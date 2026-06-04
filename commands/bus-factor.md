# /bus-factor

**Usage:** `/bus-factor [path]`

Show knowledge concentration risk for all tracked files under `path`. Surfaces critical single-owner files, stale dead zones, and a summary risk breakdown.

## Example

```
/bus-factor src/payments/
```

**Output (example)**

```
BUS FACTOR REPORT: src/payments/
══════════════════════════════════

File                              Top Owner %  Bus Factor  Last Active   Risk
──────────────────────────────── ──────────── ─────────── ───────────── ─────────
src/payments/invoice.ts           94%          1           2023-11-02    CRITICAL
src/payments/stripe-webhook.ts    78%          1           2024-08-15    CRITICAL
src/payments/refund.ts            67%          2           2024-10-18    HIGH
src/payments/tax-calculator.ts    52%          2           2025-01-09    MEDIUM
src/payments/currency.ts          40%          3           2025-03-22    LOW
src/payments/index.ts             35%          4           2025-04-01    LOW

Summary
───────
Analyzed:             6 files
CRITICAL:             2 (33%)
HIGH:                 1 (17%)
STALE (>180 days):    2 files
Dead Knowledge Zones: 1 (CRITICAL + STALE)

Recommended actions
───────────────────
- src/payments/invoice.ts — schedule knowledge transfer; 94% owned by one author
- src/payments/stripe-webhook.ts — add inline documentation; last active 294 days ago
```

## Notes
- Run without a path to scan the entire repo (may be slow on large repos).
- Merge commits are excluded from counts.
- Only data is reported — no quality judgments about contributors.
