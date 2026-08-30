# Review to Merge

Use this mode only when the user explicitly asks to carry a resolved pull
request through merge. Keep the contract model- and harness-agnostic: assign
authority to reviewer, issue-review, delivery-operator, and pull-request
coordinator roles rather than to a particular model or orchestration runtime.

## Authorization

The explicit invocation authorizes this workflow to:

- route verified findings under the rules below;
- update the governing issue when issue review confirms the change;
- invoke issue-owned delivery for authorized corrections;
- create and publish eligible deferred follow-up issue files and their canonical
  roadmap or index entries on the pull-request branch;
- update the existing pull-request description with those durable links;
- monitor required CI and merge the exact verified head when every gate passes;
  and
- perform the guarded post-merge cleanup defined below.

It does not authorize product or architectural decisions, a new planning system,
invented roadmap priority, external tracker substitution, approval on another
person's behalf, branch-protection bypass, administrative override, deployment,
force-push, or unrelated cleanup.

## Establish The Write Context

Complete the initial read-only review before creating a worktree. When the
verified routes require a governing-issue correction or deferred planning
write, resolve the exact pull-request head repository, owner, branch, base,
remote SHA, push authority, and current worktree ownership. Create or verify the
issue-approved dedicated linked worktree for that published head before
invoking any skill in write mode. Record its runtime path and resolved SHA in
the execution handoff, never in the portable issue.

All governing-issue, implementation, deferred-issue, roadmap, and completion-
record file writes must occur inside that verified worktree. If the branch is
checked out in an ambiguous or dirty shared checkout, belongs to another active
worktree, cannot be fetched, or cannot receive pushes, stop before writing. An
issue-review analysis may prepare a proposed coherent update before isolation
exists, but the coordinator applies no file mutation until this gate passes.

## Route The Complete Queue

Run the full `$code-review` discovery, verification, deduplication, and ranking
pipeline before changing the pull request. Require exactly one route for every
non-refuted result:

- `AUTO_CORRECT`: collect deterministic, in-scope corrections into one coherent
  batch. Use `$issue-review` to verify the governing issue and scope, then
  `$issue-delivery` to implement, validate, independently re-review, commit,
  push, update the existing pull request, follow CI, and update the Issue
  Completion Record. The explicit review-to-merge invocation supplies delivery
  authorization only while the issue remains unchanged in meaning and passes
  its normal gates.
- `DEFER_FOLLOW_UP`: use only when every contract condition in the code-review
  Review Loop Contract passes. Route the deduplicated root causes through
  `$issue-review` Deferred Follow-Up Issue Mode, then publish the resulting
  issue files and roadmap or index update as described below.
- `USER_DECISION`: pause automatic transitions. Use `$grilling` to resolve one
  dependency-aware material decision at a time, then use `$issue-review` to
  update or create the approved issue before `$issue-delivery` resumes. The
  initial invocation is not advance approval for a choice made during grilling.
- `BLOCKED`: stop and name the missing authority, access, credential, skill,
  external state, or prerequisite and its owner or clearing trigger.
- `RESIDUAL_RISK`: record the unresolved assumption. It permits merge only when
  the governing issue explicitly classifies it as nonblocking and it weakens no
  acceptance criterion or highest-risk proof; otherwise route it to
  `USER_DECISION`.

When one root cause survives two correction attempts, a correction oscillates,
or a new result exposes ambiguous approved intent, change its route to
`USER_DECISION`. Preserve stable finding IDs through every cycle.

Process all `AUTO_CORRECT` findings first as one coherent correction batch.
Revalidate and re-review the corrected head, then recompute the remaining
`DEFER_FOLLOW_UP` set before creating planning artifacts. A correction may
resolve, narrow, merge, or invalidate a proposed follow-up; never create the
issue batch from a superseded head. Publish the final deduplicated follow-up
issues as one later planning batch and perform one more current-head review.

## Capture Deferred Follow-Ups

For every `DEFER_FOLLOW_UP` batch:

1. Let `$issue-review` discover the repository's canonical issue directory,
   filename convention, roadmap or index, backlog status, next stable identity,
   and existing related issues.
2. Reuse an exact existing issue when one already owns the root cause. Otherwise
   deduplicate by root cause and create one implementation-ready **Smallest
   Coherent Slice** per independent outcome. A product-research dependency may
   instead become a bounded discovery or decision issue with an exact evidence
   outcome.
3. Record the reviewed pull request as a dependency and use the repository's
   canonical post-merge base for the eventual Branch Contract unless a verified
   stack requires another base. Do not invent priority.
4. Update the canonical roadmap or index in the same write pass.
5. When issue or roadmap content changed, stage only those files, create one
   coherent commit, and push it to the existing pull-request branch without
   rewriting history. When an exact existing issue and roadmap entry already
   contain all required ownership and evidence, make no no-op commit.
6. Update the pull-request description when needed with each stable issue path or link, the
   originating finding IDs, and the evidence that made deferral safe.
7. Re-read the remote pull request and verify that the new head, files, roadmap
   entries, and description links exist.

If the repository has no canonical planning surface, start a focused grilling
decision rather than inventing one. If the pull-request branch cannot receive
the files, stop before merge and report the exact permission, fork, or branch
protection limitation. Do not substitute chat notes, review comments, TODOs, or
external tracker items.

A planning commit changes the reviewed head. Re-run invalidated validation and
the current-head review only when the Git head changed; a PR-description-only
update requires remote metadata verification but does not create a new code
head. Preserve each deferred finding's ID and treat it as a resolved disposition
only after its issue file, roadmap entry, and PR link are verified; do not
report the unchanged implementation as a newly unresolved duplicate.

## Converge On The Current Head

After every correction or planning batch that changes the Git head:

1. Resolve the new local and remote head SHA.
2. Re-run every acceptance, Runtime Acceptance, validation, and CI proof the
   change invalidated.
3. Run a clean `$code-review` against the complete current pull-request diff.
4. Reconcile existing threads and prior findings against that head.
5. Repeat only while an authorized route makes measurable progress.

When exact follow-up reuse or a PR-description-only update leaves the Git head
unchanged, keep the existing current-head review and verify only the durable
issue and roadmap ownership plus the resulting remote PR metadata.

Do not merge from an earlier clean review, a mutation response, a stale CI run,
or an Issue Completion Record whose reviewed change head does not contain the
validated behavior. The record does not name its own containing commit. Permit
later issue, roadmap, or completion-evidence commits only after reviewing the
complete current head and verifying that those descendants invalidate no
acceptance or runtime proof.

## Merge Gates

Immediately before merge, re-read the pull request and verify:

- repository, pull-request number, base, head branch, and exact head SHA;
- the pull request is open, non-draft, conflict-free, and not superseded;
- the governing issue and Issue Completion Record name the reviewed
  behavior-changing head, and every later current-head commit is independently
  verified as issue, roadmap, or completion evidence only;
- every acceptance criterion and required current-head validation has evidence;
- every confirmed finding has a verified disposition, including durable issue,
  roadmap, and PR-link proof for `DEFER_FOLLOW_UP`;
- no unresolved blocking workflow-owned or human-owned review thread remains;
- no unresolved change request, required reviewer approval, stack dependency,
  or repository policy blocks merge; and
- every required CI check for that exact head is green.

Use the repository's established merge method. When no single method can be
discovered safely, stop rather than silently choosing a history strategy. Pass
the expected head SHA to the provider when supported. If the head changes before
the merge is verified, return to current-head validation and review.

Never approve the pull request on another person's behalf, enable auto-merge as
a substitute for current verification, bypass protection, or use an admin
override. If an external approval or check is pending, wait when the workflow
can safely monitor it; otherwise return the exact ready-to-merge blocker.

After the merge mutation, re-read provider state and verify the merged status,
base, merged head SHA, and resulting merge or squash commit before cleanup.

An open downstream pull request based on the current head branch does not by
itself block merging its upstream pull request. An unmet predecessor of the
current pull request does block it. Preserve the merged head branch until every
downstream pull request is safely retargeted or the cleanup reports that it must
remain.

## Guarded Branch Cleanup

Delete the exact remote head branch only after verified merge and only when it
is not the default branch, protected, shared, reused, or the head or base of an
open pull request. Check fork ownership and authenticated deletion authority.
Target an exact resolved ref; never use a wildcard.

For stacked pull requests, retarget each dependent pull request through the
provider's base-branch operation and verify its new base before deleting the
merged base branch. Do not rebase or force-push a published dependent branch as
part of cleanup. When retargeting alone cannot produce a correct diff, retain
the merged branch and return the downstream rewrite as a separate user-owned
decision.

Remove a linked worktree and its local branch only after verifying:

- the worktree belongs to the merged Branch Contract;
- it is clean and contains no untracked files;
- its branch has no unpushed commits or commits absent from the verified merged
  result;
- no other worktree uses the branch; and
- the deletion target is the exact resolved path and branch.

Use non-forcing deletion. A shared checkout, failed safety check, missing
permission, or provider failure retains the affected branch or worktree and is
reported precisely. Re-read remote refs, local branches, and worktree state to
verify every deletion that succeeds.

After a squash or rebase merge, provider evidence may prove that the pull
request's content merged even though its original commits are not ancestors of
the base. Delete the remote head under the normal guards, but retain any local
branch when the version-control system's non-forcing deletion refuses it. Report
that commit-identity limitation instead of weakening the no-force rule.

## Closeout

Report the merged pull request and exact head, validation and review evidence,
finding dispositions, created or reused follow-up issues and roadmap entries,
merge method and resulting commit, deleted branches and worktrees, and every
retained cleanup target with its reason. Never collapse a failed merge or
cleanup verification into success.
