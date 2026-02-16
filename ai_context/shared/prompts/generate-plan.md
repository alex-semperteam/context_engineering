# Prompt: Generate plan.md

Use this prompt with any AI agent to generate `plan.md` for a task.
Replace `MOB-<number>` with the actual Jira ticket ID.

---

```
Read the following files in order:

1. ai_context/CLAUDE.md
2. ai_context/tasks/MOB-<number>/spec.md
3. ai_context/tasks/MOB-<number>/context.md  (if it exists)
4. ai_context/rules/architecture-principles.md
5. ai_context/rules/moodle-constraints.md
6. ai_context/rules/coding-standards.md

Then generate a plan.md for task MOB-<number> using the template at:
ai_context/shared/templates/plan-template.md

The plan must include:
- Chosen implementation approach with justification
- Rejected alternatives (if considered)
- Concrete step-by-step implementation checklist
- Risk zones: upstream conflicts, platform differences, performance
- Data/migration impact (SQLite schemas, Cordova Preferences)
- Readiness criteria aligned with spec.md acceptance criteria

Do NOT write any code. Produce the plan only.
Save to: ai_context/tasks/MOB-<number>/plan.md
```
