# Project Handoff Bootstrap

This file is the stable entry point for fresh coordinator sessions working on:

`<PROJECT IDENTIFIER>`

It is not project state, product authority, or a task specification.

## Start here

Read the current handoff Router:

`<ROUTER REFERENCE>`

Then:

1. read exactly the current `[HANDOFF]` Snapshot referenced by the Router;
2. use that Snapshot to identify the current task, active decisions,
   authorities, constraints, cold paths, and next action;
3. read additional project artifacts only when the current Snapshot or current
   task requires them.

## Handoff rules

- The Router points to exactly one current Snapshot.
- Historical handoff Snapshots are not part of normal resume.
- Do not reconstruct current state from previous conversations, model memory,
  stale branches, abandoned implementations, or historical handoff records.
- The current Snapshot is coordination state only.
  It does not override project authority.
- If a material current decision is missing or ambiguous, return it to the
  Human or current authority instead of reconstructing it from history.

## Normal resume path

`Bootstrap -> Router -> current Snapshot -> required authorities -> current task`
