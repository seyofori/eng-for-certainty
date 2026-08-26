# Checkpoint Review

Use this reference only when `$code-review` is operating inside an authorized
delivery workflow and the governing issue defines the checkpoint.

## Required Input

Require:

- canonical issue path;
- checkpoint ID;
- issue base SHA;
- previous accepted checkpoint SHA, when one exists;
- frozen candidate head SHA;
- checkpoint-owned acceptance and traceability rows;
- changed production and test surfaces;
- required issue sections or appendices; and
- the governing Review Loop Contract.

Return a contract gap instead of inferring missing checkpoint scope or
authority.

## Review Scope

Use:

- `issue base -> frozen candidate head` for the first checkpoint;
- `previous accepted checkpoint head -> frozen candidate head` for later
  checkpoints; and
- targeted inspection of integration seams with earlier accepted checkpoints,
  shared contracts, configuration, deleted behavior, and affected callers.

Record the exact base, head, files, integration seams, working-tree inclusion,
and validation evidence reviewed.

Do not review a moving target. If the candidate head changes during review,
discard stale conclusions and restart against the new frozen head.

## Review Standard

Run the normal code-review pipeline:

1. Recover intent and governing contracts.
2. Choose effort from checkpoint risk, not only checkpoint size.
3. Inspect every changed surface in the checkpoint range.
4. Complete independent candidate discovery.
5. Normalize and deduplicate the complete checkpoint candidate set.
6. Skeptically verify every survivor.
7. Route every non-refuted result through the Review Loop Contract.

Do not lower review assurance because a final integration review will happen
later.

## Checkpoint Return Record

Return:

```text
checkpoint_id
review_base_sha
candidate_head_sha
reviewed_files
integration_seams_checked
owned_criteria_and_traceability_rows
validation_evidence
finding_records
residual_risks
coverage_or_independence_limitations
checkpoint_result
```

Use one `checkpoint_result`:

- `CLEAN`
- `AUTO_CORRECT`
- `USER_DECISION`
- `BLOCKED`

`RESIDUAL_RISK` is a finding route, not a valid `checkpoint_result`. A permitted
residual risk is recorded under `residual_risks` and may coexist with `CLEAN`;
an unpermitted residual risk produces `USER_DECISION`.

`CLEAN` requires:

- no unresolved `CONFIRMED` finding;
- complete disposition of prior checkpoint findings;
- evidence for checkpoint-owned proof;
- no stale review result from an earlier head; and
- every residual risk handled by the governing issue's advance rule.

A durable goal cannot change the checkpoint result or finding routes.

## Final Integration Boundary

State explicitly that the checkpoint result covers the declared checkpoint
range and integration seams only. After all checkpoints, the delivery operator
must request a final review of the full issue-base-to-current-head diff.
