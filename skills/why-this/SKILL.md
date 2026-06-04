---
name: why-this
description: Explain why a specific code block or line range exists — its origin commit, author intent, test coverage, and workaround assessment.
version: 0.1.0
triggers:
  - "why does this code exist"
  - "why this block"
  - "explain this code"
  - "what is this for"
  - "who wrote this"
---

# why-this

Answer "why does this specific code block exist?" using git blame, commit context, inline comments, and test coverage.

## Steps

1. **Accept input** — a file path with optional line range (`src/auth.ts:42-58`) or a selected code snippet. If only a snippet is given, locate the file and line range with grep.

2. **Run git blame** on the target lines:
   ```bash
   git blame -L <start>,<end> --date=short -- <file>
   ```
   Extract: commit hash, author email, date, and line content for each line in range.

3. **Fetch commit context** — for the primary commit hash from blame:
   ```bash
   git show --stat --format="%H%n%ae%n%ad%n%s%n%b" <hash>
   ```
   Also fetch the two commits before and after (`git log --oneline -3 <hash>^`) for surrounding context.

4. **Scan inline comments** — search for `TODO`, `FIXME`, `HACK`, `NOTE`, `WORKAROUND`, `XXX` within 10 lines above and below the target block:
   ```bash
   sed -n '<start-10>,<end+10>p' <file> | grep -n "TODO\|FIXME\|HACK\|NOTE\|WORKAROUND\|XXX"
   ```

5. **Find test coverage** — search test files for the function or variable name from the block:
   ```bash
   grep -rn "<identifier>" --include="*.test.*" --include="*.spec.*" --include="*_test.*"
   ```
   Note whether tests exist and what behavior they assert.

6. **Assess workaround patterns** — flag the block as a likely workaround if any of the following are true:
   - Commit message contains: `fix`, `hotfix`, `patch`, `workaround`, `hack`, `temp`, `kludge`
   - Block contains hardcoded values, magic numbers, or `if <specific_id>` conditionals
   - Inline `HACK` or `FIXME` comment is present
   - Block was added alone without corresponding tests

7. **Output format**:

   ```
   Origin
   ──────
   File:    src/payments/stripe.ts, lines 88–102
   Commit:  a1b2c3d (2023-01-10)
   Author:  alice@co.com
   Message: "fix: handle Stripe webhook signature mismatch on retry"

   Surrounding context
   ───────────────────
   Before: feat: add Stripe webhook handler (#42)
   After:  chore: add retry tests for webhook

   Inline annotations
   ──────────────────
   Line 91: // HACK: Stripe sends duplicate events on network timeout, dedupe here
   Line 99: // TODO: remove after Stripe SDK v4 releases fix

   Test coverage
   ─────────────
   Found: stripe.webhook.test.ts:204 — tests "should deduplicate retried events"
   Status: covered

   Workaround assessment
   ─────────────────────
   Likely workaround: YES
   Signals: commit message contains "fix", HACK comment present, hardcoded 5-second dedup window
   ```

## Notes
- If git blame returns no data (untracked file), say so clearly.
- Never speculate beyond what the data shows; mark uncertain inferences as "possible".
