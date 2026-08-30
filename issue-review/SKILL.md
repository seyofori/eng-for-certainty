---
name: issue-review
description: Create or review an issue, ticket, feature file, bug report, roadmap item, or implementation handoff for agent readiness. Use when asked to draft, create, tighten, validate, rewrite, prepare, or assess an issue so another agent or engineer can implement it with zero clarifying questions, including decomposing feature-sized work into Smallest Coherent Slices and validating or assigning stable numbered Conventional Commit-style filenames.
---

# Issue Creation And Review

Create or review the issue against the bar: a competent implementation agent should be able to complete it with zero clarifying questions and produce a robust, validated change. If ambiguity remains, the issue is not ready.

Use plain language at a Grade 10 reading level in the rewritten issue, review findings, decision summaries, and clarification questions so they are quick and easy to understand. Prefer short sentences and familiar words. Preserve exact domain terms, code identifiers, and contract language, and explain necessary jargon when it first appears. Never simplify away technical precision.

Use the user's engineering-for-certainty doctrine as the default engineering standard when reviewing software issues: preserve repo conventions, validate trust boundaries, keep adapters thin and domain logic explicit, prefer explicit expected-failure contracts, and require tests for critical behavior and failure paths.

Require companion engineering doctrine when the issue touches its area:

- Observability: logging, metrics, tracing, audit records, correlation IDs, telemetry, redaction, or frontend log ingestion.
- Resilience: external calls, retries, timeouts, idempotency, concurrency, queues, cron jobs, webhooks, background jobs, or async processing.
- Auth/security: cookies, sessions, CSRF, token handling, actor context, protected routes, permission checks, policy registries, secrets, or authorization boundaries.
- Frontend engineering: frontend architecture, routes or screens, API adapters, hooks, flows or views, forms, client state, accessibility, client telemetry boundaries, or web/mobile testing.

If the companion skill is available in the current agent environment, use it. Otherwise use
the local repository's equivalent doctrine. If neither is available, name the
missing doctrine and do not declare the issue implementation-ready.

For an existing issue, require its path. When the user asks to create an issue,
discover the canonical issue directory, format, index, and next stable number.
Ask for a target path only when those facts cannot be discovered safely.

## Workflow

1. **Establish the issue input**: read the existing issue, or gather the approved problem and decisions for a new issue; identify the requested change, claimed files, dependencies, and current structure.
2. **Determine delivery intent**: use the initial request when it clearly says that implementation will or will not follow immediately; otherwise ask before creating a worktree or performing the final issue write.
3. **Discover repo conventions**: inspect local docs and examples before applying generic rules, including issue filename rules and the next unused issue reference.
4. **Verify claims against code**: check paths, symbols, line references, tests, schemas, commands, and stated behavior.
5. **Decompose and name the work**: prove the issue is one Smallest Coherent Slice or create an ordered child pack, then assign every slice its exact Branch Contract and PR base.
6. **Resolve gaps with `$grilling`**: inspect discoverable facts, investigate empirical unknowns, map all user-owned decisions by dependency, and work through one material decision at a time using stable question IDs.
7. **Accumulate answers**: maintain the decision map across items; do not edit the issue during the review.
8. **Build traceability and execution**: map each acceptance criterion to its production owner and exact verification, define safe sequential or parallel implementation ownership, and set the Review Loop Contract for correction and escalation.
9. **Control issue attention**: keep the implementation contract concise, separate reusable doctrine and raw evidence, and define semantic review checkpoints when the slice is substantial.
10. **Cross-validate**: check the resolved issue, traceability ledger, checkpoint plan, execution plan, and conditional gates for contradictions and missing dependencies.
11. **Establish immediate-delivery isolation**: after shared understanding is confirmed and every selected slice has its exact Branch Contract, create or verify each selected implementation branch and linked worktree before the final write.
12. **Write and anchor once**: write the resolved issue and required planning updates in the owning context. For immediate delivery, commit them on the implementation branch before production-code changes begin.

Never write placeholders, TODOs, partial decisions, or "TBD" sections to the issue. The file is either unchanged while review is in progress or fully resolved when review is complete.

## Delivery Intent And Issue Ownership

Classify the review before the final write:

- **Immediate delivery**: the initial request clearly asks to review or prepare
  the issue and then implement, deliver, build, fix, or carry it through.
- **Backlog only**: the initial request explicitly says review only, planning
  only, backlog capture, or not to implement.
- **Unresolved intent**: neither outcome is explicit. A request such as "review
  this issue" or "create this issue" does not settle what follows. Ask whether
  implementation will follow immediately. Do not silently choose a default.

When immediate delivery could refer to more than one resulting slice and the
initial request does not select them, ask which slices will begin now. Do not
create worktrees for every child merely because the parent pack is ready.

Do not infer immediate delivery from `Ready` status, priority, a roadmap
position, a complete Branch Contract, or the issue appearing implementation-
ready. A composing workflow that already declares its branch and delivery mode,
such as review-to-merge correction or deferred-follow-up capture, supplies this
intent explicitly; do not ask again.

For every Smallest Coherent Slice selected for immediate delivery:

1. Finish discovery, decomposition, decisions, cross-validation, and shared-
   understanding confirmation before mutating git state.
2. Verify the declared base ref and resolve its SHA. Inspect repository status
   and `git worktree list --porcelain`.
3. Create or verify the exact Branch Contract branch and its dedicated linked
   worktree. If the branch belongs to another worktree, contains ambiguous work,
   or cannot be created from the declared base without changing the contract,
   stop instead of inventing another branch.
4. Perform the final issue, roadmap, index, appendix, and required reference
   writes inside that worktree. Do not finalize them on a planning branch and
   transfer implementation elsewhere.
5. Record the runtime worktree path and resolved base SHA in the execution
   handoff, never in the portable issue.
6. Stage only the finalized issue and its required planning-surface updates,
   then create one coherent Approved Issue Commit on the implementation branch.
   Do not begin production-code changes before that commit exists. Do not push
   merely because issue review completed; publication remains separately
   authorized or owned by the composing delivery workflow. Under an Existing PR
   Correction Contract, create the commit before newly authorized correction
   edits; it need not predate the pull request's existing implementation.

For backlog-only work, do not create the eventual implementation worktree merely
because the issue is ready. Write through the repository's authorized planning
workflow. When implementation is requested later, its branch must start from a
verified base containing the exact Approved Issue Commit, and every later issue
update remains with that implementation branch.

## Convention Discovery

Before gate review, inspect only the relevant local sources:

- Repo guidance: `AGENTS.md`, `CONTEXT.md`, `CONTEXT-MAP.md`, `DESIGN.md`, `README.md`, `PRODUCT_DECISIONS.md`.
- Planning surfaces: `ROADMAP.md`, `ROADMAP.DONE.md`, `TODO.md`, `docs/`, `_features/`, `features/`, `issues/`.
- Similar completed or active issue files.
- Existing tests near the affected code.
- Schema/type files named by the issue or implied by the affected area.
- Architecture or engineering doctrine docs, including `engineering-for-certainty`-style local guidance when present.

Prefer the repo's current issue format and testing style over this skill's fallback structure. If the repo has multiple contexts, use `CONTEXT-MAP.md` or nearby context docs to select the right context before reviewing terminology.

### Issue File Naming

For pending issue files, require the default filename format
`NN-<conventional-type>-<kebab-case-name>.md`, for example
`28-feat-student-progress-dashboard.md` or
`29-fix-session-report-score-rounding.md`.

- Use a two-digit, zero-padded, stable issue reference (`00`, `01`, ...).
  Assign the next unused number from the repository's canonical issue index or
  issue directory. Do not renumber existing issues when priority changes.
- Use the Conventional Commit type that describes the work: `feat`, `fix`,
  `refactor`, `test`, `docs`, `chore`, `perf`, `build`, or `ci`.
- Keep the remaining name concise and kebab-case. Do not repeat the type or a
  redundant implementation verb in the name.
- If the repository defines a stricter compatible convention, follow it. If it
  defines a conflicting convention, report the conflict instead of silently
  renaming the issue.
- Completed historical issues may retain their existing names. Numbered
  sub-issue packs may retain an explicit pack-local numbering scheme.
- When the final review rewrites or renames an issue, update the canonical
  roadmap/index and every in-repository reference in the same write pass.

If the issue uses a domain term that conflicts with the local glossary or product docs, stop and ask the user to resolve the term before continuing.

### Composed Review-to-Merge Authorization

When an explicit review-to-merge workflow invokes this skill, that invocation
supplies write authorization and shared-understanding confirmation only for a
coherent issue update derived entirely from verified findings, the existing
approved issue meaning, and discoverable repository conventions. Do not ask for
a redundant confirmation before that mechanical write.

If the update would choose or change product meaning, acceptance, scope,
architecture, public contracts, schemas, migrations, permissions, security
policy, dependencies, test strategy, planning-system structure, or priority,
route it to `USER_DECISION`, use `$grilling`, and require explicit confirmation
of the resolved issue before writing.

Mechanically bounding a new deferred issue to the verified root cause, affected
surface, and required proof is authorized when those facts have one coherent
interpretation. Choosing among plausible product outcomes, broadening beyond
that evidence, combining independent outcomes, or assigning priority remains a
user-owned scope decision.

### Deferred Follow-Up Issue Mode

When an explicitly authorized pull-request review supplies findings routed
`DEFER_FOLLOW_UP`, create durable planning artifacts without changing the
implementation:

- Re-verify that every supplied finding satisfies the composing workflow's
  contract-based deferral criteria. Do not infer deferral from Low severity.
- Search existing issue files, roadmap entries, and completed-work archives.
  Reuse or update an exact existing issue instead of creating a duplicate.
- Deduplicate new findings by root cause and create one **Smallest Coherent
  Slice** per independently implementable outcome, not one file per review
  comment or one catch-all cleanup issue.
- Make each issue implementation-ready under this skill. When later
  implementation depends on product research, create a bounded discovery or
  decision issue with an exact evidence outcome rather than a vague issue or
  placeholder.
- Follow the repository's canonical filename, issue directory, roadmap or
  index, and backlog status. Do not invent priority or silently create a new
  planning system.
- Record the reviewed pull request as a dependency and choose the eventual
  Branch Contract against the repository's canonical post-merge base unless a
  verified stack requires another base.
- Write the complete issue files and roadmap or index update in one coherent
  pass, then return their paths and stable identities to the composing
  pull-request workflow for commit, push, PR-description update, and
  current-head revalidation.
- When an exact existing issue and roadmap entry already satisfy the finding,
  verify and return them without manufacturing a no-op file change or commit.

If no canonical planning surface exists or the supplied branch cannot receive
the planning files, stop and return that exact gap. Do not substitute a chat
note, pull-request comment, external tracker item, TODO, or invented directory.

## Decomposition And Branch Contract

Every creation or review must make one explicit decomposition decision: either
the issue is already one Smallest Coherent Slice, or it must become a parent
pack of smaller child issues. Do not use line count alone.

A Smallest Coherent Slice must:

- have one coherent observable outcome;
- be independently implementable, testable, and reviewable;
- leave the repository green after integration;
- own exact acceptance criteria, production surfaces, tests, dependencies, branch, and PR;
- avoid splitting contracts, persistence, behavior, and proof into technical micro-tasks that are not meaningful alone.

The slice need not be independently deployed when the product intentionally releases only after the full pack is complete. When splitting, record the parent, ordered children, cross-slice contracts, and release boundary. Preserve existing pack-local numbering when it is already stable.

Do not create or approve one feature-sized implementation issue or pull request
when the feature contains multiple Smallest Coherent Slices. Keep the feature as
a parent issue pack, and require each child slice to own exactly one branch and
one pull request. A single feature-wide pull request fails readiness unless the
issue proves that the work is one indivisible coherent outcome rather than
several outcomes grouped for convenience.

Require each slice to record its exact conventional Branch Contract before implementation:

```text
<type>/<NN>-<short-kebab-description>
```

Preserve a platform-required prefix such as `codex/`. The type and stable issue number must agree with the issue filename. A branch suggestion or pattern without the resolved name does not pass.

For correction of an existing pull request under an explicit review-to-merge
workflow, do not rename or replace its published head branch merely to satisfy
the normal issue-derived naming convention. Instead, record an **Existing PR
Correction Contract** containing the resolved repository, pull-request number,
head owner and branch, base branch, reviewed head SHA, push authority, and
dedicated-worktree mode. This exception authorizes updates only to that existing
pull request. A fork without verified push authority is `BLOCKED`.

Record the exact pull-request base ref and worktree isolation mode with the
Branch Contract. Default to a dedicated linked worktree. Independent slices use
the verified canonical branch as their base; stacked slices use the preceding
pull-request branch. Do not substitute the default branch for a real stack
dependency or assume that a remote-tracking ref is current without verification.

Keep the issue portable: never store an absolute machine-specific worktree path
in the canonical issue. Require the pre-work execution handoff to record the
runtime path and resolved base SHA before work begins. Record a repository
convention or the user's explicit direction when the slice will use the shared
checkout instead.

Use Stacked Pull Requests only for real dependencies. Record each PR's head, base, preceding PR, merge order, and rebase or retarget procedure. Independent slices must share the canonical base branch and remain parallel rather than being forced into a stack.

## Issue Attention And Reading Contract

An issue must be complete without becoming a transcript, raw evidence store, or
copy of reusable engineering doctrine. Optimize for instruction salience: the
implementation agent must be able to distinguish approved intent, acceptance
criteria, change authority, and stop conditions from supporting detail.

For every non-trivial child issue, add a concise `Agent Start Here` section near
the top. Keep it to roughly fifteen lines or fewer and include:

- the one observable outcome;
- the exact Branch Contract and pull-request base;
- the acceptance-criterion IDs;
- the Runtime Acceptance environments and any downstream release gate;
- the allowed-change and pause boundaries;
- the review-checkpoint sequence, when present; and
- every linked appendix that must be read before a named checkpoint.

Keep reusable rules in the governing skills and repository documentation. The
issue should name the activated doctrine and record only issue-specific
decisions, contracts, risks, and deviations. Do not copy full engineering
manuals, generic review procedures, or raw test output into each issue.

Use linked appendices only for dense issue-specific material such as large
state-transition tables, error matrices, migration fixtures, or verified
baseline evidence. Critical product decisions, acceptance criteria, authority
boundaries, and pause conditions must remain in the core issue. Every appendix
required for a checkpoint must be named in that checkpoint's reading set.

Keep the Issue Completion Record concise. Record exact commands, outcomes,
finding dispositions, and durable references, but do not paste raw logs,
complete review transcripts, or repeated doctrine into the canonical issue.

Use issue length only as a review signal:

- At roughly 300 lines, run an explicit compression and decomposition check.
- At roughly 500 lines, require the issue-review handoff to explain why the
  child remains one Smallest Coherent Slice and why the remaining material
  cannot be compressed or moved to a linked appendix.
- Do not split an issue solely to satisfy a line count.
- Parent issue packs may be longer, but they must not become the direct
  implementation target for `$issue-delivery`.

For a substantial Smallest Coherent Slice, read
[Review Checkpoint Planning](references/review-checkpoint-planning.md) and
define semantic review checkpoints before approving the issue.

## Claim Verification

Report mismatches before gate review:

- File paths that are missing, renamed, or too vague.
- Line numbers that drift by 3 or more lines.
- Referenced functions, classes, types, constants, routes, tables, commands, events, or config keys that do not exist where claimed.
- Behavior claims contradicted by current code or tests.
- Dependency claims contradicted by roadmap, issue files, or completed-work archives.
- Existing adjacent documentation that the issue would leave stale or false. When an issue adds content to a README, runbook, setup guide, enumerated list, exception rule, or placeholder-swap procedure, verify the neighboring claims against the current repo rather than checking only the new instructions for internal consistency.

Do not proceed past a mismatch until the correct reference is confirmed or the issue is updated in the accumulated answers.

## Universal Gates

Check every issue against these gates.

### 1. Problem Or Motivation

The problem must be concrete.

- Bugs: state actual behavior, expected behavior, and reproduction scenario.
- Features: state missing capability, user or system value, and why now.
- Chores/refactors: state the risk, constraint, or future work unlocked.

Vague phrases like "improve this", "fix logic", or "clean up" do not pass.

### 2. Affected Surface

The issue must name the affected files, modules, routes, commands, tables, schemas, APIs, UI flows, or docs. Include line numbers when the relevant area is narrow. A whole-file reference is acceptable only when the whole file is intentionally in scope.

When behavior depends on a platform or library mechanism, name the literal mechanism and its production owner. Do not accept abstractions such as "refetch," "refresh," "protect route exit," or "handle navigation" without the concrete API, hook, event, or interception point—for example, an imperative query call or History API interception—and the symbol or module that invokes it. Verify that the installed framework and version support the named mechanism.

When the issue introduces an environment variable that a runtime component reads directly, name the literal delivery mechanism, not only its presence in `.env` or `.env.example`. Trace the specific variable from its consumer—such as a workflow expression or `process.env` read—through every required injection point, such as a Compose `environment:` block or systemd `Environment=` entry. Do not infer its delivery from a superficially similar variable.

### 3. Root Cause Or Background

For bugs, identify the root cause rather than only the symptom. For features and chores, include enough background for an agent to make local judgment calls without re-litigating the product decision.

### 4. Acceptance Criteria

Every criterion must be testable or inspectable. It should describe an observable outcome, not an implementation wish.

Good: "`getApprovedQuestions` excludes draft and rejected records."
Bad: "question filtering works correctly."

For an issue that changes observable runtime behaviour, include an acceptance
criterion requiring every issue-owned Runtime Acceptance scenario to pass
against the exact final local candidate and every applicable authorized
pre-merge preview or staging candidate. Link post-merge-only staging proof as a
separate downstream release gate rather than implying it already passed.

When a criterion uses universal or negative language ("only", "all", "every", "never", "no other"), verify it cannot be satisfied by a weaker existential check. Either the issue enumerates the exact elements the claim covers and states that each must independently hold, or it says explicitly that the check is a spot-check and why that's acceptable. A criterion like "the file contains only placeholder values" is otherwise easy to implement as "at least one placeholder is present" — a check that passes even when most of the listed values are real.

When a criterion claims mutual exclusivity ("reachable from X, not from anyone else"), require independent verification in both directions: reject every disallowed side, and identify and accept the allowed side specifically rather than merely proving that some request succeeds. If the test method has a structural blind spot in either direction, state the blind spot and require a named compensating manual or automated verification step. For example, same-Docker-host traffic may use hairpin NAT and therefore cannot prove source-IP behavior across a genuine external network boundary.

When a criterion combines a formatted or rounded value shown to a user with a pass/fail or status indicator derived from the same raw quantity, require an explicit rule preventing disagreement at rounding boundaries. Derive both from the same displayed value, or state the precise reason divergence is intentional.

### 5. Scope Boundary

State what is explicitly not changing. This prevents adjacent refactors, UX expansion, schema churn, or product decisions from leaking into the task.

### 6. Implementation Change Control

The issue must state what judgment the implementation agent may exercise without
asking, and what discoveries require pausing.

Include:

- Allowed mechanical adjustments, such as import fixes, local naming alignment,
  adapting to existing helper APIs, formatting, or adding focused test fixtures.
- Pause triggers, including new migrations, schema changes, public contract
  changes, auth or permission changes, new dependencies, cross-domain refactors,
  behavior drift, test-strategy changes, or affected files outside the named
  surface.
- The canonical surface to update if the plan changes, such as this issue file,
  `ROADMAP.md`, an ADR, or a feature file.
- Any known uncertainty that should be resolved before implementation rather
  than discovered midway.

### 7. Dependencies

Name upstream blockers, downstream dependents, and ordering constraints. Check roadmap and nearby issue files for implied dependencies the issue forgot to mention.

If this issue changes or extends a rule, convention, or domain concept that another issue file explicitly claims to inherit, match, or reuse (search sibling issue files for phrases like "match issue #N", "per issue #N", "same as #N", "inherits from #N"), or that is documented in `CONTEXT.md`/`CONTEXT-MAP.md`, open every referencing file now. Either reconcile them in this same review pass, or record an explicit follow-up issue to do so before this issue is marked ready — never leave a referencing issue or the glossary silently stale.

If the issue changes a config value, protocol, or URL with existing consumers, search for every consumer and inspect each one even when the expected conclusion is "no code change needed." State whether the change alters each consumer's failure modes, error classification, or default error state as well as its happy path. Pay particular attention to generic health checks, doctor commands, and catch-all error handlers, where a new expected failure can otherwise become a misleading operator message.

### 8. Test Approach

Name the test file or test layer. Include at least one concrete scenario in the repo's local test style. Critical behavior changes must include tests for success, expected failures, validation failures, and error mapping where those cases apply.

Before implementation, include a traceability ledger with one row per acceptance criterion:

| Criterion | Production owner | Exact test or verification |
|---|---|---|
| `<criterion ID and outcome>` | `<file + symbol/module>` | `<test file + case name, or justified manual check>` |

Split criteria that have multiple independently observable outcomes. Every row must name the code that owns the behavior and the exact evidence that will prove it; broad entries such as "frontend," "service layer," or "covered by tests" do not pass. Manual verification may substitute for automated criterion proof only when automation is impractical and the issue explains why. A Runtime Acceptance Pass is complementary proof and remains independently required for observable runtime changes even when automated tests cover the same criteria.

Require a post-implementation issue-against-diff audit by an independent reviewer or a separate skeptical pass that did not rely on the implementer's completion summary. Reconcile every ledger row against the actual production diff and test evidence, identify unplanned changes, and leave the issue unverified while any row lacks evidence.

When an acceptance criterion names a specific runtime mechanism (a "scheduled" job, a "background" retry, an "on reconnect" handler), the Test Approach must state whether verification exercises that literal mechanism or a named, justified proxy (e.g. a manual one-shot invocation of the same script the scheduler calls). An unstated substitution leaves a criterion looking tested when only an adjacent code path was actually exercised.

Apply the same rule to platform and library mechanisms named under Affected Surface: exercise the actual API, hook, event, or interception path, or name and justify the proxy and its blind spot.

When the issue introduces a new artifact covered by an existing repo-wide invariant, such as image pinning, network exposure, or secrets hygiene, inspect the shared test files or suites that encode that invariant. The Test Approach must name the existing assertions and explicitly extend them to the new service, image, port, secret, or other artifact rather than adding only scenario-specific tests.

When verification is split between standalone checks and a live or deployed instance, identify whether the issue-owned live-only portion contains the scenario most likely to expose an integration defect, such as cross-branch recombination, interacting failure paths, or multi-service behavior. If it does, the issue must remain explicitly unverified—use a status such as `Needs Verification`, not `Done` with a caveat—until that scenario has run successfully. When the environment can receive only merged changes, make the scenario an explicit downstream release gate with its owner and trigger; it blocks release rather than falsely claiming pre-merge proof.

If no local convention is visible, use:

```ts
test(`
  given <context>
  when  <action>
  then  <assertion>
`, () => {
  // ...
})
```

#### Runtime Acceptance Plan

Every issue that changes observable runtime behaviour must define a Runtime
Acceptance Plan using `$engineering-for-certainty`'s
[Runtime Acceptance Pass](../engineering-for-certainty/references/runtime-acceptance.md).
A documentation-only or other non-runtime issue may record `Not applicable`
with a concrete reason.

The plan must:

- map every accepted externally observable criterion and materially distinct
  outcome to a stable runtime scenario;
- include one complete primary journey and a targeted exploratory check of the
  changed area and its integration seams;
- name the local runtime, startup command, safe test data, real external
  boundary, exact actions or requests, expected outcomes, cleanup, and evidence;
- require local proof against the final combined candidate after automated
  validation;
- require preview or staging proof when the exact candidate can be safely
  deployed before merge and deployment is automated or separately authorized;
- record post-merge-only staging proof as a mandatory downstream release gate
  with its owner and trigger;
- identify every necessary proxy, what it proves, its blind spot, and the later
  literal gate when that blind spot is material;
- define a secret-free scenario ledger tied to the exact revision and
  environment, plus which later changes invalidate and require each scenario to
  run again;
- include a Test Identity Plan and Test Message Sink or inbox rules from
  `$engineering-auth-security` when authentication is exercised; and
- include the exact authoritative design, version, states, platforms, viewports,
  screenshots, and approved deviations required by `$engineering-frontend`'s
  Design Conformance Pass when the frontend is based on a design; verify the
  source and version are accessible before declaring the issue ready.

Do not treat an in-process handler call, component harness, mock adapter by
itself, or implementer summary as runtime proof. A running mock-backed frontend
may prove only an explicitly frontend-only slice when the plan names the adapter
as a proxy and defers live backend integration proof. Keep the issue
`Needs Verification` while required issue-owned runtime evidence is missing,
failed, or stale.

### 9. Operational And Migration Safety

If the issue changes persisted data, generated artifacts, imports, migrations, deployment config, background jobs, or external integrations, state how the change is applied, rolled back or retried, and verified.

Every database schema or data migration must use the `$engineering-for-certainty` Migration Proof Harness. Require an isolated disposable local database container, the prior released schema, representative legacy and boundary fixtures, the exact production migration mechanism, after-state schema and data assertions, application read/write proof, and supported repeat invocation or no-op behavior. Require rollback proof only when rollback is part of the deployment contract; otherwise name forward recovery. Structural code migrations do not trigger the database harness.

If the issue adds persisted state alongside existing persisted state already enumerated in documentation, update every affected table, destructive-operations warning, backup or restore runbook, and blast-radius description. Documenting the new state in isolation does not pass when existing operational guidance would become false or incomplete. For example, a warning that says an operation destroys "both" named volumes becomes false when a third volume is added.

### 10. Observability And Errors

If the issue changes runtime behavior, state expected error behavior and any logging, metrics, audit events, or user-visible messages needed by the repo's conventions. If none are needed, say why.

When telemetry changes, require typed default-deny Safe Log Events. The issue must name each event family, allowed fields, source adapter, correlation ownership, retention and reader access, and tests proving that raw errors, arbitrary context, secrets, direct PII, and forbidden source-specific fields cannot reach logs, metrics, traces, audits, or fallbacks.

For frontend operational logging, require the client emission boundary and condition, deduplication or aggregation rule, production sampling or debug policy, bounded queue and delivery behavior, backend ingestion schema, authentication or reduced anonymous event set, rate and payload limits, non-recursive failure path, and Telemetry Budget. Product analytics and authoritative backend security or audit events remain separate.

When errors use a discriminated union or coded envelope, require a code-to-details matrix that names each error code, its compatible `details` shape, its producer, and each consumer's behavior. The Test Approach must cover every valid mapping plus missing envelopes, malformed envelopes, unknown codes, and code-incompatible `details`. State the fallback behavior for invalid combinations; do not let consumers trust `details` based on shape alone or a code alone.

### 11. Engineering Certainty

The issue must make the robust implementation path clear:

- Identify the trust boundary and validation schema for every external input.
- Keep route/page/controller/adaptor work thin; name where domain logic belongs.
- State the expected-failure contract used by the repo, such as `Result<T, E>`, error unions, or the local equivalent.
- Require exhaustive handling for discriminated states, enums, result variants, or UI status unions.
- State whether config, auth context, clocks, clients, or persistence are injected or otherwise supplied through the repo's existing pattern.
- For structural refactors, require behavior-preservation tests and name what must not drift.
- Identify any companion doctrine triggered by the issue and include its requirements in the issue.

### 12. Issue Completion Record

The rewritten issue must define its completion requirements. The issue is not
complete until the implementation or integration agent writes an **Issue
Completion Record** to the canonical issue file after final review and before
reporting the issue as done. A chat summary, pull-request description, commit,
or CI result may support the record but cannot replace it.

The record must contain:

- the final status and completion date;
- the last behavior-changing reviewed head to which acceptance, validation, and
  Runtime Acceptance evidence bind;
- the production, test, configuration, and documentation surfaces actually
  changed;
- the reconciled result of every traceability row, including exact validation
  commands and outcomes;
- every Runtime Acceptance scenario result, exact revision and environment,
  proxy blind spot, invalidated proof re-run, design comparison when applicable,
  and linked downstream release gate;
- the final `$code-review` outcome and the disposition of every confirmed
  finding;
- when checkpoint reviews were used, the checkpoint ID, accepted head SHA,
  review range, review outcome, correction dispositions, and invalidated proof
  re-run for each checkpoint;
- deviations from the issue and any unplanned changes;
- residual risks and every unverified or deferred check, with a linked owner or
  trigger for downstream work; and
- branch, commit, and pull-request references when available.

The record is an evidence index, not an evidence dump. Prefer compact tables and
durable links to raw CI, pull-request, test, or review evidence.

Do not require the record to name the commit that contains the record itself.
When later commits change only the canonical issue, roadmap or index, deferred
issue files, or completion evidence, record the last behavior-changing reviewed
head and require an independent current-head review to verify that every later
commit is evidence-only and invalidates no recorded proof.

The implementation or integration agent owns the write-back. The final reviewer
must verify the completed record against the raw diff, test output, and review
evidence; the reviewer does not become the canonical issue writer. Update any
roadmap, index, or completed-work archive that tracks the issue's status in the
same write pass so those surfaces cannot contradict the issue.

Keep the issue at `Needs Verification` while any issue-owned acceptance,
review, or highest-risk verification gate lacks evidence. An explicitly
out-of-scope downstream or release gate does not block `Done` only when the
issue links it and names its owner or trigger. Never use `Done` with a caveat to
hide missing issue-owned evidence.

### 13. Review Loop Contract

Every rewritten issue must state how implementation hands off to review and how
verified findings are routed. Do not rely on a long chat prompt, an implied
agent workflow, or the existence of a durable goal. If automatic delivery is
not intended, state that the workflow is human-gated and disable automatic
correction explicitly.

For an issue intended for `$issue-delivery`, include:

```md
## Review Loop Contract

- Delivery mode: `$issue-delivery` to a ready-to-merge handoff.
- Review checkpoints: `<none; treat the issue as one delivery unit>` or
  `<ordered checkpoint IDs and outcomes>`.
- Automatic transitions: implementation -> checkpoint validation ->
  checkpoint review -> authorized corrections -> checkpoint revalidation and
  re-review -> next checkpoint -> final full validation and integration review
  -> pull request -> CI follow-through.
- Checkpoint advance rule: advance only from a clean accepted checkpoint head.
  `AUTO_CORRECT` returns to correction and re-review of the same checkpoint.
  `USER_DECISION` and `BLOCKED` pause delivery. An unresolved confirmed finding
  never advances.
- Auto-correction authority: `AUTO_CORRECT` only for confirmed, deterministic,
  in-scope corrections that preserve approved intent, architecture, contracts,
  security posture, dependencies, and test strategy.
- User decision triggers: `USER_DECISION` for product or domain meaning,
  acceptance or scope changes, architecture, public contracts, schemas,
  migrations, auth or permissions, security policy, dependencies, test
  strategy, material verifier disagreement, or missing product context.
- Blocked triggers: `BLOCKED` for missing authority, credentials, access,
  external state, required skills, or out-of-scope prerequisites.
- Residual-risk rule: `RESIDUAL_RISK` is a finding route, not a checkpoint
  result. When the issue explicitly classifies the stated assumption as
  non-blocking and it does not weaken an acceptance criterion or highest-risk
  verification gate, record the risk and permit a `CLEAN` checkpoint result.
  Otherwise use `USER_DECISION` as the checkpoint result.
- Re-review rule: after every correction batch, re-run invalidated proof and a
  clean review of the resulting head; preserve finding IDs and dispositions.
- Churn threshold: escalate when the same root cause survives two correction
  attempts or the fix oscillates. Use a stricter issue-specific limit when risk
  warrants it.
- Final integration review: checkpoint reviews do not replace a final
  `$code-review` of the complete issue-base-to-current-head diff.
- Goal behavior: a durable goal supplies persistence only while an authorized
  transition exists. It never changes a finding verdict or route, expands
  correction authority, or permits crossing a non-clean checkpoint.
- Completion target: current-head acceptance evidence, clean independent
  review, green required automated CI, current Issue Completion Record, and a
  truthful pull request with only human approval and merge remaining.
```

Tighten the default contract for the issue's risk, but never broaden automatic
authority. A durable goal supplies persistence, not permission to resolve a
material ambiguity. Reviewer contexts remain read-only; the delivery operator
owns authorized edits, validation, publication, and CI follow-through.

## Conditional Gates

Apply only when the issue scope triggers them. Use repo-specific docs and existing patterns to decide whether each gate applies.

- **Auth and permissions**: identify the server-side authorization boundary; client-only checks never suffice.
- **Observability**: require typed Safe Log Events, source-specific allowlists, correlation/request context, privacy and log-injection tests, retention and reader access, and verification. Apply `$engineering-resilience` when telemetry uses queues, retries, timeouts, circuit breakers, or an external sink.
- **Resilience**: require timeout, retry/backoff, idempotency, concurrency, and recovery behavior where relevant.
- **External data boundary**: name the validator/parser/schema used before raw data reaches domain logic; prefer `.safeParse()` or the repo's equivalent boundary API.
- **Database writes or concurrent writes**: state uniqueness constraints, idempotency, race handling, and deletion policy.
- **Destructive test operations against shared-tooling infrastructure**: when the Test Approach includes operations that delete or reset state (volume/database teardown, `down -v`-style resets, bucket/queue purges) against infrastructure whose tooling (compose project name, database name, bucket name, queue name, etc.) could also be used to run a real or production instance, require the issue to name how the test instance is isolated (a distinct project name, prefix, or environment label) so its teardown can never reach a real instance's resources.
- **Event or audit emission**: name event types and payload constraints; verify schema files if the repo has them.
- **Shell execution**: state the approved command wrapper or argument-safety pattern.
- **Outbound HTTP or third-party APIs**: require timeout, retry policy where appropriate, and named error mapping.
- **Discriminated unions or enums**: name every exhaustive handling site that must change; for coded errors, apply the code-to-details matrix and envelope tests from gate 10.
- **Result/error contracts**: follow the repo's expected-failure style and do not introduce throw-based expected failures or conflicting patterns silently.
- **Frontend behavior**: include states, accessibility expectations, responsive behavior, and the user flow that proves the change. Name and verify the literal platform or library mechanism for imperative querying, navigation interception, subscriptions, focus restoration, or similar behavior. When operational logging is present, name where and when each event emits and prove it cannot fire per render, unbounded retry, or expected domain outcome.
- **Async frontend mutations**: for every in-flight state, require a transition table with rows for each mutable control and for success, failure, retry, discard, and navigation. Each row must state whether the action is allowed, which snapshot owns the pending data, the next state, and the user-visible result. Cover edits made while a request is pending, stale or superseded responses, retry ownership, discard semantics, and navigation away/back. Every allowed transition and prohibited action must map to an exact test in the traceability ledger.
- **Generated code or fixtures**: state regeneration commands and which generated files should or should not be edited by hand.
- **Security or privacy**: state secret handling, PII exposure, data retention, and permission implications.
- **Pinned third-party tool or version-dependent defaults**: when the design relies on a tool, image, framework, or library default, verify the assumption against the exact pinned version using its source, release notes, or changelog rather than current-version knowledge. If the behavior can change silently on upgrade, require the controlling flag, environment variable, or config value to be set explicitly.

### Execution, Checkpoint Reviews, And Final Review

For non-trivial issues, include an Execution Plan that identifies which workstreams are independent and which are ordered. Use subagents in parallel only when their production ownership and traceability rows do not overlap materially.

When the issue defines review checkpoints, every checkpoint must be a coherent,
green, behavior-complete state with owned acceptance criteria, exact validation,
a frozen review head, and an explicit advance condition. Checkpoint reviews
reduce the amount of new code assessed at once; they do not create separate
issues, branches, or pull requests.

- Give each implementation subagent bounded files or symbols, acceptance criteria, and validation responsibility.
- Give every Branch Contract its own dedicated linked worktree by default,
  including the Canonical Integration Branch and each Helper Branch. Concurrent
  writes require that filesystem isolation. Record the repository convention or
  user's explicit direction when implementation will use the shared checkout.
  A clean review context does not itself require a worktree.
- Name one Canonical Integration Branch from the issue's Branch Contract. The Execution Plan must name how each Helper Branch enters it: cherry-pick coherent commits, merge the branch, or rebase and fast-forward according to repository history conventions. Never copy files between worktrees as the integration mechanism.
- Validate each helper branch, then integrate in dependency order. Resolve conflicts only on the canonical branch and re-run every affected traceability row.
- Run the complete triggered validation and issue-against-diff audit on the combined canonical branch.

After all checkpoints are accepted, run the complete triggered validation,
issue-against-diff audit, and final `$code-review` against the combined
issue-base-to-current-head diff. Revisit interactions across checkpoints,
shared contracts, configuration, deleted behavior, and integration seams.
Checkpoint evidence supports this review but cannot replace it.

Make final code review the last implementation gate. For multi-slice, medium-risk, or high-risk changes, require multiple fresh review subagents when available: independent finder passes using `$code-review` and a separate skeptical verifier that receives raw evidence without the finder's expected verdict. On the Dexwin engineering server, `code-review-dexwin` is the execution alias for the same canonical skill, not separate doctrine. For tiny low-risk changes, allow one clean reviewer plus a separate skeptical pass. If subagents are unavailable, require equivalent logically independent passes and record the limitation.

Reconcile and deduplicate every confirmed finding, obtain user adjudication when the workflow requires it, fix accepted blockers, and re-run affected proof. Then complete the Issue Completion Record gate, including reviewer verification of the written record, before declaring the issue complete.

If the repo has domain-specific gates, apply them after discovery. Examples include approved-only content, event schema invariants, tenant boundaries, import provenance, or feature-flag rules.

## Questioning Discipline

Use `$grilling` as the questioning discipline for unresolved gates and contradictions.

- **Discoverable fact**: inspect the code, docs, tests, history, or configured tools. Do not ask the user to recall repository facts.
- **User-owned decision**: map the full dependency-aware queue, then ask one material decision using a stable ID. State the gate or contradiction, concrete risk, options, and recommended answer. Keep the current decision active until it is accepted, rejected, revised, deferred, or blocked on named evidence.
- **Empirical unknown**: run a bounded reproduction, spike, benchmark, or research pass when safe and authorized. Report the result; do not turn an investigable unknown into a preference question.

After each reply, update the decision map, resolve dependencies, and recompute the queue. Inherit `$grilling`'s `Decision <position> of <total> - <remaining> remain after this` progress marker and explicit total-change notice. Present only the next eligible decision after the current one has an explicit disposition. Batch independent, low-consequence decisions only when the user explicitly asks for faster batch treatment; accept bulk replies such as `agree to all` only for that explicit batch.

Investigate and deduplicate the complete review landscape before reporting it, then present one confirmed finding at a time with its stable ID and `**Progress: Finding <position> of <total> - <remaining> remain after this**` on every response that presents or continues it. Calculate position and total using prior dispositions plus the active queue; do not show a remaining count alone. Keep the current finding active until the user accepts, rejects, revises, defers, or requests named evidence. Recompute the remaining queue after every disposition. If the total changes, state `**Queue revised: <old> -> <new>.** <reason>` before the next finding; never silently change the denominator or stable finding IDs. Batch at most ten only when the user explicitly requests a batch or complete report; there is no total finding cap.

For a long or multi-session review, persist the grilling decision map in a separate working artifact if useful. Never use partial issue content, placeholders, TODOs, or `TBD` sections as interview state.

Before rewriting, present the resolved understanding and explicitly confirm that it is shared. The user's approval authorizes the final coherent rewrite; it does not authorize partial writes during the interview.

## Cross-Validation

Before editing, verify:

- **Consistency**: acceptance criteria, scope, dependencies, implementation guardrails, and affected production owners agree; paths, symbols, and line references still exist.
- **Issue identity**: pending issue filenames follow the discovered convention, use a stable number and valid Conventional Commit type, and all roadmap/index and sibling references resolve after any rename.
- **Decomposition and branches**: the issue is one proven Smallest Coherent Slice or an ordered child pack; every slice owns exactly one Branch Contract and pull request, no feature-wide pull request spans multiple slices, only genuinely dependent pull requests are stacked, and every contract records its exact base ref and worktree isolation mode without a machine-specific path.
- **Delivery continuity**: immediate or backlog intent is explicit; ambiguous
  initial prompts were resolved by asking; every slice selected for immediate
  delivery owns the worktree used for the final issue write; and its Approved
  Issue Commit exists on the declared implementation branch before production-
  code work. Backlog-only issues did not create idle implementation worktrees.
- **Existing PR correction**: when the normal branch naming rule is bypassed for
  review-to-merge correction, the Existing PR Correction Contract resolves one
  exact repository, pull request, head owner and branch, base, expected head
  SHA, push authority, and dedicated worktree without authorizing a replacement
  PR or history rewrite.
- **Traceability**: every independently observable criterion has one or more ledger rows with an exact production owner and exact test or justified manual verification; the post-implementation audit is named.
- **Runtime acceptance**: every observable runtime change has a complete local
  plan, applicable exact-candidate preview or staging plan, real-boundary
  scenarios, secret-free evidence contract, invalidation rules, and specialist
  auth or design proof; non-runtime work has a justified `Not applicable` entry.
- **Attention budget**: the core child issue keeps approved intent, acceptance,
  authority, pause conditions, and checkpoint routing prominent; reusable
  doctrine and raw evidence are not copied into it; length signals triggered
  the required compression or decomposition check.
- **Review checkpoints**: substantial slices use a small ordered set of
  semantic, green, behavior-complete checkpoints; every checkpoint owns
  criteria, validation, reading inputs, and an advance condition; the final
  full integration review remains required.
- **Goal safety**: a durable goal cannot change a finding route, expand
  correction authority, suppress an adverse finding, or cross a non-clean
  checkpoint.
- **Contracts**: auth, trust boundaries, schemas, events, migrations, error mappings, and expected-failure behavior match repo conventions; coded errors include the complete matrix and invalid-envelope behavior.
- **Triggered doctrine**: apply the relevant observability, resilience, auth/security, and frontend requirements, including async transition tables and literal platform mechanisms when applicable.
- **Execution and review**: parallel work has non-overlapping ownership, helper commits integrate into the canonical branch, combined validation is explicit, and the final clean-context `$code-review` passes are named.
- **Review loop**: the issue explicitly selects automatic or human-gated delivery, routes mechanical corrections separately from user-owned decisions and blockers, requires current-head revalidation and re-review, and defines its churn threshold and ready-to-merge stopping condition.
- **Deferred follow-up**: when invoked for `DEFER_FOLLOW_UP`, every finding is
  still eligible under the contract, deduplicated by root cause, represented by
  an issue-review-ready coherent issue or bounded discovery issue, and present
  in the canonical roadmap without invented priority.
- **Completion record**: the issue defines who writes and verifies its Issue Completion Record, which status-tracking surfaces must change with it, and which issue-owned or explicitly downstream gates control `Needs Verification` versus `Done`.
- **Propagation**: reconcile inheriting issues, glossary/context entries, config consumers, shared invariants, and operational docs, or track an explicit prerequisite follow-up.
- **Proof strength**: universal, negative, and mutual-exclusivity claims cover every element and direction; rounded displays agree with derived status indicators; literal runtime mechanisms are exercised or use a named proxy with its blind spot.
- **Configuration delivery**: every new environment variable reaches its exact runtime consumer through named injection points; every version-dependent default is verified against the pinned version and made explicit when it may drift.
- **Documentation truth**: new instructions do not leave neighboring claims, enumerations, warnings, or runbooks false or incomplete.
- **Verification status**: work that defers its highest-risk integration scenario remains explicitly unverified rather than being marked done with a caveat.
- **Migration and telemetry evidence**: database migrations name the isolated Migration Proof Harness; frontend ingestion and other telemetry name Safe Log Event privacy tests and the Telemetry Budget.

Resolve every contradiction with the user before writing.

## Rewrite

Preserve the repo's established issue format when one exists. If no format exists, use this fallback:

```md
# <title>

Status: open
Type: Bug | Feature | Chore | Exploration
Severity: High | Medium | Low | Very Low
Branch: <exact conventional branch>
Base: <exact canonical branch or preceding stacked-PR branch>
Worktree: dedicated | shared checkout - <repository convention or explicit user direction>
Parent: <parent issue, when this is a child slice>

## Agent Start Here

## Problem / Motivation

## Root Cause / Background

## Affected Surface

## Acceptance Criteria

## Out of Scope

## Implementation Guardrails

## Dependencies

## Execution Plan

### Review Checkpoints

## Test Approach

### Traceability Ledger

## Runtime Acceptance Plan

## Review Loop Contract

## Completion Requirements

## Notes
```

Omit sections that genuinely do not apply, except keep `Runtime Acceptance Plan`
with a justified `Not applicable` entry for non-runtime work. Do not add empty
sections. Populate
`Completion Requirements` with the issue-specific write-back, evidence, status,
and propagation rules during readiness review. Add the actual `Issue Completion
Record` only after implementation evidence exists; never prefill it with
placeholders or predicted results.

For immediate delivery, return the declared branch, base ref, resolved base SHA,
runtime worktree path, and Approved Issue Commit SHA to `$issue-delivery`. For
backlog-only work, state that no implementation worktree was created. Never
report an issue as ready for immediate implementation from a different branch or
worktree than the one that contains its Approved Issue Commit.
