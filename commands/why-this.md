# /why-this

**Usage:** `/why-this [file:line]` or `/why-this [file:start-end]`

Explain why a specific line or block of code exists — its origin commit, the author's stated intent, surrounding context, test coverage, and whether it looks like a temporary workaround.

## Example

```
/why-this src/auth/jwt.ts:88-102
```

**Output (example)**

```
WHY-THIS REPORT: src/auth/jwt.ts, lines 88–102
═══════════════════════════════════════════════

Origin
──────
Commit:  c3d4e5f (2023-06-14)
Author:  carol@co.com
Message: "fix: prevent token replay after logout on mobile clients"

Surrounding commits
───────────────────
Before: feat: add refresh token endpoint (#67)
After:  test: add replay-attack coverage for jwt middleware

Inline annotations
──────────────────
Line 91: // FIXME: Redis TTL should come from config, not hardcoded 3600

Test coverage
─────────────
jwt.middleware.test.ts:312 — "should reject replayed token after logout"
Status: covered

Workaround assessment
─────────────────────
Likely workaround: POSSIBLE
Signals: FIXME on hardcoded TTL, introduced immediately after a bug fix commit
```

## Notes
- If no line range is given, uses the current selection or prompts for one.
- Works on any file tracked by git.
