---
name: issue-review
description: Review an existing issue, ticket, feature file, bug report, roadmap item, or implementation handoff for agent readiness. Use when Codex is asked to tighten, validate, rewrite, prepare, or assess an issue so another agent or engineer can implement it with zero clarifying questions, including validating or assigning a stable numbered Conventional Commit-style filename.
---

# Issue Review

Review the issue file against the bar: a competent implementation agent should be able to complete it with zero clarifying questions and produce a robust, validated change. If ambiguity remains, the issue is not ready.

Use plain language at a Grade 10 reading level in the rewritten issue, review findings, decision summaries, and clarification questions so they are quick and easy to understand. Prefer short sentences and familiar words. Preserve exact domain terms, code identifiers, and contract language, and explain necessary jargon when it first appears. Never simplify away technical precision.

Use the user's engineering-for-certainty doctrine as the default engineering standard when reviewing software issues: preserve repo conventions, validate trust boundaries, keep adapters thin and domain logic explicit, prefer explicit expected-failure contracts, and require tests for critical behavior and failure paths.

Require companion engineering doctrine when the issue touches its area:

- Observability: logging, metrics, tracing, audit records, correlation IDs, telemetry, redaction, or frontend log ingestion.
- Resilience: external calls, retries, timeouts, idempotency, concurrency, queues, cron jobs, webhooks, background jobs, or async processing.
- Auth/security: cookies, sessions, CSRF, token handling, actor context, protected routes, permission checks, policy registries, secrets, or authorization boundaries.
- Frontend testing/accessibility: web/mobile test-stack decisions, E2E coverage, accessibility assertions, keyboard behavior, focus management, screen-reader behavior, or platform-specific UI testing.

If the companion skill is available in the Codex session, use it. If it is not available, apply the local repo's equivalent docs or explicitly record the gap in the issue review.

If no issue path is provided, ask for one before proceeding.

## Workflow

1. **Read the issue**: identify the requested change, claimed files, dependencies, and current structure.
2. **Discover repo conventions**: inspect local docs and examples before applying generic rules, including issue filename rules and the next unused issue reference.
3. **Verify claims against code**: check paths, symbols, line references, tests, schemas, commands, and stated behavior.
4. **Decompose and name the work**: prove the issue is one Smallest Coherent Slice or create an ordered child pack, then assign every slice its exact Branch Contract and PR base.
5. **Resolve gaps with `$grilling`**: inspect discoverable facts, investigate empirical unknowns, and batch user-owned decisions into dependency-aware rounds with stable question IDs.
6. **Accumulate answers**: maintain the decision map across rounds; do not edit the issue during the review.
7. **Build traceability and execution**: map each acceptance criterion to its production owner and exact verification, then define safe sequential or parallel implementation ownership.
8. **Cross-validate**: check the resolved issue, traceability ledger, execution plan, and conditional gates for contradictions and missing dependencies.
9. **Write once**: rewrite the issue only after every gate passes and the full picture is consistent.

Never write placeholders, TODOs, partial decisions, or "TBD" sections to the issue. The file is either unchanged while review is in progress or fully resolved when review is complete.

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

## Decomposition And Branch Contract

Every review must make one explicit decomposition decision: either the issue is already one Smallest Coherent Slice, or it must become a parent pack of smaller child issues. Do not use line count alone.

A Smallest Coherent Slice must:

- have one coherent observable outcome;
- be independently implementable, testable, and reviewable;
- leave the repository green after integration;
- own exact acceptance criteria, production surfaces, tests, dependencies, branch, and PR;
- avoid splitting contracts, persistence, behavior, and proof into technical micro-tasks that are not meaningful alone.

The slice need not be independently deployed when the product intentionally releases only after the full pack is complete. When splitting, record the parent, ordered children, cross-slice contracts, and release boundary. Preserve existing pack-local numbering when it is already stable.

Require each slice to record its exact conventional Branch Contract before implementation:

```text
<type>/<NN>-<short-kebab-description>
```

Preserve a platform-required prefix such as `codex/`. The type and stable issue number must agree with the issue filename. A branch suggestion or pattern without the resolved name does not pass.

Record the full path to the worktree where the slice's work will happen alongside its Branch Contract. Default every slice to its own dedicated worktree branched from the remote default branch (for example `origin/main`), not local `HEAD`; omit the path only when the user has explicitly said this slice's work should happen without one, and record that direction, and any explicit override of the base ref, instead of the default.

Use Stacked Pull Requests only for real dependencies. Record each PR's head, base, preceding PR, merge order, and rebase or retarget procedure. Independent slices must share the canonical base branch and remain parallel rather than being forced into a stack.

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

Split criteria that have multiple independently observable outcomes. Every row must name the code that owns the behavior and the exact evidence that will prove it; broad entries such as "frontend," "service layer," or "covered by tests" do not pass. Manual verification is acceptable only when automation is impractical and the issue explains why.

Require a post-implementation issue-against-diff audit by an independent reviewer or a separate skeptical pass that did not rely on the implementer's completion summary. Reconcile every ledger row against the actual production diff and test evidence, identify unplanned changes, and leave the issue unverified while any row lacks evidence.

When an acceptance criterion names a specific runtime mechanism (a "scheduled" job, a "background" retry, an "on reconnect" handler), the Test Approach must state whether verification exercises that literal mechanism or a named, justified proxy (e.g. a manual one-shot invocation of the same script the scheduler calls). An unstated substitution leaves a criterion looking tested when only an adjacent code path was actually exercised.

Apply the same rule to platform and library mechanisms named under Affected Surface: exercise the actual API, hook, event, or interception path, or name and justify the proxy and its blind spot.

When the issue introduces a new artifact covered by an existing repo-wide invariant, such as image pinning, network exposure, or secrets hygiene, inspect the shared test files or suites that encode that invariant. The Test Approach must name the existing assertions and explicitly extend them to the new service, image, port, secret, or other artifact rather than adding only scenario-specific tests.

When verification is split between standalone checks and a live or deployed instance, identify whether the live-only portion contains the scenario most likely to expose an integration defect, such as cross-branch recombination, interacting failure paths, or multi-service behavior. If it does, the issue must remain explicitly unverified—use a status such as `Needs Verification`, not `Done` with a caveat—until that scenario has run successfully.

If no local convention is visible, use:

```ts
it(`
  given <context>
  when  <action>
  then  <assertion>
`, () => {
  // ...
})
```

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
- the production, test, configuration, and documentation surfaces actually
  changed;
- the reconciled result of every traceability row, including exact validation
  commands and outcomes;
- the final `$code-review` outcome and the disposition of every confirmed
  finding;
- deviations from the issue and any unplanned changes;
- residual risks and every unverified or deferred check, with a linked owner or
  trigger for downstream work; and
- branch, commit, and pull-request references when available.

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

### Parallel Execution And Final Review

For non-trivial issues, include an Execution Plan that identifies which workstreams are independent and which are ordered. Use subagents in parallel only when their production ownership and traceability rows do not overlap materially.

- Give each implementation subagent bounded files or symbols, acceptance criteria, and validation responsibility.
- Give each Helper Branch its own worktree by default, the same as any other Branch Contract; concurrent workstreams need it for filesystem isolation, and a single-threaded implementation defaults to one too. Skip the worktree only when the user explicitly authorized working directly in the current checkout, and record that exception. A Clean Review Context does not itself require a worktree.
- Name one Canonical Integration Branch from the issue's Branch Contract. The Execution Plan must name how each Helper Branch enters it: cherry-pick coherent commits, merge the branch, or rebase and fast-forward according to repository history conventions. Never copy files between worktrees as the integration mechanism.
- Validate each helper branch, then integrate in dependency order. Resolve conflicts only on the canonical branch and re-run every affected traceability row.
- Run the complete triggered validation and issue-against-diff audit on the combined canonical branch.

Make final code review the last implementation gate. For multi-slice, medium-risk, or high-risk changes, require multiple fresh review subagents when available: independent finder passes using `$code-review` and a separate skeptical verifier that receives raw evidence without the finder's expected verdict. On the Dexwin engineering server, `code-review-dexwin` is the execution alias for the same canonical skill, not separate doctrine. For tiny low-risk changes, allow one clean reviewer plus a separate skeptical pass. If subagents are unavailable, require equivalent logically independent passes and record the limitation.

Reconcile and deduplicate every confirmed finding, obtain user adjudication when the workflow requires it, fix accepted blockers, and re-run affected proof. Then complete the Issue Completion Record gate, including reviewer verification of the written record, before declaring the issue complete.

If the repo has domain-specific gates, apply them after discovery. Examples include approved-only content, event schema invariants, tenant boundaries, import provenance, or feature-flag rules.

## Questioning Discipline

Use `$grilling` as the questioning discipline for unresolved gates and contradictions.

- **Discoverable fact**: inspect the code, docs, tests, history, or configured tools. Do not ask the user to recall repository facts.
- **User-owned decision**: ask in a dependency-aware round using stable IDs. State the gate or contradiction, concrete risk, options, and recommended answer. Accept bulk replies such as `agree to all`, plus exceptions by ID.
- **Empirical unknown**: run a bounded reproduction, spike, benchmark, or research pass when safe and authorized. Report the result; do not turn an investigable unknown into a preference question.

After each reply, update the decision map, resolve dependencies, and generate only the newly unblocked questions. Start another round when answers materially change the decision tree. Ask a singleton only when one blocking decision genuinely gates all useful downstream questions.

Present review findings in Review Batches of at most ten. State whether more verified findings remain and continue through as many batches as necessary; there is no total finding cap. This finding limit is distinct from `$grilling`'s Interview Round limit for user-owned decisions.

For a long or multi-session review, persist the grilling decision map in a separate working artifact if useful. Never use partial issue content, placeholders, TODOs, or `TBD` sections as interview state.

Before rewriting, present the resolved understanding and explicitly confirm that it is shared. The user's approval authorizes the final coherent rewrite; it does not authorize partial writes during the interview.

## Cross-Validation

Before editing, verify:

- **Consistency**: acceptance criteria, scope, dependencies, implementation guardrails, and affected production owners agree; paths, symbols, and line references still exist.
- **Issue identity**: pending issue filenames follow the discovered convention, use a stable number and valid Conventional Commit type, and all roadmap/index and sibling references resolve after any rename.
- **Decomposition and branches**: the issue is one proven Smallest Coherent Slice or an ordered child pack; every slice has an exact Branch Contract naming its worktree path (or the user's explicit direction to skip one), and only genuinely dependent PRs are stacked.
- **Traceability**: every independently observable criterion has one or more ledger rows with an exact production owner and exact test or justified manual verification; the post-implementation audit is named.
- **Contracts**: auth, trust boundaries, schemas, events, migrations, error mappings, and expected-failure behavior match repo conventions; coded errors include the complete matrix and invalid-envelope behavior.
- **Triggered doctrine**: apply the relevant observability, resilience, auth/security, and frontend requirements, including async transition tables and literal platform mechanisms when applicable.
- **Execution and review**: parallel work has non-overlapping ownership, helper commits integrate into the canonical branch, combined validation is explicit, and the final clean-context `$code-review` passes are named.
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
Worktree: <full path to the dedicated implementation worktree, or "none — user directed no worktree">
Parent: <parent issue, when this is a child slice>

## Problem / Motivation

## Root Cause / Background

## Affected Surface

## Acceptance Criteria

## Out of Scope

## Implementation Guardrails

## Dependencies

## Execution Plan

## Test Approach

### Traceability Ledger

## Completion Requirements

## Notes
```

Omit sections that genuinely do not apply. Do not add empty sections. Populate
`Completion Requirements` with the issue-specific write-back, evidence, status,
and propagation rules during readiness review. Add the actual `Issue Completion
Record` only after implementation evidence exists; never prefill it with
placeholders or predicted results.
