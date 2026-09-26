# Review Loop Contract

Use this contract only when `$code-review` is the read-only Independent
Reviewer inside a composing workflow such as `$issue-delivery`. Direct review
keeps the interactive Review Queue behavior from `SKILL.md`.

## Required Issue Contract

The governing issue must state authorized transitions, the automatic-correction
boundary, user-owned decisions, required revalidation and re-review, the churn
threshold, and the completion condition. An outer review-to-merge workflow must
separately authorize durable follow-up and publication writes. Never infer
authority from a goal or a general request to finish.

## Review Ownership

The Independent Reviewer owns candidate discovery, normalization,
deduplication, skeptical verification, and final evidence verdicts. Every
review context stays read-only: it may inspect and run safe non-mutating proof,
but it must not edit, commit, push, publish comments, or resolve threads.

The Delivery Operator owns routes, corrections, publication, CI follow-up, and
user escalation. This separates whether a claim is true from what the delivery
workflow does next.

## Finding Routes

After receiving the complete verified queue, the Delivery Operator attaches
exactly one route to every non-refuted candidate: `AUTO_CORRECT`,
`DEFER_FOLLOW_UP`, `USER_DECISION`, `BLOCKED`, or `RESIDUAL_RISK`.

Use `AUTO_CORRECT` only when the finding is `CONFIRMED`; the failure and
correction are fully inside the approved issue; the correction has one clear
interpretation; it preserves approved architecture and public contracts; it
adds no dependency, migration, schema, permission, security-policy, or test-
strategy choice; it requires no choice between plausible product meanings; it
does not hide, weaken, or replace required proof; and verifier evidence has no
material disagreement. It authorizes the Delivery Operator—not the Independent
Reviewer—to apply the smallest correction and focused proof.

Use `DEFER_FOLLOW_UP` only in an explicitly authorized review-to-merge workflow
when the confirmed finding violates no current acceptance criterion or promised
behavior; weakens no security, permission, data-integrity, migration-safety,
operational-reliability, or required-validation obligation; conceals no known
regression; leaves the pull request independently releasable; and can become a
bounded coherent implementation, discovery, or decision issue. Severity is
supporting evidence, not the deferral rule. The reviewer returns root-cause
evidence, why the current change remains safe, the minimum affected surface,
and a follow-up seed. The Delivery Operator owns issue creation, prioritization,
publication, and current-head revalidation. This route is unavailable during
checkpoint review or standalone `$issue-delivery`.

Use `USER_DECISION` for material intent, scope, architecture, public contract,
schema, migration, permission, security, dependency, or test-strategy choices;
`NEEDS_CONTEXT`; product-sensitive `CONDITIONAL` results; evidence disagreement;
oscillating fixes; or the same root cause surviving the issue's correction
threshold. Use `BLOCKED` for
missing authority, access, external state, skills, or prerequisites. Use
`RESIDUAL_RISK` for a `CONDITIONAL` result whose explicit assumption does not
justify changing implementation.

## Checkpoint Advancement

After routing the review queue, the Delivery Operator assigns `CLEAN`,
`AUTO_CORRECT`, `USER_DECISION`, or `BLOCKED`. `DEFER_FOLLOW_UP` is not
permitted. `RESIDUAL_RISK` may coexist with `CLEAN` only when the issue makes
the assumption non-blocking without weakening acceptance or highest-risk proof.

## Delivery Return Record

After discovery, deduplication, and verification finish, return every result:

```text
finding_id
verdict
checkpoint_id_when_applicable
review_base_sha
candidate_head_sha
file_and_line
failure_scenario
evidence
suggested_correction
route_relevant_facts
follow_up_issue_seed_when_applicable
proof_invalidated_by_correction
required_rereview_scope
```

The Delivery Operator augments records with `route`, `route_rationale`, and
`checkpoint_result_when_applicable`, then may batch coherent corrections and
deduplicate authorized follow-ups.

## Re-Review

After correction, review the resulting head, rerun affected proof, reassess
prior findings, and inspect the full resulting diff. Preserve IDs for existing
root causes. Checkpoint review never replaces final full integration review.
Do not declare the loop clean until the current head has no unresolved
`CONFIRMED` finding, every prior finding has a disposition, and every declared
coverage or independence limitation is recorded.
