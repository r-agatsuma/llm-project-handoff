# [HANDOFF] <checkpoint>

This record is a handoff Snapshot once selected by the Router.

Before activation it MAY be edited as a candidate handoff record.
Once activated, it MUST NOT be modified.

It is coordination state only and does not override the project authorities
referenced below.

## CURRENT STATE

Describe only the current coordination state needed to resume work.

Include, as applicable:

- current baseline or checkpoint;
- landed / completed work relevant to the next action;
- current task;
- immediate following gates.

Do not copy detailed task specifications that already have a durable authority.

## CURRENT GOAL

State the current intended progression or next reviewable checkpoint.

## ACTIVE DECISIONS

Reference durable decisions whose consequences still affect the current path.

Use stable identifiers or bounded references.
Do not re-summarize full decision rationale in the Snapshot.

If a small, non-obvious consequence of a durable decision is necessary for
next-action selection, record that bounded consequence under
`CONSTRAINTS / DO NOT INFER` while keeping the durable decision reference here.

## AUTHORITY MAP

Identify the durable artifacts that own relevant project truth.

For each reference, make its role clear enough that a fresh coordinator knows
why it may need to read it.

## CONSTRAINTS / DO NOT INFER

Record non-obvious current constraints that materially affect next-action
selection.

If a material current decision is missing or ambiguous, it MUST NOT be
reconstructed from historical state.

## COLD / DO NOT RESURRECT

List historical paths that remain discoverable but are not current execution
paths.

Omit obsolete state entirely when it poses no realistic resurrection risk.

## NEXT ACTION

State the next coordinator action or bounded sequence of actions.

Prefer references to durable task contracts over duplicated implementation
instructions.

## HANDOFF VALIDATION

Record only lightweight validation evidence available before activation.

Examples:

- candidate checked for self-containment;
- required current authority references resolve;
- candidate contains no historical handoff inheritance links;
- candidate does not duplicate detailed task or decision authority.

Post-activation evidence, such as a successful fresh-session resume or the
Router currently pointing to this Snapshot, MUST live outside this immutable
Snapshot.

Do not turn this section into a general test report.
