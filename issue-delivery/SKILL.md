---
name: issue-delivery
description: Deliver an approved, implementation-ready issue through implementation, issue-owned validation, independent code review, authorized correction loops, pull-request creation, and CI to a ready-to-merge handoff. Use when the user asks an agent to act as the delivery operator for a canonical issue, carry an issue through the complete build-review-PR-CI flow, or continue until only human approval and merge remain.
---

# Issue Delivery

Act as the delivery operator for one approved Smallest Coherent Slice. Coordinate
the engineering skills and repository tools that own each stage. Do not absorb
their doctrine, weaken their gates, or let an implementation context serve as
its own independent reviewer.

## Required Input And Skills

Require the canonical issue path or stable issue identifier. Resolve the path
from the repository only when the identity is unambiguous.

Load and follow:

- `$engineering-for-certainty` for implementation and change control;
- every companion it triggers for observability, resilience, auth/security, or
  frontend work;
- `$code-review` for independent, evidence-verified review; and
- `$pull-request-creation` for branch publication and pull-request creation or
  update.

If a required skill is unavailable, stop and name it. Do not reconstruct its
contract from memory.

Treat an explicit durable goal invocation as the persistence contract for the
delivery. Do not create a durable goal merely because this skill was invoked.
When a goal is active, never mark it complete at an intermediate milestone.

## Preconditions

Before changing code, verify that the canonical issue:

- is approved and implementation-ready;
- is one Smallest Coherent Slice;
- names its exact Branch Contract, pull-request base, and worktree mode;
- contains testable acceptance criteria and a complete traceability ledger;
- contains a complete Runtime Acceptance Plan for observable runtime changes,
  or a justified `Not applicable` entry for non-runtime work;
- states implementation change-control boundaries;
- defines semantic review checkpoints when the slice is substantial, or
  explicitly states that the issue is small enough to use one delivery unit;
- contains a Review Loop Contract; and
- defines its Issue Completion Record and status-propagation requirements.

Also resolve the exact Approved Issue Commit. For immediate review-and-delivery,
take its SHA, implementation branch, and worktree from the `$issue-review`
handoff. For a previously integrated backlog issue, resolve the commit on the
verified pull-request base that contains the approved issue revision. The
declared implementation branch must contain that commit before production-code
work begins. Under an Existing PR Correction Contract, it must contain the
commit before newly authorized correction edits begin; the commit may postdate
the pull request's existing implementation.

Inspect the current repository instructions, status, worktrees, default
branch, relevant source and tests, and the issue's claimed dependencies. Record
the runtime worktree path, resolved base SHA, and Approved Issue Commit SHA in
the execution handoff, not in the portable issue.

If the issue is not ready, stop before implementation and report the exact
missing or contradictory contract. Use `$issue-review` only when the user also
asks to revise the issue; do not silently respec approved work.

## Authority Boundary

The user's invocation authorizes the in-scope implementation, validation,
review coordination, mechanical correction loop, branch publication,
pull-request creation or update, and CI repair needed to reach the completion
condition.

It does not authorize changing product intent, acceptance criteria,
architecture, public contracts, schemas, migrations, permissions, security
policy, dependencies, or scope without the decision required by the issue's
Review Loop Contract. It also does not authorize merge, auto-merge, deployment,
reviewer assignment, or unrelated cleanup.

When an outer **Review-to-Merge Mode** invokes this skill, this authority and
completion boundary do not change. Deliver only the approved issue and return
the exact ready-to-merge head and evidence to the outer workflow. The outer
workflow owns `DEFER_FOLLOW_UP` planning artifacts, the final merge decision,
and guarded post-merge branch cleanup; this skill must not absorb those writes
or treat the outer workflow's merge authority as implementation authority.

## Goal And Gate Interaction

A durable goal is a persistence mechanism, not transition authority.

When a goal is active:

- continue only while the current delivery state has an authorized next action;
- never change a review verdict or finding route to preserve momentum;
- never treat the goal's stopping condition as permission to change product
  meaning, scope, architecture, contracts, security posture, dependencies, or
  test strategy;
- keep `AUTO_CORRECT` work inside the current checkpoint until its corrected
  head is revalidated and re-reviewed;
- pause and yield on `USER_DECISION` or `BLOCKED`, leaving the goal incomplete;
- do not cross a checkpoint with an unresolved confirmed finding; and
- mark the goal complete only at the ready-to-merge completion condition.

An active but incomplete goal may be waiting for the user or an external owner.
It does not require the operator to keep acting when no authorized transition
exists.

## Delivery Workflow

### 1. Establish The Implementation Context

When issue review immediately preceded delivery, verify and reuse the declared
implementation branch and linked worktree established by `$issue-review`. Do not
create a second implementation context. Confirm its base ref, resolved base SHA,
and Approved Issue Commit before editing production code.

For a previously integrated backlog issue, create or verify the declared branch
and dedicated linked worktree from the verified base that contains the exact
Approved Issue Commit. Confirm the commit is an ancestor of the implementation
head and that the canonical issue content matches the approved revision.

If the declared branch does not contain the Approved Issue Commit, do not begin
implementation. A purely mechanical mismatch may be reconciled by applying the
exact Approved Issue Commit to the declared branch when that preserves its base
and Branch Contract and introduces no unrelated change. Otherwise pause for
`$issue-review` or user direction. Never rewrite history, rename the declared
branch, or create or publish a second implementation branch as a workaround.
Preserve unrelated user work and stop on an ambiguous mixed worktree.

When an outer review-to-merge workflow supplies an issue-approved **Existing PR
Correction Contract**, use its exact published head and base as the branch
identity instead of requiring a conventional replacement branch. Verify the
current remote head SHA and push authority, then attach a dedicated linked
worktree to that branch before editing. Do not rename the branch, open a
replacement pull request, rewrite history, or proceed against a fork head that
cannot receive the correction. Verify the Approved Issue Commit is present on
that branch before applying any newly authorized correction.

Publish a short pre-work handoff containing the issue, branch, base ref, base
SHA, Approved Issue Commit SHA, runtime worktree path, selected validation, and
triggered doctrine.

### 2. Deliver Review Checkpoints

If the issue defines multiple review checkpoints, read
[Checkpoint Delivery](references/checkpoint-delivery.md) and execute its loop
for each checkpoint in order.

If the issue defines no separate checkpoints, treat the full implementation as
one delivery unit and continue with the normal issue-wide validation and review
steps.

Follow only the current checkpoint's affected surface, guardrails, acceptance
criteria, traceability rows, and required reading. Use TDD where required by
`$engineering-for-certainty`.

Classify discoveries before acting:

- Apply **mechanical** changes that preserve approved intent and scope.
- Pause for **material** discoveries that require a product, contract,
  architectural, security, dependency, migration, or scope decision.
- Stop for **blocking** contradictions, missing prerequisites, or unsafe
  operations.

Reflect every approved material change in the canonical issue before resuming
implementation.

### 3. Complete Full Issue-Owned Validation

Run every applicable traceability row against the combined canonical branch.
Run formatting, linting, unit, integration, E2E, migration, mutation, security,
accessibility, or manual proof required by the issue and triggered doctrine.

After applicable automated validation passes, execute the issue's Runtime
Acceptance Plan using `$engineering-for-certainty`'s Runtime Acceptance Pass
contract. Run the local scenarios against the final combined candidate through
the real external boundary, including the primary journey and exploratory check.
Use the triggered frontend, auth/security, and observability doctrine for design
comparison, Test Identity Plans, disposable inboxes, Test Message Sinks, and
secret-free evidence.

Run preview or staging scenarios when that environment safely exposes the exact
candidate and deployment is already automated or separately authorized. This
workflow does not authorize deployment by itself. If the environment appears
only after pull-request creation, keep the issue `Needs Verification`, create or
update only the draft state permitted by `$pull-request-creation`, and run the
pending scenarios when the environment becomes available. Record post-merge-only
staging proof as a downstream release gate with its owner and trigger.

Distinguish commands actually run from recommended or remote-only checks. Keep
the issue at `Needs Verification` while issue-owned proof is missing, failed, or
stale. Tie every runtime result to the exact local commit or deployed build.

### 4. Run Final Full Integration Review

Invoke `$code-review` against the complete issue-base-to-current-head diff and
the canonical issue. Use the issue's requested review effort, or let
`$code-review` derive it from risk. Keep every finder and verifier read-only.

Review all changed surfaces and interactions across checkpoint boundaries,
including shared contracts, configuration, deleted behavior, and integration
seams. Checkpoint review evidence is an input, not a substitute for this final
review.

Let the review finish discovery, verification, deduplication, and ranking
before changing code. Do not create a noisy one-finding, one-fix cycle while
other independent findings are still being discovered.

### 5. Route Findings And Correct The Change

Route every verified review result through the issue's Review Loop Contract:

- **AUTO_CORRECT**: apply the smallest correction when the finding is
  confirmed, fully inside approved scope, has one clear interpretation, and
  does not choose new product or architectural meaning.
- **USER_DECISION**: pause when the correction could change approved behavior,
  scope, architecture, public contracts, schemas, migrations, permissions,
  security posture, dependency choice, or test strategy; when product context
  is missing; or when reviewers materially disagree.
- **BLOCKED**: stop when safe correction requires missing authority, access,
  credentials, external state, or a prerequisite outside the issue.
- **RESIDUAL_RISK**: record the unresolved assumption without changing the
  implementation. For checkpoint advancement, return `CLEAN` only when the
  issue explicitly permits the risk and it does not weaken acceptance or
  highest-risk proof; otherwise return `USER_DECISION`.

Apply all independent `AUTO_CORRECT` findings as one coherent correction batch
where practical. Preserve the original finding IDs and record each disposition.
For a user decision, present the evidence, impact, concrete options, and a
recommendation. Do not reinterpret a pause as permission to choose silently.

### 6. Revalidate And Re-Review

After a correction batch:

1. Re-run the tests and proof invalidated by the corrections.
   This includes every Runtime Acceptance scenario the changed production,
   dependency, runtime-configuration, deployment, or test-data surface could
   affect.
2. Run a clean `$code-review` pass against the new head.
3. Re-verify prior findings and inspect the complete resulting diff for
   regressions or new issues.
4. Reconcile the new queue with prior finding IDs and dispositions.

Continue while corrections are authorized and each cycle makes measurable
progress. Escalate to `USER_DECISION` when the same root cause survives two
attempted correction cycles, the fix oscillates between alternatives, or a new
finding exposes ambiguity in the approved issue. An issue-specific Review Loop
Contract may set a stricter threshold.

Do not treat a clean review of an earlier commit as evidence for the current
head.

### 7. Write The Local Completion Evidence

Update the canonical issue's Issue Completion Record with the actual diff,
traceability outcomes, validation, checkpoint IDs and accepted head SHAs,
checkpoint and final-review results, finding dispositions, deviations, residual
risks, Runtime Acceptance scenario ledger and environment/build identities,
design comparison and proxy blind spots when applicable, branch, and commit
references. Identify the last behavior-changing reviewed head rather than
trying to name the commit that contains the record itself. Keep the record
concise and keep the status truthful when remote evidence is still pending.

### 8. Create Or Update The Pull Request

Invoke `$pull-request-creation`. Let it verify the Branch Contract, evidence,
intentional commits, push, PR body, stack position, and remote state. Do not
bypass a publication stop condition from inside this composing skill.

### 9. Follow CI To A Terminal State

Monitor every required automated check for the current PR head.

When pull-request automation creates a required preview environment, run the
pending Runtime Acceptance scenarios against the deployed head, update the Issue
Completion Record, obtain an independent `$code-review` audit of the new runtime
evidence, and invoke `$pull-request-creation` again to reconcile draft or ready
state.

- For an attributable in-scope failure, diagnose it, apply the smallest
  authorized correction, run affected local proof, commit and push, then return
  to independent review before trusting the new head.
- For a known flaky check, retry only when repository policy permits it and the
  retry cannot hide a deterministic defect.
- For an unrelated failure, service outage, missing secret, permission problem,
  or externally owned gate, record the evidence and pause instead of editing
  unrelated code or claiming success.

Any production code, configuration, test, generated artifact, or product or
operational documentation change made after review makes the previous
acceptance and final-review proof stale. A later canonical issue, roadmap, or
completion-evidence commit requires current-head review and record
reconciliation, but it does not invalidate runtime proof unless its diff changes
or contradicts the behavior, acceptance, test, or environment contract.

### 10. Reconcile The Ready-To-Merge Handoff

Re-read the PR and verify its base, head, head SHA, commits, body, issue links,
stack position, automated checks, and unresolved review state. Update the Issue
Completion Record and every repository status surface required by the issue so
they describe the same reviewed change head and evidence. If the current head
is later, verify that every descendant changes only canonical issue, roadmap,
or completion-evidence surfaces and invalidates no recorded proof. Review the
complete current head; do not create a self-referential evidence-update loop.

## Completion Condition

Declare delivery complete only when:

- every defined checkpoint has a recorded accepted head and clean advance
  decision;
- the current PR head satisfies every approved acceptance criterion;
- every issue-owned validation and highest-risk verification gate has evidence;
- every required issue-owned Runtime Acceptance scenario passed against the
  exact current candidate, and every post-merge-only pass is linked as a
  downstream release gate with an owner and trigger;
- the current head has passed independent `$code-review`, and any commits after
  the recorded behavior-changing head are verified evidence-only descendants;
- every confirmed finding has a recorded disposition and no unresolved blocker
  remains;
- the pull request truthfully describes and points to the current head;
- every required automated CI check for that head is green;
- the Issue Completion Record and linked status surfaces are current; and
- only human approval and the merge action remain.

Branch creation, implementation completion, a green local test run, one review
pass, PR creation, a push, or CI start is progress, not completion.

## Pause Handoff

When progress requires the user or an external owner, keep the delivery and any
active durable goal incomplete. Report:

- the exact stage and current head;
- the current checkpoint ID and its last frozen or accepted head;
- the blocking evidence;
- work completed and validation still current;
- the smallest decision, authority, credential, or external state needed;
- options and a recommendation when it is a user-owned decision; and
- the exact next action after the blocker clears.

Never hide an unresolved issue behind a caveated `Done` or a completed goal.
