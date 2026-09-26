# Review Checkpoint Planning

Use this reference when one Smallest Coherent Slice is still substantial enough
that waiting until the end would create a large, difficult review.

## Contents

- When To Use Checkpoints
- Checkpoint Standard
- Checkpoint Table
- Advance Rules
- Final Integration Review

## When To Use Checkpoints

Define review checkpoints when the slice has multiple meaningful implementation
stages, crosses several layers, changes a high-risk contract, or is likely to
produce a diff that would be difficult to review confidently in one pass.

Do not create checkpoints for a tiny or mechanical change. Checkpoints add
review cost and should reduce uncertainty, not merely divide work into equal
line counts.

Prefer two to five checkpoints. More than five is a decomposition warning:
reconsider whether the issue contains multiple Smallest Coherent Slices.

## Checkpoint Standard

Every checkpoint must:

- produce a coherent behavior or contract, not an arbitrary file or line-count
  boundary;
- leave the repository green under the validation owned by that checkpoint;
- be behavior-complete for its owned acceptance and traceability rows;
- name its production and test surfaces;
- name every triggered specialist pass, the proof it owns, and any known
  limitation or required later literal gate;
- identify every issue section or appendix required for implementation and
  review;
- freeze a candidate head before independent review;
- remain current until all authorized corrections are revalidated and
  re-reviewed; and
- record the final accepted head SHA before the next checkpoint begins.

A checkpoint is not a separate issue, branch, pull request, or deployment unit.
It is a review and evidence boundary inside one Smallest Coherent Slice.

For design-backed frontend work, follow
[`$engineering-frontend`'s Design Conformance And
Audit](../../engineering-frontend/references/design-conformance.md). Assign each
Design Audit Matrix row to the earliest checkpoint where the state is reachable
and behaviour-complete through the running application. A named approved mock
adapter may serve as a proxy only when its integration blind spot and later
literal gate are recorded. A component harness, static render, or screenshot
does not make the row checkpoint-complete.

## Checkpoint Table

Use this compact form:

| ID | Behavior complete | Owned criteria and traceability rows | Production and test surfaces | Specialist passes and owned proof | Required reading | Validation | Review range rule |
|---|---|---|---|---|---|---|---|
| `CP1` | `<observable behavior>` | `<AC and ledger IDs>` | `<files, modules, tests>` | `<passes + evidence IDs; design rows when applicable>` | `<issue sections or appendices>` | `<exact commands or manual proof>` | `<issue base -> frozen head>` |
| `CP2` | `<observable behavior>` | `<AC and ledger IDs>` | `<files, modules, tests>` | `<passes + evidence IDs; design rows when applicable>` | `<issue sections or appendices>` | `<exact commands or manual proof>` | `<previous accepted head -> frozen head, plus integration seams>` |

Do not repeat the full Review Loop Contract in every row.

## Advance Rules

A checkpoint has these result transitions:

```text
CLEAN          -> record accepted head; begin the next checkpoint
AUTO_CORRECT   -> correct, revalidate, and re-review the same checkpoint
USER_DECISION  -> pause; keep delivery and any durable goal incomplete
BLOCKED        -> pause; keep delivery and any durable goal incomplete
```

`RESIDUAL_RISK` is a finding route, not a checkpoint result. When the issue
explicitly permits the stated risk and it does not weaken acceptance or
highest-risk proof, record it under `residual_risks` and return `CLEAN` for the
checkpoint. Otherwise return `USER_DECISION`.

`DEFER_FOLLOW_UP` is not a checkpoint route. It is available only to an outer
review-to-merge workflow after pull-request publication; use `USER_DECISION`
when a checkpoint finding cannot be corrected under its approved contract.

A durable goal never changes these transitions. It supplies persistence only
while an authorized transition exists.

Do not advance with an unresolved confirmed finding. Do not downgrade, suppress,
or reinterpret a finding to preserve momentum.

`CLEAN` also requires current evidence for every triggered specialist pass. The
checkpoint record names each pass, its owned proof, outcome, and limitation; it
does not replace the normal code-review pipeline with an angle-by-angle
checkbox sheet.

## Final Integration Review

Checkpoint reviews do not prove the complete feature integration. After the
last checkpoint:

1. Run all issue-owned validation against the combined head.
2. Review the full issue-base-to-current-head diff.
3. Revisit shared contracts and interactions across checkpoint boundaries.
4. Reconcile every checkpoint finding and residual risk.
5. Record the final review and accepted heads in the Issue Completion Record.
