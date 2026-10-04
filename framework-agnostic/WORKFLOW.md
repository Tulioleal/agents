# Framework-agnostic development workflow

## Repository rules precondition

Before substantial implementation, consume `AGENTS.md` when present.

- New repository: establish rules through `repository-rules bootstrap`.
- Existing repository without standardized rules: establish them through `repository-rules derive`.
- Ordinary feature work must not regenerate repository rules.
- Confirmed repository-level architecture/tooling changes should update rules deliberately through `repository-rules update`.


This workflow is intentionally independent of OpenSpec or any other specification framework.

## Roles

1. **Coordinator** — plans, delegates, owns workflow transitions and acceptance policy.
2. **Reader** — retrieves focused codebase context.
3. **Builder** — performs one bounded implementation task.
4. **Test Runner** — performs authoritative deterministic checks.
5. **Verifier** — independently checks semantic compliance.
6. **Visual Reviewer** — optional read-only visual gate.

## State machine

```text
request
  ↓
plan
  ↓
reader (when discovery is needed)
  ↓
builder
  ↓
test-runner
  ├─ FAIL → builder → test-runner
  └─ PASS
       ↓
    verifier
       ├─ BLOCKING → builder → test-runner → verifier
       └─ CLEAR
            ↓
      visual-reviewer (when applicable)
            ├─ FAIL → builder → test-runner → verifier → visual-reviewer
            └─ PASS
                 ↓
             complete
                 ↓
          commit proposal
```

## Repair-loop policy

Each bounded implementation task has independent repair budgets:

- deterministic gate: **3** corrective Builder repairs;
- semantic gate: **2** corrective Builder repairs;
- visual gate: **2** corrective Builder repairs;
- same unresolved root cause: **3** corrective attempts.

There is **no global repair ceiling** across gates.

A repair is counted only when a failed gate causes Builder to modify the implementation. The initial implementation and gate reruns without code changes do not consume budget.

If a gate-specific budget or the same-root-cause limit is exhausted, autonomous repair for that failure path stops and the issue is escalated with current evidence preserved.

Budgets reset when moving to a new bounded task.

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

## Gate policy

Completion requires:
- approved scope implemented,
- deterministic checks passing,
- no BLOCKING semantic or code-quality findings,
- warnings reviewed for concrete project risk,
- visual criteria passing when applicable,
- clean final diff with no unintended changes.

Quality is layered:
- repository tooling enforces deterministic rules,
- Builder follows the minimum implementation quality floor,
- Verifier reviews semantic quality issues with concrete engineering consequences.

## Separation rules

- Builder may use targeted checks as feedback, but Test Runner owns the authoritative deterministic gate.
- Verifier never fixes the implementation it reviews.
- Coordinator owns acceptance policy but should use independent evidence instead of re-performing every specialist's work.
- Dependency mutation belongs to Builder; package-manager validation/setup belongs to Test Runner.
- Git commit execution remains user-controlled unless explicitly authorized.

## Model selection

Model assignments in these files are baseline placeholders matching the current setup. Role prompts and workflow semantics should remain unchanged when benchmarking alternative models later.
