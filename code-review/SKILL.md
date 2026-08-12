---
name: code-review
description: Perform evidence-verified, staff-level code reviews for diffs, pull requests, commits, branches, local changes, or specific files. Apply engineering-for-certainty as the governing doctrine and require relevant engineering companion skills for auth/security, resilience, observability, and frontend changes.
---

# Code Review

## Objective

Find material engineering risks with high recall, then independently refute or verify every candidate before reporting it. Prioritize correctness, security, data integrity, reliability, and operational safety over style. Keep ordinary reviews read-only.

Use logical independence between finder passes. Use subagents when available and worthwhile for broad changes, security-sensitive code, concurrency or data-integrity work, or independent verification. Do not create one subagent per angle mechanically. Give subagents raw artifacts or bounded review areas without leaking expected conclusions.

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
5. Rank and report confirmed findings, then residual risks and open questions.
6. After the user verifies the reported findings, analyze whether the governing issue or handoff could have prevented them and propose targeted `issue-review` skill improvements.

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

## Phase 1: Find Candidate Issues

Treat every observation as a candidate, not a finding. Run each relevant angle independently so one framing does not suppress another.

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

For operational or administrative tooling (migration, rotation, cleanup, or batch scripts) that accepts a caller-supplied scope and loops until a completion condition, verify the scope's existence is checked before the loop starts, or that "no matching target" is distinguished from "target already complete" in the result and exit status. Otherwise an invalid scope (a typo'd ID, a wrong key) silently reports false success against live data instead of failing loudly.

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

For independent verification, give the verifier the candidate, diff, relevant files, and raw context without the finder's preferred verdict. Ask it to seek both confirming and refuting evidence. If subagents are unavailable or unjustified, perform a separate skeptical pass with the same standard.

### Targeted validation

Run the narrowest safe command that materially changes confidence: a focused test, type check, build, linter, static analysis, minimal reproduction, integration test, or schema or migration validation.

Distinguish validation executed from validation merely recommended. A passing test refutes a candidate only when the test exercises the disputed behavior and contains a meaningful assertion. Do not mutate production systems or external state during validation.

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

Present confirmed findings in Review Batches of at most ten, ranked by severity and impact. There is no total finding cap. State whether more verified findings remain, obtain feedback on the current batch when the workflow requires adjudication, and continue until every confirmed finding has been presented. Never treat a batch boundary as permission to omit Critical or High findings; rank them into the earliest batch.

Then provide:

1. Brief understanding of the change.
2. Scope, effort, doctrine skills applied, and the checks each activated companion added.
3. Validation performed and results.
4. Conditional residual risks, `NEEDS_CONTEXT` questions, and remaining coverage gaps.

Before delivering each batch, verify that the selected scope and effort are recorded, every candidate has exactly one verdict, every changed surface in scope was inspected, and all limitations and residual risks are classified. Keep the review read-only and perform no external writes unless the user explicitly requested them through a composing workflow.

Also verify that every directly affected companion area was identified and its
skill loaded, and that doctrine observations passed the same materiality,
reachability, refutation, and verdict requirements as every other candidate.

If no material issues survive verification, state `No material issues identified.` Do not manufacture findings to fill the format.

Use human-readable output by default. Provide structured JSON when the user requests machine-readable output, preserving severity, verdict, file, line, summary, failure scenario, evidence, and suggested fix.

## Phase 4: Feed Verified Findings Back Into Issue Review

Run this phase only after the user has reviewed the reported issues and explicitly confirmed which findings are valid, or when the user explicitly requests the retrospective. Do not delay the initial code-review report while waiting for this feedback.

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
- A direct request to review a GitHub PR and publish comments should select `$pull-request-review` before analysis. If it is unavailable, keep the review read-only and name the missing workflow instead of reconstructing GitHub mutation behavior here.
- With an explicit fix request or `--fix`, present the review first, then begin a separate implementation phase. Do not auto-fix `CONDITIONAL` or `NEEDS_CONTEXT` candidates. Validate applied fixes and summarize the resulting changes.

Do not post externally or modify the working tree unless the user explicitly requested that action.
