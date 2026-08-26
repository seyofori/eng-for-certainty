---
name: pull-request-creation
description: Verify completed issue work, enforce its branch and evidence contracts, publish the intended commits, and create or update a GitHub pull request with correct readiness, issue identity, stack position, validation, and risk details. Use when the user asks to turn completed local work into a reviewable PR.
---

# Pull Request Creation

Load and follow `$engineering-for-certainty`. Convert completed, verified local work into a truthful GitHub handoff. Own branch verification, intentional publication, PR creation or update, readiness state, and remote verification.

Do not implement missing work, perform code review, repair CI, invent evidence, or broaden the issue. Stop when the work is not ready unless the user explicitly authorizes a work-in-progress PR.

## Preconditions

Resolve and read:

- the canonical issue or implementation handoff;
- its stable issue number and exact Branch Contract, including the declared
  pull-request base ref and worktree isolation mode;
- the repository's branch, commit, PR-title, PR-template, and release conventions;
- the intended base branch or preceding stacked-PR branch;
- the pre-work execution handoff's runtime worktree path and resolved base SHA;
- the complete local diff, commits, untracked files, and validation evidence;
- the traceability ledger, Runtime Acceptance Plan and current scenario evidence
  when observable runtime behaviour changed, and final independent review
  evidence.

If no canonical issue exists, ask for the issue identity before publishing. Distinguish the repository's local numbered issue from a GitHub issue number; never invent `Closes #N` from a local filename.

## Workflow

### 1. Verify Scope And Branch

Inspect repository status and `git worktree list --porcelain` before staging or
creating a branch. Resolve the default branch and upstream without fetching,
pulling, switching, or rewriting history unless that operation is necessary for
the requested publication and authorized.

Require the issue's exact conventional branch name:

```text
<type>/<NN>-<short-kebab-description>
```

Preserve a platform-required prefix, for example:

```text
codex/<type>/<NN>-<short-kebab-description>
```

If completed changes are still on the default branch, create the recorded branch before committing when that safely preserves the current work. If the current non-default branch conflicts with the Branch Contract, stop rather than silently publishing under another identity.

Verify that the branch descends from the Branch Contract's declared base and
that the recorded resolved base SHA was truthful when implementation began.
Independent slices use the verified canonical branch; stacked slices use the
preceding pull-request branch. Do not substitute `origin/main` or another
default branch for a stacked base, and do not call a remote-tracking ref current
without fetching or otherwise verifying it when that action is necessary and
authorized.

When the Branch Contract requires a dedicated worktree, verify the pre-work
execution handoff's runtime path and confirm the implementation branch belongs
to a linked worktree rather than the shared checkout. Treat implementation in
the shared checkout without a recorded repository convention or explicit user
direction as a Branch Contract deviation and stop to reconcile it before
publication. The runtime path may differ across machines; never store it in the
canonical issue.

Inspect every changed and untracked file. Reject unrelated work, unexplained generated artifacts, accidental binaries, stale planning changes, or ambiguous ownership. Never default to staging an entire mixed worktree.

### 2. Verify Completion Evidence

Confirm that:

- every acceptance criterion maps to production code and exact evidence;
- the combined canonical branch, not only helper branches, passed required validation;
- final independent code review completed and accepted findings were fixed;
- triggered doctrine for migrations, auth/security, resilience, observability, and frontend work is satisfied;
- the Migration Proof Harness evidence exists when a database migration is present;
- material deviations are reflected in the canonical issue;
- no required evidence is represented by a placeholder, caveat, or unverified claim;
- every required issue-owned Runtime Acceptance scenario passed against the
  exact current commit or deployed build, every invalidated scenario was rerun,
  and applicable auth and Design Conformance evidence is secret-free and
  complete.

If remote-only validation or a pull-request-created preview Runtime Acceptance
Pass is the only remaining evidence, leave the issue `Needs Verification` and
use draft readiness until that evidence and the independent final audit succeed.
A post-merge-only staging pass may remain a linked downstream release gate with
an owner and trigger; it blocks release, not issue-owned publication proof.

### 3. Resolve Stack Position

Use a Stacked Pull Request only when the issue records a real dependency. Verify the parent issue pack, preceding PR, head and base branches, merge order, and rebase or retarget procedure.

Independent slices must target the canonical base branch directly. Do not create a stack merely because several PRs belong to the same feature.

### 4. Commit And Push Intentionally

- Stage only the confirmed files or hunks.
- Preserve coherent existing commits; do not squash or rewrite them without authorization.
- Create a Conventional Commit only when intentional uncommitted work remains in scope.
- Include rationale, migration notes, validation, and issue references in the body when the subject is insufficient.
- Run the required pre-publication checks after the final staged or committed state.
- Push the exact branch with upstream tracking.

Stop on failed checks, rejected pushes, remote divergence, or ambiguous credentials. Do not force-push unless the user explicitly authorizes the exact rewrite.

### 5. Create Or Update The Pull Request

Prefer the configured GitHub connector after the branch is pushed; use authenticated GitHub CLI fallback only when the connector cannot represent the repository or stack correctly. Update an existing PR for the same head branch rather than opening a duplicate.

Use a conventional title that preserves issue identity, for example:

```text
feat(learning): issue 08 - persist draft answers
```

Follow a stricter compatible repository convention when present. Include `Closes #123` only for an actual GitHub issue intended to close on merge.

Write a conditional PR body containing only applicable sections:

1. Canonical local issue and GitHub issue link.
2. Problem and intended outcome.
3. What changed.
4. Explicitly excluded scope.
5. Important design decisions.
6. Exact automated validation and Runtime Acceptance results, including the
   tested revision and local, preview, or staging environment.
7. Migration proof evidence.
8. Security, privacy, resilience, and observability effects.
9. UI screenshots and accessibility evidence.
10. Risks and rollback or recovery path.
11. Stack position and dependencies.
12. Deliberately deferred follow-up work.

Do not paste empty boilerplate, claim checks that did not run, or hide unresolved work in a completed-looking checklist.

### 6. Derive Pull Request Readiness

Choose exactly one outcome from evidence:

- **Do not create:** material implementation or evidence is incomplete and the user did not request WIP publication.
- **Draft:** the user explicitly requested WIP publication, or required evidence can only run after PR creation. Keep the issue `Needs Verification`.
- **Ready for review:** the issue is verified complete, the traceability ledger,
  current Runtime Acceptance evidence, and final review are satisfied, accepted
  findings are fixed, and no known blocker remains.

Pending GitHub CI alone does not make a completed PR a draft. When draft status exists only to obtain remote evidence, verify that evidence, complete the final audit, and mark the PR ready when every gate passes.

### 7. Verify Remote State

Re-read the PR and verify the repository, number, URL, base, head, head SHA, title, body, draft state, issue links, and stack dependency. Confirm the remote branch contains the intended local commit.

Report the PR URL, readiness, branch and base, commits published, validation evidence, stack position, and anything still unverified. Never claim publication succeeded from a local push or mutation response alone.

## Write Safety

- Treat mixed worktrees, branch mismatches, missing issues, and incomplete evidence as stop conditions.
- Never stage unrelated user changes or use destructive Git recovery.
- Never publish secrets, private fixtures, raw logs, or sensitive local paths in the PR body.
- Never merge, enable auto-merge, request reviewers, assign people, or alter labels unless the user separately requested those actions.
