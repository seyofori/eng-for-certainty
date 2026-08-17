---
name: engineering-for-certainty
description: Core certainty-first coding doctrine for software projects. Use by default for implementation, review, debugging, planning, and code generation; pair with companion skills when work touches observability, resilience, auth/security, or frontend engineering.
---

# Engineering for Certainty

Use this skill by default for coding projects. It is the core doctrine for implementation, review, debugging, planning, and code generation. Keep the defaults broadly applicable. Preserve the repo's existing stack and conventions by default. If the task is already a structural refactor, keep behavior stable while normalizing naming and layout within the refactored scope, even when the legacy naming convention differs. Apply companion skills only when the work directly modifies files in those areas or explicitly integrates with their APIs.

## Priorities

Resolve instruction conflicts in this order, top to bottom.

1. Preserve existing repo conventions unless the user asked for a migration.
2. If the task is already a structural refactor, keep behavior stable while normalizing naming and layout within the refactored scope.
3. Apply specialized companion skills only when the work directly modifies files in those areas or explicitly integrates with their APIs.
4. Always validate boundaries.
5. Keep adapters thin and business logic explicit.
6. Use `Result<T, E>` / `ResultAsync<T, E>` for expected failures.
7. Write tests for critical paths and expected failures first.
8. If a case is still unclear, prefer the most conservative interpretation that preserves existing behavior and ask for clarification before changing architecture or stack.

## Companion Skills

Apply these only when relevant:

- Use `$engineering-observability` for logs, metrics, traces, audits, Better Stack or OpenTelemetry setup, correlation and trace context, sanitization/redaction, client telemetry ingestion, and signal capacity or retention.
- Use `$engineering-resilience` for external-call timeouts/retries, mutation safety, concurrency, idempotency, queues, cron, webhooks, and async processing.
- Use `$engineering-auth-security` for cookies, sessions, CSRF, token handling,
  auth boundaries, centralized Fastify auth-plugin boundaries, typed actor
  context propagation, permission enforcement, and shared permission-registry
  conventions.
- Use `$engineering-frontend` for frontend architecture, API integration, forms, accessibility quality bars, and web/mobile testing doctrine.

Companion activation adds relevant checks and validation obligations. It does not authorize implementation, external writes, or scope expansion beyond the user's request.

## Uncertainty Routing

Classify unresolved inputs before acting:

- **Discoverable fact**: inspect the repository, documentation, history, configured tools, or runtime evidence.
- **User-owned decision**: present concrete options, tradeoffs, and a recommendation; wait when the choice would materially change behavior, architecture, scope, or product meaning.
- **Empirical unknown**: use a bounded reproduction, test, spike, benchmark, or research pass. Do not ask the user to guess what evidence can establish.

## Preferred Defaults

- Language: TypeScript.
- Package manager: pnpm.
- Web frontend: React + Vite + TanStack Router + Tailwind.
- Mobile: Expo React Native.
- Form UI library (web/mobile): TanStack Form.
- Server state and query orchestration (web/mobile): TanStack Query.
- Backend: Fastify.
- Database queries: Drizzle.
- Validation: Zod.
- Result library: neverthrow.
- Path aliases: for new TypeScript/JavaScript projects, use named `#...`
  aliases for private imports within an app or package, and reserve
  `@scope/package` specifiers for workspace or published packages. Prefer
  `package.json#imports` where supported, with matching TypeScript, bundler, and
  test configuration as required. Preserve established repository conventions
  unless the user requests a migration.

## Linting and Formatting

- For new projects or TypeScript/JavaScript projects that do not already standardize on another tool, prefer ESLint for linting and Prettier for formatting.
- Preserve the repo's existing toolchain when one already exists; this rule overrides the ESLint/Prettier default for established repos. Do not migrate a repo from Biome, dprint, Rome, Standard, or another established formatter/linter unless the user explicitly asks.
- Use ESLint for correctness, maintainability, and bug-prevention rules. Use Prettier for layout and whitespace only. Do not duplicate formatting rules in ESLint.
- Prefer ESLint flat config for new projects.
- For TypeScript projects, prefer `typescript-eslint` for parsing and TypeScript-aware rules.
- In monorepos, ESLint and Prettier should be wired to work consistently across apps and packages. Prefer shared root config or shared config packages over drifting per-package defaults.
- Prefer repo scripts that make the distinction explicit: `lint`, `lint:fix`, `format`, and `format:check`.

### Recommended ESLint Defaults

- `@typescript-eslint/no-unused-vars` with `_`-prefixed unused parameters/variables ignored when intentionally unused.
- `@typescript-eslint/no-floating-promises`.
- `@typescript-eslint/no-misused-promises`.
- `@typescript-eslint/consistent-type-imports`.
- `@typescript-eslint/switch-exhaustiveness-check`.
- `eqeqeq`.
- `curly`.
- `prefer-const`.

### Recommended Prettier Defaults

- Keep Prettier opinionated and minimal; avoid style bikeshedding.
- For new projects without an existing convention, default to:
  - `printWidth: 100`
  - `singleQuote: true`
  - `trailingComma: "all"`
  - `semi: true`
  - `arrowParens: "always"`
- If a repo already has an established formatting style, keep that style instead of reformatting unrelated code.

### Recommended Git Hook Defaults

- Prefer local git hooks or equivalent local automation for fast feedback when the repo supports them.
- Pre-commit should run formatting and linting against staged files, plus the unit-test command when the repo's unit suite is reliably fast enough for commit-time feedback.
- Pre-push should run the broader test gates: full unit tests, integration tests, and E2E tests when those commands already exist in the repo.
- Prefer explicit repo scripts for hook entry points, such as `test:unit`, `test:integration`, and `test:e2e`.
- Do not silently skip missing commands. Either wire hooks only to commands that exist or fail with a clear message so the repo's guarantees stay trustworthy.

## Version Control and Releases

Use explicit version-control semantics so project history communicates intent and release impact.

- Preserve the repo's established branch, commit, and release conventions when they exist.
- For new repos or repos without a stated convention, prefer Conventional Commits, conventional branch names, and SemVer.
- Treat commit messages, branch names, and version bumps as part of the engineering contract. They should help future maintainers understand what changed, why it changed, and whether consumers must react.

### Conventional Commits

- Use Conventional Commits for human-authored commits by default:
  `<type>(<scope>): <description>`.
- Prefer these commit types unless the repo defines another set:
  `feat`, `fix`, `docs`, `refactor`, `test`, `build`, `ci`, `perf`, `style`, `chore`, and `revert`.
- Use a scope when it clarifies ownership, such as `feat(auth): ...`, `fix(api): ...`, or `docs(issue-review): ...`.
- Keep the description imperative and concrete. Prefer "validate invite token expiry" over "updates auth stuff".
- Mark breaking changes explicitly with `!` after the type or scope and explain the impact in the body, for example `feat(api)!: require actor context`.
- Use commit bodies for rationale, migration notes, validation performed, and issue references when the subject alone is not enough.
- Do not hide behavior changes inside `chore`, `refactor`, or `style` commits. If runtime behavior changes, use the type that describes the user or system impact.

### Conventional Branching

- Use conventional branch names for new branches when the repo does not specify another pattern:
  `<type>/<short-kebab-description>`.
- Align branch type with the dominant intent: `feat/`, `fix/`, `docs/`, `refactor/`, `test/`, `build/`, `ci/`, `perf/`, `chore/`, or `release/`.
- Keep branch names short, lowercase, and kebab-case. Include a ticket or issue key only when the repo uses one, such as `fix/pm-123-token-expiry`.
- When a change mixes unrelated intents, split the work instead of using a vague branch such as `misc` or `updates`.
- For Codex-created branches, preserve the platform-required prefix when present, then apply the conventional name after it, such as `codex/fix/token-expiry`.

### Worktree Isolation

- Default to implementing every branch in its own dedicated git worktree, not the shared checkout, even for a single-threaded issue with no parallel workstreams.
- Skip the worktree only when the user has explicitly said this work should happen without one. A convenience preference from the agent is not authorization.
- Branch the worktree from the remote default branch, such as `origin/main`, not from local `HEAD`, so implementation starts from the canonical up-to-date base rather than whatever the shared checkout happens to have checked out. Use local `HEAD` as the base only when the user explicitly directs it.
- Record the full worktree path alongside the branch name before implementation begins, and record the exception explicitly when the user opted out.

### Semantic Versioning

- For packages, public APIs, plugins, CLIs, schemas, SDKs, and shared contracts, follow SemVer unless the repo has a documented alternative.
- Increment `MAJOR` for breaking changes, including removed APIs, incompatible schema changes, changed error contracts, changed CLI flags, or behavior that existing consumers reasonably depend on.
- Increment `MINOR` for backward-compatible features, new optional fields, new endpoints, new commands, or new capabilities.
- Increment `PATCH` for backward-compatible fixes, documentation corrections shipped with a package, internal refactors with no consumer-visible behavior change, and compatible dependency or build fixes.
- Treat database migrations, generated contracts, and exported TypeScript types as release-impacting surfaces when downstream consumers depend on them.
- Record migration guidance for every breaking change. The guidance should state who is affected, what they must change, and how to verify the migration.
- Do not bump versions mechanically. Choose the bump from the highest-impact change in the release.
- In monorepos, follow the repo's versioning model. If packages are independently versioned, bump only affected packages and any dependents whose published contract changes.

## First Move

Before editing code:

1. Classify the request's action mode. Answer, explain, plan, review, and diagnose authorize investigation and recommendations only; change, build, implement, or fix authorize in-scope edits. Do not infer write authority from a companion skill or from discovering a possible improvement.
2. Read the relevant local instructions, README, docs, ADRs, plan files, and nearby implementation examples.
3. Identify the repo's equivalents for contracts, adapters/routes/controllers, services/domain logic, repositories/persistence, hooks, flows, views, and tests.
4. For non-trivial work, perform a gap review before implementation. Resolve missing scope, contracts, errors, persistence behavior, orchestration, UI states, and test strategy.
5. Do not implement from an inconsistent plan. Update the plan or state the unresolved gap first.

## Mid-Implementation Discoveries

During implementation, do not silently expand the plan. Classify discoveries as
mechanical, material, or blocking.

Mechanical discoveries may be handled without user confirmation when they
preserve the issue's intent, behavior, architecture, and scope. Examples include
import fixes, local naming alignment, formatting, adapting to an existing
equivalent helper, or adding a narrowly required test fixture.

Material discoveries require pausing before further implementation. Pause when
the work would:

- Change acceptance criteria, user-visible behavior, public API contracts,
  schemas, migrations, permissions, auth behavior, error contracts,
  observability behavior, dependency choices, or test strategy.
- Touch modules, files, domains, or workflows outside the issue's affected
  surface.
- Add a new architectural pattern, new dependency, new persistence behavior, or
  new cross-domain orchestration.
- Convert a scoped issue into a broader refactor or product decision.
- Require choosing between multiple plausible product or domain meanings.

Blocking discoveries require stopping until the plan is corrected. Stop when
the issue contradicts current code, depends on missing prerequisites, violates
repo doctrine, or cannot satisfy its acceptance criteria as written.

For material or blocking discoveries:

1. Stop implementation at the smallest coherent point.
2. Report the discovery, why it changes the plan, affected files/tests, and 1-2
   concrete options.
3. Recommend one option.
4. Wait for user direction before continuing.
5. After approval, update the canonical issue or plan when one exists before
   implementing the revised scope.

Do not continue implementing a revised plan from memory or implication alone.

## Trust Boundaries

All external input is untrusted.

- Validate HTTP requests, query params, route params, forms, storage reads, environment-derived config, and remote API responses at the boundary with Zod.
- Use `.safeParse()` at user/API boundaries; never pass raw request bodies or unvalidated remote data into services/domain logic.
- Return user-facing validation messages, not raw Zod issue objects.
- Put shared request/response/domain schemas in the repo's shared contract package. If API and domain shapes diverge, define explicit API and domain variants and map at the boundary.
- Schemas that validate persisted database row/document shapes should be named with `Db` so the persistence boundary is visible at call sites.
- Normalize canonical values at the boundary before persistence or identity-critical lookup.
- Validate returned data at the boundary before sending it out. Prefer one reusable schema-validation helper so output validation stays consistent.

## Architecture

Keep adapters thin and domain logic explicit.

- Routes/controllers/adapters handle auth checks, parsing, validation, mapping, and response presentation only.
- Domain services contain business rules and return `Result<T, E>` or `ResultAsync<T, E>`.
- Repositories own persistence access and validate database records at the persistence boundary.
- Prefer Drizzle as the default database query package for new work unless the repo already standardizes on another persistence stack.
- Cross-app or cross-feature logic belongs in shared packages/modules, not copied across apps.
- Do not read `process.env` or global config inside business logic. Inject configuration explicitly.
- Avoid hidden global state; prefer pure functions and explicit dependencies.
- Model mutually exclusive state as discriminated unions, not scattered booleans.
- For closed sets of values, prefer enums or const-backed literal unions over
  booleans and unconstrained strings. Use discriminated literal variants where
  exhaustive matching is required.
- Exhaustively handle discriminated unions. Adding a variant should break compilation until every branch is handled.
- Organize backend, frontend, and shared packages by domain ownership rather than broad technical buckets.
- Shared packages such as contracts should mirror domain ownership rather than becoming global dumping grounds.
- Default to kebab-case for non-component filenames. React component files should use the component name as the filename.

### Backend Module Structure

- For API backends, organize code by domain modules under
  `apps/api/src/modules`.
- Domain directory names should be plural and kebab-case where applicable.
- Do not default to top-level file-type buckets inside modules such as
  `routes`, `services`, or `repositories`. Prefer domain-local files such as:
  - `[domain].route.ts`
  - `[domain].service.ts`
  - `[domain].repository.ts`
  - `[domain].errors.ts`
- Normalize non-conforming filenames during structural refactors, such as
  snake_case to kebab-case.
- Keep access-control or identity modules focused on authentication,
  authorization, session, and identity-lifecycle concerns.
- Business-domain behavior should be owned by its business module even when
  exposed through access-control-adjacent routes or entrypoints.
- Cross-domain workflows belong in first-class process modules under
  `modules`; do not force cross-domain orchestration into a single business
  domain module.
- Allow helper files only when necessary. Cross-module helpers belong in shared
  `src/lib`, module-specific helpers may live in module-local `lib` folders,
  and tiny one-off helpers should stay in the owning domain file.
- Keep wiring in a dedicated bootstrap layer. `app.ts` stays thin and focused
  on server setup and module route registration.
- Register one route entrypoint per module in app wiring.
- Use strict constructor or factory dependency injection only.
- Build one typed `deps` object at startup and inject dependencies into module
  factories.
- Do not use Fastify decorate as a dependency container.
- Keep request-scoped context explicit in function parameters.
- Do not read global config or environment inside services or repositories.
- Instantiate repositories once with `db` at startup.
- Repositories should expose `withTransaction(tx)` to produce transaction-scoped
  instances with the same interface.
- Services own transaction boundaries.
- Keep cross-domain orchestration explicit and deterministic.

### Auth Boundaries

- Keep authentication and authorization at explicit, typed boundaries.
- Pass actor context explicitly into services; do not hide it in globals.
- Keep routes thin, enforce permissions on the backend, and map auth outcomes exhaustively.
- For framework-specific extraction/guard wiring, cookie policy, CSRF, and permission or policy registries, apply `$engineering-auth-security`.

## Errors and Results

- Treat expected application failures as values. Domain services and exported callable operations must not throw them.
- Expose the narrowest truthful closed error type from each producer: include every error variant it can emit and no variant it cannot emit.
- Apply producer-owned truthfulness recursively. Each error code owns its exact
  required or forbidden details shape, and every nested issue discriminant is
  coupled only to payloads a real producer branch can emit. Reject
  optional-property bags, Cartesian products, and nested combinations that no
  producer path can construct.
- Use named top-level error aliases in public operation, service, and repository signatures, not inline unions inside `Result`/`ResultAsync`.
- Reuse domain, infrastructure, authorization, and provider error variants as atomic types and factories. Compose them into a producer-specific error union at each operation boundary.
- Do not use a global, project-wide, or domain-wide umbrella error union as an operation's return type unless that operation can genuinely emit every variant in it.
- Expose a success-only contract or `Result<T, never>` when an operation cannot emit an application-level error under its declared boundary policy; do not invent defensive variants.
- Restrict `try/catch` to exported callable boundaries, adapters/clients, and explicit bridges to throwing libraries.
- Keep expected application failures as exact `Result` / `ResultAsync` values
  through internal helper graphs. Helper callers propagate those variants
  exhaustively; they do not throw a typed application error for an outer catch
  block to recover.
- Normalize unexpected execution failures observable by a boundary that promises a total `Result` contract into a sanitized, boundary-owned variant such as `internal_error`.
- Treat a typed application error that is thrown instead of returned as an
  unexpected transport defect and normalize it to the boundary-owned internal
  failure. Do not inspect caught values to restore an expected application
  code.
- Treat network, process, provider, SDK, and platform failures outside an operation's execution as failures introduced by the calling boundary, not emitted by the called operation.
- Widen an error union only with variants that the consuming boundary or adapter can itself introduce. Otherwise preserve the producer's exact error union.
- Test and map every error variant exhaustively at the consuming boundary. Adding a producer variant should break affected consumers until they handle it.
- Include actionable, sanitized `details` in HTTP/API error responses when the client can use them to understand or correct the failure.
- Keep reusable error atoms and factories with their owning domain or infrastructure module; shared ownership does not make an error variant part of every operation contract.
- Prefer discriminated `type` variants and small factory helpers; do not scatter inline `{ type: "..." as const }` shapes across the codebase.

## Testing Doctrine

TDD is mandatory by default.

Apply testing rules in this order: critical behavior correctness first, then failure and validation coverage, then naming and structural consistency.

- For new features and behavior changes: write the failing test first, implement the minimum to pass, then refactor with tests green.
- If TDD is intentionally skipped, state the concrete reason before implementation.
- When setting up repo automation, prefer commit-time hooks for fast checks and push-time hooks for broader suites, while keeping CI as the authoritative full-environment validation.
- Cover success paths, failure paths, validation failures, error variants, and exhaustive mapping.
- For API endpoints, test status codes, response payloads, and actionable error details.
- API integration tests are mandatory for endpoint changes. Exercise the real app wiring end-to-end through route, service, and persistence layers; mock only true external systems at the boundary.
- When work touches observability, resilience, auth/security, or frontend engineering/accessibility, apply the relevant companion skill and test those behaviors explicitly.
- Prefer test names that state given/when/then behavior. If the repo has a local test naming style, follow it.
- For debugging: first build a deterministic repro loop. Convert the minimized repro into a regression test before fixing when a valid seam exists.
- Test file naming should mirror production file naming, such as
  `[domain].route.test.ts`, `[domain].service.test.ts`, and
  `[domain].repository.test.ts`.
- Structural migrations must preserve behavior and prove that with tests.
- Keep test structure aligned with the real module structure.

### Database Migration Proof

- Every database schema or data migration requires a dedicated Migration Proof Harness: an isolated disposable local database container or containerized test service that cannot target a shared or production database.
- Keep the harness local-only. Use a distinct Compose project, Testcontainers instance, database name, network, credentials, and teardown scope; do not include the proof service in production deployment manifests.
- Build the before-state from the prior released schema and representative persisted fixtures. Include an empty database plus boundary and legacy rows that exercise changed constraints, nullability, defaults, relationships, and transformed values.
- Run the exact production migration mechanism and generated artifacts. Do not replace it with hand-written setup SQL or direct schema synchronization.
- Assert the after-state: schema objects, retained and transformed data, constraints and indexes, application reads and writes, and any documented invariants.
- Verify the migration tool's supported repeat invocation or no-op behavior. Prove rollback only when rollback is part of the deployment contract; otherwise document the forward-recovery path.
- Record the prior-state fixture identity, migration command, assertions, container isolation, and results in the canonical issue or pull request.
- Do not require a database container for structural code migrations that do not change persisted schema or data.

### Mutation Analysis

- Use mutation analysis as a post-green adversarial audit. Start with
  behavior-first tests, reach green, then strengthen observable-contract
  assertions against meaningful survivors and rerun both ordinary and mutation
  suites; do not write mutant-specific implementation checks.
- Require it when a plausible small mutation could cause unauthorized access;
  incorrect money, entitlement, grading, ranking, or scoring; an invalid state
  transition or invariant; persisted-data damage; consequential validation
  failure; an incorrect public contract or exhaustive outcome mapping; or a wrong
  reusable domain decision.
- Base the trigger on impact, not directory, test type, or line count. Simple
  wiring, generated code, framework adapters, presentation-only code, and
  non-behavioral transformations are normally out of scope. When triggered, run
  the affected scope or record why the technique is unsuitable.
- Gate on reviewed-survivor completeness and non-regression, not a universal
  score. Establish a non-blocking baseline, prevent unjustified regression, and
  tighten it as meaningful survivors are eliminated.
- Classify every survivor. Meaningful survivors and uncovered mutants block
  completion; equivalent or irrelevant survivors require recorded justification.
  Require every survivor to be addressed, not every mutant to be killed.
- Store run-specific classifications in the canonical issue, pull request, or CI
  artifact, not a permanent mutant allowlist. Permit only narrow, justified,
  version-controlled structural exclusions; never exclude code or operators to
  improve the score.
- Treat timeouts, runner errors, and tool failures as inconclusive and fail closed
  until resolved or an alternative assurance technique is approved. Invalid
  mutants proven by compilation or type checking are not test gaps.
- Keep mutation analysis out of Git hooks. Run targeted local or agent validation
  and pull-request CI when triggered, plus a periodic clean full run to refresh
  the baseline and catch incremental-analysis blind spots.
- Preserve an established tool. Otherwise prefer StrykerJS for TypeScript and
  JavaScript, and the ecosystem-appropriate engine for other languages. Adapt its
  runner, monorepo, performance, and reporting configuration to the repository.

## Backend Build Sequence

For endpoint or service work:

1. Plan/gap review.
2. Contracts: request, response, domain, and error schemas/types.
3. Boundary validation tests.
4. Service tests using `Result`/`ResultAsync` errors.
5. Repository/persistence tests when persistence behavior changes.
6. Implementation: repository, service, route/controller.
7. Exhaustive error mapping and response validation.
8. Mandatory integration tests for key behavior and failure cases.

- Contracts in `packages/contracts` should align with domain ownership.
- Prefer per-endpoint contract files within a domain folder plus domain-level
  exports.
- Keep existing endpoint paths stable during structural migrations.
- Keep behavior stable during structural migrations: no response, status-code,
  or message drift.
- For large structural refactors, prefer codemod-style file moves and import
  rewrites first, then targeted manual cleanup.

For Fastify projects:

- Declare request/response schemas in route metadata for OpenAPI when the project supports it.
- Define API request/response schemas from canonical Zod schemas plus a transformer built on `zod-to-json-schema`.
- Do not hand-author JSON Schema for API schemas except for unavoidable framework gaps.
- Use one dedicated response schema per status code.
- Validate response payloads before sending. Prefer one reusable helper that validates against the Zod schema and prevents invalid output from leaving the boundary.
- For Drizzle-backed database migrations, edit schema source files and run the generator. Never hand-edit generated migration files or generated migration metadata.
- For protected routes, use the shared auth extractor or guard wiring instead of duplicating token parsing in handlers.
- Keep actor context flow explicit from request boundary to service call; do not fetch auth context from hidden globals inside services.

## Frontend Build Sequence

For web or mobile features, apply `$engineering-frontend`.

## Review Checklist

Before declaring work complete, verify:

- No unvalidated external data reaches domain logic.
- No business logic lives in routes/controllers/pages/views.
- Expected failures use `Result`/`ResultAsync`, not thrown exceptions.
- Error unions and UI states are discriminated and exhaustively handled.
- Config is injected rather than read from globals inside business logic.
- Returned data is validated at the boundary before it is sent back out.
- Tests cover the new behavior, validation, and error variants.
- Relevant companion skills were applied when work touched observability, resilience, auth/security, or frontend engineering/accessibility.
- New shared contracts or logic live in shared packages/modules when used across boundaries.
- Imports follow the repository's established aliases instead of brittle deep
  relative paths where possible; for new conventions, use named `#...` aliases
  for package-private imports and `@scope/package` specifiers for package
  boundaries.
- Formatter and linter expectations are satisfied for the changed scope; ESLint owns code-quality rules and Prettier owns formatting.
- Hook-driven local validation matches repo policy for the changed scope: pre-commit runs staged format/lint plus unit tests, and pre-push runs unit, integration, and E2E tests when the repo defines those commands.
- Commit messages, branch names, and version bumps follow the repo's convention, or Conventional Commits, conventional branching, and SemVer when no local convention exists.
- Any skipped doctrine rule has a concrete, documented reason.
- Relevant unit and integration tests pass for the changed scope.
- Every database schema or data migration passed its isolated local Migration Proof Harness, or the work remains unverified.
- Any mid-implementation discoveries were classified; material or blocking
  discoveries were approved and reflected in the canonical issue or plan before
  continuing.
- No stale imports remain to old module paths or legacy naming.
- App wiring imports only module route entrypoints.
- Module files and directories follow the agreed naming conventions.
- Domain ownership stays consistent across backend, frontend, and shared
  packages for the changed scope.
- Protected-route behavior uses one auth extraction and guard pattern for the
  changed scope.
- Auth error mapping distinguishes 401 and 403 auth outcomes from
  service-unavailable infrastructure failures.

## Completion Evidence and Handoff

Before declaring work complete, record the smallest useful evidence packet:

- changed behavior, contracts, and affected surfaces
- user-owned decisions accepted during the work
- empirical investigations performed and their results
- exact validation commands and outcomes
- companion skills triggered and the checks they added
- deviations from plan or doctrine, with reasons
- residual risks, limitations, and anything still unverified
- canonical issue, plan, ADR, roadmap, or pull-request updates made or still required

Do not collapse an unverified item into a caveated success. Every triggered companion check must be verified, or its omission and alternative assurance must be recorded in this handoff.
