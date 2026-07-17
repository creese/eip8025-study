# Grandine Execution Proof Study — Ground Rules

This file defines stable rules that apply across the study.

## Document authority

`workflow/ROADMAP.md` defines phase order, prerequisites, primary
outputs, handoffs, and human checkpoints. `workflow/PHASE-AUDIT.md`
defines the phase-completion-audit procedure, and
`workflow/SPEC-AUTHORING.md` defines the specification-authoring
procedure. Files under `workflow/phase-briefs/` are authoritative
drafting sources for phases whose specifications have not yet been
written.

A phase specification under `phases/` is authoritative for execution
only when it is nonempty and contains a `Done when` section. A newly
created or revised phase specification must not be executed until the
user has reviewed its actual git diff and explicitly authorized
execution. Follow only the current phase's specification; do not read
other phase files unless the current task explicitly declares them as
inputs.

When no executable phase specification exists, the phase's design
brief under `workflow/phase-briefs/` is authoritative for its intended scope,
constraints, outputs, and completion goals. A design brief is drafting
source for the future phase specification; it is not silently treated
as a complete executable prompt.

A phase is complete only after its completion audit reports `PASS`, or
the user explicitly accepts a `REVIEW REQUIRED` result and authorizes
advancement. A phase must not be executed or audited as complete until
its executable phase specification exists. The existence of an output
file alone does not establish completion.

If authoritative documents loaded for the current task appear to
conflict, stop and report the conflict rather than choosing,
combining, or silently overriding rules.

## Session types and scope

- Harvest sessions run fixed commands and save source material
  verbatim; they perform no interpretation, summarization, or
  synthesis.
- Synthesis and schema-filling sessions use only their declared saved
  artifacts. They perform no live research unless the current phase is
  explicitly a harvest session that permits it.
- Use a separate, bounded session for each phase, workflow task, or
  explicitly defined batch. Load only the inputs declared by the
  current phase specification or, for a workflow task, by the
  applicable workflow procedure and task prompt; do not read other
  notes files, matrices, raw artifacts, repositories, phase briefs, or
  phase specifications merely because they appear relevant.
- Prior conversation history is never evidence or authoritative
  project state.
- If a required declared input is absent, stop and report it; never
  substitute another source.
- Never inspect live Lighthouse and Grandine sources in the same
  session. Cross-implementation synthesis may use only the declared
  saved artifacts required by its phase specification and must not
  perform live repository or network inspection. 
- Resume behavior, batch sizes, and permitted phase combinations are
  defined by the current phase specification.

## Refs

Refs are pinned in `notes/refs.md` before dependent work begins. Never
search for, guess, discover, choose, substitute, or silently update a
branch, PR, repository, or SHA that is supposed to be pinned.

A session touching pinned repository content must perform the
verification gate defined by its current phase specification — never a
gate recalled from memory or taken from another phase. If the pin
cannot be verified, or the observed repository state differs from the
recorded pin: stop, report the discrepancy or delta, and wait for the
user's re-pin decision. Never continue past a failed pin or
repository-state gate.

## User decision authority

The user makes all re-pin, scope, go/no-go, provisional-baseline, and
proposal-content decisions. A model may identify the need for such a
decision but must never make it.

## Command success

The success of every command that produces evidence, changes project
state, or gates later work must be checked — at minimum its exit code,
plus any stderr policy defined by the current phase specification —
before dependent work continues. Output from an unchecked command is
never treated as successful harvest, publication, or gate passage.

## Artifact governance

- `notes/raw/` is the append-only published-evidence store. Existing
  raw artifacts must never be edited, replaced, or deleted.
- New raw artifacts are staged outside `notes/raw/` in the
  phase-declared staging location, validated there as a complete
  artifact set, and published into `notes/raw/` only after that
  validation succeeds. Publication is no-clobber: it must fail rather
  than overwrite an existing raw artifact.
- Temporary, staging, stderr, diagnostic, and recovery files belong in
  the phase-declared location under `.work/` or `/tmp`. They must
  never be created under `notes/raw/` and are never cited as evidence.
- Matrices and synthesis documents are derived artifacts, built only
  from the declared evidence and control inputs named by their phase
  specifications.
- Live research findings must be saved as raw artifacts before they
  are used in synthesis.
- Model-assisted edits are verified through the actual git diff, never
  through a model's description of its changes.

## Citation

Every substantive synthesis claim carries evidence in the form defined
by the current artifact's schema.

In prose artifacts, each factual or interpretive claim ends in a
matrix row identifier, [raw:<path>], or an existing [OPEN-Qn].
Headings, document structure, and purely connective text are exempt.

In structured artifacts such as TSV files, evidence belongs in the
schema-defined evidence or citation field; claims in free-text fields
follow any additional citation rules in the current phase
specification.

A [raw:<path>] pointer must name a published artifact that exists
under `notes/raw/`. Files under `.work/` or `/tmp` are never evidence.

Uncited substantive claims are not permitted, including claims
introduced or strengthened during prose-tightening edits.

## Evidence findings

- A negative or absence claim requires saved evidence identifying what
  was searched or inspected and the result. An unsaved search,
  informal impression, or model recollection is not evidence of
  absence.
- A contested or reviewer-contested classification must cite the
  specific saved comment expressing the objection. Never infer
  contention solely from thread resolution state or other metadata.
- WIP, TODO, provisional, reverted, or scaffolding evidence must never
  silently be treated as a finalized intended design; the current
  phase specification defines its classification.

## Provisional baseline

A finding that depends on a baseline recorded as
PROVISIONAL-SPEC-BASELINE must carry the applicable provisional tag on
the affected derived claim or row, and may not support a conclusion
stronger than needs-input until the baseline is confirmed. A
provisional-baseline mismatch is never a confirmed proposal finding.

The user decides whether to confirm or replace a provisional baseline;
a model may identify the need for clarification but may not make the
re-pin decision.

## Open questions

`notes/open-questions.md` is append-only. Question IDs are monotonic and
are never reused or renumbered. A new ID is assigned only when the
question is first written to notes/open-questions.md; models must not
assign a new [OPEN-Qn] identifier inline before that write. A phase
may propose an unnumbered question when its specification permits it
and may reference an existing [OPEN-Qn].

## Commit authority

Do not commit. After completing any authorized file changes, report
the files created or changed and stop for user review.

## Proposal authorship

The user writes the proposal and makes final scope and content
decisions. Models must not draft proposal prose; they may critique
claims, identify gaps, check citations, and ask questions.

Prose-tightening edits are permitted only when the user explicitly
requests them. Such edits must not introduce or strengthen factual or
interpretive claims, and the user verifies them through the actual
git diff rather than a model-reported summary.
