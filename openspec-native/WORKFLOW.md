# OpenSpec-native development workflow

## Repository rules precondition

Before substantial implementation, consume `AGENTS.md` when present.

- New repository: establish rules through `repository-rules bootstrap`.
- Existing repository without standardized rules: establish them through `repository-rules derive`.
- Ordinary feature work must not regenerate repository rules.
- Confirmed repository-level architecture/tooling changes should update rules deliberately through `repository-rules update`.


This workflow uses OpenSpec as the authoritative specification and lifecycle layer while keeping execution responsibilities separated across agents.

## Ownership

### OpenSpec owns
- change scope,
- proposal/spec/design artifacts,
- tasks,
- progress/lifecycle state,
- update/sync/archive semantics,
- semantic verification procedure.

### Coordinator owns
- routing,
- execution granularity,
- user approval boundaries,
- deterministic and semantic gate policy,
- transition decisions,
- Git closure proposal.

### Specialist agents own
- Reader: codebase context,
- Builder: one bounded implementation task,
- Test Runner: deterministic verification,
- Spec Verifier: official OpenSpec semantic verification,
- Visual Reviewer: optional visual gate.

## Execution state machine

```text
OpenSpec change
   ↓
status + apply instructions
   ↓
coordinator selects next bounded task
   ↓
reader (when code discovery is needed)
   ↓
builder
   ↓
test-runner
   ├─ FAIL → builder → test-runner
   └─ PASS
       ↓
spec-verifier / OpenSpec verify
   ├─ implementation issue → builder → test-runner → verify
   ├─ artifact/design issue → OpenSpec update → resume implementation
   └─ clear
       ↓
visual-reviewer (when applicable)
   ├─ FAIL → builder → test-runner → verify → visual-review
   └─ PASS
       ↓
coordinator gate
       ↓
OpenSpec archive
       ↓
final git inspection
       ↓
commit proposal
```

## Important design choice: bounded apply

The coordinator keeps implementation granular: one bounded task is delegated at a time.

OpenSpec provides authoritative tasks, requirements, context, and state, but the child builder is not asked to autonomously consume the entire remaining change. This preserves:
- tighter context,
- independent review between tasks,
- easier failure isolation,
- cleaner future model benchmarking,
- coordinator control over scope transitions.

## Repair-loop policy

Each bounded OpenSpec task has independent repair budgets:

- deterministic gate: **3** corrective Builder repairs;
- semantic/OpenSpec verification gate: **2** corrective Builder repairs;
- visual gate: **2** corrective Builder repairs;
- same unresolved root cause: **3** corrective attempts.

There is **no global repair ceiling** across gates.

A repair is counted only when a failed gate causes Builder to modify the implementation. The initial implementation and gate reruns without code changes do not consume budget.

If a gate-specific budget or the same-root-cause limit is exhausted, autonomous repair for that failure path stops and the issue is escalated with current evidence preserved.

Budgets reset when advancing to a new bounded OpenSpec task. An OpenSpec update does not reset the budget unless the task is materially redefined and explicitly treated as new.

## Cycle-report telemetry

During model evaluation, the coordinator persists one self-contained report at the end of every execution cycle.

Reports live at:

```text
.agent-runs/<short-run-name>/000.md
.agent-runs/<short-run-name>/001.md
...
```

Rules:

- `<short-run-name>` is descriptive, stable for the run, filesystem-safe, and no more than 25 characters;
- numbering is zero-based and incrementing;
- reports are never overwritten;
- every report records the exact effective provider/model/variant/reasoning configuration;
- each invoked agent gets an execution summary of no more than 250 characters;
- repair counters and user-intervention counters are cumulative for the bounded task;
- user interventions are categorized, but human response duration is excluded from timing;
- workflow time is active execution time only;
- a cycle ends before waiting for user input and the next cycle starts when autonomous execution resumes;
- token, cache, tool-call, retry, time, and cost telemetry are recorded only when directly available;
- quality is stored as gate/findings evidence, not as an overall score;
- reports are telemetry and do not alter workflow decisions.

## Archive policy

Archive is allowed only when:
- all required tasks are complete,
- deterministic validation passes,
- zero CRITICAL/blocking semantic findings remain,
- no applicable requirement/scenario is silently unverified,
- visual criteria pass when applicable.

Warnings are reviewed by the coordinator before archive and do not automatically trigger refactoring. Suggestions are informational unless explicitly promoted into scope.

Code-quality review is layered:
- repository tooling enforces deterministic rules,
- Builder follows the minimum implementation quality floor,
- Spec Verifier adds a narrow semantic code-quality review after deterministic validation.

## OpenSpec update policy

When implementation reality contradicts or materially changes the approved artifacts, do not let Builder improvise a new contract. Route the artifacts through the official OpenSpec update workflow, then continue from the updated state.

## Model selection

Model lines are baseline assignments only. Model benchmarking is intentionally outside this workflow and should later alter model assignments without changing role contracts or gate semantics.
