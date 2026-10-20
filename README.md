# llm-project-handoff

A lightweight protocol for handing off long-running project context between LLM
or agent sessions without replaying the entire project history.

The core idea is simple:

```text
static bootstrap
    -> mutable router
        -> one current immutable handoff snapshot
            -> bounded references to durable project authority
                -> current task
```

The protocol is designed for projects where:

- sessions are disposable;
- conversation history is too large or unreliable to act as project state;
- only a small subset of past decisions matters to the next action;
- stale implementation paths must not be accidentally resurrected;
- product, architecture, and task authority already live somewhere durable
  such as Issues, RFCs, ADRs, repository policy, or another tracker.

The goal is not to preserve all context.

The goal is to preserve enough **current coordination state** to route a fresh
session to the right authorities and next action.

## Problem

A long-running project accumulates far more information than a fresh LLM
session should read.

Naively summarizing everything has two common failure modes:

1. important rationale is dropped while low-value technical detail survives;
2. old state remains visible and is incorrectly treated as current.

This protocol therefore treats handoff as **attention routing**, not archival
summarization.

Historical state may still exist, but it is intentionally removed from the
normal resume path.

## Model

### Bootstrap

A small, stable file or equivalent entry point.

Its job is only to identify the Router and explain the resume path.

It should not contain changing project state.

### Router

A mutable pointer to exactly one current handoff snapshot.

The Router is not a history index.

When handoff state advances, the current pointer is replaced rather than
appended.

### Handoff snapshot

An immutable projection of the coordination state that matters now.

A snapshot typically contains:

- current state;
- current goal;
- active durable decision references;
- authority map;
- constraints / do-not-infer rules;
- cold / do-not-resurrect paths;
- next action;
- lightweight validation status.

A snapshot should reference detailed product or task authority instead of
copying it.

### Durable authority

The protocol does not define where product truth must live.

Examples include:

- GitHub / GitLab Issues;
- Jira;
- ADRs or RFCs;
- repository policy files;
- design specifications;
- task contracts.

The current snapshot points to these sources when they are required.

## Normal resume path

A fresh coordinator should be able to recover with a bounded read path:

```text
Bootstrap
  -> Router
  -> current Snapshot
  -> explicitly required authorities
  -> current task
```

Historical handoff snapshots are not part of normal resume.

## Key properties

- **Small current state** — the handoff is not a compressed project archive.
- **Exactly one current snapshot** — no ambiguity about which handoff is live.
- **Immutable snapshots** — old state remains inspectable without being edited
  into new state.
- **No historical handoff chain** — previous snapshots are not linked into the
  current read path.
- **Active-state materialization** — if a past decision still matters, it is
  represented in the current snapshot or referenced as a current durable
  decision.
- **Bounded authority references** — detailed specifications stay at their
  authoritative source.
- **Explicit negative state** — abandoned paths may be marked cold so a model
  does not resurrect them simply because they still exist.
- **No archaeology by default** — missing current decisions are returned to the
  Human / coordinator rather than reconstructed from project history.

## Scope

This repository standardizes project handoff state.

It intentionally does **not** standardize:

- coding-agent orchestration;
- Git branching or worktree policy;
- issue tracker choice;
- implementation Issue format;
- review / revision workflow;
- memory or compaction algorithms;
- product or architecture authority;
- cryptographic snapshot immutability.

Those remain project-local concerns.

## Protocol

See [PROTOCOL.md](./PROTOCOL.md) for the normative protocol.

Templates and implementation examples are expected to stay intentionally small.
The protocol should remain usable with GitHub Issues, other trackers, or local
files without requiring a specific orchestration tool.

## Status

Early draft.

The protocol originated from a real multi-session software project where a
fresh ChatGPT coordinator successfully resumed work through the bounded path:

```text
Project bootstrap
  -> handoff Router
  -> current snapshot
  -> active decision / repository policy
  -> product authorities
  -> current implementation task
```

That operational test motivates the current v0 design, but the repository is
intended to generalize beyond that project and beyond any single LLM product.
