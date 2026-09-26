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
- checkpoint-owned triggered specialist passes, including each pass's owned
  proof, available evidence, and known limitations;
- checkpoint-owned Design Audit Matrix rows when design conformance applies;
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

Treat the checkpoint's specialist-pass list as required input, not as a ceiling.
If the changed surface triggers an omitted companion or specialist pass, record
the planning discrepancy and activate the pass. Do not return `CLEAN` until its
required proof exists and the checkpoint contract truthfully records it.

For design-backed work, follow
[`$engineering-frontend`'s Design Conformance And
Audit](../../engineering-frontend/references/design-conformance.md). Audit the
current checkpoint's owned rows, affected earlier rows, and integration seams.
Expand to the complete implemented-to-date matrix when the impact cannot be
bounded safely. The final integration review still audits the complete issue
matrix.

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
triggered_specialist_pass_records
design_audit_scope_and_outcomes_when_applicable
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

`DEFER_FOLLOW_UP` is not permitted during checkpoint review. It requires an
outer review-to-merge workflow that owns durable planning and pull-request
writes; use `USER_DECISION` when such a route is proposed at a checkpoint.

`CLEAN` requires:

- no unresolved `CONFIRMED` finding;
- complete disposition of prior checkpoint findings;
- evidence for checkpoint-owned proof;
- current evidence and a classified outcome for every triggered specialist
  pass, with every limitation recorded;
- complete current design-audit evidence for the required checkpoint scope when
  design conformance applies;
- no stale review result from an earlier head; and
- every residual risk handled by the governing issue's advance rule.

A durable goal cannot change the checkpoint result or finding routes.

The specialist-pass record does not replace the normal code-review angles with
a checklist. The reviewer still plans and completes the normal finder packets,
verification, deduplication, and routing required by `$code-review`.

## Final Integration Boundary

State explicitly that the checkpoint result covers the declared checkpoint
range and integration seams only. After all checkpoints, the delivery operator
must request a final review of the full issue-base-to-current-head diff.
