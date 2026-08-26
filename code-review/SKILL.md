---
name: code-review
description: Perform evidence-verified, staff-level code reviews for diffs, pull requests, commits, branches, local changes, or specific files. Apply engineering-for-certainty as the governing doctrine and require relevant engineering companion skills for auth/security, resilience, observability, and frontend changes.
---

# Code Review

## Objective

Find material engineering risks with high recall, then independently refute or verify every candidate before reporting it. Prioritize correctness, security, data integrity, reliability, and operational safety over style. Keep ordinary reviews read-only.

Treat review assurance and execution mechanism as separate concerns. Low, Medium, High, and Max describe the required depth and assurance. Workflow orchestration, parallel subagents, sequential clean contexts, and separated coordinator passes describe how that assurance is pursued.

Treat parallelism as an execution technique, not as a review angle. Use logically independent finder and verifier contexts when the runtime supports them and the review surface provides useful independent work packets. Scale finder count by effective diff size, risk, and change topology rather than creating one worker per review angle.

Keep finder prompts independent. Give each finder raw artifacts, governing contracts, and a bounded review surface without other finders' conclusions. Give verifiers a normalized candidate claim and raw evidence without the finder's preferred verdict.

Use the most suitable permitted execution mechanism for the specific review. Do not require Workflow when ordinary subagents or clean sequential contexts can satisfy the review obligations. Degrade gracefully when an execution mechanism is unavailable while preserving the requested review depth as far as the runtime reasonably permits. Record any limitation that materially reduces context independence, coverage, or verification confidence.

## Required Engineering Doctrine

Before gathering review scope, load and follow `$engineering-for-certainty`.
Treat it as the governing engineering standard while honoring its priority for
explicit user requirements and established repository conventions.

Load each companion whenever the reviewed change directly modifies its area or
explicitly integrates with its APIs:

- Load `$engineering-observability` for logs, metrics, traces, telemetry, audit
  records, correlation context, redaction, alerting, or frontend log ingestion.
- Load `$engineering-resilience` for external calls, retries, timeouts, mutation
  safety, idempotency, concurrency, queues, cron tasks, webhooks, jobs, or other
  async processing.
- Load `$engineering-auth-security` for authentication, authorization, cookies,
  sessions, CSRF, tokens, actor context, permissions, protected routes, policy
  enforcement, or secrets.
- Load `$engineering-frontend` for frontend modules, routes or screens, API
  adapters, forms, accessibility, client state, or web and mobile testing.

If `$engineering-for-certainty` or a triggered companion is unavailable, stop
before reviewing. Name every missing skill and tell the user to install
`code-review` together with the core doctrine and required companions. Do not
reconstruct missing doctrine from memory.

Use doctrine observations as candidate issues, not automatic findings. Every
candidate must be applicable to the changed surface, identify a material
consequence, and survive the normal verification pipeline. Do not infer
process-only violations, such as failure to use TDD, from a diff that cannot
establish how the work was performed.

Use each activated companion as a finder and verification obligation, not only
as background reading. Record the core doctrine, activated companions, and the
checks they added in the final review scope.

## Pipeline

Run the review as six distinct phases:

1. Gather the target, intent, governing rules, and validation context.
2. Find candidate issues through independent review angles.
3. Normalize and deduplicate candidates by root cause.
4. Verify every survivor and run targeted validation where useful.
5. Rank confirmed findings into one review queue and work through one finding at a time, or return the routed queue to an authorized composing delivery workflow, then report residual risks and open questions.
6. After the user verifies the reported findings, analyze whether the governing issue or handoff could have prevented them and propose targeted `issue-review` skill improvements.

### Composing Delivery Mode

When `$code-review` is invoked as the read-only analysis engine inside
`$deliver-issue` or another explicitly authorized delivery workflow, read and
follow [Review Loop Contract](references/review-loop-contract.md) before Phase
0. The composing workflow must supply a governing issue with correction and
escalation authority.

Keep every finder and verifier read-only. After completing the normal full
discovery and verification pipeline, attach `AUTO_CORRECT`, `USER_DECISION`,
`BLOCKED`, or `RESIDUAL_RISK` routing to every non-refuted result and return the
complete record to the composing workflow. This mode overrides the ordinary
one-at-a-time presentation only for findings the issue authorizes for automatic
correction; it does not weaken evidence, review independence, or user ownership
of material decisions.

### Checkpoint Review Mode

When the composing delivery workflow identifies a review checkpoint, read and
follow [Checkpoint Review](references/checkpoint-review.md) before Phase 0.

Checkpoint Review Mode keeps the normal discovery, verification, deduplication,
severity, independence, and routing standards. It changes only the declared
review range and the checkpoint-specific return record.

A checkpoint review must finish the complete candidate landscape for that
checkpoint before returning findings. Do not alternate between discovering one
finding and correcting it while other checkpoint findings remain undiscovered.

Checkpoint Review Mode never replaces the final full integration review.

## Phase 0: Gather the Review Scope

### Resolve the target

Honor a user-supplied PR, branch, commit, range, file list, or explicit base before applying defaults.

For an implicit local or branch review:

1. Inspect repository status before selecting the diff.
2. Prefer the merge-base range `@{upstream}...HEAD` when an upstream exists.
3. Otherwise use the repository's actual default branch when discoverable, then a local `main` or `master`, then `HEAD~1` as the final fallback.
4. Include staged and unstaged changes for an implicit local or branch review. For an explicit committed target, exclude them unless the user asks to include local work.
5. List relevant untracked files separately because `git diff HEAD` does not include them.
6. Detect renames, submodules, generated files, lockfiles, migrations, and binary changes instead of silently excluding them.
7. Record the selected base, head, working-tree inclusion, and reviewed file set. Explain an unexpectedly empty target.

Do not fetch, pull, switch branches, or mutate repository state merely to gather a review unless the user authorized it.

### Recover intent and contracts

Read the smallest sufficient set of artifacts that define the intended behavior:

- PR or issue description and acceptance criteria.
- Feature files, plans, ADRs, and relevant documentation.
- Schemas, API contracts, migrations, configuration, and public types.
- Existing tests and nearby implementation examples.
- The issue's Runtime Acceptance Plan and current scenario evidence, including
  exact revision and environment, proxy blind spots, design comparison,
  invalidated re-runs, and downstream release gates when applicable.

For a checkpoint review, also read the checkpoint row, its owned acceptance and
traceability rows, its required appendices, the previous accepted checkpoint
head, and the frozen candidate head. Do not load unrelated issue history or raw
evidence unless a candidate requires it.

Compare the diff with the stated intent. Treat an incomplete or contradictory change as a candidate even when the edited code is internally consistent.

### Read governing instructions

Read every applicable `AGENTS.md`, `CLAUDE.md`, repository instruction, ancestor instruction, and relevant architecture or contribution document. For convention findings, cite the exact governing rule; do not report vibes-based style preferences.

### Choose review effort

Honor an explicit user effort level. Otherwise scale effort by change risk, not only diff size:

- **Low**: resolve scope, scan each hunk and enclosing function, audit removed behavior, and verify a small set of high-confidence candidates.
- **Medium**: run all relevant core finder angles, trace changed contracts, inspect tests, and verify every survivor.
- **High**: run independent focused passes, activate relevant specialist angles, and independently verify material candidates.
- **Max**: use the widest justified fan-out, trace broader contracts and architecture, and run the strongest safe targeted validation.

Increase effort for privilege boundaries, irreversible writes, migrations, public contracts, concurrency, financial or sensitive data, core workflows, and weak test coverage. State the chosen effort and reviewed scope in the final response.

At High or Max effort on a multi-file change, enumerate every changed file that is not generated, vendored, or a binary asset, and confirm each received at least an Angle A and Angle B pass before candidate-gathering is called complete. Do not let a large new module absorb review depth at the expense of smaller adjacent files in the same diff — a five-line configuration or deployment change fails exactly as often as a five-hundred-line new module, and gets less scrutiny by default.

### Plan review depth and execution separately

Make review-planning decisions first:

- What review depth does the requested effort and change risk require?
- Which changed surfaces require coverage?
- How many genuinely independent finder work packets exist?
- Which candidates would require additional independent verification?
- Which targeted validation could materially change confidence?

Then make execution-planning decisions:

- Can ordinary parallel subagents execute the work packets effectively?
- Would scripted Workflow orchestration materially improve coordination, isolation, latency, or context containment?
- Is Workflow available and already permitted?
- If permission or enablement is required, is the expected benefit large enough to justify involving the user?
- Which fallback will preserve the review obligations if Workflow is not used?

### Choose the execution mechanism

Support these execution mechanisms:

1. Workflow-based orchestration when available, permitted, and materially useful.
2. Parallel independent subagents.
3. Sequential clean or isolated review contexts.
4. Deliberately separated passes in the coordinator context.

Do not assume Workflow is better merely because it is available. Prefer ordinary subagents when the review requires only a small number of independent workers and the coordinator can manage their results without material context pressure or latency.

Workflow is materially useful when one or more of these apply:

- the plan requires more independent workers than the coordinator can comfortably manage turn by turn;
- the review has several coherent surfaces plus a second-stage verifier fan-out;
- finder and verifier stages can be productively pipelined;
- intermediate candidate and verification records would materially crowd the coordinator context;
- the same branching, deduplication, or retry pattern must run repeatedly;
- the expected latency reduction or context isolation is substantial.

A high-risk change alone does not require Workflow. A small high-risk change may be better served by two carefully scoped ordinary subagents.

When running in Claude Code and considering Workflow or Agent orchestration, read [Claude Code Review Orchestration](references/claude-code-orchestration.md) and follow its permission, safety, and fallback rules.

### Preserve requested assurance

A fallback execution mechanism may increase latency or reduce context independence without changing the requested review obligations. Preserve the requested effort as far as reasonably possible.

Do not claim that the requested assurance was fully achieved when runtime limitations prevented required coverage or independence. Complete the supportable review and record:

- the requested review effort;
- the actual execution mechanism;
- the coverage and verification completed;
- the specific assurance property that could not be achieved;
- the strongest available alternative used.

### Size the finder budget

Estimate effective diff size from additions plus deletions in reviewed text files. Exclude generated, vendored, lockfile, and binary changes from the line calculation, but still inspect them when they affect contracts, dependencies, deployment, or runtime behavior.

Use these finder ranges:

- **Low**: 1 finder.
- **Medium**: 1-3 finders.
- **High**: 2-6 finders.
- **Max**: 3-8 finders.

Within the selected range, target approximately one finder per 200 effective changed lines, then adjust for the number of genuinely independent work packets.

Raise the target for:

- authentication, authorization, tenant isolation, money, sensitive data, migrations, destructive persistence, concurrency, or irreversible writes;
- changed public contracts, environment schemas, deployment configuration, or cross-layer behavior;
- weak integration coverage or unusually broad architectural reach.

Lower the target when the diff is mechanically repetitive or cannot be divided without separating code from the contracts needed to understand it. Never spawn empty or substantially duplicate finders merely to reach a numeric target.

Diff size controls review capacity, not risk. A small high-risk change may require more independent review than a large routine change.

## Phase 1: Find Candidate Issues

Treat every observation as a candidate, not a finding. Run each relevant angle independently so one framing does not suppress another.

### Plan finder work packets

Treat Angles A-L as coverage obligations, not worker identities. Build a work packet map before starting finders.

Prefer these packet types:

- **Coverage shards**: coherent changed modules, domains, or file groups. Every shard receives line-by-line correctness, removed-behavior, and testing-quality coverage.
- **Contract tracing**: changed exports, endpoints, schemas, errors, database shapes, environment variables, consumers, tests, and deployment manifests.
- **Triggered specialist passes**: security, data integrity, resilience, observability, frontend, or performance when the changed surface activates that doctrine.
- **Global consistency pass**: reuse, simplification, sibling behavior, architectural ownership, and cross-shard invariants.

A work packet may cover several related angles, and an important angle may appear in several packets. Do not isolate a deletion from the old behavior it provided or isolate a contract from its callers and deployment consumers.

At High and Max effort, start independent packets concurrently when the selected execution mechanism permits. Require every non-generated changed file to receive Angle A and Angle B coverage regardless of how packets are divided.

### A. Line-by-line correctness

Inspect every hunk and its enclosing function or module. Look for inverted conditions, off-by-one behavior, missing awaits, falsy-zero or empty-value bugs, copy-paste errors, swallowed failures, invalid state transitions, wrong defaults, and incomplete branches.

Report unchanged code only when the change makes it newly reachable, changes its inputs or ordering, removes a protecting invariant, changes its lifecycle, or expands its trust boundary. Attach the candidate to the closest causal changed line when possible.

### B. Removed-behavior audit

For every meaningful deletion or replacement:

1. Name the validation, guard, cleanup, error mapping, ordering, side effect, or invariant the old code provided.
2. Locate where the new code re-establishes it.
3. Create a candidate when the behavior disappeared, weakened, or moved behind a narrower condition.

### C. Cross-file contract tracing

For each changed exported function, endpoint, event, schema, database shape, error contract, or public type:

- Inspect callers, consumers, callees, and relevant adapters.
- Compare old and new preconditions, return shapes, nullability, error behavior, timing, and side effects.
- Check sibling implementations for inconsistent updates.
- Check whether tests exercise the real integration boundary.
- For a changed environment or configuration schema, also trace it to every deployment manifest that assigns those variables: CI/CD workflow files, Compose/Kubernetes manifests, and other infrastructure-as-code. Confirm each supported deployment-environment value has at least one config value that satisfies the schema; a refinement that rejects every legal value under one environment is a boot-breaking defect, not a style issue. Separately check whether a manifest's variable-substitution syntax (for example `${VAR:-}`) can produce an empty string when a variable is merely unset — most schema validators treat an empty string differently from an absent one.

### D. Security and authorization

Inspect trust boundaries, authentication, authorization, tenant isolation, input validation, output sanitization, secrets, injection risks, unsafe deserialization, session or token handling, and privilege changes. Trace actor context and permission checks end to end rather than inferring safety from route names or UI restrictions.

### E. Data integrity and persistence

Inspect schemas, migrations, transactions, uniqueness, foreign keys, destructive writes, partial updates, backfills, ordering, serialization, precision, compatibility, and rollback behavior. Look for durable corruption or loss paths as well as immediate failures.

For operational or administrative tooling—such as migration, rotation, cleanup, or batch commands—that accepts a caller-supplied scope, verify it distinguishes an invalid or nonexistent scope from a valid scope with no remaining work. Establish scope existence before processing, or return distinct outcomes and exit statuses for `scope_not_found`, `already_complete`, and `completed`. For iterative tooling, also verify that zero matches or lack of progress cannot be mistaken for successful completion.

### F. Reliability and concurrency

Inspect retries, timeouts, cancellation, idempotency, queues, webhooks, cron, races, atomicity, resource cleanup, error recovery, and partial failure. Verify whether an operation can be repeated safely and whether failure leaves state recoverable.

### G. Reuse

Search the repository before claiming duplication. Name the existing helper, abstraction, or shared module and explain the material divergence or defect risk caused by reimplementation.

### H. Simplification and state modeling

Look for redundant or derivable state, near-duplicate branches, contradictory booleans, deep nesting, dead code, and unnecessary indirection. Create a candidate only when the complexity causes credible defect or maintenance risk; name the simpler form.

### I. Performance and resource lifetime

Inspect redundant work, sequential independent calls, unbounded queries or loops, hot-path and startup cost, repeated parsing or allocation, N+1 behavior, cache invalidation, memory retention, and closures that capture large scopes. Tie the concern to a realistic workload.

### J. Observability and operations

Check whether failures can be detected, attributed, and diagnosed. Inspect structured errors, logs, metrics, traces, correlation context, redaction, alerts, and recovery signals when the change alters operational behavior. Do not demand instrumentation unrelated to the changed risk.

### K. Testing quality

Check whether tests cover the changed contract, success paths, expected failures, boundaries, and regression scenario. Detect tests that mock away the disputed behavior, assert implementation details, pass vacuously, or omit real wiring. Treat missing tests as supporting evidence for a behavior risk, not automatically as a standalone finding.

For observable runtime changes, independently audit whether the Runtime
Acceptance scenarios cover every accepted observable outcome, one complete
primary journey, and targeted exploration through the real external boundary.
Check that the evidence belongs to the reviewed revision and environment, that
necessary proxies and design deviations are explicit, and that later changes did
not make the evidence stale. Do not infer runtime correctness from green tests or
the implementer's completion summary.

### L. Architectural altitude and conventions

Look for special cases bolted onto shared infrastructure, workarounds that bypass existing abstractions, and fixes that leave the same invariant broken for sibling consumers. Name the deeper mechanism that should own the behavior. Report convention violations only when an applicable rule can be cited and the violation is material.

Use the required companions for their specialist finder and verification
passes. Also increase review depth automatically for migrations, destructive
persistence, public APIs, caching, hot paths, and other high-impact surfaces
that do not belong to a companion skill.

## Candidate Standard

Normalize each candidate before verification:

```text
file
line
category
provisional_severity
summary
failure_scenario
code_path
supporting_evidence
assumption
refuting_evidence_to_find
suggested_fix
```

Write `failure_scenario` as concrete input or state -> executed path -> wrong output, corruption, security exposure, outage, or material maintenance consequence. Drop candidates that have no nameable material failure or consequence.

Allow finders to return their strongest candidates first. Use a soft limit of six candidates per angle to control noise, but never suppress additional Critical or High candidates. Record when an angle was truncated.

## Phase 2: Normalize and Deduplicate

Collapse candidates that share one root cause. Prefer one finding that names all affected consumers over repeated symptoms. Keep candidates separate when they require different fixes or have independently actionable failure paths.

Re-evaluate provisional severity after deduplication; broad impact may raise severity, while duplicated symptoms must not inflate it.

Maintain one coordinator-owned candidate ledger. Normalize incoming candidates to the Candidate Standard and attach their finder packet and evidence source.

Finders may return their strongest candidates before completing their whole packet. At High or Max effort, verification may begin while other finders are still working only after the coordinator has normalized the candidate and established a sufficiently stable root-cause claim.

Do not begin early verification for a candidate likely to merge with findings from another active packet. If later evidence changes, broadens, or merges the root-cause claim, discard the stale verification result and verify the final normalized claim again.

The coordinator may reconsider Workflow once after candidate normalization when the discovered verifier fan-out is materially larger or more complex than the initial plan. Do not reconsider it after the user has declined it during the same review.

Do not finalize the finding queue until every finder has completed, every changed surface has received its required coverage, and global deduplication is complete.

## Phase 3: Verify Every Survivor

For each candidate:

1. Restate the precise failure claim and required code path.
2. Identify the evidence that would disprove it.
3. Search relevant code, tests, contracts, schemas, documentation, configuration, migrations, call sites, and runtime constraints.
4. Check guards, types, feature flags, transaction boundaries, retries, tests, deployment assumptions, and usage constraints.
5. Reconstruct reachability from entrypoint to failure.
6. Assign exactly one verdict.

Use these verdicts:

- **CONFIRMED**: Repository or runtime evidence supports a reachable, material failure.
- **CONDITIONAL**: The failure is realistic but depends on a clearly stated environment, state, workload, or product assumption.
- **REFUTED**: Cited evidence proves the path is guarded, impossible, already handled in the change, or immaterial. Drop it.
- **NEEDS_CONTEXT**: Required product or operational context cannot be recovered. Ask or list it as an open question; do not present it as a finding.

Before finalizing a `CONDITIONAL` verdict, check for in-repo evidence that already resolves the stated assumption — integration or sandbox documentation, fixtures, existing tests, or configuration defaults describing the real current environment or upstream behavior. If that evidence shows the assumption already holds today, reclassify the candidate as `CONFIRMED` instead of leaving it `CONDITIONAL`.

Route uncertainty before assigning `NEEDS_CONTEXT`:

- **Discoverable fact**: inspect code, tests, contracts, documentation, configuration, history, or available tools.
- **User-owned decision**: ask a numbered question that states the decision, impact, options, and recommendation. Keep it out of findings until answered.
- **Empirical unknown**: run the narrowest safe targeted validation or investigation that can resolve it. Do not ask the user to guess runtime behavior.

Only `CONFIRMED` candidates become normal findings. Place `CONDITIONAL` candidates under residual risk with their assumptions. If further evidence establishes reachability, reclassify the candidate as `CONFIRMED` before reporting it as a finding. Never report a finding solely because a subagent proposed it.

Prefer two independent evidence points for Critical or High findings when practical. One direct point is enough for mechanically provable failures such as a type error, missing export, failing command, unreachable path, or reproduced defect.

### Independent skeptical verification

Give every normalized survivor at least one skeptical verification pass that is logically independent from its finder. Ask the verifier to seek both confirming and refuting evidence and to cite the exact guard, call path, contract, runtime constraint, test, or reproduction supporting its assessment.

For Critical or High candidates, or candidates with material uncertainty, use a second independent verifier when available. Give the verifiers different mandates where useful:

- one attempts to prove the claimed path unreachable, guarded, or immaterial;
- one reconstructs reachability and material impact from the entrypoint.

Use a third verifier only when the first two materially disagree, rely on incompatible assumptions, or leave an important evidentiary gap.

Do not determine verdicts by majority vote. Evidence outranks vote count. One direct reproduction may establish a defect; one cited guard may refute several unsupported confirmations. The coordinator must reconcile the evidence and assign the final verdict under the normal CONFIRMED, CONDITIONAL, REFUTED, and NEEDS_CONTEXT definitions.

When subagents are unavailable, perform the same verifier roles as deliberately separate skeptical passes. Record the lack of clean-context independence as a review limitation.

### Targeted validation

Run the narrowest safe command that materially changes confidence: a focused test, type check, build, linter, static analysis, minimal reproduction, integration test, or schema or migration validation.

Distinguish validation executed from validation merely recommended. A passing test refutes a candidate only when the test exercises the disputed behavior and contains a meaningful assertion. Do not mutate production systems or external state during validation.

For Runtime Acceptance evidence, keep the reviewer read-only. Reconcile the
scenario ledger against the issue, diff, deployed build identity, screenshots or
sanitized responses, and current repository state. Use a safe read-only runtime
reproduction only when it can materially resolve a disputed claim without
creating accounts, changing shared data, deploying, or exercising destructive
behaviour.

Return missing, failed, stale, secret-bearing, or wrong-revision runtime evidence
as a verification gap to the composing delivery workflow. Create a normal code
finding only when that gap and the inspected implementation support a concrete,
reachable, material failure; do not turn process incompleteness alone into a
code defect.

## Severity and Ranking

Use severity consistently:

- **Critical**: Likely exploitable security issue, irreversible data loss or corruption, severe outage, or broken core workflow.
- **High**: Likely production bug, privilege-boundary failure, reliability regression, or missing protection for important data.
- **Medium**: Reachable edge-case bug, operational blind spot, material performance issue, maintainability risk likely to cause defects, or meaningful test gap tied to changed behavior.
- **Low**: Minor but concrete risk worth fixing that is unlikely to cause material harm soon.

Rank by severity and impact, then confidence, breadth, category, and urgency. When otherwise comparable, place correctness, security, data integrity, and reliability before cleanup, altitude, and convention findings. Do not let a trivial correctness issue outrank a materially higher-severity architectural risk.

## Output

Put actionable findings first. For each finding include:

- Severity and verifier verdict.
- Clickable file and precise line.
- One-sentence defect statement.
- Concrete failure scenario and affected code path.
- Evidence and impact.
- Suggested correction.
- Explicit assumption when applicable.
- Missing regression test when relevant.

Investigate, verify, deduplicate, and rank the complete finding set before presenting the first result. This broad analysis prevents the current finding from being framed without a later-known dependency or shared root cause.

Present one confirmed finding at a time in severity and impact order. Give it a stable ID and show `**Progress: Finding <position> of <total> - <remaining> remain after this**` on every response that presents or continues it. Calculate position as prior dispositions plus the current finding; calculate total as prior dispositions plus the current and queued findings. Do not show a remaining count alone; a percentage may appear only as secondary information. Keep discussing the current finding until the user accepts it as valid, rejects it, requests a revision, defers it, or asks for named evidence. Only then present the next finding, after recomputing the queue for dependencies, duplicates, or invalidated claims. If recomputation changes the total, state `**Queue revised: <old> -> <new>.** <reason>` before the next finding; never silently change the denominator or stable finding IDs. A generic request to review code is not a request for batching.

In Composing Delivery Mode, use the reference contract's delivery return record
instead. Complete the full queue before returning it, batch only
`AUTO_CORRECT` findings for the operator, and route material decisions back to
the user without treating delivery authorization as adjudication.

In Checkpoint Review Mode, return the checkpoint result to the composing
workflow using the checkpoint reference contract. A clean checkpoint result
means only that the declared checkpoint range and integration seams satisfy the
checkpoint gate. It is not a clean final review of the complete issue.

Batch at most ten findings only when the user explicitly requests a batch or complete report. Preserve the same evidence for every item, allow adjudication by stable ID, and never treat a batch boundary as permission to omit Critical or High findings. There is no total finding cap.

When the review queue is empty, provide this closeout:

1. Brief understanding of the change.
2. Scope, requested effort, actual execution mechanism, effective diff size, planned and actual finder count, finder work packets, verifier count, doctrine skills applied, and the checks each activated companion added.
3. Validation performed and results.
4. Conditional residual risks, `NEEDS_CONTEXT` questions, and remaining coverage gaps.

Before presenting the first finding, confirm that:

- every planned finder packet completed or has a recorded fallback;
- every candidate has exactly one final verdict;
- no verifier result refers to a superseded pre-deduplication claim;
- any Workflow decline or execution limitation is recorded once without weakening the evidence standard;
- the reported review effort distinguishes requested assurance from assurance actually achieved.

Before delivering each finding or an explicitly requested batch, verify that the selected scope and effort are recorded, every candidate has exactly one verdict, every changed surface in scope was inspected, and all limitations and residual risks are classified. Keep the review read-only and perform no external writes unless the user explicitly requested them through a composing workflow.

Also verify that every directly affected companion area was identified and its
skill loaded, and that doctrine observations passed the same materiality,
reachability, refutation, and verdict requirements as every other candidate.

If no material issues survive verification, state `No material issues identified.` Do not manufacture findings to fill the format.

Use human-readable output by default. Provide structured JSON when the user requests machine-readable output, preserving severity, verdict, file, line, summary, failure scenario, evidence, and suggested fix.

## Phase 4: Feed Verified Findings Back Into Issue Review

Run this phase only after the user has reviewed the reported issues and explicitly confirmed which findings are valid, or when the user explicitly requests the retrospective. Do not delay the initial code-review report while waiting for this feedback.

Do not run this retrospective automatically during Composing Delivery Mode.
Finishing delivery does not authorize editing review doctrine; wait for the
user's separate confirmation or request.

For each user-confirmed finding:

1. Recover the governing issue, ticket, feature file, plan, or implementation handoff used for the change. If none exists, state that the finding cannot support an `issue-review` improvement.
2. Trace the finding to the planning artifact: identify the missing, ambiguous, contradictory, weakly testable, or unverifiable instruction that allowed the defect. Distinguish a planning failure from an implementation mistake made despite clear guidance.
3. Inspect the current `issue-review` skill before proposing changes. Check whether an existing gate already covers the failure and was merely not followed; do not propose duplicate doctrine. If `issue-review` is unavailable, skip this retrospective and state that dependency clearly.
4. Test generality. Propose a skill change only when it would prevent a recurring class of issue-writing or issue-review failures across projects, not when it encodes project-specific facts or the details of one bug.
5. Prefer the smallest change that adds a check, sharpens an existing gate, or requires stronger evidence. Preserve the distinct roles: `issue-review` improves implementation instructions; `code-review` diagnoses the resulting code.

Do not recommend an `issue-review` change for:

- Findings the user rejects or has not verified.
- Pure implementation errors that contradicted clear, sufficient issue guidance.
- Gaps already covered explicitly by the current `issue-review` skill unless the evidence shows that wording is too weak or easy to misapply.
- Defects that could not reasonably have been anticipated during issue preparation.

Present the retrospective separately from the code findings. For each proposed skill update include:

- The verified finding or recurring failure class it addresses.
- The causal gap in the governing issue or handoff.
- The current `issue-review` section affected.
- The exact proposed wording or a compact patch.
- Why the change is generalizable and how it would have made the defect less likely.
- Any cost, false-positive risk, or added review burden.

End with one of these explicit outcomes:

- `Proposed issue-review improvements:` followed by the proposals, and ask the user whether to apply them.
- `No issue-review update warranted.` followed by the evidence-bound reason.

Never edit the `issue-review` skill during this retrospective unless the user separately authorizes the update.

## Comment and Fix Modes

Keep a normal review read-only.

- For GitHub inline publication, use `$pull-request-review` as the composing workflow. This skill supplies verified findings and evidence; the composing workflow owns existing-thread reconciliation, user adjudication, responsible-engineer tagging, fix snippets, GitHub writes, and post-write verification.
- When this skill is already running inside `$pull-request-review`, return stable finding IDs and complete candidate records to that workflow after verification. Do not load the composing skill from inside this skill or post comments directly.
- When this skill is running inside `$deliver-issue`, follow the Review Loop Contract, return routed finding records, and leave every correction, commit, push, PR, and CI action to that composing workflow.
- A direct request to review a GitHub PR and publish comments should select `$pull-request-review` before analysis. If it is unavailable, keep the review read-only and name the missing workflow instead of reconstructing GitHub mutation behavior here.
- With an explicit fix request or `--fix`, present the review first, then begin a separate implementation phase. Do not auto-fix `CONDITIONAL` or `NEEDS_CONTEXT` candidates. Validate applied fixes and summarize the resulting changes.

Do not post externally or modify the working tree unless the user explicitly requested that action.
