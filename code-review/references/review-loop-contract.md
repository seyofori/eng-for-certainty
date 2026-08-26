# Review Loop Contract

Use this contract only when `$code-review` is the read-only analysis engine
inside a composing delivery workflow such as `$deliver-issue`. Direct code
review keeps the ordinary interactive Review Queue behavior from `SKILL.md`.

## Contents

- Required Issue Contract
- Review Ownership
- Finding Routes
- Checkpoint Advancement
- Delivery Return Record
- Re-Review

## Required Issue Contract

The governing issue must state:

- whether implementation may transition automatically into code review, pull
  request creation, and CI follow-through;
- which confirmed findings may be corrected without user adjudication;
- which changes remain user-owned decisions;
- the revalidation and re-review required after correction;
- the repeated-churn threshold that forces escalation; and
- the ready-to-merge completion condition.

If the issue has no Review Loop Contract, return that gap to the composing
workflow. Do not infer correction authority from the existence of a goal or
from a general request to finish the work.

## Review Ownership

Keep finders and verifiers read-only. They may inspect and run safe read-only or
non-mutating validation, but they must not edit the implementation, commit,
push, post comments, or resolve threads.

The composing delivery workflow owns corrections, publication, CI follow-up,
and user escalation. This preserves independence between the implementation
and the evidence used to judge it.

## Finding Routes

After normal verification, attach exactly one route to every non-refuted
candidate:

- `AUTO_CORRECT`
- `USER_DECISION`
- `BLOCKED`
- `RESIDUAL_RISK`

### AUTO_CORRECT

Use only when all of these are true:

- the finding is `CONFIRMED`;
- the failure and correction are fully inside the approved issue surface;
- the correction has one clear interpretation under the acceptance criteria;
- it preserves the approved architecture and public contracts;
- it adds no dependency, migration, schema, permission, or security-policy
  choice;
- it does not require choosing between plausible product meanings;
- it does not hide, weaken, or replace required proof; and
- verifier evidence does not contain a material disagreement.

An `AUTO_CORRECT` route authorizes the delivery operator, not the reviewer, to
apply the smallest correction and focused regression proof.

### USER_DECISION

Use when correction could change product intent, acceptance criteria, scope,
architecture, public behavior or contract, schema or migration behavior,
permissions, security posture, dependency choice, or test strategy. Also use
it for `NEEDS_CONTEXT`, product-sensitive `CONDITIONAL` results, material
verifier disagreement, oscillating fixes, or the same root cause surviving the
issue's correction threshold.

Return the exact decision, evidence, impact, options, and recommendation. Do
not present an uncertain material claim as an automatic fix.

### BLOCKED

Use when safe progress requires missing authority, credentials, access,
external state, an unavailable required skill, or an out-of-scope prerequisite.
Name the owner or trigger that can clear the block.

### RESIDUAL_RISK

Use for a normal `CONDITIONAL` result whose assumption remains genuinely
unresolved but does not justify changing the implementation. Preserve the
assumption and the evidence that would promote, refute, or close it.

## Checkpoint Advancement

When the review targets a delivery checkpoint:

- `CLEAN` permits the operator to record the accepted head and begin the next
  checkpoint.
- `AUTO_CORRECT` returns to correction, invalidated proof, and re-review of the
  same checkpoint.
- `USER_DECISION` pauses for user adjudication.
- `BLOCKED` pauses for the named authority, access, credential, skill, external
  state, or prerequisite.
- A finding routed `RESIDUAL_RISK` does not create a fifth checkpoint result.
  It permits a `CLEAN` result only when the governing issue explicitly
  classifies the assumption as non-blocking and it does not weaken acceptance
  or highest-risk proof. Otherwise the checkpoint result is `USER_DECISION`.

Do not advance with an unresolved confirmed finding. Do not infer advancement
authority from a durable goal, completion target, deadline, or general request
to finish.

## Delivery Return Record

Return the complete verified queue to the composing workflow only after normal
discovery, deduplication, and verification finish. For every result include:

```text
finding_id
verdict
route
checkpoint_id_when_applicable
review_base_sha
candidate_head_sha
checkpoint_result_when_applicable
file_and_line
failure_scenario
evidence
suggested_correction
route_rationale
proof_invalidated_by_correction
required_rereview_scope
```

This delivery return may batch all `AUTO_CORRECT` items so the operator can
apply one coherent correction batch. It overrides one-at-a-time presentation
only for those automatically authorized items. Present `USER_DECISION` items
through the composing workflow's user-decision discipline.

## Re-Review

After any correction, review the resulting head rather than relying on the old
verdict. Re-run affected proof, re-verify previous findings, inspect the full
resulting diff for regressions, and preserve finding IDs across cycles. Assign
new IDs only to genuinely new root causes.

For checkpoint reviews, the re-reviewed candidate head must become the recorded
accepted head before the next checkpoint begins. A review of an earlier
candidate does not validate a corrected checkpoint.

Checkpoint reviews do not replace the final full integration review.

Do not declare the review loop clean until the current head has no unresolved
`CONFIRMED` finding, every prior finding has a disposition, and every declared
coverage or independence limitation is recorded.
