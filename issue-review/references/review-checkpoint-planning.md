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
- identify every issue section or appendix required for implementation and
  review;
- freeze a candidate head before independent review;
- remain current until all authorized corrections are revalidated and
  re-reviewed; and
- record the final accepted head SHA before the next checkpoint begins.

A checkpoint is not a separate issue, branch, pull request, or deployment unit.
It is a review and evidence boundary inside one Smallest Coherent Slice.

## Checkpoint Table

Use this compact form:

| ID | Behavior complete | Owned criteria and traceability rows | Production and test surfaces | Required reading | Validation | Review range rule |
|---|---|---|---|---|---|---|
| `CP1` | `<observable behavior>` | `<AC and ledger IDs>` | `<files, modules, tests>` | `<issue sections or appendices>` | `<exact commands or manual proof>` | `<issue base -> frozen head>` |
| `CP2` | `<observable behavior>` | `<AC and ledger IDs>` | `<files, modules, tests>` | `<issue sections or appendices>` | `<exact commands or manual proof>` | `<previous accepted head -> frozen head, plus integration seams>` |

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

A durable goal never changes these transitions. It supplies persistence only
while an authorized transition exists.

Do not advance with an unresolved confirmed finding. Do not downgrade, suppress,
or reinterpret a finding to preserve momentum.

## Final Integration Review

Checkpoint reviews do not prove the complete feature integration. After the
last checkpoint:

1. Run all issue-owned validation against the combined head.
2. Review the full issue-base-to-current-head diff.
3. Revisit shared contracts and interactions across checkpoint boundaries.
4. Reconcile every checkpoint finding and residual risk.
5. Record the final review and accepted heads in the Issue Completion Record.
