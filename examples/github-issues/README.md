# GitHub Issues implementation example

This directory shows one concrete implementation of the
[LLM Project Handoff Protocol](../../PROTOCOL.md).

It is an example, not a normative requirement.

## Mapping

A simple GitHub-based implementation can use:

| Protocol concept | GitHub implementation |
|---|---|
| Bootstrap | stable project file such as `HANDOFF.md` |
| Router | one mutable GitHub Issue |
| Snapshot | one GitHub Issue per handoff checkpoint |
| Active decision | ADR / RFC / design-decision Issue or other durable record |
| Durable authority | task Issues, design Issues, repository files, specifications |

Suggested title conventions:

```text
[HANDOFF ROUTER] Current project handoff
[HANDOFF] <checkpoint>
[ADR] <decision>
```

Only the handoff prefixes are relevant to this example.
Projects may use different conventions.

## Bootstrap

Place a small bootstrap file somewhere available to every fresh coordinator
session.

Start from [templates/HANDOFF.md](../../templates/HANDOFF.md).

The bootstrap should contain the stable URL or identifier of the Router Issue,
not the changing Snapshot number.

## Router Issue

Create exactly one mutable Router Issue from
[templates/router.md](../../templates/router.md).

Its `CURRENT HANDOFF` section points to exactly one current Snapshot Issue.

Example:

```md
## CURRENT HANDOFF

#42 — `[HANDOFF] pre-deployment checkpoint`
```

Do not append earlier Snapshots below it.

When handoff state advances, replace `#42` with the new Snapshot.

## Snapshot Issues

Create a new Issue for each handoff checkpoint using
[templates/snapshot.md](../../templates/snapshot.md).

A candidate Snapshot Issue may be edited while it is being prepared and
validated.

Once the Router points to it, treat its body as immutable.

When a later Snapshot becomes current:

- leave the old Issue unchanged;
- do not link the old Snapshot from the Router;
- do not link the old Snapshot from the new Snapshot for state inheritance.

Old Snapshots remain available through explicit GitHub search when historical
investigation is intentionally required.

## Typical transition

Suppose the Router currently points to `#42`.

To advance:

1. update any durable ADR / task / authority records that must change;
2. create `#47 [HANDOFF] post-review checkpoint`;
3. edit `#47` until it is self-contained and validated;
4. update the Router Issue so `CURRENT HANDOFF` points to `#47`;
5. stop editing `#47`.

The Router update commits the handoff state only.

If a durable authority changed before step 4 and the transition fails, the old
Snapshot remains current. If it now conflicts with current authority, surface
that conflict for resolution rather than reconstructing intent from history.

## Historical discovery

Normal resume should not search old handoffs.

If historical investigation is explicitly required, GitHub search can discover
them by title prefix, for example:

```text
is:issue "[HANDOFF]"
```

The search path is exceptional and should not be required to resume normal
project work.

## What not to put in the Router

Do not use the Router as a mini project dashboard.

Avoid duplicating:

- current task details;
- current baseline details;
- active ADR summaries;
- product decisions;
- cold-path lists;
- historical Snapshot links.

Those belong in the current Snapshot or their actual durable authority.

## What not to put in the Snapshot

Avoid copying:

- full task specifications;
- full ADR bodies;
- repository-wide agent policy;
- product architecture documents.

Reference those artifacts instead.

The Snapshot should remain a small projection of current coordination state.
