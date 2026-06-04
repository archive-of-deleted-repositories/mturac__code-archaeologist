# /excavate

**Usage:** `/excavate [file]`

Run a full archaeology report on a file — combining git history timeline, why-this analysis on the most complex blocks, and bus factor risk.

## What it does

1. Runs `git-history` skill on the file to produce the evolution timeline
2. Identifies the top 3 largest inflection-point commits and runs `why-this` on the changed lines
3. Runs `bus-factor` scoped to just this file
4. Produces a single unified report

## Example

```
/excavate src/payments/stripe-webhook.ts
```

**Output (example)**

```
ARCHAEOLOGY REPORT: src/payments/stripe-webhook.ts
════════════════════════════════════════════════════

EVOLUTION TIMELINE
──────────────────
Created:     2022-03-01 by alice@co.com
Last touch:  2024-08-15 by bob@co.com
Total commits: 18  |  Authors: 3  |  Major rewrites: 2

Date         Author           Commit   Change      Inflection  Linked
2022-03-01   alice@co.com     a1b2c3   +180/−0     YES         —
2023-01-10   alice@co.com     d4e5f6   +210/−45    YES         #42
2024-08-15   bob@co.com       g7h8i9   +12/−3      no          #101

INFLECTION DEEP-DIVES
─────────────────────
[d4e5f6 — 2023-01-10] Stripe v3 migration (#42)
  Lines 88–102: HACK — dedup retried webhook events (Stripe SDK bug, temp)
  Lines 120–135: new signature verification — no known workaround signals

BUS FACTOR
──────────
Top owner: alice@co.com — 78% of commits
Bus factor: 1
Risk: CRITICAL
Last active: 2024-08-15 (294 days ago)
```

## Notes
- Pass a directory to run excavate on all files in it (summarized view).
- Combine with `/bus-factor` for a repo-wide risk picture.
