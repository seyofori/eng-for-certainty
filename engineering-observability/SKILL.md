---
name: engineering-observability
description: Explicit observability doctrine for backend and frontend operational telemetry, including safe structured logging, OpenTelemetry tracing, W3C context propagation, metrics, redaction, correlation, client ingestion, test-message separation, capacity protection, retention, and sink failure. Use when work touches logs, metrics, traces, spans, telemetry, alerts, audits, request IDs, frontend logging and tracing, or secret-bearing test message sinks.
---

# Engineering Observability

Use this companion skill with `$engineering-for-certainty` when work touches operational telemetry. Apply the doctrine in order: centralized configuration, signal-specific contracts, tracing and correlation, client ingestion, delivery and capacity, then verification.

## Scope

Apply this skill to:

- centralized logger setup or configuration
- backend or frontend logs, metrics, traces, alerts, and audit records
- trace instrumentation, span attributes, context propagation, sampling, or export
- redaction, sanitization, retention, and log access
- correlation or request ID propagation
- database, provider, SDK, queue, job, or infrastructure failure logging
- frontend operational event or span emission and client-to-backend ingestion
- separation of authentication Test Message Sinks from logs and telemetry

## Centralized Configuration And Signal Boundaries

- Configure resource metadata, privacy policy, exporters, sampling, and health centrally while preserving signal-specific contracts.
- Send logs and audits through typed event schemas, metrics through named instruments with bounded attributes, and traces through an approved tracer provider, instrumentation, processors, and exporters.
- Inject the logger, tracer, meter, or a narrow signal-specific facade into services and repositories; do not import hidden telemetry singletons into domain code.
- When a bespoke logger, tracer, or facade object is hand-built for a script, CLI, or test harness instead of using the shared implementation, verify it forwards every parameter its declared interface accepts. A structurally-typed shim can satisfy an interface while silently discarding an argument, dropping all structured context from every call site that relies on it.
- Accept typed Safe Log Events, not arbitrary context bags, raw request objects, raw errors, or spread objects. Give metrics and manual spans explicit allowlisted attribute contracts.
- Define a closed schema per event family. Reject unknown keys and bound every accepted string, array, nesting level, and payload size before forwarding.
- Keep logging out of business transactions and critical request completion. Logging failure must not change a successful business outcome unless the event is an explicitly authoritative audit requirement.
- When `NODE_ENV` is `production`, missing required configuration for the repository's selected logging sink or required tracing exporter is a startup error. When the repository selects Better Stack, this includes its required Better Stack configuration. In `development`, default to console logging and a local or no-op tracing exporter when remote sinks are absent. Treat an unset `NODE_ENV` as development only in a verified local development environment.
- If the primary sink fails, use a bounded sanitized stderr or console fallback and surface safe aggregate health or alert signals when the repo supports them. Never recursively log a logger failure through the failing path.

## Safe Log Event Contract

Prefer controlled fields such as:

```text
event, level, service, component, operation, stage, outcome,
errorClass, errorCode, correlationId, requestId, durationMs,
retryCount, environment, deploymentVersion
```

- Use stable event and error codes instead of free-form explanations.
- Never log secrets, API keys, tokens, cookies, authorization headers, session identifiers, connection strings, encryption material, or raw request and response bodies.
- Never log direct personal data, user-entered values, form fields, file contents, URLs with query strings, console arguments, storage contents, or arbitrary third-party payloads.
- Do not persist raw `Error.message`, `stack`, `cause`, Zod issues, provider errors, or serialized error objects. Diagnose with controlled operation, stage, class, code, correlation, and deployment metadata.
- Normalize error class through an allowlist. Do not trust an arbitrary constructor name or driver property.
- Allow an approved opaque actor or record identifier only when the event cannot meet its operational or audit purpose without it. Prefer keyed pseudonymization when joinability is needed without direct identity, and document retention and access implications.
- Sanitize CR, LF, delimiters, and other log-injection characters in every remaining string field before encoding.
- Treat redaction as defense in depth, not as permission to accept arbitrary input.

### Test Message Sinks Are Not Telemetry

An authentication Test Message Sink may expose an OTP, magic link, or test
message through a mail-catcher UI or API, provider test tool, or isolated
terminal output only under `$engineering-auth-security`'s non-production rules.
It is a delivery adapter, not an operational log or Safe Log Event.

- Keep its secret-bearing output out of logger, audit, metrics, tracing, error
  reporting, CI-log collection, and telemetry-export pipelines.
- Do not solve test-message retrieval by weakening redaction or allowing tokens,
  message bodies, mailbox credentials, sessions, or direct identity into a log
  schema.
- Disable or reject the sink in production and keep access and retention bounded
  in non-production environments.
- Runtime Acceptance evidence may record that the message was retrieved and
  accepted; it must not reproduce the secret or message body.

## Source-Specific Failure Adapters

Create small allowlist-based adapters for database, HTTP/provider, queue, job, validation, and platform failures. Unknown fields never pass through automatically.

For a recognized PostgreSQL driver error, allow only bounded normalized fields when present:

```text
db.sqlstate
db.schema
db.table
db.column
db.constraint
```

- Read `code`, `schema`, `table`, `column`, and `constraint` from machine-readable driver properties and normalize their log field names.
- Never parse error prose to recover metadata.
- Never log `message`, `stack`, `detail`, `hint`, query text, parameters, values, internal query or context, positions, or unknown driver properties.
- Omit an identifier when the application permits database object names derived from tenant or user data.
- Keep missing metadata normal; do not widen the allowlist to fill a diagnostic gap.

Output-validation failures and unexpected infrastructure failures must still produce a Safe Log Event through their owning source adapter.

## Tracing Activation And Instrumentation

Require tracing when the work explicitly changes tracing; crosses services, processes, queues, webhooks, or other asynchronous boundaries; changes a critical multi-dependency path that logs and metrics cannot diagnose; or extends a path already traced by the repo. Do not add tracing to simple local work without one of these triggers.

- Preserve a compatible existing tracing stack. Otherwise use OpenTelemetry with W3C Trace Context.
- Prefer maintained framework and library instrumentation for supported HTTP, RPC, database, provider, and messaging boundaries. Add targeted manual spans for significant domain operations, unsupported integrations, or missing causal links; do not create one span per function.
- Create inbound server or consumer spans, outbound client or producer spans, and inject or extract context at every supported causal boundary on the affected path.
- Use parent context for direct causal continuation, links for fan-out, fan-in, batch, or otherwise non-parental relationships, and a root span for cron or background work with no valid upstream context.
- Let `$engineering-frontend` own where significant client spans begin. Activate browser or mobile tracing only for critical journeys that meet the same risk trigger.

## Trace Context And Span Contract

- Follow the applicable OpenTelemetry semantic convention for span names, kinds, attributes, and error status before defining custom fields.
- Keep span names stable and low-cardinality. Put approved execution identifiers in bounded attributes, never in span names, and namespace application-specific attributes.
- Review every automatic instrumentation's emitted attributes and capture settings. Allowlist or sanitize them before export; disable HTTP headers, query strings and userinfo, database statements and parameters, messaging payloads, RPC bodies, and provider request or response capture unless a narrower safe contract explicitly permits a field.
- Mark a span as `Error` with a predictable low-cardinality `error.type` when the instrumented operation throws, returns an error result, or otherwise fails its declared contract.
- Record a sanitized propagated exception at one owning boundary when it materially helps diagnosis. Never export raw messages, stacks, causes, bodies, query text, parameters, or third-party payloads.
- Represent handled expected domain outcomes with a bounded outcome attribute rather than error status. Leave successful status unset unless the applicable convention requires `Ok`.
- Treat incoming baggage as untrusted. Propagate only approved bounded keys, never put secrets or direct identity in baggage, and never use baggage for authentication or authorization.

## Correlation And Ownership

- Give each identifier one lifecycle: a request ID identifies one inbound request when the API convention needs it; trace and span IDs identify the connected execution and current operation; a separate correlation ID exists only for a business workflow spanning multiple traces or when an external contract requires it.
- Add active trace and span IDs to structured logs. Propagate trace context through causally connected boundaries; do not manufacture request IDs for queues, cron, or jobs.
- Derive actor, tenant, environment, and deployment context from trusted server state. Never trust a frontend payload to assert identity or authority.
- Generate authoritative security and audit events on the backend. Frontend events are advisory operational telemetry only.

## Client Telemetry Ingestion

- Send frontend operational events and trace payloads through dedicated controlled backend endpoints. Never export client telemetry directly to Better Stack, an OpenTelemetry Collector, or another third-party sink.
- Treat each endpoint as an untrusted public telemetry boundary. Use a strict discriminated union for operational events and a closed, bounded client trace contract. Permit OTLP only when the receiver validates and reduces it to that restricted contract before export.
- Reject unknown fields, wrong content types, unsupported versions, oversized bodies or batches, excessive span or event counts, excessive attributes or links, excessive string lengths, and invalid or implausible timestamps.
- Allowlist accepted span names, kinds, resource fields, attributes, events, links, and baggage-derived fields. Reject or discard client-owned identity, tenant, environment, deployment, service authority, and exporter-routing metadata.
- Authenticate ingestion by default. If pre-auth telemetry is required, expose a reduced anonymous event set with lower limits and no client-provided identity.
- Apply rate limits and abuse protection before expensive decoding or forwarding when the framework permits. Do not persist or log raw rate-limit keys such as IP addresses.
- Attach server-owned ingestion time, request ID, verified actor or tenant context, environment, and deployment metadata after validation.
- Reject raw messages, stacks, breadcrumbs, console arguments, storage values, request bodies, form values, and URLs with query strings.
- Return a generic response. Invalid telemetry must not disclose validation internals or create another unsafe log containing the rejected payload.
- Prevent recursive ingestion: client or endpoint logging failures must not emit another frontend event through the same path.

## Sampling And Export

- Configure sampling explicitly by environment. Development and tests may sample every trace at bounded volume; production must declare and justify its root policy from measured traffic, cost, and diagnostic needs.
- Use parent-based sampling so child spans respect the upstream decision. Use Collector-side tail sampling for slow or failed traces only when the deployment supports it and the policy is documented.
- Keep all client exporter credentials and routing on the controlled backend. Preserve a compatible server-owned exporter; otherwise prefer OTLP through an OpenTelemetry Collector, while allowing direct backend-to-provider export when the same safety and capacity rules hold.
- Treat missing or structurally invalid required production tracing configuration as a startup failure. After successful startup, tracing and exporter failures must fail open and never fail a business operation.
- Export asynchronously through bounded queues and batches with explicit timeouts. Bound flush and shutdown; drop spans after capacity is exhausted and surface only safe aggregate failure and drop signals.

## Delivery, Capacity, And Retention

- Treat telemetry as best-effort except when an audit event is explicitly authoritative. Use bounded in-memory or dedicated telemetry queues, batch delivery, explicit timeouts, and a circuit breaker around external sinks.
- Apply `$engineering-resilience` to queues, retries, timeouts, backoff, circuit breaking, concurrency, and sink outages.
- Return after bounded validation and enqueue rather than waiting synchronously for the external sink. Do not write frontend telemetry through the application database or a business transaction.
- Define one observability budget with signal-specific limits. For traces include root sampling, sampled throughput, span attributes/events/links/baggage, attribute cardinality and length, queue and batch capacity, flush interval, export timeout, provider-outage duration, and dropped-span behavior.
- For frontend events include peak clients, maximum events and bytes per client, sustained and burst rate, queue capacity, and provider-outage duration.
- Under pressure, drop or sample low-priority telemetry, increment safe aggregate drop metrics, and preserve business traffic. Never create an unbounded queue or retry storm.
- Restrict access to persisted telemetry by least privilege, audit access when required, and define per-signal retention and deletion from operational, contractual, regional, and privacy needs. Do not keep telemetry indefinitely by default.

## Testing And Review

- Test production versus development startup behavior for every required configured sink where a valid seam exists.
- Test event schemas, unknown-key rejection, length and size limits, log-injection sanitization, and correlation propagation.
- Seed sensitive values into raw errors, requests, database parameters, provider responses, and frontend payloads; prove that none reaches logs, metrics, traces, audits, or fallbacks.
- Test every source adapter's allowed and forbidden fields, including missing database metadata.
- When a Test Message Sink exists, prove it is unavailable in production and
  cannot forward secret-bearing content into logs, audits, metrics, traces,
  errors, CI artifacts, fallbacks, or exporters.
- Test frontend event and trace ingestion authentication, reduced anonymous contracts when present, forged identity and authority, PII smuggling, schema/version rules, span/event/attribute/link limits, batch and body limits, rate limits, and generic failures.
- Exercise the real changed HTTP, queue, job, webhook, or client-to-backend boundary where a valid integration environment exists. Prove W3C context injection/extraction, correct parent or link relationships, root-span creation, semantic names and kinds, bounded attributes, and the final exported trace.
- Test error and expected-outcome semantics, parent-based sampling, unsampled application correctness, and that client trace export reaches only the controlled backend.
- Load-test sustained traffic, bursts, malicious clients, repeated frontend-error loops, a full queue, sink timeouts, and provider outages against the Telemetry Budget.
- Test logger and trace-exporter failure, timeout, saturation, dropping, fallback, and bounded shutdown without recursion, unbounded memory growth, or business-operation failure.
- Verify per-signal retention, reader access, and authoritative audit ownership when the change affects them.
- Before completion, verify every triggered check or record its omission and alternative assurance in the `$engineering-for-certainty` handoff.
