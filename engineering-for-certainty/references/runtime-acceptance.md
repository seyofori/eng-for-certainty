# Runtime Acceptance Pass

Use this reference for every issue that changes observable runtime behaviour.
The pass complements automated tests by exercising the assembled running system
through the boundary a real caller uses.

## Applicability And Sequence

- Require the pass for APIs, user interfaces, commands, workers, scheduled
  jobs, webhooks, migrations with runtime effects, and external integrations.
- A documentation-only or other non-runtime issue may record `Not applicable`
  with a concrete reason.
- Run the applicable automated validation first, then the local Runtime
  Acceptance Pass against the final combined candidate.
- Repeat the pass against a preview or staging environment when it safely runs
  the exact candidate before merge and deployment is already automated or
  separately authorized.
- When staging receives only merged changes, record the staging pass as a
  mandatory downstream release gate with its owner and trigger. Do not imply
  that issue delivery authorizes deployment.

## Scenario Contract

The issue's Runtime Acceptance Plan must include:

- every accepted externally observable acceptance criterion;
- every materially distinct accepted outcome, such as success, validation
  rejection, expected failure, authorization denial, empty state, and retry;
- one complete primary user or system journey; and
- a targeted exploratory check of the changed area and its integration seams.

Automated tests retain exhaustive value combinations and low-level boundary
coverage. Do not manually repeat a large parameterized test matrix.

## Real Boundary

- **API:** start the real application and send network requests through the
  public endpoint. Exercise routing, middleware, validation, authentication,
  serialization, and local persistence where applicable.
- **Web UI:** use browser control to navigate the running application. Check
  visible states and interactions, browser-console errors, and failed network
  requests.
- **Native or operating-system-dependent UI:** use computer, emulator, or
  device control appropriate to the supported platform.
- **Commands, jobs, webhooks, and scheduled work:** exercise the literal
  operational trigger when it is safely available.
- **External integrations:** use a provider sandbox or other real test boundary
  when available.

An in-process handler call, isolated component render, or mock adapter by itself
is not a Runtime Acceptance Pass. A running frontend exercised through a mock
adapter may prove an explicitly frontend-only slice when the adapter is named as
a proxy; it cannot prove backend integration. When the literal mechanism cannot
run safely, name the proxy, what it proves, its blind spot, and the later gate
that must exercise the real mechanism when the blind spot is material.

Apply `$engineering-auth-security` when authentication or disposable identities
are involved. Apply `$engineering-frontend` for UI execution and design
comparison. Apply `$engineering-observability` whenever a test message sink or
telemetry boundary is involved.

## Evidence Ledger

Record a concise evidence index rather than raw dumps:

| Scenario | Criteria | Revision and environment | Action | Expected and observed outcome | Result |
|---|---|---|---|---|---|
| `<stable ID>` | `<criterion IDs>` | `<commit/build + local/preview/staging>` | `<request or user actions>` | `<sanitized outcome>` | `Pass` or `Fail` |

Add only the applicable supporting facts:

- preconditions and safe test-data setup;
- exact API method, path, sanitized request, status, and material response fields;
- UI viewport, platform, navigation steps, console and network result, and
  screenshots when visual proof matters;
- Test Identity Plan, Design Conformance Pass, or proxy reference;
- cleanup performed; and
- unexpected observations, including non-blocking observations.

Never record passwords, OTPs, magic links, access or mailbox tokens, sessions,
cookies, private payloads, personal data, or unredacted logs.

## Staleness And Status

Tie every pass to the exact local commit or deployed build. A later production,
dependency, runtime-configuration, deployment, or test-data change invalidates
each scenario it could affect. Re-run those scenarios against the new head.
Documentation-only changes do not invalidate unrelated runtime evidence.

Keep the issue `Needs Verification` while required issue-owned runtime evidence
is missing, failed, or stale. A post-merge staging or other downstream release
gate may remain open only when the issue links it and names its owner and
trigger; that gate blocks the release rather than disguising missing issue-owned
proof.
