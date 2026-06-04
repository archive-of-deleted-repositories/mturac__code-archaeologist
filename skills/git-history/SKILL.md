---
name: git-history
description: Trace the full evolution of a file or function through git history, identify major rewrites, and build a narrative timeline of why the code changed.
version: 0.1.0
triggers:
  - "show history of"
  - "trace evolution"
  - "git history"
  - "timeline of"
  - "how did this file evolve"
---

# git-history

Trace the full git history of a file or function and produce a chronological narrative of how it evolved.

## Steps

1. **Accept input** — a file path or function name. If a function name is given, locate the file first using `grep -rn "def <name>\|function <name>\|const <name>\|func <name>" .`

2. **Fetch full history** — run:
   ```bash
   git log --follow --patch --all --stat -- <file>
   ```
   Capture: commit hash (short), author email, date, commit message, and `--stat` line count diff.

3. **Extract key data** — for each commit record:
   - Commit hash, author, date, message
   - Lines added / lines deleted (from `--stat`)
   - Total net change per commit

4. **Identify inflection points** — flag any commit where `lines_added + lines_deleted > 50`. These are major rewrites or large feature additions. Mark them prominently in the output.

5. **Find linked issues / PRs** — scan each commit message for patterns like `#\d+`, `fixes #\d+`, `closes #\d+`, `PR #\d+`. Collect all references.

6. **Build the narrative** — answer:
   - When was this file first committed, and by whom?
   - What were the top 3 largest change events?
   - Who has touched it most recently?
   - Are there long quiet periods followed by bursts of activity?

7. **Output format**:

   **Timeline Table**
   | Date | Author | Commit | Change (+/-) | Inflection? | Linked |
   |------|--------|--------|-------------|-------------|--------|
   | 2023-01-10 | alice@co.com | `a1b2c3` | +210 / -5 | YES | #42 |

   **Narrative Summary**
   > `payments.service.ts` was created on 2022-03-01 by alice@co.com as part of the initial checkout flow. The biggest rewrite happened on 2023-01-10 (+210 lines), linked to issue #42 (Stripe v3 migration). Since then it has been touched 6 times by 3 authors, last modified on 2024-08-15 by bob@co.com.

## Notes
- If the repo has no git history, say so and exit cleanly.
- Never surface personal opinions about contributor quality.
- Truncate patch output after 200 lines per commit to avoid context overflow.
