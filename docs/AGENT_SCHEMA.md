# Agent and subagent file schema

This project uses OpenCode V2 Markdown agents. A role file is YAML frontmatter followed by a Markdown system prompt.

## Canonical shape

```md
---
description: Reviews an implementation without modifying it
mode: subagent
color: warning
permissions:
  - action: edit
    resource: "*"
    effect: deny
  - action: shell
    resource: "git diff*"
    effect: allow
---

System prompt body.
```

## Frontmatter

### `description`

Short responsibility description. For subagents, keep it specific enough for the coordinator to choose the correct specialist.

### `mode`

- `primary`: top-level orchestrating agent.
- `subagent`: child specialist.
- `all`: usable in either position.

This project uses `primary` for coordinators and `subagent` for specialists.

### `model`

The canonical role files intentionally **do not set this field**. Model assignment is deployment/benchmark configuration, not part of the role contract.

OpenCode accepts either:

```yaml
model: provider/model
```

or:

```yaml
model: provider/model#variant
```

If a subagent does not configure a model, it can inherit the parent/session model. For role-specific deployments or benchmarks, configure the effective model outside the canonical role file.

### `color`

UI presentation only. It does not alter behavior.

### `permissions`

OpenCode V2 ordered permission rules:

```yaml
permissions:
  - action: edit
    resource: "*"
    effect: deny
  - action: edit
    resource: ".agent-runs/**"
    effect: allow
```

Each rule has:

- `action`: tool/action being controlled;
- `resource`: command/path/agent pattern;
- `effect`: `allow`, `ask`, or `deny`.

Rules are ordered; later matching rules can narrow or override broad rules.

Actions used by this project include:

- `edit` — file writes/patches;
- `shell` — shell commands;
- `subagent` — child-agent invocation;
- `question` — explicit user-question capability where configured.

## Prompt-body convention

Role prompts are organized around behavior rather than model identity. They generally contain:

1. role and responsibility boundary;
2. authoritative inputs;
3. execution procedure;
4. gate/repair/escalation policy where applicable;
5. output/reporting contract;
6. explicit prohibitions.

## Primary agent vs subagent

The file format is the same. The distinction is responsibility.

### Coordinator (`primary`)

- receives the user task;
- plans and delegates;
- controls workflow transitions and repair budgets;
- evaluates specialist evidence;
- writes `.agent-runs/**` telemetry;
- does not implement application code.

### Specialists (`subagent`)

- receive bounded work;
- perform one concern;
- return evidence/results;
- do not own the entire workflow;
- do not self-approve beyond their assigned gate.

## Effective agent configuration

For evaluation purposes, treat these as separate dimensions:

```text
role contract + provider/model + variant/reasoning configuration = effective agent configuration
```

This is why canonical role files remain model-agnostic.
