# Review Execution

Use this reference to realize the Independent Reviewer contract on any capable
agent harness. The shared skill defines responsibilities and evidence; the
active harness chooses concrete agents, models, providers, reasoning settings,
permissions, tools, runtimes, and isolation.

## Ownership

The active Independent Reviewer owns the review plan, candidate ledger,
normalization, deduplication, final verdicts, and complete return record.
Delegated finder and verifier contexts return evidence to that owner and never
gain workflow mutation authority.

## Execution Paths

Choose the strongest available path that preserves independence and coverage:

1. managed orchestration for several bounded packets;
2. parallel isolated review contexts for independent packets;
3. sequential clean review contexts when parallelism is unavailable; or
4. explicitly separated discovery and verification passes in the active
   Independent Reviewer context when that context did not implement the work.

Record the requested assurance and the mechanism actually used. If the active
context implemented the candidate, a same-context pass is supplemental
self-review, not Independent Review; return that limitation as a blocking gate.

## Read-Only Safety

Enforce read-only review through harness permissions or an equivalently
constrained mechanism. A prompt alone is not a permission boundary. Review
contexts may run safe, non-mutating validation but must not edit, commit, push,
publish, resolve threads, or alter external state.

## Finder And Verifier Packets

Give each finder raw governing requirements, exact diff boundaries, relevant
repository contracts, validation evidence, and a distinct review angle. Return
raw candidates with reachability, impact, and refuting evidence.

After normalization, give each verifier the candidate claim, raw evidence, and
relevant code—not another reviewer's conclusion as authority. Verification is
not voting. The Independent Reviewer assigns the final verdict from the
evidence and records disagreement.

When evidence depends on user-owned context, return a structured
`NEEDS_CONTEXT` candidate. The user-facing workflow asks the question; review
helpers do not contact the user independently.

## Completion Barrier

Do not close until every planned packet completed or has a recorded fallback,
every candidate has one final verdict, the current frozen head was reviewed,
and all coverage or independence limits are explicit.
