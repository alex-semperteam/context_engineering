# AI Context Engineering Convention

---

## Background

When using AI tools through prompt engineering, systemic problems arise:

- context exists only in dialogue and disappears between sessions;
- AI doesn't know architectural constraints, patterns, and project decisions;
- each request requires repeated context explanation;
- decision-making logic is stored in heads, not in the repository;
- different team members get different results from the same AI tool;
- migration between AI tools requires rewriting all prompts.

**Context Engineering** solves these problems by moving context from prompts into structured repository files. You describe rules, architecture, and constraints once — AI reads them automatically during each interaction.

---

## Architectural Goals

1. **Reproducibility** — any AI agent, after reading `ai_context/`, receives the same context as the team.
2. **Tool portability** — changing AI tools doesn't require rewriting the structure.
3. **Single source of truth** — only documents in `ai_context/` are canonical. All indexes are derived.
4. **Traceability** — from requirement to proof of execution: `spec → context → plan → implementation → review → tests`.
5. **Depth scalability** — documentation level is determined by task risk, not its existence.

---

## Root Structure

```
ai_context/
  manifests.yaml        # derived task index for CI and automation
  rules/                # permanent rules and constraints
  tasks/                # tasks (one directory = one task)
  shared/               # shared templates, prompts, checklists
  adapters/             # translation layer for specific tool formats
  README.md             # entry point for humans
```

**Principle:** the root is self-sufficient. Removing external tools doesn't destroy access to knowledge.

---

## Task Lifecycle

Any work evolves linearly:

1. requirement appears (`spec`)
2. constraints are fixed (`context`)
3. strategy is chosen (`plan`)
4. actual implementation is formed (`implementation`)
5. audit is conducted (`review`)
6. proof of coverage is created (`tests-review`)

The task directory should allow restoring this chain without external sources.

```
ai_context/tasks/<TASK-ID>/
  spec.md
  context.md
  plan.md
  implementation.md
  review.md
  tests-review.md
  artifacts/             # optional: diagrams, diff summaries, schemas
```

---

## Authorship and Responsibility

Distribution is determined by distortion risk.

**Intent (human-owned):** `spec.md`, `context.md`, `plan.md`  
AI can prepare drafts, but putting into action requires explicit engineer confirmation.

**Actual state (AI-efficient):** `implementation.md`, `review.md`, `tests-review.md`  
Automation is acceptable; humans perform result acceptance.

---

## Rules (rules/)

Long-lived constraints applied to all tasks. Rarely change from feature to feature.

```
ai_context/rules/
  coding-standards.md        # code standards, style, naming
  security-policy.md         # security rules
  architecture-principles.md # architectural patterns and constraints
  testing-requirements.md    # coverage requirements and test types
```

Format — Markdown with YAML frontmatter:

```yaml
id: rule-coding-001
kind: rule
version: 2
applies_to: ["*.ts"]
```
---

## Task Documents

### spec.md

Primary requirement and task identity point. Immutable, except for clarifications. Contains metadata defining AI and CI behavior.

```yaml
id: TASK-123
kind: spec
source: jira|github|internal
complexity: trivial|normal|critical
status: active
```

### context.md

Local knowledge required before making changes: affected modules, contracts, dependencies, constraints.

```yaml
kind: context
```

### plan.md

Execution strategy based on spec + context. Contains chosen approach, rejected alternatives, risk zones, data impact, and readiness criteria. Can change with new information.

```yaml
kind: plan
```

### implementation.md

Recording observable result: actual structural decisions, deviations from plan, tradeoffs, technical debt.

```yaml
kind: implementation
```

### review.md

Assessment of correctness and consequences: architectural notes, compliance with rules, production readiness.

```yaml
kind: review
```

### tests-review.md

Proof of coverage. Chain: requirement → code → automatic proof → manual verification. Records coverage strategy, critical scenarios, and gaps.

```yaml
kind: tests-review
```

---

## Complexity Levels

```
complexity: trivial | normal | critical
```

| level    | spec | context | plan | implementation | review | tests |
|----------|------|---------|------|----------------|--------|-------|
| trivial  | ✓    | –       | –    | ✓              | –      | –     |
| normal   | ✓    | opt.    | ✓    | ✓              | short  | ✓     |
| critical | ✓    | ✓       | ✓    | ✓              | ✓      | ✓     |

**Invariant:** regardless of level, connection to requirement and explanation of actual result are always needed.

CI may require increasing `complexity` when: changing public APIs, data migrations, security impact, changing external dependencies.

---

## Task Statuses

```
status: active | historical | obsolete
```

Status is stored in `spec.md`. Changed through PR, like code. By default, AI agents work only with `active`.

- **active** — task in progress or recently stabilized
- **historical** — stabilized in production, archive for future
- **obsolete** — replaced by another solution or canceled

---

## Shared Resources (shared/)

```
ai_context/shared/
  prompts/        # reusable prompts for document generation
  templates/      # templates for spec.md, plan.md, etc.
  checklists/     # checklists for PR, security, review
```

---

## manifests.yaml

Machine-readable index of all tasks. Built from metadata of each task's `spec.md`.

**Purpose:** accelerated discovery, integrity control, status filtering for CI and adapters.

**Key property:** deleting the manifest doesn't lead to knowledge loss — it's fully recoverable from file structure.

Example entry:

```yaml
- id: TASK-123
  kind: spec
  path: tasks/TASK-123/spec.md
  status: active
  complexity: normal
```

---

## Adapters

Adapters are a translation layer from canonical structure to formats expected by specific tools.

```
ai_context/adapters/
  cursor/
  copilot/
  claude-code/
  internal-ci/
```

**Why they exist:** different environments expect different integration points — special directories, JSON indexes, flat file sets. Without adapters, teams duplicate data outside the system.

**Adapter constraints:** don't modify source documents; don't maintain own version of truth; don't add knowledge outside repository. If something is missing — the data model changes, not the adapter.

**Architectural test:** deleting any adapter shouldn't break the team's workflow.

---

## Minimum Implementation Threshold

If the team does only three things:

1. maintains `rules/`;
2. stores each task in isolated directory `tasks/<ID>/`;
3. has at least `spec.md` and `implementation.md` —

knowledge loss dramatically decreases, and AI reliability increases.
