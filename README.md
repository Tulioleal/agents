# Agentic Development Workflows

Reusable multi-agent software-development workflows for OpenCode, with bounded implementation, deterministic validation, semantic verification, repository rules, repair budgets, and execution telemetry.

The role contracts are model-agnostic: canonical agent files do not assign a model.

## Workflows

Two execution modes share the same separation of responsibilities.

### Framework-agnostic

`framework-agnostic/` uses the user's approved plan and repository instructions as the feature contract.

```text
Coordinator
    ↓
  Reader
    ↓
 Builder
    ↓
Test Runner
    ↓
 Verifier
    ↓
Visual Reviewer (optional)
```

### OpenSpec-native

`openspec-native/` uses OpenSpec as the authoritative source for feature scope, artifacts, tasks, verification procedure, update flow, and archive lifecycle. The coordinator still delegates one bounded implementation task at a time.

## Roles

### Coordinator

Plans, delegates, interprets gate evidence, controls repair budgets and escalation, and writes cycle telemetry. It does not implement application code.

### Reader

Read-only codebase discovery. Returns the minimum context required for a bounded task.

### Builder

Implements bounded code changes under repository rules and the minimum engineering-quality contract.

### Test Runner

Executes authoritative deterministic checks such as lint, typecheck, tests, build, runtime readiness, and validation process lifecycle.

### Verifier / Spec Verifier

Performs independent semantic verification after deterministic checks pass. The OpenSpec variant also executes the official OpenSpec verification process.

### Visual Reviewer

Optional read-only visual gate for explicit UI criteria.

## Repository rules

Project-specific engineering rules live in `AGENTS.md` and are intentionally separate from agent behavior and feature requirements.

Reusable skills:

```text
skills/repository-rules/
skills/project-bootstrap/
```

`repository-rules` supports:

- `bootstrap` — create rules for a new project;
- `derive` — derive rules from an existing codebase;
- `update` — apply a confirmed repository-level decision;
- `audit` — detect rule drift.

`project-bootstrap` handles initial stack/architecture/tooling and then delegates repository-rule generation to `repository-rules`.

## Repair budgets

Independent budgets apply to each bounded task:

| Gate | Maximum corrective repairs |
|---|---:|
| Deterministic | 3 |
| Semantic | 2 |
| Visual | 2 |
| Same unresolved root cause | 3 |

There is no global repair ceiling.

The initial implementation does not consume repair budget. A repair is counted only when a failed gate sends work back to Builder and implementation changes.

## Cycle telemetry

During model evaluation, the coordinator writes one report after every execution cycle:

```text
.agent-runs/
└── <short-run-name>/
    ├── 000.md
    ├── 001.md
    └── ...
```

The run name is stable, filesystem-safe, and at most 25 characters.

Reports capture:

- effective provider/model/variant/reasoning configuration;
- per-agent summary (≤250 characters);
- repair counters;
- user intervention count/category;
- active autonomous execution time;
- input/output/reasoning/cache tokens when available;
- tool calls and retries;
- cost telemetry when available;
- raw quality evidence.

Human response waiting is excluded from active workflow time.

Workflow telemetry records evidence; it does not assign model scores or select a winner.

## Model configuration

The canonical role files intentionally omit `model`. Assign models at deployment or benchmark time.

OpenCode supports model variants using:

```text
provider/model#variant
```

Available variants are model-specific. Resolve them from the OpenCode model catalog before an experiment rather than assuming every model exposes the same variant names.

## Agent file format

See [`docs/AGENT_SCHEMA.md`](docs/AGENT_SCHEMA.md) for the OpenCode V2 Markdown schema, permission rules, primary/subagent convention, and model/variant separation.

## Repository layout

```text
.
├── README.md
├── docs/
│   └── AGENT_SCHEMA.md
├── evaluation/
│   ├── CYCLE_REPORT.template.md
│   ├── README.md
│   └── TELEMETRY_SCHEMA.md
├── framework-agnostic/
│   ├── WORKFLOW.md
│   └── *.md
├── openspec-native/
│   ├── WORKFLOW.md
│   └── *.md
└── skills/
    ├── README.md
    ├── repository-rules/
    └── project-bootstrap/
```

## Design principles

- model choice is independent from role definition;
- deterministic validation is independent from semantic review;
- implementation agents do not approve their own work;
- repository rules, role contracts, and feature requirements are separate layers;
- repair loops are bounded by concern;
- normal workflow telemetry stores evidence rather than benchmark scores;
- benchmark campaigns must freeze role prompts and permissions while comparing models.
