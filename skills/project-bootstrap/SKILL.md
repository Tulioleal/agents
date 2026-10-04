---
name: project-bootstrap
description: Establishes the initial stack, architecture, repository structure, tooling, and validation baseline for a new software project, then hands repository-specific rule generation to repository-rules bootstrap.
---

# Project Bootstrap

Use this skill only when the user wants to initialize or define a new software project rather than merely document repository rules.

This skill is separate from `repository-rules`.

Its responsibility is:

> HOW THE PROJECT STARTS

`repository-rules` is responsible for:

> HOW THE REPOSITORY MUST BE WORKED ON

## Outputs

Depending on user scope, establish:

- selected stack;
- architecture boundaries;
- repository structure;
- package/runtime tooling;
- lint/type/format/test baseline;
- canonical validation commands;
- initial dependency policy;
- optional CI baseline;
- confirmed inputs for `repository-rules bootstrap`.

Do not define feature requirements unless needed to choose architecture. Do not embed OpenSpec lifecycle rules.

## Procedure

### 1. Gather project constraints

Prefer existing user decisions. Identify only decisions that materially affect bootstrap: application type, runtime/language, frontend/backend boundaries, persistence, deployment, architectural authentication requirements, testing expectations, package manager, and infrastructure constraints.

### 2. Separate decisions

Classify each as:

- `ALREADY DECIDED`;
- `SAFE DEFAULT`;
- `ARCHITECTURAL DECISION`.

Use safe defaults for reversible low-impact setup choices. Ask only for unresolved architectural decisions that materially affect the generated baseline.

### 3. Establish deterministic tooling early

Prefer mechanical enforcement where possible:

- compiler/type checking;
- linting;
- formatter when desired;
- test runner;
- build command;
- CI checks when requested.

Do not replace architectural rules with tooling that cannot express them reliably.

### 4. Establish repository structure

Create only structure justified by current architecture. Avoid speculative folders, layers, services, factories, repositories, or abstractions for future possibilities.

### 5. Hand off repository rules

Once project decisions are coherent, run the conceptual equivalent of `repository-rules` in `bootstrap` mode using the confirmed project decisions, generated tooling, and repository structure.

`AGENTS.md` remains owned by the repository-rules process.

## Completion

Bootstrap is complete when:

- initial architecture is coherent;
- deterministic validation has canonical commands;
- repository structure reflects current needs;
- no speculative architecture was introduced;
- `AGENTS.md` has been generated through `repository-rules bootstrap`;
- the project is ready for feature planning through either the framework-agnostic workflow or OpenSpec.
