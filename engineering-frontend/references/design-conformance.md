# Design Conformance And Audit

Use this reference whenever an issue, pull request, implementation handoff, or
repository document identifies an authoritative design for a changed frontend
surface. It defines the issue-owned Design Conformance Pass and the independent
code-review Design Audit.

## Purpose And Authority

Verify both:

1. **Design fidelity**: the running implementation materially matches the
   approved visual and interaction target.
2. **Engineering integrity**: the result remains accessible, responsive,
   consistent with the design system, compatible with platform conventions,
   and complete across required operational states.

Do not turn this pass into subjective art direction or unsolicited redesign.
When an authoritative design conflicts with accepted behaviour, accessibility,
the design system, or platform conventions, surface the conflict for a decision
instead of silently choosing a side.

Use this authority order:

1. the exact design source and approved baseline recorded by the Design
   Reference Manifest;
2. frozen design images for human visual comparison;
3. the HTML/Tailwind rendition as a derived, non-authoritative visualization
   aid; and
4. the running implementation being evaluated.

An inspiration image, mood board, or casual visual example does not become an
authoritative design unless the issue explicitly adopts it as one.

## Issue-Ready Design Baseline

Before a design-backed issue becomes implementation-ready, retrieve the
relevant source and create a **Design Reference Manifest**. Do this at issue
readiness, when scope and accepted states are stable, rather than during early
idea capture or for the first time during code review.

The manifest must record:

- source type and canonical URL;
- Figma file key and exact page, frame, component, or node identifiers, or the
  equivalent identifiers for another design source;
- design version or branch identifier when available;
- source modification time and retrieval time;
- relevant states, component variants, and prototype interactions;
- target platforms, viewports, themes, locales, and material system settings;
- applicable design-system, component-library, variable, token, font, and asset
  references;
- every approved deviation;
- the Design Evidence Bundle path; and
- the source-signature method and value, including any known blind spot.

Prefer an exact source version or branch identifier. When the available tooling
exposes the relevant node data, create a deterministic signature over those
nodes while excluding fetch timestamps and other transient output. Record the
tool and algorithm used. Do not store a whole raw design document merely to
obtain a signature.

When exact relevant-node signatures are unavailable, preserve the strongest
stable version or approved snapshot the source supports and name the drift-
detection limitation. A file-wide modification timestamp may trigger focused
inspection, but it does not by itself prove that an issue-owned node changed.

If the source, approved version, or required nodes cannot be accessed, the issue
is not implementation-ready. Do not replace them with memory or an unapproved
screenshot.

## Design Evidence Bundle

Preserve the repository's existing evidence convention. When none exists, use:

```text
<canonical issue directory>/evidence/<canonical issue stem>/design/
```

For `docs/issues/28-feat-checkout-recovery.md`, the fallback is:

```text
docs/issues/evidence/28-feat-checkout-recovery/design/
```

The full canonical issue stem, not only its number, identifies the evidence.
Do not confuse a repository-local issue number with a GitHub issue number.

The bundle must contain or durably reference:

```text
design/
|-- manifest.md
|-- source-signature.json
|-- images/
|   |-- <state>--<platform>-<viewport>.png
|   `-- ...
|-- reference-web/
|   |-- README.md
|   |-- index.html
|   `-- assets/
`-- platform-notes/
    `-- <platform>.md
```

Follow a repository's artifact registry, Git LFS, or external evidence policy
when it prohibits ordinary tracked binaries. The issue-facing evidence path must
still contain durable references that another reviewer can resolve. An external
artifact convention does not itself authorize an upload; use only the writes
authorized by the issue-creation workflow and stop when additional authority is
required.

### Frozen images

Preserve one image for every issue-owned designed state and viewport in the
Design Audit Matrix. Images are the stable human-readable baseline. Record the
source node, viewport, export time, and file hash for each image.

### HTML/Tailwind rendition

Preserve a self-contained HTML/Tailwind rendition for every design-backed
frontend issue, including native targets. It is a portable visual model that a
reviewer can open and resize; it is not proof of native platform behaviour.

- Make the rendition usable locally without an unrecorded network dependency.
- Bundle required assets and compiled styling under `reference-web/`, subject
  to repository licensing and storage rules.
- Represent every issue-owned matrix state and declared viewport through
  separate pages, sections, or deterministic reference-only controls so a
  reviewer can reach each target without editing the export.
- Record the exporting tool, version, command or procedure, target viewport,
  fonts, and known limitations.
- Compare the rendition with the pinned design and frozen images before
  accepting it as evidence.
- Label generated markup as non-authoritative. Do not copy its component
  boundaries, semantics, absolute positioning, or accessibility behaviour into
  production merely because the exporter generated them.

When the rendition disagrees with the pinned source, classify it as
`REFERENCE_EXPORT_DEFECT` and regenerate it. Never change production code to
match a defective reference export.

### Platform-specific supplements

Add the smallest supplement needed for behaviour the HTML rendition cannot
faithfully communicate, such as native safe areas, system keyboard interaction,
haptics, platform navigation, system components, dynamic type, gestures, or
assistive-technology behaviour. Use a Figma prototype, interaction recording,
platform capture, or concise platform note as appropriate.

Never include secrets, personal data, private test content, or authentication
material in the evidence bundle.

## Design Audit Matrix

Create a matrix before implementation and keep it current when the approved
issue changes:

| Reference | Runtime state | Platform | Viewport | Interaction | Evidence | Result |
|---|---|---|---|---|---|---|
| `<node + artifact>` | `<state>` | `<platform>` | `<width x height>` | `<action>` | `<links>` | `<outcome>` |

Coverage must include:

- every state, viewport, platform, component variant, and interaction accepted
  by the issue;
- loading, empty, error, permission, recovery, focus, keyboard, and
  accessibility states when they are reachable, even when the design is silent;
- every relevant variant of a changed component;
- affected consumers when shared components, tokens, variables, assets, fonts,
  or layout primitives change; and
- the integration seams where the changed surface meets surrounding UI.

Do not audit unrelated screens merely because they live in the same design
file. For a local component change, keep the matrix local. For a shared design-
system or global-token change, expand to every materially affected variant and
representative high-risk consumer, plus broader visual-regression evidence when
the impact cannot be bounded safely.

Every row ends as `CONFORMANT`, `APPROVED_DEVIATION`,
`IMPLEMENTATION_MISMATCH`, `DESIGN_DRIFT`, `DESIGN_CONFLICT`, `EVIDENCE_GAP`,
`REFERENCE_EXPORT_DEFECT`, or `NOT_APPLICABLE`. A `NOT_APPLICABLE` row requires
a concrete reason.

## Execute The Comparison

For every applicable matrix row:

1. **Inspect the source.** Confirm the exact node, state, variant, viewport, and
   source signature. Inspect relevant layout constraints, spacing, typography,
   colour, assets, hierarchy, component properties, responsive rules, and
   prototype behaviour.
2. **Prepare the runtime.** Run the exact reviewed revision and reach the
   equivalent state through the real interface. Use controlled content and the
   declared viewport, platform, theme, locale, and system settings. For web,
   record unexpected console errors and failed network requests. Use only the
   issue's authorized seeded or disposable non-production state; independent
   review does not authorize account creation or mutation of shared systems.
3. **Compare renderings.** Capture the implementation and compare it side by
   side with the frozen image. Use an overlay or image diff when geometry,
   spacing, alignment, sizing, typography, cropping, responsive behaviour, or a
   disputed difference makes it useful. Do not demand universal pixel equality
   across rendering engines.
4. **Exercise behaviour.** Check each applicable hover, focus, pressed,
   disabled, loading, transition, scrolling, resizing, gesture, and navigation
   behaviour. Verify keyboard and assistive-technology behaviour separately;
   an image cannot prove them.
5. **Record the result.** Link the design reference, runtime capture, exact
   revision and environment, comparison method, measurements when material,
   and the typed outcome. Explain accepted rendering variance instead of
   silently ignoring it.

Existing snapshot or visual-regression tests support this work but do not
replace it. Those tests usually prove that the implementation did not change;
this pass proves that it matches the approved design.

## Detect Design Drift

Before judging implementation fidelity, retrieve the current authoritative
source again and compare its exact version or relevant-node signature with the
issue baseline.

- An unchanged signature permits comparison against the approved baseline.
- A changed file with unchanged relevant nodes is not issue-relevant drift;
  record the inspection that established that conclusion.
- A changed relevant node is `DESIGN_DRIFT`. Determine whether the approved
  issue baseline remains authoritative or whether the issue must adopt the new
  design before judging the code.
- Do not overwrite the approved baseline merely to match the latest source.
  Adoption of a changed target requires an approved issue revision and a
  regenerated evidence bundle.
- If the source is inaccessible, record `EVIDENCE_GAP`. Frozen artifacts still
  explain the approved target, but they cannot prove that the source did not
  move.

## Classify And Route Results

| Outcome | Meaning | Treatment |
|---|---|---|
| `CONFORMANT` | The implementation materially matches the approved design | Pass |
| `APPROVED_DEVIATION` | The difference has a linked prior approval | Pass with decision reference |
| `IMPLEMENTATION_MISMATCH` | The reviewed implementation diverges from the approved design | Code-review candidate |
| `DESIGN_DRIFT` | The current source differs from the approved issue baseline | Resolve design authority before judging code |
| `DESIGN_CONFLICT` | The design conflicts with accepted behaviour, accessibility, the design system, or platform conventions | User decision unless an objective governing rule resolves it |
| `EVIDENCE_GAP` | Required source, state, viewport, runtime, or evidence is missing, inaccessible, or stale | Leave conformance unverified |
| `REFERENCE_EXPORT_DEFECT` | A frozen image or HTML/Tailwind rendition does not faithfully represent the pinned source | Regenerate evidence; do not change production code |
| `NOT_APPLICABLE` | The declared row does not apply for a concrete reason | Record the reason |

An `IMPLEMENTATION_MISMATCH` becomes a normal finding only after the code-review
verification pipeline confirms that it is reachable and material. A mismatch is
material when it changes one or more of:

- visual hierarchy or user comprehension;
- task completion or interaction behaviour;
- accessibility;
- supported responsive behaviour;
- a design-system component, variable, or token contract;
- content visibility, clipping, overflow, ordering, or usable target size;
- the identity, meaning, or recognizable treatment of an approved asset; or
- a repeated pattern whose individual error becomes significant across the UI.

Subpixel anti-aliasing, platform font rasterization, minor colour interpolation,
and rendering-engine differences are normally immaterial when they do not alter
the accepted design.

In Composing Delivery Mode, map outcomes into the existing routing contract:

- confirmed implementation mismatch -> `AUTO_CORRECT`, `USER_DECISION`, or
  another already-authorized finding route;
- design drift or unresolved design conflict -> `USER_DECISION`;
- required missing or stale evidence -> `BLOCKED` or the workflow's named
  verification gap; and
- an accepted non-blocking limitation -> `RESIDUAL_RISK`.

## Independent Code-Review Audit

When this reference is triggered, `$code-review` must create a named design-
audit specialist packet and use at least Medium review effort for the affected
surface. The reviewer must inspect the source and running implementation;
implementer summaries and screenshots alone are insufficient.

- Complete and classify the whole Design Audit Matrix before finalizing the
  finding queue.
- Give every candidate the normal independent skeptical verification.
- At High or Max effort, have an independent verifier repeat the complete
  matrix, including rows the first pass marked conformant, rather than checking
  only suspected mismatches.
- Treat broad shared-component, design-system, global-token, or responsive-rule
  changes as High design-audit risk unless repository evidence safely bounds
  their impact.
- Record the source and baseline, drift result, evidence-bundle path, matrix
  coverage, comparison methods, verifier coverage, typed outcomes, approved
  deviations, and anything unverified in the review closeout.

Keep all reviewer contexts read-only. The delivery workflow owns authorized
evidence regeneration and production corrections, followed by invalidated-proof
re-runs and clean independent re-review.

## Staleness

Tie runtime comparisons to the exact revision and environment. Re-run affected
rows after a production, dependency, runtime-configuration, test-data, design-
system, asset, font, evidence-reference, or approved-design change invalidates
them. Documentation-only changes do not invalidate unrelated comparison rows.

Keep the issue `Needs Verification` while any required source check, evidence
artifact, matrix row, or independent audit is missing, failed, inaccessible, or
stale.
