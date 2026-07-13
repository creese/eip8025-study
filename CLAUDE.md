# EIP-8025 Grandine study — ground rules

## Document authority

This file defines stable rules that apply across the study.

ROADMAP.md defines phase order, prerequisites, primary outputs,
handoffs, and human checkpoints. It also contains authoritative design
briefs for phases whose specifications have not yet been written.

When a phase specification exists under phases/, that file is
authoritative for the phase's execution details. Follow only the
specification for the current phase; do not read other phase files
unless the current task explicitly declares them as inputs.

When no phase specification exists, the corresponding design brief in
ROADMAP.md is authoritative for the phase's intended scope,
constraints, outputs, and completion goals. A design brief is drafting
source for the future phase specification; it is not silently treated
as a complete executable prompt.

A phase is complete only after every criterion in its specification's
Done when section has been verified. If no specification exists yet,
the roadmap design brief defines the completion goals that the future
specification must cover. The existence of an output file alone does
not establish completion.

If CLAUDE.md, ROADMAP.md, and the current phase specification appear
to conflict, stop and report the conflict rather than choosing,
combining, or silently overriding rules.

## Overall approach

- Mechanical harvest sessions run fixed commands, save source material
  verbatim, and perform no interpretation or synthesis.
- Synthesis and schema-filling sessions use only their declared saved
  artifacts. They do not rely on conversation memory or perform live
  research unless the current phase is explicitly a harvest session
  that permits external research.
- Refs are pinned before dependent work begins. A model must not
  discover, choose, substitute, or silently update a ref.
- The user makes re-pin, scope, go/no-go, and proposal-content
  decisions.
- The user writes the proposal. Models may critique it or tighten prose
  only under the conditions stated below.

## Refs

Refs are pinned in notes/refs.md. Never search for, guess, substitute,
or silently update a branch, PR, repository, or SHA that is supposed
to be pinned.

A session touching pinned repository content must perform the
verification gate defined by its current phase specification. If the
pin cannot be verified or the observed repository state differs from
the recorded pin: stop, report the discrepancy or delta, and wait for
the user's re-pin decision.

Do not impose a different gate from memory or from another phase.

## Session scope

- Use a separate, bounded session for each phase or explicitly defined
  batch.
- Load only the inputs declared by the current phase specification.
- Do not read other notes files, matrices, raw artifacts, repositories,
  or phase specifications merely because they appear relevant.
- Do not treat prior conversation history as evidence or authoritative
  project state.
- If a required declared input is absent, stop and report it rather
  than substituting another source.
- Never combine Lighthouse and Grandine inspection or synthesis in one
  session.
- Resume behavior, batch sizes, and any permitted phase combinations
  are defined by the current phase specification. If that specification
  does not yet exist, its ROADMAP.md design brief preserves the
  intended behavior until it is migrated into the phase file.

## Artifact governance

- notes/raw/ is the append-only published-evidence store. Existing raw
  artifacts must never be edited, replaced, or deleted.
- Temporary, staging, stderr, diagnostic, and recovery files must not
  be created under notes/raw/. They belong in the phase-declared
  location under .work/ or /tmp and must never be cited as evidence.
- Harvest sessions save source material verbatim and do not interpret
  or summarize it.
- Matrices and synthesis documents are derived artifacts. They may be
  built only from the declared evidence and control inputs named by
  their phase specifications.
- Live research findings must be saved as raw artifacts before they
  are used in synthesis.
- Model-assisted edits are verified through the actual git diff, not
  through a model's description of its changes.

## Citation

Every substantive synthesis claim carries evidence in the form defined
by the current artifact's schema.

In prose artifacts, each factual or interpretive claim ends in a
matrix row identifier, [raw:<path>], or an existing [OPEN-Qn].
Headings, document structure, and purely connective text do not require
citations.

In structured artifacts such as TSV files, evidence belongs in the
schema-defined evidence or citation field. Claims in free-text fields
follow any additional citation rules in the current phase
specification.

A [raw:<path>] pointer must name a published artifact that exists
under notes/raw/. Temporary or recovery files under .work/ or /tmp are
never evidence.

Uncited substantive claims are not permitted, including claims
introduced or strengthened during prose-tightening edits.

## Evidence findings

- A negative or absence claim requires saved evidence identifying what
  was searched or inspected and the result. An unsaved search,
  informal impression, or model recollection is not evidence of
  absence.
- A contested or reviewer-contested classification must cite the
  specific saved comment or evidence expressing the objection. Do not
  infer contention solely from thread resolution state or other
  metadata.
- WIP, TODO, provisional, reverted, or scaffolding evidence must not
  silently be treated as a finalized intended design. The current
  phase specification defines its classification.

## Provisional baseline

When a finding depends on a baseline recorded as
PROVISIONAL-SPEC-BASELINE, the affected derived claim or row must carry
the applicable provisional tag.

A provisional-baseline-dependent finding may not support a conclusion
stronger than needs-input until the baseline is confirmed.

The user decides whether to confirm or replace a provisional baseline.
A model may identify the need for clarification but may not make the
re-pin decision.

## Open questions

notes/open-questions.md is append-only.

Question IDs are monotonic. A new ID is assigned only when the
question is first written to notes/open-questions.md; models must not
assign a new [OPEN-Qn] identifier inline before that write.

IDs are never reused or renumbered. A phase may propose an unnumbered
question when its specification permits it and may reference an
existing [OPEN-Qn].

## Commit authority

Do not commit unless the current phase specification explicitly
instructs the session to do so.

Phase and batch commit timing is defined by the applicable phase
specification or, until that specification exists, by its ROADMAP.md
design brief.

When the current specification says not to commit, report the files
created or changed and stop for user review.

## Proposal authorship

The user writes the proposal and makes final scope and content
decisions.

Models must not draft proposal prose. They may critique claims,
identify gaps, check citations, and ask questions.

Prose-tightening edits are permitted only when the user explicitly
requests them. Such edits must not introduce or strengthen factual or
interpretive claims, and the user verifies them through the actual
git diff rather than a model-reported summary.

## Cross-phase prohibitions

- Do not discover, choose, substitute, or silently update pinned refs.
- Do not read undeclared inputs or use conversation memory as evidence.
- Do not substitute another source when a declared input is missing.
- Do not let live web or repository findings reach synthesis before
  they are saved as declared raw evidence.
- Do not treat a provisional-baseline mismatch as a confirmed proposal
  finding.
- Do not infer contention solely from thread metadata.
- Do not assert absence without saved negative evidence.
- Do not continue after a required pin or repository-state gate fails.
- Do not accept a model's summary of its edits as verification; inspect
  the actual git diff.
- Do not let a model make final re-pin, scope, go/no-go, or
  proposal-content decisions.
