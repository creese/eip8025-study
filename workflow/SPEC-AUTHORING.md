# Specification authoring

If the next phase has no executable phase specification, run a
separate session to create it only after the audit reports `PASS`, or
after the user explicitly accepts a `REVIEW REQUIRED` result and
authorizes advancement.

Follow CLAUDE.md and use:

* the corresponding design brief under `workflow/phase-briefs/` declared by the
  task prompt;
* any applicable reusable workflow files declared by the task prompt;
* applicable decisions recorded in `notes/refs.md`;
* existing phase specifications only as structural references, and
  only those the task prompt declares.

It must incorporate applicable requirements from its drafting inputs
rather than depend on those files at execution time. The new phase
specification must be self-contained and executable.

An executable phase specification must define, for its own
operations:

* failure handling for each step, including what is reported and what
  is cleaned up or preserved;
* recovery from an interrupted or partial publication;
* safe, idempotent resume behavior, so a rerun either completes the
  remaining work cleanly or stops without corrupting or duplicating
  state;
* the coordination and ordering of publication when the phase both
  publishes append-only evidence and modifies a control file such as
  `notes/refs.md`, including a guard against duplicate control-file
  entries;
* the durable artifacts — including any required validation receipt —
  that a later read-only audit needs to verify each `Done when`
  criterion.

Translate the design brief's completion goal into explicit `Done when`
criteria.

Each criterion must describe an observable final condition and
identify, directly or through the specification's declared inputs, the
artifacts needed for later read-only verification. A prior model
report is not evidence that a criterion was satisfied.

Do not rely solely on session-time facts. If an essential process fact
cannot be reconstructed from final state, require a durable validation
receipt, transcript, or log.

A specification-authoring session must not execute the phase, inspect
the target repository, use network access, or modify study evidence.

If an unresolved decision prevents a safe executable specification,
stop and report the blocker.

After creating or revising a phase specification, inspect the actual
git diff and report:

* whether the target specification was created or changed;
* any unresolved drafting blocker;
* all files changed.

Commit behavior is governed by `CLAUDE.md`.
