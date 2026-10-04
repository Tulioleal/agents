---
name: repository-rules
description: Creates, derives, updates, or audits repository-specific engineering rules in AGENTS.md without coupling them to a development workflow or specification framework.
---

# Repository Rules

Create and maintain the repository-specific rules that tell development agents how software must be built in this codebase.

This skill is framework-agnostic. It must not encode OpenSpec lifecycle behavior or agent orchestration behavior.

The output contract is `AGENTS.md`.

## Responsibility boundary

This skill defines:

> HOW THIS CODEBASE WORKS

It does not define:

- how coordinator/builder/tester/verifier agents behave;
- what the current feature must implement;
- OpenSpec proposal/spec/task lifecycle;
- benchmark or model-selection policy.

Keep these layers separate:

```text
Agent contracts  -> HOW AGENTS WORK
AGENTS.md         -> HOW THIS CODEBASE WORKS
Spec / plan       -> WHAT IS BEING BUILT
```

## Mode selection

Determine repository state before acting. Use exactly one primary mode.

### `bootstrap`

Use when there is no meaningful implementation yet and repository rules must be established from intended architecture and engineering decisions.

### `derive`

Use when meaningful code/configuration exists but repository rules do not yet exist or are clearly incomplete.

### `update`

Use when `AGENTS.md` already exists and a new explicit project decision, tooling change, architecture change, or confirmed convention requires updating it.

### `audit`

Use when rules already exist and the task is to detect drift between `AGENTS.md` and the current repository. Audit is read-only unless the user explicitly asks to apply confirmed corrections afterward.

If the mode was not explicitly requested, infer it from repository state and state the selected mode before proceeding.

Suggested detection:

```text
AGENTS.md exists?
├─ yes
│  ├─ user asks to change rules -> update
│  └─ user asks to inspect consistency -> audit
└─ no
   ├─ meaningful code/config exists -> derive
   └─ empty/new repository -> bootstrap
```

Do not ask the user to choose a mode if repository state makes the mode clear.

## Evidence classes

Never treat every observed code pattern as a rule.

### CONFIRMED

Supported by one or more of:

- explicit user decision;
- existing project documentation;
- tool configuration;
- CI configuration;
- architecture/design documentation;
- a strong, consistent implementation boundary with clear intent.

Confirmed rules may be written into `AGENTS.md`.

### STRONGLY INFERRED

A consistent repository pattern with meaningful architectural evidence, but no explicit source declaring it mandatory.

Do not silently promote strongly inferred conventions into hard rules when doing so could constrain future architecture. When low-risk and clearly descriptive, they may be documented as conventions rather than prohibitions. Otherwise surface them for confirmation.

### AMBIGUOUS

Examples:

- two competing architectural patterns coexist;
- old and new conventions coexist;
- it is unclear whether a pattern is intentional or historical;
- the decision materially changes architecture, dependencies, state management, persistence, API shape, or testing strategy.

Ask only about ambiguities that materially affect future implementation.

## General rule-authoring principles

Every rule must be:

- project-specific;
- actionable;
- concise;
- observable in implementation;
- relevant to future changes.

Do not duplicate generic agent-quality rules such as:

- write clean code;
- avoid overengineering;
- keep changes focused;
- do not modify unrelated work.

Those belong to agent contracts.

Do not encode style rules that deterministic tooling already enforces reliably.

Prefer:

```text
tooling = deterministic enforcement
AGENTS.md = project context and architectural constraints
```

If a repository rule can later be enforced reliably by linting, static analysis, tests, dependency-boundary tooling, schema checks, or CI, recommend migrating enforcement there while keeping only necessary architectural context in `AGENTS.md`.

## Standard AGENTS.md structure

Use this section order unless a section is genuinely inapplicable:

```markdown
# Repository Instructions

## 1. Project Overview
## 2. Architecture
## 3. Technology Stack
## 4. Repository Structure
## 5. Implementation Conventions
## 6. Architecture Boundaries
## 7. Error Handling
## 8. Testing
## 9. Validation Commands
## 10. Dependencies
## 11. Generated Files
## 12. Prohibited Patterns
## 13. Change Scope Rules
```

Do not add empty sections merely to satisfy the template.

## `bootstrap` procedure

Use this when the project has not meaningfully started.

1. Read available project intent, architecture notes, selected stack, runtime/package-manager choices, deployment constraints, and product requirements that materially affect architecture.
2. Build a decision inventory:
   - `DERIVABLE`: already implied by explicit choices; record without asking.
   - `RECOMMENDED DEFAULT`: reversible, low-impact convention; propose a default without over-questioning.
   - `ARCHITECTURAL DECISION`: material choices such as persistence boundary, server/client responsibility, module organization, state management, error model, API boundary, testing layers, and dependency policy. Ask only when unresolved.
3. Generate `AGENTS.md` from confirmed decisions.
4. Do not scaffold the entire application. If the user wants project creation, use `project-bootstrap`.

## `derive` procedure

1. Inspect repository evidence, prioritizing package manifests, lockfiles, compiler/type configuration, lint/format configuration, test configuration, CI, directory structure, README/contributing docs, architecture docs, representative implementation paths, and runtime/deployment configuration.
2. Infer architecture carefully: domain/business logic, persistence/network access, client/server boundaries, state management, error handling, test organization, validation commands, generated artifacts, and dependency boundaries.
3. Classify findings as `CONFIRMED`, `STRONGLY INFERRED`, or `AMBIGUOUS`.
4. Ask only about material ambiguities.
5. Generate `AGENTS.md` without encoding accidental historical inconsistency as policy.

## `update` procedure

Read the current `AGENTS.md`, repository state, and the new confirmed decision.

- Change only affected rules.
- Determine whether the old rule is obsolete, narrowed, broadened, or contradicted.
- Check whether another section depends on it.
- Avoid duplicating the new rule across sections.
- Do not rewrite unrelated sections for style.

## `audit` procedure

Audit is evidence-first and read-only by default.

Compare `AGENTS.md` against current repository structure, tooling configuration, CI, representative implementation, and explicit project documentation.

Classify findings:

- `RULE VIOLATION`: implementation clearly contradicts an active rule.
- `RULE DRIFT`: repository has consistently evolved away from the rule and it may now be obsolete.
- `STALE COMMAND`: documented validation/tool command no longer matches configuration.
- `MISSING RULE`: a confirmed architectural decision materially affects implementation but is absent from `AGENTS.md`.
- `AMBIGUOUS`: evidence is insufficient to tell whether code or rule should change.

Do not automatically modify code to satisfy an audit. Do not automatically weaken a rule merely because violations exist.

For each finding, recommend the likely next action: fix implementation, update rule, investigate, or migrate the rule to deterministic tooling.

## Quality check before writing AGENTS.md

Verify:

- every hard rule has a reason grounded in project evidence or an explicit decision;
- generic coding advice has not leaked into repository rules;
- deterministic style rules are delegated to tooling;
- contradictory rules do not exist;
- validation commands match repository configuration;
- prohibited patterns are concrete;
- no OpenSpec-specific lifecycle instructions are present;
- the document is short enough to be consumed frequently by agents.
