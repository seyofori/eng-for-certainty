# Checkpoint Review

Use this reference only when `$code-review` is the Independent Reviewer inside
an authorized delivery workflow and the governing issue defines the checkpoint.

## Required Input

Require the canonical issue path, checkpoint ID, issue base SHA, previous
accepted checkpoint SHA when present, frozen candidate head, owned acceptance
and traceability rows, changed surfaces, specialist-pass evidence, applicable
design-audit rows, required appendices, and Review Loop Contract. Return a
contract gap instead of inventing missing scope or authority.

## Review Scope

Use `issue base -> candidate` for the first checkpoint and `previous accepted
checkpoint -> candidate` later. Inspect integration seams with earlier work,
shared contracts, configuration, deleted behavior, and callers. Record exact
SHAs, files, seams, working-tree inclusion, and proof. Restart if the candidate
changes. Activate any newly triggered companion or specialist pass.

## Review Standard

Run the normal pipeline: recover intent, choose effort from risk, inspect every
surface, discover candidates, normalize and deduplicate them, skeptically verify
every survivor, and return route-relevant facts without selecting routes.

## Checkpoint Return Record

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
review_outcome
```

Use `CLEAN_EVIDENCE`, `FINDINGS_RETURNED`, or `REVIEW_BLOCKED`. The Independent
Reviewer does not select routes or a checkpoint result. The Delivery Operator
applies the Review Loop Contract and derives `CLEAN`, `AUTO_CORRECT`,
`USER_DECISION`, or `BLOCKED`.

`CLEAN_EVIDENCE` requires no unresolved confirmed finding, complete prior
finding dispositions, current checkpoint proof, classified specialist and
design-audit outcomes, no stale head, and explicit residual-risk evidence. A
goal cannot change the review outcome, routes, or checkpoint result.

## Final Integration Boundary

The outcome covers only the declared checkpoint range and seams. After all
checkpoints, the Delivery Operator must request final Independent Review of the
full issue-base-to-current-head diff.
