# eng-for-certainty

This repository is automatically synced from [seyofori/skills](https://github.com/seyofori/skills) at source commit `65578413b65eaae7023ac6da238a8ed77208af77`.

Do not edit this repository directly. Make changes in `seyofori/skills` and let the sync workflow publish them here.

See [CONTEXT.md](CONTEXT.md) for the shared workflow vocabulary and ownership boundaries.

## Included skills

- `engineering-for-certainty`
- `engineering-observability`
- `engineering-resilience`
- `engineering-auth-security`
- `engineering-frontend`
- `code-review`
- `pull-request-review`
- `pull-request-creation`
- `deliver-issue`
- `issue-review`
- `grilling`
- `domain-modeling`
- `grill-me`
- `grill-with-docs`

## Automated issue delivery

### 1. Prepare and approve the issue

Invoke `issue-review` with the idea, feature, or existing issue. It
prepares the parent pack and Smallest Coherent Slices, including each
slice's Branch Contract, acceptance criteria, traceability,
change-control boundaries, Review Loop Contract, and any semantic
review checkpoints.

Review and approve the resulting issue before delivery begins.

### 2. Let the delivery operator run

Invoke `deliver-issue` with the approved issue. Use a durable goal
when delivery should continue across turns:

`/goal Use $deliver-issue to deliver <issue path> to a ready-to-merge handoff.`

`deliver-issue` coordinates implementation, checkpoint validation,
independent review, authorized corrections, revalidation, final
integration review, pull-request creation or update, and CI
follow-through.

Confirmed deterministic in-scope findings may be corrected and
re-reviewed automatically. The operator stops when only human
approval and merge remain.

### 3. Handle human decision gates

Human input is required when delivery encounters product or domain
meaning, scope, architecture, public contracts, schemas, migrations,
permissions, security policy, dependencies, test strategy, material
reviewer disagreement, missing context, or an external blocker.

A durable goal supplies persistence, not authority. It cannot broaden
automatic correction authority or cross an unresolved decision,
blocker, or confirmed finding.

## Install

Install the skill family:

```bash
npx skills add seyofori/eng-for-certainty
```

Install a companion with its core dependency:

```bash
npx skills add seyofori/eng-for-certainty   --skill engineering-for-certainty   --skill engineering-resilience
```

Replace `engineering-resilience` with `engineering-observability`, `engineering-auth-security`, or `engineering-frontend` for another companion bundle.

Install code review with its complete engineering doctrine:

```bash
npx skills add seyofori/eng-for-certainty   --skill engineering-for-certainty   --skill engineering-observability   --skill engineering-resilience   --skill engineering-auth-security   --skill engineering-frontend   --skill code-review
```

Install issue review with its complete engineering doctrine:

```bash
npx skills add seyofori/eng-for-certainty   --skill engineering-for-certainty   --skill engineering-observability   --skill engineering-resilience   --skill engineering-auth-security   --skill engineering-frontend   --skill grilling   --skill issue-review
```

Issue review requires every companion triggered by the issue. The
complete bundle above prevents a frontend, auth, resilience, or
observability issue from being reviewed without its governing doctrine.

Install pull request review with the complete review doctrine:

```bash
npx skills add seyofori/eng-for-certainty   --skill engineering-for-certainty   --skill engineering-observability   --skill engineering-resilience   --skill engineering-auth-security   --skill engineering-frontend   --skill code-review   --skill pull-request-review
```

Install pull request creation:

```bash
npx skills add seyofori/eng-for-certainty   --skill engineering-for-certainty   --skill pull-request-creation
```

Install the delivery operator with its implementation, review, and
publication doctrine:

```bash
npx skills add seyofori/eng-for-certainty   --skill engineering-for-certainty   --skill engineering-observability   --skill engineering-resilience   --skill engineering-auth-security   --skill engineering-frontend   --skill code-review   --skill pull-request-creation   --skill deliver-issue
```

Install docs-backed grilling:

```bash
npx skills add seyofori/eng-for-certainty   --skill grilling   --skill domain-modeling   --skill grill-with-docs
```

Install general plan grilling:

```bash
npx skills add seyofori/eng-for-certainty   --skill grilling   --skill grill-me
```
