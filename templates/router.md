# Handoff Router

This record is the mutable routing point for project handoff state.

It is not project authority.
It is not a task specification.
It is not a history index.

## CURRENT HANDOFF

`<CURRENT SNAPSHOT REFERENCE>`

A fresh coordinator MUST treat the record above as the only current handoff
Snapshot.

## ROUTING RULES

- This Router MUST point to exactly one current Snapshot.
- Historical Snapshots MUST NOT be linked from this Router.
- Previous, superseded, or ancestor Snapshot links MUST NOT be retained here.
- The Router MUST NOT own or duplicate current coordination state beyond
  information required to route to the current Snapshot.
- When a new Snapshot becomes current, replace the current pointer instead of
  appending history.
- Historical handoffs MAY be discovered only when historical investigation is
  explicitly required.

## UPDATE PROCEDURE

To advance handoff state:

1. create or update any required durable decisions;
2. prepare and validate a candidate handoff record;
3. replace `CURRENT HANDOFF` above with the candidate, making it current;
4. do not add a link to the previous Snapshot.

The Router update is the commit point for handoff state only.
