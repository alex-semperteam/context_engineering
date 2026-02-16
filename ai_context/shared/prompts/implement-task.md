# Prompt: Implement a Task

Use this prompt to ask an AI agent to implement one step from plan.md.
Replace `MOB-<number>` and `<N>` with actual values.

---

```
Read the following files in order:

1. ai_context/CLAUDE.md
2. ai_context/tasks/MOB-<number>/spec.md
3. ai_context/tasks/MOB-<number>/context.md  (if it exists)
4. ai_context/tasks/MOB-<number>/plan.md
5. ai_context/rules/coding-standards.md
6. ai_context/rules/architecture-principles.md
7. ai_context/rules/moodle-constraints.md
8. ai_context/rules/testing-requirements.md

Implement step <N> from plan.md.

Requirements:
- Follow coding-standards.md strictly (naming, file size, Angular 20 control flow, no prohibited patterns)
- Do NOT modify any upstream files unless they are already marked with // FORK:
- Write tests per testing-requirements.md for the complexity level in spec.md
- After implementing, update ai_context/tasks/MOB-<number>/implementation.md

Do not implement more than one step without confirmation.
```
