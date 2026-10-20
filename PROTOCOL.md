# LLM Project Handoff Protocol

## 1. Purpose

This protocol defines a lightweight mechanism for transferring **current
project coordination state** between LLM or agent sessions.

It is designed for environments where sessions are disposable and where
re-reading or re-summarizing the complete project history is undesirable.

The protocol does not attempt to preserve all historical context.

Its purpose is to ensure that a fresh session can identify:

- where the project is now;
- what goal is active;
- which durable decisions still matter;
- which artifacts are authoritative;
- which paths must not be resumed;
- what action should happen next.

Normative terms such as MUST, MUST NOT, SHOULD, and MAY are used in the usual
RFC-style sense.

## 2. Concepts

### 2.1 Bootstrap

A **Bootstrap** is a small, stable entry point for a fresh session.

It MUST identify the current Router.

It SHOULD explain the normal resume path.

It MUST NOT become a second copy of changing project state.

Typical forms include:

- a project file such as `HANDOFF.md`;
- a fixed project instruction;
- another stable location available to every new coordinator session.

### 2.2 Router

A **Router** is the mutable pointer to the current handoff snapshot.

A Router:

- MUST point to exactly one current Snapshot;
- MUST NOT act as a history index;
- MUST NOT retain links to previous, superseded, or ancestor Snapshots;
- MUST NOT own or duplicate current coordination state beyond information
  required to route to the current Snapshot.

Changing the Router pointer is the operation that makes a prepared Snapshot
current.

### 2.3 Snapshot

A **Snapshot** is an immutable projection of current coordination state.

A Snapshot is not a project specification and is not product authority.

A Snapshot SHOULD contain only information required to resume current work.

A typical Snapshot may contain:

- CURRENT STATE
- CURRENT GOAL
- ACTIVE DECISIONS
- AUTHORITY MAP
- CONSTRAINTS / DO NOT INFER
- COLD / DO NOT RESURRECT
- NEXT ACTION
- HANDOFF VALIDATION

The exact headings are not normative.

The semantics are.

### 2.4 Durable authority

A **Durable authority** is an artifact that owns detailed project truth outside
the handoff layer.

Examples include:

- design authority Issues;
- task Issues;
- ADRs / RFCs;
- repository policy;
- specifications;
- accepted review findings;
- tracker records.

The protocol does not prescribe the storage system.

### 2.5 Active decision

An **Active decision** is a durable decision whose consequences still affect the
current path.

Snapshots SHOULD reference active decisions by stable identifier instead of
copying or re-summarizing their full rationale.

A project MAY use ADRs, RFCs, tracker Issues, or another durable decision format.

### 2.6 Cold path

A **Cold path** is historical state that remains available but MUST NOT be
treated as a current execution path.

Examples include:

- abandoned implementations;
- obsolete issue sequences;
- superseded experiments;
- stale branches;
- failed comparison runs.

Explicitly marking cold state is useful when its continued existence could cause
an LLM to resurrect it.

## 3. Normal resume algorithm

A fresh coordinator SHOULD resume in this order:

1. read the Bootstrap;
2. read the Router;
3. read exactly the current Snapshot identified by the Router;
4. read only the authorities explicitly required by the Snapshot or current
   task;
5. continue from the stated NEXT ACTION.

Historical Snapshots MUST NOT be searched or read during normal resume.

During normal resume, a material current decision that is genuinely missing or
ambiguous MUST NOT be reconstructed from historical state.

The coordinator MUST instead return the ambiguity to the Human or current
authority for resolution.

Reading a current durable authority that directly resolves the question is not
historical reconstruction and does not make the decision "missing" for this
rule.

## 4. Snapshot construction rules

### 4.1 Materialize current state

A new Snapshot MUST be sufficient to represent current coordination state
without requiring the previous Snapshot.

If information from an older Snapshot is still active, it MUST be represented
again in the new Snapshot or referenced through a current durable authority.

A Snapshot MUST NOT say, in effect, "read the previous handoff to understand
this state."

### 4.2 Do not inherit handoff history

Current Snapshots MUST NOT link to previous or superseded handoff Snapshots as
part of normal state inheritance.

The Router MUST NOT expose historical handoff links either.

Historical Snapshots MAY remain discoverable through explicit search or archival
mechanisms when historical investigation is intentionally requested.

### 4.3 Reference, do not duplicate, detailed authority

Snapshots SHOULD reference detailed product, architecture, security, and task
authority rather than copying those documents.

A reference SHOULD be bounded enough that the next session knows what artifact
to read and why.

The handoff layer MUST NOT silently become a competing specification.

### 4.4 Preserve non-obvious current constraints

Small pieces of coordination state that materially change future behavior SHOULD
be kept even when they are shorter than the detailed technical state around
them.

Examples include:

- a current execution method;
- a current authority boundary;
- a non-obvious prohibition;
- a still-active rationale reference;
- a path that must remain cold.

The purpose is not maximum compression.

The purpose is correct next-action selection.

### 4.5 Remove inactive state

When state is no longer current, it MUST be removed from the next Snapshot or
explicitly retained as a Cold path.

The current Snapshot is a projection of active coordination state, not an event
log.

## 5. Router update procedure

To advance handoff state:

1. create or update any required durable decisions;
2. prepare a candidate handoff record;
3. verify that the candidate is self-contained for current coordination;
4. replace the Router's current pointer with the candidate, making it the new
   current Snapshot;
5. do not append a link to the previous Snapshot.

The Router pointer replacement is the **commit point** for the handoff state
only.

It does not imply transactional rollback of external durable authorities.

If preparation fails before the Router update, the old Snapshot remains current.
If external authority has already advanced and now conflicts with that old
Snapshot, the conflict is handled according to Section 8.

No stronger transactional mechanism is required by this protocol.

## 6. Immutability

A candidate handoff record MAY be edited while it is being prepared and
validated.

Once the Router selects that record as the current Snapshot, the Snapshot MUST
NOT be modified.

Historical Snapshots remain immutable after the Router moves elsewhere.

v0 treats this as a procedural invariant.

Storage systems such as GitHub Issues may technically allow in-place edits; the
protocol requires maintainers not to use that capability to mutate a current or
historical Snapshot.

Projects that require stronger integrity MAY add mechanisms such as:

- body digests;
- content-addressed storage;
- signed records;
- Git-backed snapshots.

Such mechanisms are optional hardening and are outside the v0 core protocol.

## 7. Validation

The preferred validation is operational, not quiz-based.

A handoff is successful when a fresh session can continue the intended project
path without:

- resurrecting cold state;
- asking the Human to restate material context already present in the handoff;
- treating the handoff as product authority;
- reconstructing current rules from historical state.

A project MAY also perform mechanical checks such as:

- Router points to exactly one Snapshot;
- required Snapshot sections are present;
- current authority references resolve;
- no historical handoff links are present.

The protocol does not require identical natural-language answers across models.

## 8. Authority boundaries

The handoff layer is coordination state only.

It MUST NOT override product, architecture, security, task, or repository
authority.

When the Snapshot and an authoritative artifact materially conflict, the
conflict MUST be returned for resolution.

The handoff MUST NOT silently resolve that conflict by rewriting the authority.

## 9. Historical investigation

Historical handoff state is intentionally removed from the normal resume path.

When historical investigation is explicitly required, old Snapshots MAY be
discovered by:

- a fixed title prefix such as `[HANDOFF]`;
- tracker search;
- archival search;
- repository history.

This search is an exception path.

Historical handoff discovery MUST NOT become a normal prerequisite for resuming
current work.

## 10. Storage independence

The protocol is storage-agnostic.

One implementation may use:

- a project file as Bootstrap;
- one tracker Issue as Router;
- immutable tracker Issues as Snapshots;
- separate ADR / task Issues as durable authority.

Another implementation may use local files, Jira, GitLab, a database, or a
different combination.

Conformance depends on the semantics above, not on the specific storage
provider.

## 11. Non-goals

This protocol does not define:

- how coding agents implement tasks;
- how task Issues are structured;
- review or revision orchestration;
- Git branch / worktree policy;
- product architecture governance;
- LLM memory or compaction behavior;
- automatic context ranking;
- archival retention policy;
- cryptographic integrity requirements.

Projects may layer those systems on top of this protocol.

## 12. Minimal conforming shape

A minimal implementation needs only:

```text
Bootstrap
  -> Router
      -> Current Snapshot
          -> Durable Authority
              -> Current Task
```

with the following invariants:

1. one current Snapshot;
2. Router contains no handoff history;
3. Snapshot does not depend on previous Snapshots;
4. current durable decisions are referenced, not recursively summarized;
5. obsolete state is removed or explicitly marked cold;
6. missing material decisions are not reconstructed from history;
7. Router replacement commits the new handoff state only.
