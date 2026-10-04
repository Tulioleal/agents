# Evaluation telemetry

The normal development workflow records observational telemetry under:

```text
.agent-runs/<short-run-name>/NNN.md
```

This telemetry is intentionally different from the controlled benchmark.

## Timing

Only autonomous workflow execution time is measured.

A cycle ends before the workflow waits for user input. The next cycle begins when execution resumes. Human response duration is excluded.

Primary timing fields:

- cycle active time;
- cumulative task active time;
- per-agent active time when available.

Per-agent durations may overlap and are diagnostic only.

## Quality

Workflow reports do not assign a quality score.

They record raw quality evidence:

- deterministic gate result;
- relevant test/build/type/lint results;
- semantic findings by severity;
- unverified requirements;
- regressions;
- scope violations;
- code-quality findings;
- visual criterion results;
- acceptance state.

Formal quality scoring belongs to the benchmark because the benchmark has controlled fixtures and ground truth.

## Model/economic telemetry

Record the effective:

- provider;
- model identifier;
- variant;
- reasoning/thinking mode;
- reasoning effort/budget;
- input/output/reasoning/cache tokens;
- tool calls;
- retries;
- cost when directly available or deterministically calculated.

Never estimate unavailable runtime values.

## User intervention

Record intervention count and category, not human response duration.

The original request is not an intervention.
