# Engineering Skill Workflows

This context defines the boundaries between issue preparation, code analysis, GitHub review workflows, and implementation evidence in the engineering skill family.

## Language

**Smallest Coherent Slice**:
The smallest issue that is independently implementable, testable, and reviewable while leaving the repository green.
_Avoid_: Micro-task, smallest possible task

**Branch Contract**:
The exact implementation branch name, declared pull-request base ref, and
worktree isolation mode recorded by an issue before implementation begins.
Machine-specific worktree paths are execution evidence, not part of the
portable issue contract.
_Avoid_: Suggested branch, branch hint, implied base, absolute worktree path

**Implementation Worktree**:
A dedicated linked git worktree for one Branch Contract. Its runtime path and
resolved base SHA are recorded in the pre-work execution handoff, never in the
canonical issue.
_Avoid_: Shared checkout, canonical issue path

**Issue Completion Record**:
The durable evidence written to the canonical issue by the implementation or integration agent after final review and before the issue is marked done. It records the final status, completion date, affected surfaces, reconciled traceability evidence, validation results, review outcome, deviations, residual risks, deferred checks, and available branch, commit, or pull-request references.
_Avoid_: Completion summary, PR-only evidence, chat-only handoff

**Runtime Acceptance Pass**:
An agent-operated check that exercises a running system through its real
external boundary to verify an issue's observable behaviour after automated
tests pass.
_Avoid_: Automated test suite, in-process handler invocation, code inspection

**Test Identity Plan**:
The issue-specific contract for obtaining, using, resetting, and cleaning up
disposable identities during authentication acceptance scenarios.
_Avoid_: Personal account, production customer, unmanaged temporary account

**Test Message Sink**:
An isolated non-production delivery adapter that exposes authentication emails
or messages to authorized tests without sending their secrets through
operational telemetry.
_Avoid_: Production inbox, ordinary application log, exported OTP event

**Design Conformance Pass**:
An issue-owned comparison of a running frontend against the exact authoritative
design states, versions, platforms, and viewports named by its design contract.
_Avoid_: Memory-based comparison, subjective looks-close check, universal pixel equality

**Delivery Operator**:
The composing workflow implemented by `$deliver-issue` that carries one
approved issue through implementation, issue-owned validation, independent code
review, authorized correction loops, pull-request creation, and CI follow-up.
It coordinates the owning skills without turning reviewer contexts into editors
or choosing user-owned product and architectural decisions.
_Avoid_: Autonomous product owner, code reviewer, merge bot

**Review Loop Contract**:
The issue-owned authority and stopping contract that separates automatic
mechanical corrections from user decisions and blockers, then requires
revalidation and independent re-review of every corrected head.
_Avoid_: Fix everything, reviewer edits, implied goal authority

**Review Checkpoint**:
A semantic, behavior-complete review boundary inside one Smallest Coherent
Slice. It owns acceptance and traceability rows, exact validation, a frozen
candidate head, and an explicit advance condition.
_Avoid_: Arbitrary commit, line-count slice, separate issue, separate PR

**Accepted Checkpoint Head**:
The immutable commit SHA that passed checkpoint-owned validation and independent
review after every authorized correction. The next checkpoint begins from this
head.
_Avoid_: Latest local state, originally reviewed SHA, moving review target

**Final Integration Review**:
The independent code review of the complete issue-base-to-current-head diff
after all review checkpoints pass. It checks cross-checkpoint interactions and
remains required even when every checkpoint was reviewed separately.
_Avoid_: Last checkpoint review, cumulative assumption

**Ready-to-Merge Handoff**:
The delivery state in which the current pull-request head satisfies the issue,
has current validation and independent review evidence, has green required
automated CI, and has no unresolved confirmed finding or blocker. Only human
approval and the merge action remain; the term does not claim that required
human approval has already happened.
_Avoid_: PR opened, ready for review, merged, deployed

**Code Review**:
Platform-neutral analysis that verifies candidate defects and reports confirmed findings without external writes by default.
_Alias_: `code-review-dexwin` on the engineering server
_Avoid_: PR review, separate Dexwin review doctrine

**Pull Request Review**:
A GitHub-specific workflow that uses **Code Review**, obtains a comment or fix
disposition for each confirmed finding, publishes only comment dispositions,
and routes fix dispositions through issue-owned delivery.
_Avoid_: Code review, reviewer implementation, comment-only adjudication

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

**Review Queue**:
The complete ranked set of verified findings awaiting user disposition, with one current finding discussed at a time unless the user explicitly requests a batch.
_Avoid_: Review batch, finding cap, report-only list

**Decision Queue**:
The dependency-aware set of unresolved user-owned decisions, with one current decision discussed to an explicit disposition before the queue is recomputed.
_Avoid_: Interview round, questionnaire, review queue

**Queue Progress**:
The visible current-item position, current known total, and count remaining after the current item across prior dispositions and the active queue.
_Avoid_: Remaining count alone, immutable total, percentage-only progress

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

**Domain API Factory**:
A domain-owned frontend factory named `create[Domain]Api` that produces one
stable, typed adapter surface from explicit production or mock options. The
factory remains pure and does not read application environment or global
configuration.
_Avoid_: Global API container, environment-reading factory, generic `createApi`

**Domain API Entrypoint**:
The domain adapter module that reads validated application configuration once,
calls its **Domain API Factory**, and exports stable operations for hooks and
other consumers. Mock selection stops here; consuming frontend layers never
receive environment values or mock configuration.
_Avoid_: Mock-aware hook, per-call mode flag, raw environment read

**Mock Outcome**:
A mock-only success-or-failure instruction for one frontend API operation. Its
`type` discriminant selects either the operation's exact success value or exact
failure value, with an optional delay.
_Avoid_: Untyped fixture, thrown expected failure, production input flag

**Mock Scenario**:
A domain-owned set of **Mock Outcomes** used to construct a mock implementation
of that domain's API. Application configuration may select a named scenario;
isolated tests may pass the scenario directly.
_Avoid_: Hook mock, random default, environment value passed through the UI

**Frontend Component**:
A UI unit that does not represent one complete navigable screen and is owned by the narrowest module, domain, or application boundary that contains all of its uses.
_Avoid_: Partial view, globally shared by default

**Full-Screen View**:
The presentational surface for one complete navigable screen, such as a web route page, mobile stack or tab screen, or a full-screen modal treated as a navigation destination.
_Avoid_: Section view, panel view, viewport-filling component

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
- Each **Smallest Coherent Slice** owns one **Branch Contract** and one pull
  request. A parent issue pack does not collapse its child slices into one
  feature-wide pull request.
- A **Branch Contract** defaults to one **Implementation Worktree**. Independent
  slices use the verified canonical branch as their pull-request base; a
  **Stacked Pull Request** uses its preceding pull-request branch.
- Every completed issue owns one **Issue Completion Record** in its canonical issue file; the implementation or integration agent writes it, and the final reviewer verifies it before the issue is marked done.
- Every issue that changes observable runtime behaviour owns a **Runtime
  Acceptance Pass**. A non-runtime issue records that the pass is not applicable
  and explains why.
- A **Runtime Acceptance Pass** runs locally before pull-request readiness and
  repeats against a staging or preview environment when that environment safely
  exposes the exact candidate version before merge and deployment is authorized.
  When staging receives only merged changes, its pass is a mandatory downstream
  release gate instead of an issue-completion gate.
- A **Runtime Acceptance Pass** exercises every accepted externally observable
  outcome, one complete primary journey, and a targeted exploratory check of the
  changed area and its integration seams. Automated tests retain exhaustive
  value combinations and low-level boundary coverage.
- A **Runtime Acceptance Pass** uses the real external boundary: network requests
  to a running API, browser control for a running web interface, computer or
  device control for native or operating-system-dependent interfaces, and the
  literal operational trigger for jobs or integrations when safely available.
  A necessary proxy names what it proves, its blind spot, and the later gate that
  must exercise the literal mechanism when that blind spot is material.
- A **Runtime Acceptance Pass** leaves a concise, secret-free scenario ledger
  tied to the exact revision and environment. A later production, dependency,
  runtime-configuration, deployment, or test-data change invalidates every
  scenario it could affect and requires those scenarios to run again.
- An authentication **Runtime Acceptance Pass** follows a **Test Identity Plan**.
  Local environments prefer seeded disposable users and a **Test Message Sink**;
  staging prefers team-controlled test inboxes for repeatability but may use a
  private agent-created disposable inbox to prove real external delivery for the
  tested build and recipient provider. A staging **Test Message Sink** proves
  only application-side delivery and is sufficient when real provider delivery
  is not an acceptance criterion.
- A **Test Message Sink** is disabled in production, isolated from **Safe Log
  Events**, limited to disposable test identities, access-controlled, and
  short-lived. Runtime evidence records successful retrieval without recording
  the OTP, magic link, session, or other authentication secret.
- A frontend **Runtime Acceptance Pass** includes a **Design Conformance Pass**
  whenever its issue names an authoritative design. Material mismatches fail the
  pass; conflicts between the design, accepted behaviour, design system,
  accessibility, or platform conventions require an explicit decision instead
  of a silent deviation.
- `$engineering-for-certainty` owns the universal **Runtime Acceptance Pass**
  rule; `$issue-review` authors its issue-specific plan; the **Delivery Operator**
  executes it; **Code Review** audits its coverage and evidence; **Pull Request
  Creation** gates publication on current proof; and the triggered frontend,
  auth/security, and observability doctrines own their specialist constraints.
- Every implementation-ready issue states a **Review Loop Contract**. A durable
  goal supplies persistence but does not expand correction authority.
- A **Delivery Operator** consumes the issue, preserves the ownership boundaries
  of **Code Review** and **Pull Request Creation**, and finishes at a
  **Ready-to-Merge Handoff**.
- A substantial **Smallest Coherent Slice** may contain a small ordered set of
  **Review Checkpoints** without creating extra branches or pull requests.
- Every **Review Checkpoint** advances only from a reviewed candidate to an
  **Accepted Checkpoint Head** with no unresolved confirmed finding.
- `AUTO_CORRECT` returns to the current checkpoint; `USER_DECISION` and
  `BLOCKED` pause delivery and leave any durable goal incomplete.
- `RESIDUAL_RISK` is a finding route, never a checkpoint result. A checkpoint
  may return `CLEAN` with a recorded residual risk only when the governing issue
  explicitly permits it and acceptance or highest-risk proof is not weakened.
- A durable goal supplies persistence only while an authorized transition
  exists. It cannot change a finding route or cross a non-clean checkpoint.
- After the last checkpoint, **Final Integration Review** remains required
  before pull-request publication and the **Ready-to-Merge Handoff**.
- **Code Review** finder and verifier contexts remain read-only during delivery;
  the **Delivery Operator** applies only issue-authorized corrections and
  returns material ambiguity to the user.
- An issue remains `Needs Verification` while any issue-owned acceptance, review, or highest-risk verification gate lacks evidence. An explicitly out-of-scope downstream or release gate does not block issue completion when the issue links it and names its owner or trigger.
- A **Pull Request Review** uses **Code Review** as its analysis engine. After
  the complete queue is adjudicated, findings marked `fix` pass through
  `$issue-review` before the **Delivery Operator** changes the existing pull
  request under its approved issue and branch contracts.
- **Pull Request Creation** consumes completed local work and produces a GitHub pull request; it does not implement or review the change.
- **Pull Request Creation** derives draft or ready status from **Pull Request Readiness**; verified completed work is ready for review.
- A **Responsible Engineer** applies **Pending Review** and notifies the reviewer after pushing requested corrections and regression evidence; the label does not transfer thread-resolution ownership.
- A **Stacked Pull Request** depends on exactly one earlier base in an ordered stack; independent slices target the canonical base branch directly.
- A **Code Review** produces one **Review Queue** after investigating, verifying, deduplicating, and ranking the complete finding landscape.
- Every current item from a **Review Queue** or **Decision Queue** displays **Queue Progress** as `Finding 11 of 30 - 19 remain after this` or `Decision 11 of 30 - 19 remain after this`.
- When recomputation changes the known total, **Queue Progress** states the previous total, new total, and reason before presenting the next item; stable item IDs do not change.
- A **Pull Request Review** tags the **Responsible Engineer** only for a finding
  the user chooses to publish as a comment. A successfully delivered `fix`
  disposition does not also publish the proposed inline comment.
- Independent finder and verifier passes use separate **Clean Review Contexts**.
- Each **Helper Branch** contributes validated commits to exactly one **Canonical Integration Branch** through the issue's named Git integration strategy, never by copying files between worktrees.
- A **Canonical Integration Branch** is reviewed and validated as a combined change before **Pull Request Creation**.
- A grilling workflow maps a **Decision Queue** and discusses one current decision at a time; a **Code Review** discusses one current finding from its **Review Queue** at a time.
- A database migration requires one **Migration Proof Harness**; a structural code migration does not.
- **Diagnostic Database Metadata** may include bounded schema, table, column, constraint, and SQLSTATE identifiers from a recognized driver error.
- Persisted backend logs and audits satisfy typed **Safe Log Event** contracts; metrics use named instruments with bounded attributes; traces use approved tracer, semantic-convention, attribute, propagation, sampling, and export contracts.
- **Client Telemetry Ingestion** accepts only frontend event families that can be converted into **Safe Log Events** and client traces that can be reduced to a closed bounded trace contract; authoritative security and audit events are generated by the backend.
- `engineering-frontend` owns when a **Frontend Operational Event** or significant client span begins; `engineering-observability` owns its signal contract, propagation, delivery, ingestion, export, privacy, retention, and capacity policy.
- A **Domain API Entrypoint** selects production or mock options from validated configuration and passes them to its pure **Domain API Factory**. Both implementations satisfy the same exact operation contracts; mock-only configuration cannot enter those contracts or any consuming frontend layer.
- A **Domain API Factory** receives a **Mock Scenario** only when constructing its mock implementation. Each **Mock Outcome** becomes a callable operation with the same input and return type as its production counterpart.
- A **Full-Screen View** composes **Frontend Components**; a component used by one module belongs to that module, while a shared component belongs to the narrowest boundary that owns all of its uses.
- Client spans leave the client only through **Client Telemetry Ingestion**; downstream trace delivery follows **Server-Owned Trace Export** whether the backend exports directly or through an OpenTelemetry Collector.
- **Client Telemetry Ingestion** and **Server-Owned Trace Export** must satisfy their signal-specific **Telemetry Budgets** under normal load, event storms, malicious traffic, a full queue, and provider outages.
- Every **Pull Request Review** performs **Thread Reconciliation** without requiring a separate user request.
- **Thread Reconciliation** automatically replies to and resolves only workflow-owned threads verified as fixed or obsolete; human-owned threads are inspected and reported without being resolved automatically.

## Example dialogue

> **Engineer:** "Should this feature be one issue and one large PR?"
> **Reviewer:** "No. Define each **Smallest Coherent Slice** and its **Branch Contract**, then use **Pull Request Review** to work through the **Code Review** queue one finding at a time before publishing comments."
>
> **Engineer:** "How far through the findings are we?"
> **Reviewer:** "**Queue Progress** is Finding 11 of 30, with 19 remaining after this one."

## Flagged ambiguities

- "PR review" and "code review" were used interchangeably - resolved: **Code Review** owns analysis, while **Pull Request Review** owns GitHub adjudication and publication.
- "Delivery operator" implied authority to make every decision - resolved: the
  **Delivery Operator** automates transitions and mechanical correction loops,
  while the issue's **Review Loop Contract** preserves user-owned product,
  scope, architecture, contract, migration, security, and dependency decisions.
- "Checkpoint passed" implied that the whole issue was reviewed - resolved: a
  **Review Checkpoint** proves its declared range and integration seams; the
  **Final Integration Review** still proves the combined issue diff.
- "Keep going until done" implied permission to push through adverse findings -
  resolved: a durable goal preserves persistence but cannot change review
  routes, correction authority, or checkpoint advance conditions.
- "Ready to merge" implied that human approval had already happened - resolved:
  a **Ready-to-Merge Handoff** ends immediately before human approval and the
  merge action.
- `code-review-dexwin` appeared to name a separate skill - resolved: it is the engineering server alias for **Code Review**, not an independent review contract.
- "Stacked" was used as a synonym for parallel issue work - resolved: a **Stacked Pull Request** has an explicit dependency, while independent pull requests share the canonical base.
- "Clean subagent" was used as a worktree requirement - resolved: a reviewer needs a **Clean Review Context**, which does not itself require a worktree; implementation still defaults to one **Implementation Worktree** per **Branch Contract**.
- "Final worktree" was used as though worktrees themselves are merged - resolved: commits from **Helper Branches** are integrated into the **Canonical Integration Branch**; files are never copied between worktrees as the integration mechanism.
- "Draft PR" was treated as the default publication result - resolved: **Pull Request Readiness** determines the state, and verified completed work produces a pull request ready for review.
- `pending-review` was treated as a formal workflow-status category - resolved: **Pending Review** is a reviewer-attention signal applied after corrections and evidence are ready for another look.
- "Frontend logging" included both operational diagnosis and product analytics - resolved: **Frontend Operational Events** are a bounded observability surface; product analytics remains separate.
- "Server-owned export" and "OpenTelemetry Collector" were treated as synonyms - resolved: **Server-Owned Trace Export** is the ownership and security rule; a Collector is one optional server-side delivery topology.
- "Re-review" implied a separately requested workflow - resolved: **Thread Reconciliation** runs during every **Pull Request Review** when existing unresolved threads are present.
- "Existing threads" did not distinguish workflow and human ownership - resolved: all are inspected, but automatic resolution is limited to workflow-owned threads.
- "Round" and "batch" were the default units for both questions and findings - resolved: use a **Decision Queue** or **Review Queue**, keep one material item current until disposition, and batch only when the user explicitly requests it.
- A remaining count such as "20 findings" hid the endpoint - resolved: show **Queue Progress** with current position, current total, and remaining-after-current, and explain every total change.
- "Migration" included both persisted-database changes and structural refactors - resolved: only database schema or data migrations require a **Migration Proof Harness**.
- "Global component" was used for any component shared by multiple modules - resolved: a **Frontend Component** moves only to the narrowest shared owner, reaching the application-level components boundary only when its uses span unrelated domains.
