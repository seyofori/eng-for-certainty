# Checkpoint Delivery

Use this loop when the approved issue defines more than one review checkpoint.

## Before Each Checkpoint

Load only the checkpoint's required operating set:

- the core issue contract and `Agent Start Here`;
- the current checkpoint row;
- its owned acceptance criteria and traceability rows;
- its production and test surfaces;
- every appendix explicitly required by that checkpoint;
- prior accepted checkpoint heads and unresolved residual risks; and
- the governing Review Loop Contract.

Do not treat earlier implementation summaries as substitutes for these sources.

## Checkpoint Loop

For each checkpoint:

1. Implement only the current checkpoint's approved behavior and surfaces.
2. Run its exact validation and the repository gates needed to leave the branch
   green.
3. Create a coherent local checkpoint commit under the repository's history
   conventions and freeze its candidate head SHA.
4. Invoke `$code-review` in Checkpoint Review Mode.
5. Use the issue base as the first checkpoint base. For later checkpoints, use
   the previous accepted checkpoint SHA as the primary review base.
6. Require the reviewer to inspect the new range plus its integration seams
   with earlier accepted checkpoints and any shared contract it changes.
7. Wait for complete checkpoint discovery, verification, deduplication, and
   routing before editing.
8. Route every result through the Review Loop Contract.
9. Apply one coherent batch of independent `AUTO_CORRECT` findings where
   practical.
10. Re-run invalidated proof, freeze the corrected head, and re-review the same
    checkpoint.
11. Record the accepted head SHA only when the checkpoint passes its advance
    rule.
12. Begin the next checkpoint from that accepted head.

## Advance Rule

Advance only when:

- the reviewed candidate and accepted head are the same;
- all checkpoint-owned traceability rows have evidence;
- no unresolved confirmed finding remains;
- every prior checkpoint finding has a disposition;
- correction-invalidated proof has been re-run; and
- every residual risk is explicitly permitted by the issue.

`AUTO_CORRECT` returns to the same checkpoint. `USER_DECISION` and `BLOCKED`
pause delivery and leave any durable goal incomplete.

`RESIDUAL_RISK` remains a finding route. Record a permitted residual risk and
return `CLEAN`; return `USER_DECISION` when the issue does not permit it.

The existence of a goal never permits advancement, finding suppression,
severity reduction, weakened proof, or silent selection of product meaning.

## Churn

Escalate to `USER_DECISION` when:

- the same root cause survives two correction cycles;
- the fix oscillates between alternatives;
- a new finding exposes ambiguity in the issue;
- the proposed correction crosses the checkpoint's approved surface; or
- reviewers materially disagree.

## After The Last Checkpoint

Return to the main `$deliver-issue` workflow. Run complete issue-owned
validation and a final full integration review of the issue-base-to-current-head
diff before creating or updating the pull request.
