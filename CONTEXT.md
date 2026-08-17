# Engineering Skill Workflows

This context defines the boundaries between issue preparation, code analysis, GitHub review workflows, and implementation evidence in the engineering skill family.

## Language

**Smallest Coherent Slice**:
The smallest issue that is independently implementable, testable, and reviewable while leaving the repository green.
_Avoid_: Micro-task, smallest possible task

**Branch Contract**:
The exact implementation branch name and full worktree path recorded by an issue before implementation begins, defaulting to a dedicated worktree unless the user has explicitly directed that this work happen without one.
_Avoid_: Suggested branch, branch hint, implied working directory

**Issue Completion Record**:
The durable evidence written to the canonical issue by the implementation or integration agent after final review and before the issue is marked done. It records the final status, completion date, affected surfaces, reconciled traceability evidence, validation results, review outcome, deviations, residual risks, deferred checks, and available branch, commit, or pull-request references.
_Avoid_: Completion summary, PR-only evidence, chat-only handoff

**Code Review**:
Platform-neutral analysis that verifies candidate defects and reports confirmed findings without external writes by default.
_Alias_: `code-review-dexwin` on the engineering server
_Avoid_: PR review, separate Dexwin review doctrine

**Pull Request Review**:
A GitHub-specific workflow that uses **Code Review**, obtains user adjudication, and publishes only accepted findings.
_Avoid_: Code review

**Pull Request Creation**:
The publication workflow that verifies completed local work against its issue and branch contract before pushing and opening or updating the GitHub pull request.
_Avoid_: Implementation, code review, PR body drafting

**Pull Request Readiness**:
The evidence-derived state that determines whether publication is blocked, produces a draft, or produces a pull request ready for review.
_Avoid_: Always draft, always ready

**Pending Review**:
The `pending-review` pull-request label that signals to the reviewer that corrections and regression evidence are ready for another look.
_Avoid_: Workflow status, permission to resolve review threads

**Stacked Pull Request**:
A pull request whose branch and review diff depend on an earlier pull-request branch in an explicitly ordered issue pack.
_Avoid_: Parallel PR, unrelated dependent branch

**Review Batch**:
A group of at most ten verified findings presented together for user adjudication, with no limit on later batches.
_Avoid_: Review cap, finding limit

**Interview Round**:
A dependency-aware group of at most ten user-owned decision questions asked by a grilling workflow.
_Avoid_: Review batch

**Migration Proof Harness**:
An isolated disposable local database container that proves a database migration against prior-state fixtures using the production migration mechanism.
_Avoid_: Migration test, structural-migration container

**Diagnostic Database Metadata**:
An allowlisted set of non-value database error identifiers used for operational diagnosis without logging error prose, SQL, parameters, or unknown driver fields.
_Avoid_: Raw database error, serialized driver error

**Safe Log Event**:
A typed default-deny telemetry record whose event family owns a closed schema of bounded operational fields and excludes raw errors, arbitrary context, and direct personal data.
_Avoid_: Sanitized arbitrary object, logger context bag

**Client Telemetry Ingestion**:
The controlled untrusted backend boundary that validates closed frontend operational-event and trace contracts, attaches server-owned context, strips client-claimed authority, and forwards only accepted telemetry.
_Avoid_: Client-to-vendor export, client audit authority, unrestricted OTLP receiver

**Frontend Operational Event**:
A safe log event emitted once at an owned client boundary or meaningful failure transition for operational diagnosis rather than product analytics.
_Avoid_: Render log, clickstream event, raw client error

**Server-Owned Trace Export**:
The security and operational rule that trace credentials, routing, buffering, and downstream provider export remain on trusted backend infrastructure. An OpenTelemetry Collector is a preferred optional server-side pipeline, not the definition of this ownership rule.
_Avoid_: Client exporter credentials, direct client-to-Collector export, Collector required in every deployment

**Telemetry Budget**:
The explicit signal-specific volume, sampling, cardinality, payload, queue, timeout, drop, and provider-outage limits that an observability pipeline must satisfy without degrading business traffic.
_Avoid_: Unlimited best-effort telemetry, one undifferentiated budget, assumed capacity

**Responsible Engineer**:
The issue's named GitHub implementer, falling back to the pull-request author when no implementer is named.
_Avoid_: Last line author, inferred owner

**Clean Review Context**:
A fresh review context that receives the raw change and governing contracts without another reviewer's expected verdict.
_Avoid_: Clean worktree, pre-seeded verdict

**Canonical Integration Branch**:
The branch named by an issue's branch contract into which validated helper-branch commits are integrated before combined verification and pull-request creation.
_Avoid_: Helper branch, copied final worktree

**Helper Branch**:
A temporary branch in a separate implementation worktree that owns a bounded workstream and produces coherent commits for the canonical integration branch.
_Avoid_: Canonical branch, uncommitted worktree

**Thread Reconciliation**:
The mandatory pull-request-review phase that inventories existing unresolved review threads and re-verifies each original claim against the latest pull-request head.
_Avoid_: Explicitly requested re-review, outdated-line cleanup

## Relationships

- A parent issue contains one or more ordered **Smallest Coherent Slices** when the work cannot remain one coherent issue.
- Each **Smallest Coherent Slice** owns one **Branch Contract**.
- Every completed issue owns one **Issue Completion Record** in its canonical issue file; the implementation or integration agent writes it, and the final reviewer verifies it before the issue is marked done.
- An issue remains `Needs Verification` while any issue-owned acceptance, review, or highest-risk verification gate lacks evidence. An explicitly out-of-scope downstream or release gate does not block issue completion when the issue links it and names its owner or trigger.
- A **Pull Request Review** uses **Code Review** as its analysis engine.
- **Pull Request Creation** consumes completed local work and produces a GitHub pull request; it does not implement or review the change.
- **Pull Request Creation** derives draft or ready status from **Pull Request Readiness**; verified completed work is ready for review.
- A **Responsible Engineer** applies **Pending Review** and notifies the reviewer after pushing requested corrections and regression evidence; the label does not transfer thread-resolution ownership.
- A **Stacked Pull Request** depends on exactly one earlier base in an ordered stack; independent slices target the canonical base branch directly.
- A **Code Review** can produce one or more **Review Batches**.
- A **Pull Request Review** tags the **Responsible Engineer** only after the user accepts a finding.
- Independent finder and verifier passes use separate **Clean Review Contexts**.
- Each **Helper Branch** contributes validated commits to exactly one **Canonical Integration Branch** through the issue's named Git integration strategy, never by copying files between worktrees.
- A **Canonical Integration Branch** is reviewed and validated as a combined change before **Pull Request Creation**.
- An **Interview Round** contains decisions, while a **Review Batch** contains verified findings.
- A database migration requires one **Migration Proof Harness**; a structural code migration does not.
- **Diagnostic Database Metadata** may include bounded schema, table, column, constraint, and SQLSTATE identifiers from a recognized driver error.
- Persisted backend logs and audits satisfy typed **Safe Log Event** contracts; metrics use named instruments with bounded attributes; traces use approved tracer, semantic-convention, attribute, propagation, sampling, and export contracts.
- **Client Telemetry Ingestion** accepts only frontend event families that can be converted into **Safe Log Events** and client traces that can be reduced to a closed bounded trace contract; authoritative security and audit events are generated by the backend.
- `engineering-frontend` owns when a **Frontend Operational Event** or significant client span begins; `engineering-observability` owns its signal contract, propagation, delivery, ingestion, export, privacy, retention, and capacity policy.
- Client spans leave the client only through **Client Telemetry Ingestion**; downstream trace delivery follows **Server-Owned Trace Export** whether the backend exports directly or through an OpenTelemetry Collector.
- **Client Telemetry Ingestion** and **Server-Owned Trace Export** must satisfy their signal-specific **Telemetry Budgets** under normal load, event storms, malicious traffic, a full queue, and provider outages.
- Every **Pull Request Review** performs **Thread Reconciliation** without requiring a separate user request.
- **Thread Reconciliation** automatically replies to and resolves only workflow-owned threads verified as fixed or obsolete; human-owned threads are inspected and reported without being resolved automatically.

## Example dialogue

> **Engineer:** "Should this feature be one issue and one large PR?"
> **Reviewer:** "No. Define each **Smallest Coherent Slice** and its **Branch Contract**, then use **Pull Request Review** to adjudicate each **Code Review** batch before publishing comments."

## Flagged ambiguities

- "PR review" and "code review" were used interchangeably - resolved: **Code Review** owns analysis, while **Pull Request Review** owns GitHub adjudication and publication.
- `code-review-dexwin` appeared to name a separate skill - resolved: it is the engineering server alias for **Code Review**, not an independent review contract.
- "Stacked" was used as a synonym for parallel issue work - resolved: a **Stacked Pull Request** has an explicit dependency, while independent pull requests share the canonical base.
- "Clean subagent" was used as a worktree requirement - resolved: a reviewer needs a **Clean Review Context**, which does not itself require a worktree; every **Branch Contract** still defaults to its own worktree for implementation, and skips one only when the user explicitly says so.
- "Final worktree" was used as though worktrees themselves are merged - resolved: commits from **Helper Branches** are integrated into the **Canonical Integration Branch**; files are never copied between worktrees as the integration mechanism.
- "Draft PR" was treated as the default publication result - resolved: **Pull Request Readiness** determines the state, and verified completed work produces a pull request ready for review.
- `pending-review` was treated as a formal workflow-status category - resolved: **Pending Review** is a reviewer-attention signal applied after corrections and evidence are ready for another look.
- "Frontend logging" included both operational diagnosis and product analytics - resolved: **Frontend Operational Events** are a bounded observability surface; product analytics remains separate.
- "Server-owned export" and "OpenTelemetry Collector" were treated as synonyms - resolved: **Server-Owned Trace Export** is the ownership and security rule; a Collector is one optional server-side delivery topology.
- "Re-review" implied a separately requested workflow - resolved: **Thread Reconciliation** runs during every **Pull Request Review** when existing unresolved threads are present.
- "Existing threads" did not distinguish workflow and human ownership - resolved: all are inspected, but automatic resolution is limited to workflow-owned threads.
- "Round" referred to both questions and findings - resolved: **Interview Round** contains decision questions, while **Review Batch** contains verified findings.
- "Migration" included both persisted-database changes and structural refactors - resolved: only database schema or data migrations require a **Migration Proof Harness**.
