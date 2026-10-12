# Testing and visual-gate policy

## Testing principle

Tests protect explicit behavior and stable invariants. They must not accidentally turn today's repository content into tomorrow's product contract.

### Exact cardinality is appropriate when
- the requirement explicitly specifies the number;
- the number is a domain/algorithmic invariant;
- a page-size, limit, maximum, minimum, or fixed-capacity rule is specified;
- the test creates and owns a fixture with a deliberate fixed size.

### Exact cardinality is usually inappropriate when
- a carousel/list/gallery/feed is data-driven;
- content is expected to grow;
- the assertion merely reflects the current number of repository records/assets;
- adding one valid item forces unrelated tests to update only their expected total.

Prefer presence, ordering, interaction, state, bounds, and empty/single/multiple behavior assertions.

A deterministic failure is evidence. The coordinator classifies whether it represents a product defect, stale/over-specified test, environment failure, or unresolved requirement ambiguity. Tests do not outrank approved requirements.

## Visual evidence ownership

Visual Reviewer owns both acquisition and evaluation of visual evidence.

```text
test-runner → application ready / URL
                ↓
visual-reviewer → navigate / state / viewport / screenshot / visual verdict
                ↓
verifier → contrast supplied visual evidence with requirements; never acquire it
```

If supplied evidence is insufficient, Verifier emits `VISUAL_EVIDENCE_REQUIRED` and Coordinator routes the gap back to Visual Reviewer.
