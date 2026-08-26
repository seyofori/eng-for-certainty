---
name: engineering-auth-security
description: Explicit auth and session security doctrine. Use when work touches cookies, tokens, sessions, CSRF, authentication boundaries, test identities or message sinks, actor context, protected routes, authorization, roles, permissions, or policy enforcement.
---

# Engineering Auth Security

Use this companion skill with `$engineering-for-certainty` when work touches authentication, authorization, token or session handling, cookie policy, or permission boundaries.

## Scope

Apply this skill when the work includes:

- login, logout, refresh, or session lifecycle changes
- cookie or token storage decisions
- CSRF handling
- permission checks or authorization rules
- roles, permissions, or policy registry changes
- auth middleware, guards, or backend auth boundaries
- runtime acceptance of signup, sign-in, verification, recovery, role, or
  session flows

## Doctrine

### Session and Token Handling

- Prefer `httpOnly` secure cookies unless all of the following are true: the requirement is recorded in repo docs or an ADR, the feature needs direct client-side cookie reads, and a server-managed session alternative was evaluated and rejected for a documented technical reason.
- Never store long-lived secrets or refresh tokens in insecure client storage.
- Make CSRF protection explicit when cookie-based auth is used.
- Session validation and refresh boundaries must be explicit and testable.
- Treat auth/session configuration as injected infrastructure, not hidden global state.

### Authorization Model

Apply this subsection in order: shared permission source first, registry shape second, role modeling third, and boundary enforcement throughout.

- Keep auth and permission checks explicit in both backend and UI. UI checks never replace backend authorization.
- Use shared permission constants or shared auth contracts where available; do not hard-code authorization literals when a shared package exists.
- Model authorization with granular string permission keys rather than coarse
  role-name checks or boolean capability flags.
- Define permission keys in a shared package so backend, frontend, and shared
  contracts can depend on the same source of truth.
- In repos with a dedicated permissions package, define permissions as a nested
  resource/action registry in the shape
  `{ resource: { action: "resource.action" } }`.
- Keep the top-level resource key, nested action key, and string literal
  aligned so the registry stays self-describing and easy to audit. Prefer
  `role: { create: "role.create" }`, not flattened maps, renamed aliases, or
  values that drift from the canonical permission name.
- Export shared permission registries as `as const` so callers get stable
  literal types from the same source of truth.
- Example:
  ```ts
  export const PERMISSIONS = {
    role: { create: "role.create", update: "role.update" },
    user: { create: "user.create" },
  } as const;
  ```
- Prefer seeded system roles plus explicit custom roles when the product needs
  role management. Seeded roles should stay system-managed and immutable unless
  the repo intentionally models a controlled cloning or migration flow.
- Keep role provenance and scope explicit in the model. Do not infer protected
  or platform-level roles from naming conventions alone.

### Policy and Backend Boundaries

- For Fastify backends, centralize bearer-token or session-cookie extraction in one boundary helper that resolves input into typed actor context.
- Expose extraction through app or plugin wiring and use one guard pattern for protected routes. Keep route handlers thin: call the extractor or guard, map boundary failures, and pass actor context explicitly into services.
- Keep authorization policy registries endpoint-keyed using method-and-path strings and a flat shape when the repo uses route-owned policies. Keep policy resolution deterministic and testable.
- Centralize policy definitions in a server-side registry referenced by routes
  and services.
- Policy definitions should support `all_of` and `any_of` semantics when the
  authorization model needs composition.
- Clients should not send authorization policy definitions in normal business
  requests; the server resolves required policy from trusted route or service
  wiring.
- Unknown policy keys, invalid policy configuration, and unknown role or
  permission keys should fail fast as configuration defects rather than falling
  through to implicit allow or deny behavior.
- Authorization failures should return structured, stable error codes that can
  be mapped exhaustively at the boundary.
- Authentication and authorization error variants may be shared as reusable atoms, but each protected operation must compose only the variants its own guard or policy evaluation can emit.
- Public or otherwise unprotected operations must not include authentication or authorization variants merely because those variants exist in a shared package.
- A route, guard, or adapter that introduces authentication or authorization checks may widen the underlying operation's error union with the exact failures introduced by those checks.
- In Fastify or similar backends, perform auth checks before parsing request bodies when parsing errors could leak information.

## Runtime Authentication Acceptance

For an authentication Runtime Acceptance Pass, follow `$engineering-for-certainty`'s
[Runtime Acceptance Pass](../engineering-for-certainty/references/runtime-acceptance.md)
and require an issue-specific Test Identity Plan.

The plan must name the environment, disposable identities and roles, how inbox
or message access is obtained, approved secret delivery, reset and session
revocation, test-data isolation, cleanup, and any CAPTCHA, SMS, passkey,
physical-device, or human-approval boundary.

Use this hierarchy without weakening the real application flow:

- Locally, prefer seeded disposable users for existing-account scenarios and a
  Test Message Sink or provider test mode for signup, verification, invitation,
  and recovery flows.
- On preview or staging, prefer a team-controlled test inbox for repeatability.
  When one is unavailable, a private agent-created disposable inbox is a valid
  standard method when the application and inbox provider permit automated test
  use, the mailbox is private or protected by an unguessable access token, and
  only synthetic staging data is used.
- A real message delivered from staging to an external disposable inbox proves
  the tested build's delivery path to that recipient provider. Do not widen that
  claim to every provider, domain, spam-placement policy, or production setup.
- Never use a publicly readable temporary inbox for OTPs, magic links, password
  resets, sessions, or other bearer secrets.
- Creating a disposable identity or inbox is authorized only when the issue's
  Runtime Acceptance Plan names that operation and environment. It does not
  authorize unrelated third-party accounts or production customer data.

A Test Message Sink is a non-production delivery adapter, not an operational
log:

- Keep it separate from the logger, audit, metrics, tracing, error reporting,
  and telemetry-export pipelines.
- Disable or reject it in production. Limit it to disposable test identities,
  authorized test-environment readers, and short bounded retention.
- It may expose a message through a mail-catcher UI or API, provider test tool,
  or isolated terminal output. Do not send its secret-bearing content through
  normal application logs merely because the environment is non-production.
- A staging Test Message Sink proves application-side message generation and
  routing, not delivery through the real provider and public mail network.
- Use OTPs, magic links, mailbox credentials, and sessions only to complete the
  scenario. Never copy them into issues, pull requests, screenshots, logs, or
  completion evidence.

Treat CAPTCHA, SMS, passkeys, hardware keys, physical-device requirements, and
manual account approval as explicit blocked or downstream gates unless the
repository already provides a safe non-production mechanism. Never add a
production auth bypass for acceptance testing.

## Testing and Review

- Test login, logout, refresh, and expired-session behavior when touched.
- Test backend permission enforcement, not only UI gating.
- Test seeded-role protection, custom-role behavior, and shared permission-key
  and registry-shape validation when role management or permission-registry
  changes.
- Test policy-registry resolution and both `all_of` and `any_of` policy
  evaluation when authorization rules change.
- Test CSRF behavior whenever cookie-based auth changes.
- Verify the Runtime Acceptance evidence exercises the real auth boundary with
  the approved Test Identity Plan, revokes or cleans up disposable state where
  supported, and records no authentication secret.
- Test that every Test Message Sink is unavailable in production and cannot send
  OTPs, magic links, mailbox tokens, sessions, or message bodies into
  operational telemetry or ordinary logs.
- Verify secure storage and transport assumptions match the selected architecture.
- Verify that auth and permission variants appear only on operations whose declared boundary can emit them.
- Before completion, verify every triggered check or record its omission and alternative assurance in the `$engineering-for-certainty` handoff.
