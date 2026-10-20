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
Do not re-summarize full decision rationale unless the rationale itself is
necessary current coordination state and has no durable home.

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

Record lightweight evidence that the handoff is usable, when available.

Examples:

- a fresh session resumed through the bounded read path;
- current authority references were verified;
- the Router points to this Snapshot;
- known non-blocking hardening remains deferred.

Do not turn this section into a general test report.
