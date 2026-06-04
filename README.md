# code-archaeologist

A Claude Code plugin that uncovers intent and evolution in legacy codebases through git history, comments, and structural patterns.

**Core question it answers:** *Why does this code exist — and who still understands it?*

## Install

```bash
claude plugin install code-archaeologist
```

Or from source:

```bash
git clone https://github.com/mturac/code-archaeologist
claude plugin install ./code-archaeologist
```

## Commands

| Command | What it does |
|---------|-------------|
| `/excavate [file]` | Full archaeology report: timeline + inflection deep-dives + bus factor |
| `/why-this [file:line]` | Explain why a specific code block exists |
| `/timeline [file]` | Chronological evolution table for a file |
| `/bus-factor [path]` | Knowledge concentration risk per file |

## Example: `/timeline src/payments/stripe-webhook.ts`

```
EVOLUTION TIMELINE: src/payments/stripe-webhook.ts
══════════════════════════════════════════════════════

Date         Author              Commit   +Lines  −Lines  Inflection  Links
──────────── ─────────────────── ──────── ─────── ─────── ─────────── ──────
2022-03-01   alice@co.com        a0b1c2   +180    −0      YES (birth) —
2023-01-10   alice@co.com        d4e5f6   +210    −45     YES         #42
2024-08-15   bob@co.com          g7h8i9   +12     −3      no          #101

Summary
───────
First commit:   2022-03-01
Last commit:    2024-08-15
Total commits:  18
Authors:        3
Major rewrites: 2 (flagged above)
Linked items:   #42, #101
```

## Skills

- **git-history** — full file evolution with inflection detection and issue linking
- **why-this** — blame + commit context + inline annotations + workaround detection
- **bus-factor** — per-file knowledge concentration scoring with staleness detection

## Requirements

- Git repo with commit history
- Claude Code with plugin support

## License

MIT
