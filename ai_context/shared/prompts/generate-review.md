# Prompt: Generate review.md

Use this prompt to ask an AI agent to review the implementation of a task.

---

```
Read the following files:

1. ai_context/tasks/MOB-<number>/spec.md
2. ai_context/tasks/MOB-<number>/plan.md
3. ai_context/tasks/MOB-<number>/implementation.md
4. ai_context/rules/coding-standards.md
5. ai_context/rules/architecture-principles.md
6. ai_context/rules/security-policy.md
7. ai_context/rules/moodle-constraints.md

Review the implementation against:
- All acceptance criteria in spec.md
- Coding standards (naming, file size, Angular 20 patterns, no prohibited patterns)
- Architecture (delegate pattern, service singletons, lazy loading, no core/addons cross-import)
- Security (token handling, storage, XSS, Cordova permissions)
- Moodle upstream constraints (no unintentional upstream file edits)

Produce review.md using the template at:
ai_context/shared/templates/review-template.md

Include:
- Overall status: APPROVED / APPROVED WITH COMMENTS / NEEDS CHANGES
- Each finding with severity (critical / major / minor), description, and suggestion
- Architectural observations
- Follow-up tasks that should be created in Jira

Save to: ai_context/tasks/MOB-<number>/review.md
```
