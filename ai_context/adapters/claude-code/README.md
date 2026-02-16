# Claude Code Adapter

Export of context for Claude Code. The processor reads `context.json` in this directory.

Claude Code reads `CLAUDE.md` from the repository root automatically, and any files placed in `.claude/` directory.

Run from repo root:

```bash
aictx export claude-code
```

Source:
- https://docs.anthropic.com/en/docs/claude-code