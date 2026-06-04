---
name: bus-factor
description: Assess knowledge concentration risk across a codebase path — who owns what, which files are dangerously siloed, and what has gone stale.
version: 0.1.0
triggers:
  - "bus factor"
  - "knowledge concentration"
  - "who owns this"
  - "knowledge risk"
  - "single point of knowledge"
---

# bus-factor

Quantify knowledge concentration risk per file and surface siloed or stale areas of the codebase.

## Steps

1. **Accept input** — a directory path or file glob (e.g., `src/`, `lib/**/*.ts`). Default to the current repo root if none given.

2. **Enumerate files** — list all tracked files under the path:
   ```bash
   git ls-files <path>
   ```
   Skip binary files and lockfiles (`*.lock`, `*.sum`, `*.snap`, `package-lock.json`, `yarn.lock`).

3. **Count commits per author per file** — for each file:
   ```bash
   git log --format='%ae' -- <file> | sort | uniq -c | sort -rn
   ```
   Also capture the date of the most recent commit:
   ```bash
   git log -1 --format='%ad' --date=short -- <file>
   ```

4. **Calculate bus factor score** — for each file:
   - Total commits = sum of all author commit counts
   - Author share % = (author_commits / total_commits) × 100
   - Bus factor = number of authors who individually hold >20% share
   - Risk level:
     - `CRITICAL` — one author >80% share OR bus_factor = 1
     - `HIGH` — bus_factor = 2, top author >60%
     - `MEDIUM` — bus_factor = 2–3
     - `LOW` — bus_factor ≥ 4

5. **Flag stale files** — mark any file where `last_commit_date` is older than 180 days as `STALE`. Stale + CRITICAL = `DEAD KNOWLEDGE ZONE`.

6. **Output format** — sorted by risk (CRITICAL first):

   **Risk Table**
   | File | Top Owner % | Bus Factor | Last Active | Risk |
   |------|-------------|------------|-------------|------|
   | src/billing/invoice.ts | 94% | 1 | 2023-11-02 | CRITICAL |
   | src/auth/jwt.ts | 67% | 2 | 2024-10-15 | HIGH |
   | src/api/routes.ts | 45% | 3 | 2025-01-20 | MEDIUM |

   **Summary**
   ```
   Analyzed: 48 files
   CRITICAL: 3 files (6%)
   HIGH:     7 files (15%)
   STALE:    11 files (23%) — no commits in 180+ days
   Dead Knowledge Zones: 2 (CRITICAL + STALE)
   ```

   **Recommended actions**
   - Pair-program sessions on CRITICAL files before contributors leave
   - Add inline documentation to stale CRITICAL files
   - Mark stale files with `# OWNER UNKNOWN` header comment if no active owner exists

7. **Guardrail** — output only aggregate statistics and risk labels. Never rank, compare, or make quality judgments about individual contributors.

## Notes
- Large repos (>500 files): offer to run per-directory in batches.
- If a file has only 1 total commit, report bus_factor as 1 and flag for review.
- Merge commits skew counts; use `--no-merges` in all git log calls.
