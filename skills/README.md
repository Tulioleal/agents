# Shared skills

## Repository rules

`repository-rules` is shared by both development workflows and owns the lifecycle of `AGENTS.md`.

Modes:

- `bootstrap`: new/empty project;
- `derive`: existing project without standardized rules;
- `update`: confirmed repository decision changed;
- `audit`: detect drift between rules and reality.

## Project bootstrap

`project-bootstrap` is optional. Use it when stack, architecture, initial structure, and deterministic tooling must also be established. It hands rule generation to `repository-rules bootstrap`.

## Separation of concerns

```text
project-bootstrap  -> HOW THE PROJECT STARTS
repository-rules   -> HOW THIS CODEBASE WORKS
agent contracts    -> HOW AGENTS WORK
plan / OpenSpec    -> WHAT IS BEING BUILT
```

Ordinary feature work consumes `AGENTS.md`; it does not regenerate it.
