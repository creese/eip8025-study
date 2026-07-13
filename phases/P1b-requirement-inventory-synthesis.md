# Phase 1b — requirement inventory (synthesis session)

This is a SYNTHESIS session, not a harvest: reading, judging relevance,
and paraphrasing are the task. The binding rules are: read only the
declared inputs; every claim cites evidence; output is the matrix
below. Do not commit.

STUDY_ROOT=/work

## Precondition (verify before any task step; stop if unmet)
Two raw artifacts must already exist, harvested by the separate
mechanical session Phase 1a-iii (which runs before this session):
- "$STUDY_ROOT"/notes/raw/eip-8025-post-pr.md — the complete EIP-8025
  text as of the pinned EIP PR head (primary extraction source)
- "$STUDY_ROOT"/notes/raw/eip-8025-pre-pr.md — the complete EIP-8025
  text as of the pinned merge-base (pr_delta comparison only)
This session must not reconstruct either text from diff hunks and
must not read any repo clone. If either artifact is absent: stop,
report.

## Declared inputs — read these and nothing else
- "$STUDY_ROOT"/notes/raw/eip-8025-post-pr.md (complete post-PR EIP text)
- "$STUDY_ROOT"/notes/raw/eip-8025-pre-pr.md  (complete pre-PR EIP text)
- "$STUDY_ROOT"/notes/raw/eip-threads.md      (PR description + threads)
- "$STUDY_ROOT"/notes/raw/spec-manifest.txt
- "$STUDY_ROOT"/notes/raw/spec-files/         (matched spec snapshot files)
- "$STUDY_ROOT"/notes/raw/spec-todos.txt      (WIP/TODO/DRAFT markers)
- "$STUDY_ROOT"/notes/refs.md                 (context: pins, [OPEN-Q1])
Do not read other notes files, the repo clones, or the live web. If a
needed input is missing, stop and say so.

## Overwrite protection and output staging
FINAL_TSV="$STUDY_ROOT"/notes/matrix/requirements.tsv
TMP_TSV="$STUDY_ROOT"/notes/matrix/.requirements.tsv.tmp

Create "$STUDY_ROOT"/notes/matrix/ if absent.

Stop and report if either of these already exists:
- "$FINAL_TSV"
- "$TMP_TSV"

Write the matrix only to "$TMP_TSV" until extraction, reconciliation,
and all validation checks pass. Do not write directly to "$FINAL_TSV".

Write "$TMP_TSV" in small bounded chunks. Do not embed the complete
inventory in one shell command, heredoc, or Python source file.

After each chunk, verify that every completed row has exactly nine
tab-separated fields. Do not alter, merge, or regenerate requirement
content unless validation identifies a specific row error.

On any failure before publication, remove "$TMP_TSV" only if this
session created it, then stop.

After all validation checks pass, publish with:

mv "$TMP_TSV" "$FINAL_TSV"

Capture and check the exit code immediately. If publication fails,
preserve "$TMP_TSV", report which temporary and final paths exist,
and do not claim completion.

## Context you must respect
- The spec snapshot is a PROVISIONAL-SPEC-BASELINE (a general ef-tests
  pin, not a confirmed EIP-8025 target — see [OPEN-Q1] and refs.md).
  spec-files/ was gathered by case-insensitive over-capturing search;
  some files may be irrelevant.
- The complete post-PR EIP text (eip-8025-post-pr.md) is the primary
  normative extraction source. The pre-PR EIP text is the comparison
  source for determining pr_delta; review threads are used only to
  determine contested status and related notes. The eip-files/ diffs
  are NOT inputs to this session and are not evidence for any claim
  in its outputs.

## Task
1. Relevance triage: triage exactly the files listed in
   spec-manifest.txt, in manifest order. Read only manifest-listed
   files. If a manifest-listed file is missing from spec-files/:
   stop, report. If spec-files/ contains files not in the manifest:
   do not read them; list them in session output under "Unexpected
   files" and continue. For each triaged file, one line in the
   output's "Input triage" section: relevant / irrelevant to
   EIP-8025, with a one-clause reason citing the file
   ([raw:spec-files/<name>]). Irrelevant files contribute no
   requirements.
2. Extract every operative normative statement keyed by an UPPERCASE
   RFC-style keyword: MUST, MUST NOT, SHOULD, SHOULD NOT, MAY. Treat
   SHALL as MUST, SHALL NOT as a MUST-level prohibition, and REQUIRED
   as MUST; record negative polarity in paraphrased_requirement
   (e.g. "must not ..."). Lowercase normative-sounding prose (e.g.
   "the node must reject") does not generate a row; where such prose
   appears to impose a real obligation not covered by any
   uppercase-keyed row, record it in session output as an observation
   or proposed open question — never as a silent row and never
   silently dropped.
   Extract from eip-8025-post-pr.md top-to-bottom, then from relevant
   spec files in manifest order, each top-to-bottom. req_ids are
   assigned in this extraction order; a source=both row's req_id is
   assigned at its EIP-side position.
   An occurrence is operative only where the text imposes the
   obligation; keywords in examples, quotations, or rationale
   narrative are not extracted. Borderline uppercase cases get a row
   plus an explanatory note rather than silent omission.
   During the pre/post comparison: requirements present in
   eip-8025-pre-pr.md but absent from the post-PR text are never TSV
   rows. List them in session output under "Removed by PR".

## "Removed by PR" scope
"Removed by PR" is a supplemental, best-effort audit section: note
removals encountered while determining pr_delta. It does not require
an exhaustive extraction pass over the pre-PR document and is not
part of the completeness criterion for the post-PR requirement
matrix.

## pr_delta determination
Operative normative text: the sentence or sentences that impose the
obligation, excluding headings, examples, surrounding rationale, and
pure formatting. Changes outside the operative normative text never
change pr_delta.
Whitespace normalization (used by the unchanged comparison): replace
every contiguous run of whitespace characters with one ASCII space
and trim leading and trailing whitespace. Preserve all words,
punctuation, capitalization, normative-keyword spelling, and
polarity. Formatting-only rewrapping or paragraph joins that change
no words or punctuation are therefore ignored; any remaining textual
difference is modified.
Two distinct matching concepts apply in this prompt:
- EQUIVALENCE (used only for source=both): the strict four-element
  test — the actor bound; the triggering condition; the normative
  strength and polarity (one combined element); and the required
  behavior — all agree.
- CORRESPONDENCE (used only for pr_delta): the pre-PR and post-PR
  provisions represent the same obligation lineage across revisions,
  considering their subject, actor, section context, triggering
  condition, normative strength and polarity, and required behavior;
  one or more of those elements may have changed across revisions.
  A change in normative strength (e.g. MUST to SHOULD) or polarity
  (obligation to prohibition) does not break lineage: where
  correspondence otherwise holds, such rows are modified, not added.
Match post-PR requirements to pre-PR requirements by correspondence,
not strict equivalence. Classify:
- added — no plausible corresponding pre-PR obligation exists, based
  on a confident review of the complete pre-PR text.
- unchanged — a corresponding pre-PR obligation exists and the
  operative normative text is identical after whitespace
  normalization as defined above.
- modified — a corresponding pre-PR obligation exists but its
  operative normative text differs after normalization; this
  includes editorial changes, substantive changes (including
  strength or polarity changes), splits, and combinations.
Pre-PR locator requirement: for every pr_delta=unchanged or modified
row, notes identifies the corresponding pre-PR section heading plus
enough searchable text or another stable locator to find the matched
obligation in eip-8025-pre-pr.md, ending with
[raw:eip-8025-pre-pr.md]. A short distinctive phrase is permitted as
a locator — it is a finding aid, not content reproduction, and does
not relax the rule that paraphrased_requirement contains paraphrase
only. For splits or combinations, identify every matched pre-PR
obligation.
Split and combined obligations: a post-PR row may correspond to
multiple pre-PR obligations (the PR combined them), and multiple
post-PR rows may correspond to one pre-PR obligation (the PR split
it). Such rows are classified modified. Explain every split or
combination in notes, with locators for each matched pre-PR
obligation, ending with [raw:eip-8025-pre-pr.md].
Ambiguous correspondence: if a plausible but uncertain pre-PR
correspondence exists, classify the row as modified — never added —
explain the uncertainty and the rejected alternative in notes with
supporting citations, and propose an unnumbered open question in
session output. Use added only where review of the complete pre-PR
text supports a confident finding that no corresponding obligation
exists.

## Staged output format
Write this TSV first to "$TMP_TSV". After validation, publish it as
"$FINAL_TSV" according to the output-staging rules above.

TSV, not Markdown. Tab-separated header row (9 columns):
req_id	source	section	paraphrased_requirement	rfc_level	pr_delta	contested	evidence	notes
- req_id: R001, R002, ... zero-padded, in extraction order.
- source: EIP-PR | spec-snapshot | both. "spec-snapshot" or "both"
  only for requirements actually present in a relevant spec file.
- section: the exact heading (and subsection where present) in the
  source document — this column is the precise locator; evidence
  carries file pointers only. For source=both, format as:
  EIP: <heading> | spec: <heading>
- paraphrased_requirement: your words, one sentence where possible.
  Do not copy source sentences verbatim.
- rfc_level: MUST | SHOULD | MAY. Negative polarity lives in
  paraphrased_requirement, never in this column.
- pr_delta: unchanged | modified | added | n/a, per the pr_delta
  determination rules above. Compared against eip-8025-pre-pr.md.
  For source=both, pr_delta describes the EIP side of the merged
  requirement; n/a is used only for spec-snapshot rows with no EIP
  side.
- contested: yes | no. Every contested=yes row includes BOTH, in
  notes: (1) enough metadata to locate the specific comment uniquely
  by search within eip-threads.md — e.g. author and timestamp, thread
  path/position, comment identifier, or a short distinctive quoted
  phrase — and (2) the pointer [raw:eip-threads.md]. An objection in
  a thread marked isResolved:true, or withdrawn in-thread, is
  recorded in notes but is not by itself contested=yes.
- evidence: required, non-empty; [raw:<path>] pointers supporting
  BOTH the requirement and its pr_delta value:
  - EIP-PR rows cite eip-8025-post-pr.md; spec-snapshot rows cite the
    relevant spec file; source=both rows cite both normative sources.
  - pr_delta=unchanged or modified: cite both eip-8025-pre-pr.md and
    eip-8025-post-pr.md; notes carries the pre-PR locator per the
    pr_delta determination rules.
  - pr_delta=added: cite both full-text artifacts, and notes states
    the negative finding explicitly — that no corresponding pre-PR
    obligation (per the correspondence rule above) was found in the
    complete pre-PR text — ending with [raw:eip-8025-pre-pr.md].
- notes: optional except where a rule above requires it; any claim in
  it ends with [raw:<path>] (or references an existing [OPEN-Qn]).
  Where spec-todos.txt marks a source location WIP/TODO/DRAFT, note
  it here with [raw:spec-todos.txt].
- Field hygiene: no tab or newline characters inside any field;
  collapse internal whitespace runs to single spaces; every data row
  has exactly 9 fields.
- The TSV contains requirement rows only — no triage lines, open
  questions, commentary, or removal records.

## source=both
One row with source=both only where the EIP and spec statements agree
on all four of: the actor bound; the triggering condition; the
normative strength and polarity (one combined element); and the
required behavior — the strict EQUIVALENCE test, which applies only
here, not to pre/post matching. If any of the four differs: separate
rows, cross-referenced by req_id in notes — the difference itself
may be proposal-relevant.

## Also produce (in session output, not the TSV)
- Input triage list (Task step 1).
- "Unexpected files" list (may be empty).
- "Removed by PR" list (may be empty).
- Any lowercase-normative observations (may be empty).
- Any new open questions: propose them in the session output; do NOT
  write to open-questions.md — I assign IDs and append manually.
Every supplemental observation, "Removed by PR" entry, lowercase-
normative observation, and proposed open question ends with
supporting [raw:<path>] citations. Existing open questions already
assigned may additionally be referenced as [OPEN-Qn]; do NOT assign
IDs to newly proposed questions. Pure filesystem facts — e.g. "an
unexpected file named X exists" — need no citation; any interpretive
statement about content, relevance, or meaning does.

## Reconciliation
Run case-sensitive, whole-word GNU grep using complete declared paths;
do not depend on the current working directory.

For the post-PR EIP:

grep -nE '\b(MUST|SHOULD|MAY|SHALL|REQUIRED)\b' \
  "$STUDY_ROOT"/notes/raw/eip-8025-post-pr.md

Run the same command separately for every relevant manifest-listed
spec file, using its complete path under:

"$STUDY_ROOT"/notes/raw/spec-files/

Include the full hit list in session output. This check covers the
normative-keyword scope of this inventory.

Mappings may be many-to-one (several hits supporting one req_id) or
one-to-many (one hit yielding several req_ids). Every hit maps to at
least one req_id or carries a one-clause not-operative disposition
(e.g. "rationale narrative", "inside example"). Every req_id traces
back to at least one uppercase keyword hit. Unmapped hits or
untraceable req_ids = not done.

## Validation
- Every data row has exactly 9 tab-separated fields.
- Every rfc_level, pr_delta, contested value is from its enum.
- Every evidence field is non-empty, satisfies the pr_delta evidence
  rules (including the explicit no-correspondence note for added),
  and every [raw:<path>] names a file that exists under notes/raw/.
- Every pr_delta=unchanged or modified row has a pre-PR locator in
  notes per the pr_delta determination rules.
- Every contested=yes row has both locator and pointer per the schema.
- Report total row count and counts grouped by source, pr_delta,
  rfc_level, and contested in session output.

## Done when
- Every operative uppercase-keyword requirement in eip-8025-post-pr.md
  and in relevant spec files has a row.
- Reconciliation list present; zero unmapped hits; zero untraceable
  req_ids.
- Validation checks pass, including pre-PR locators on every
  unchanged/modified row; all grouped counts reported.
- The validated temporary TSV was successfully published as
  "$FINAL_TSV"; "$TMP_TSV" no longer exists.
- Every contested=yes row cites a specific thread comment per the
  schema; every WIP-marked requirement is noted with
  [raw:spec-todos.txt].
- requirements.tsv published; Input triage, "Unexpected files",
  "Removed by PR", lowercase-normative observations, and proposed
  open questions present in session output (each may be empty), each
  entry cited where required by the supplemental-claims rule; no new
  [OPEN-Qn] IDs assigned.
- Nothing committed.
