# Cursor Adapter

Provides `.cursorrules` file for Cursor IDE, generated from `ai_context/rules/`.

---

## Setup

Copy `.cursorrules` to the **repository root**:

```bash
cp ai_context/adapters/cursor/.cursorrules .cursorrules
```

Cursor picks up `.cursorrules` automatically from the project root.

---

## What It Contains

A condensed version of:
- `ai_context/CLAUDE.md` — project overview
- `ai_context/rules/coding-standards.md`
- `ai_context/rules/architecture-principles.md`
- `ai_context/rules/security-policy.md`
- `ai_context/rules/moodle-constraints.md`

---

## Keeping It in Sync

When you update any `rules/*.md` file, regenerate `.cursorrules`:

```bash
# Manual: review the diff and update adapters/cursor/.cursorrules
# or run (if generation script is added):
node ai_context/adapters/cursor/build.js
```

> **Remember:** `.cursorrules` is a projection — if it conflicts with `rules/`, the rules win.

---

## Cursor `@Docs` Integration (optional)

You can also point Cursor's `@Docs` at specific `rules/` files for deeper context:
1. Cursor → Settings → Docs → Add
2. Point to `ai_context/rules/architecture-principles.md`
3. Point to `ai_context/rules/moodle-constraints.md`
