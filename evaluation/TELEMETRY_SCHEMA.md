# Telemetry schema

The cycle-report Markdown files are the human-readable representation of these logical fields.

```yaml
identity:
  run:
  cycle:
  task:

models:
  <role>:
    provider:
    model:
    variant:
    reasoning_mode:
    reasoning_effort:
    reasoning_budget:

timing:
  cycle_started_at:
  cycle_completed_at:
  cycle_active_ms:
  task_active_ms:
  agent_active_ms: {}

usage:
  <role>:
    input_tokens:
    output_tokens:
    reasoning_tokens:
    cache_read_tokens:
    cache_write_tokens:
    tool_calls:
    retries:

economics:
  <role>:
    cost:
    source:
    pricing_version:
    provider_reconciled:

autonomy:
  interventions_cycle:
  interventions_cumulative:
  categories: []

repairs:
  deterministic_used:
  semantic_used:
  visual_used:
  same_root_highest:
  current_root_cause:

quality_evidence:
  deterministic_gate:
  checks: []
  semantic_findings:
  unverified_requirements: []
  regressions: []
  scope_violations: []
  code_quality_findings: []
  visual_results: []
  acceptance_state:

diagnostics:
  changed_files: []
  failure_category:
  rework_attribution:
  unresolved_uncertainty:
```

Human response duration is intentionally absent.
