# Phase 2c — cluster synthesis (synthesis session)

This is a SYNTHESIS session, not a harvest: reading the two declared
matrices, judging, grouping, and writing the cluster document are the
task, together with a constrained, non-evidence consultation of the
Phase 2bq lead register per the lead-register rule below. The binding
rules are: read only the declared inputs; every substantive claim
cites evidence; outputs are the cluster document, the open-question
appends, and the validation receipt defined below.
Do not commit.

Remediation status (2026-07-16): the Phase 2c completion audit
reported BLOCK on one criterion — an uncited substantive sentence in
the introductory prose of notes/02-clusters.md (the defect and the
sole authorized corrective execution are defined in the Remediation
R1 section below). Until R1 completes, the published Phase 2c state
is not audit-complete. R1 modifies exactly one published derived
artifact ("$FINAL_MD", only within the defective sentence and only
under the R1 guards), publishes exactly one new durable artifact
("$R1_RECEIPT"), preserves the original validation receipt
unmodified as the historical record of the original execution, and
changes nothing else. This revised specification is executable only
after the user has reviewed its actual git diff and explicitly
authorized execution.

This session performs no live research: it must not read anything
under repos/, must not use network access, and must not read files
under notes/raw/ (raw pointers are propagated from matrix evidence
fields and existence-checked only, per the citation rules below). It
touches no pinned repository content, so no pin-verification gate
applies; if any step nevertheless seems to require repository or
network inspection, stop and report instead.

STUDY_ROOT=/work

## Paths

FINAL_MD="$STUDY_ROOT"/notes/02-clusters.md
OPEN_Q="$STUDY_ROOT"/notes/open-questions.md
RECEIPT="$STUDY_ROOT"/notes/receipts/P2c-validation-receipt.md
WORK_DIR="$STUDY_ROOT"/.work/p2c
TMP_MD="$WORK_DIR"/02-clusters.md.tmp

Remediation R1 paths (used only by the Remediation R1 section):

R1_WORK="$STUDY_ROOT"/.work/p2c-r1
R1_PRISTINE="$R1_WORK"/02-clusters.pre.md
R1_TMP="$R1_WORK"/02-clusters.corrected.md
R1_BASELINE="$R1_WORK"/baselines.txt
R1_RECEIPT_STAGE="$R1_WORK"/receipt.tmp
R1_RECEIPT="$STUDY_ROOT"/notes/receipts/P2c-R1-remediation-receipt.md

Create "$WORK_DIR" (and "$WORK_DIR"/stderr, "$WORK_DIR"/derived) and
"$STUDY_ROOT"/notes/receipts/ if absent. Everything under "$WORK_DIR"
is temporary or diagnostic, is never evidence, and is never cited.
"$R1_WORK" (and "$R1_WORK"/stderr) is created only when Remediation
R1 runs; everything under it is likewise temporary, never evidence,
and never cited. R1_TMP is deliberately distinct from TMP_MD so the
original resume rules and R1 never misread each other's staged
files.

## Preconditions (verify before any task step; stop if unmet)

These artifacts must already exist:
- "$STUDY_ROOT"/notes/matrix/lh-files.tsv   (Phase 2b classification)
- "$STUDY_ROOT"/notes/matrix/requirements.tsv (Phase 1b inventory)
- "$STUDY_ROOT"/notes/leads/p2b-lead-register.md (Phase 2bq lead
  register; discovery input only, never evidence)
- "$OPEN_Q"                                  (append-only question log)
- "$STUDY_ROOT"/notes/refs.md                (context: pins, PROV-SPEC,
  [OPEN-Q1])
If any is absent: stop, report which, and do not substitute another
source.

Then apply the resume rules:
- If "$FINAL_MD" and "$RECEIPT" both exist AND the last line of
  "$RECEIPT" is the literal end-marker "END-OF-RECEIPT P2c":
  Whenever a sub-case below reports a completed state, first compare
  the lead register's current SHA-256 against the value recorded in
  "$RECEIPT", and report exactly one of two distinct completed
  states:
  - register hash matches: a completed, independently re-auditable
    phase state (unqualified completion);
  - register absent or hash differs: a completed, publication-time
    validated artifact set that is NOT currently independently
    re-auditable. The published synthesis stands (its claims never
    rest on the register) and nothing is changed or overwritten, but
    the execution-time lead set and per-lead disposition accounting
    can no longer be re-derived from the declared input: the receipt
    is their sole surviving record. Report the delta (recorded vs
    observed hash, or absence) and leave the disposition — accept
    the receipt's record, restore the register, or another remedy —
    to the user.
  A completed state must never be reported without this comparison,
  and the second state must never be reported as unqualified
  completion.
  - If "$TMP_MD" does not exist: the original execution already
    completed. Apply the R1 dispatch below.
  - If "$TMP_MD" exists and cmp shows it byte-identical to
    "$FINAL_MD": publication completed but cleanup was interrupted.
    Remove "$TMP_MD", check the exit code, report the recovery,
    change nothing else under the original procedure, and apply the
    R1 dispatch below.
  - If "$TMP_MD" exists but differs from "$FINAL_MD": stop and
    report the inconsistent state; change nothing.
  R1 dispatch (2026-07-16 amendment): with the original execution
  complete, enter Remediation R1 at its entry classification R1.0
  and nothing else. R1.0 classifies every fresh, interrupted, and
  completed R1 state — keyed on "$R1_RECEIPT" and the R1 working
  files — before any R1 write; only a state R1.0 classifies as
  fresh begins at R1.1, and an absent "$R1_RECEIPT" alone never
  implies a fresh run. Do not report the phase as complete unless
  the R1.7 completed-state handling reports it.
  Any completed state reported after this dispatch remains subject
  both to the lead-register comparison above and to R1.7's POST_SHA
  comparison; neither may be skipped.
  A receipt file lacking the final end-marker is an interrupted
  receipt write, never a completed phase.
- If "$FINAL_MD" exists and "$RECEIPT" is absent or lacks the
  end-marker: interrupted publication — follow "Recovery from
  interrupted publication" below and perform no other work.
- If "$FINAL_MD" does not exist but "$TMP_MD" exists: a prior run was
  interrupted before publication. Do not extend or edit the stale
  draft incrementally. First, if "$TMP_MD" contains any
  [[OPEN-Q-PENDING:<subject>]] placeholder tokens, run step 6 (its
  duplicate guard absorbs questions the interrupted run already
  appended) to resolve them. Then re-run every check in "Validation"
  against "$TMP_MD", re-deriving the lead dispositions per the
  lead-register rule if the prior run's records under
  "$WORK_DIR"/derived are absent. If all pass, continue at
  "Publication"; if any fails, preserve "$TMP_MD", stop, and report
  the failing checks (do not delete a file this session did not
  create).
- Otherwise (neither final nor staged output exists): proceed with a
  fresh run.

## Declared inputs — read these and nothing else

- "$STUDY_ROOT"/notes/matrix/lh-files.tsv
- "$STUDY_ROOT"/notes/matrix/requirements.tsv
- "$STUDY_ROOT"/notes/leads/p2b-lead-register.md (discovery input
  only, per the lead-register rule below: it directs attention within
  the two matrices and contributes no claims, no citations, and no
  question subjects; this phase never modifies it)
- "$OPEN_Q" (existing question IDs; append target per the procedure
  below)
- "$STUDY_ROOT"/notes/refs.md (context only: PROVISIONAL-SPEC-BASELINE
  / PROV-SPEC decision and [OPEN-Q1]; contributes no claims of its
  own)
Do not read other notes files, phase files, briefs, raw artifacts,
repo clones, or the live web. If a needed input is missing, stop and
say so.

(Remediation R1 runs only from the completed-state R1 dispatch and
declares its own inputs in its section — including read-only
"$FINAL_MD" and "$RECEIPT", which the original execution wrote
rather than read. The list above governs the original execution;
the R1 list governs R1. The two never apply in the same session.)

## Column identification (stop if ambiguous)

The matrices are consumed through their own header rows, not through
a schema recalled from another phase.

From the requirements.tsv header identify:
- the requirement identifier column (req_id values, format R-prefixed
  zero-padded numbers);
- the provenance column distinguishing EIP-derived requirements from
  spec-snapshot-derived requirements (used by the PROV-SPEC rule).

From the lh-files.tsv header identify:
- the row identifier column (its values are what [row_id] citations
  name);
- the requirement-mapping column (holding one or more req_id tokens,
  or NONE);
- the cluster-assignment column (holding provisional-taxonomy cluster
  IDs);
- any column(s) recording evidence depth — whether implementation
  code and/or tests were read — and any column(s) or markers
  recording WIP/TODO/reverted status (used by the confidence and
  WIP-scaffolding rules; if none exist, apply the fallbacks in those
  rules).

If any of the first three lh-files.tsv columns or either
requirements.tsv column cannot be identified unambiguously from the
header and data: stop and report what was found. Never guess a
column's meaning.

## Embedded cluster taxonomy (incorporated; no execution-time dependency)

Provisional cluster taxonomy, fixed before Phase 2b and shared by 2b
and 2c:

C1 SSZ/types · C2 gossip validation · C3 Req/Resp · C4 proof engine ·
C5 EL integration · C6 block import/forkchoice · C7 config/CLI ·
C8 validator/prover service · C9 sync · C10 tests/CI · C-unassigned.

Remap rule: 2c may split or merge clusters but must append a remap
table (`old_id → new_id, date`) to notes/02-clusters.md; row
citations are interpreted through the remap. (Task step 4
operationalizes this, adding the row-level split row assignment the
table format alone cannot express.)

Shared mistake to avoid: treating WIP scaffolding (devnet hacks,
TODOs, reverted code) as Lighthouse's intended design.

## Lead-register rule (discovery input; never evidence)

Phase 2bq's lead register,
"$STUDY_ROOT"/notes/leads/p2b-lead-register.md, is a durable
non-evidence register of candidate leads distilled from the Phase 2b
session reports. For this phase it is a discovery and investigation
index only:

- Consult it solely to direct attention within this phase's declared
  evidence (the two matrices). Neither the register, its lead
  identifiers, nor the Phase 2b session reports is evidence: none of
  them may be cited or otherwise support any claim, and no citation
  form in this specification can name them.
- A lead affects "$FINAL_MD" only after it is independently
  established from this phase's declared inputs and cited under the
  citation rules below; the resulting claim must stand exactly as it
  would had the lead never existed.
- Open-question authority is unchanged: a question this phase logs
  must be motivated and cited from this phase's declared evidence
  alone, and its entry — including its subject identifier — never
  references the register or a lead identifier. That the register
  contains a similar lead neither permits nor prevents such a
  question; it is an independently derived Phase 2c question, not a
  promotion.
- Promotion proper — carrying a register lead into "$OPEN_Q" on the
  authority of the register or the Phase 2b session reports, without
  independent evidence grounding — is prohibited to this phase and
  reserved to the user.
- A lead this phase's declared evidence cannot verify enters no claim
  and no question; it remains in the register, and it is reported as
  consulted but unverifiable, for the user, in the receipt's
  lead-disposition record and in session output.

Procedure: read the register once, in full, after task step 2 and
before or during task step 3. A lead never obliges a document or
open-question change: content enters "$TMP_MD" only where the
declared evidence and this phase's schema warrant it on their own,
and disposition accounting must never expand, pad, or reshape the
document. For each lead in the register, record under
"$WORK_DIR"/derived exactly one disposition for the receipt:
- ESTABLISHED — the lead's subject is covered in "$TMP_MD" by content
  independently established and cited from the declared matrices per
  the citation rules (note where in the document; the content must be
  warranted by the schema regardless of the lead);
- NO-EFFECT — the declared evidence can verify the lead, but its
  subject warrants no coverage in "$TMP_MD" and no open question
  (e.g. immaterial to the cluster schema, out of this document's
  scope, or adding nothing the schema calls for), with a one-clause
  reason; or
- UNVERIFIABLE — the declared evidence cannot verify the lead; no
  claim and no question was entered for it.
Dispositions are finalized only once "$TMP_MD" is complete (a lead
provisionally marked one way may be reclassified before validation).
They are process records, never evidence, and are never cited from
"$FINAL_MD". If a lead identifier is textually identical to a matrix
row_id, a req_id, or another legitimately citable token, record the
collision in the receipt and apply the register-hygiene check to
register references only (the register path and register-specific
labels), never to legitimate matrix citations.

This rule adds a constrained discovery input only: this phase's
substantive scope, evidence standard, orphan analysis, cluster
taxonomy, and open-question authority are unchanged.

## Citation rules for notes/02-clusters.md

Every substantive factual or interpretive claim ends in one of:
- [row_id] — a row identifier that exists in lh-files.tsv;
- [Rnnn] — a requirement row identifier that exists in
  requirements.tsv (both matrices' row identifiers are valid matrix
  row citations);
- [raw:<path>] — a path that exists under notes/raw/. Such pointers
  may only be propagated from evidence fields of the two matrices or
  from notes/refs.md; this session verifies each cited path exists
  (filesystem test only) and does not read the file;
- [OPEN-Qn] — a question ID present in notes/open-questions.md at
  validation time (pre-existing, or appended by this phase per the
  procedure below, which runs before final citation validation).

Placeholder (drafting state only): a claim or orphan entry needing a
question that does not yet exist in "$OPEN_Q" ends, while drafting,
with the token [[OPEN-Q-PENDING:<subject>]], where <subject> is the
req_id, row_id, or short stable finding slug the question is about.
A durable [OPEN-Qn] is never invented inline; it comes only from
"$OPEN_Q" — pre-existing, reused, or appended by step 6, which then
replaces every placeholder with its durable ID. A placeholder is not
a valid citation: validation fails if any remains, so no state of
"$TMP_MD" or "$FINAL_MD" is ambiguous — placeholders mean step 6 has
not completed; durable IDs mean it has.

Citation suitability (checked in step 7): a citation must come from
a source able to bear the claim. Any claim asserting what the
Lighthouse PR implements or how it behaves, its test coverage or
gap, its evidence depth, or a confidence basis must include at least
one [row_id]. [Rnnn] supports only claims about a requirement's
content, provenance, level, or scope — alone it never supports a
statement about Lighthouse implementation, behavior, tests, or
confidence. A [raw:<path>] pointer carries at most the same kind of
support as the matrix evidence field it was propagated from
(lh-files.tsv-propagated paths for Lighthouse-side claims;
requirements.tsv-propagated paths for requirement-side claims), and
never substitutes for the required [row_id]. [OPEN-Qn] marks a claim
as logged and unresolved; it supports no positive implementation or
behavior claim by itself.

Headings, document structure, and purely connective text are exempt.
Per-cluster row-count statements and the zero-row annotation for an
empty cluster are treated as structural annotations: their mechanical
basis is recorded in the validation receipt, and any interpretation
added to them (e.g. why a cluster is empty) is a claim requiring a
citation as above. Where a claim is interpreted through the remap
table, the cited [row_id] still names the lh-files.tsv row verbatim;
the remap table, not an edited citation, carries the reassignment.

The lead register and its lead identifiers are never citations and
never appear in the document (lead-register rule).

Uncited substantive claims are not permitted, including any
introduced or strengthened while tightening prose.

## PROV-SPEC rule (decision recorded in notes/refs.md)

The spec snapshot behind requirements.tsv is a
PROVISIONAL-SPEC-BASELINE ([OPEN-Q1]). Any claim, coverage statement,
gap assessment, orphan classification, or proposal implication whose
force depends on a spec-snapshot-derived requirement (per the
provenance column) must carry the tag PROV-SPEC and may not support a
conclusion stronger than needs-input until [OPEN-Q1] is resolved. For
requirements recorded as present in both the EIP and the spec
snapshot, the tag is required only where the claim depends on the
spec-side text as distinct from the EIP text. A provisional-baseline
mismatch is never a confirmed finding. If the provenance column
cannot be identified, stop (see Column identification).

## WIP-scaffolding rule

Where lh-files.tsv marks a row WIP, TODO, devnet-hack, reverted, or
scaffolding, claims resting on that row must present it as
provisional scaffolding, never as Lighthouse's finalized intended
design; say explicitly which it is. If lh-files.tsv carries no such
markers at all, note that limitation once in the document (citing at
least one row whose content motivated the caution, or omitting the
note if no caution is warranted) and do not infer WIP status from
memory.

## Confidence rule

Confidence is assigned per cluster as H, M, or L with a one-clause
basis:
- H only where the cited rows record that both implementation code
  and its tests were read for the material supporting the cluster's
  claims;
- M where the cited rows record implementation code read but not
  tests;
- L where claims are inferred from paths, names, or metadata alone.
This session reads no code: confidence is capped by what the cited
rows themselves record. If lh-files.tsv has no evidence-depth
column(s), confidence is capped at M for rows whose content
demonstrates code-level description and L otherwise, and the
per-cluster basis clause must say the cap was applied.

## Command success and stderr policy

Every command that produces evidence, builds a derivation, changes
project state, or gates later work: capture stderr to a file under
"$WORK_DIR"/stderr/, and check the exit code immediately.
- For grep used to test presence/absence: exit 0 = matches, exit 1 =
  no matches (a valid result for absence derivations), exit ≥ 2 =
  failure — stop.
- For every other command: nonzero exit = failure — stop.
- Non-empty stderr on an otherwise successful command: stop and
  report unless the content is demonstrably benign and quoted in the
  session report.
On any such stop: report the step, the command, expected vs observed;
preserve "$WORK_DIR" contents and any staged files; delete nothing
under notes/; never partially publish.

## Task

1. Mechanical input validation. Verify every data row of each matrix
   has the same field count as its header. Verify every
   cluster-assignment value is a taxonomy ID from the embedded list
   (C1–C10 or C-unassigned). Verify every requirement-mapping token
   is NONE or matches the req_id format, splitting multi-token fields
   on their evident separator. Verify every non-NONE token names a
   req_id present in requirements.tsv. Any violation is a cross-phase
   input inconsistency: stop and report it (row, field, observed
   value); never repair, drop, or reinterpret an input row.

2. Mechanical derivations. Under "$WORK_DIR"/derived, build and save:
   (a) per-cluster row lists and counts; (b) orphan list A — every
   req_id in requirements.tsv referenced by zero lh-files.tsv rows;
   (c) orphan list B — every lh-files.tsv row whose
   requirement-mapping is NONE. Record the exact commands and
   resulting counts for the receipt. These derived files are working
   copies, never evidence.

3. Cluster synthesis. The lead register may direct attention here
   per the lead-register rule (its consultation procedure runs after
   step 2 and before or during this step); nothing it contributes is
   citable, and every claim must stand on the declared matrices
   exactly as it would had the register never existed. Write the
   document to "$TMP_MD" only (never directly to "$FINAL_MD"), in
   small bounded chunks — do not embed the whole document in one
   shell command or heredoc. One section
   per final (post-remap) cluster, covering every cluster that is a
   taxonomy ID or a remap new_id. Each nonempty cluster section
   contains, in order:
   - req_ids: the req_ids its rows map to;
   - claim bullets: what the Lighthouse PR does for this cluster,
     each claim ending [row_id], [Rnnn], [raw:<path>], an existing
     [OPEN-Qn], or — where a new question is needed — the
     [[OPEN-Q-PENDING:<subject>]] placeholder resolved by step 6;
     citation suitability per the citation rules;
   - test coverage or gap: a cited statement of what test evidence
     the rows record, or the gap;
   - confidence: H/M/L with basis per the confidence rule;
   - proposal implications: bullets only, no prose paragraphs, each
     cited.
   An empty final cluster gets a one-line section noting zero
   assigned rows (structural annotation; mechanical basis in the
   receipt), plus a cited claim or a logged open question only if
   interpretation is offered.

4. Split/merge and remap table. Splitting or merging provisional
   clusters is permitted where the rows support it. The document ends
   with a remap table with columns `old_id → new_id, date` (today's
   date). If no split or merge was made, the table is present with a
   single explicit row stating "no remaps — identity mapping" and the
   date. Where a split maps one old_id to more than one new_id, the
   table alone cannot place rows: immediately after it, a "Split row
   assignment" block lists, for each split old_id, every lh-files.tsv
   row_id assigned to that old cluster under exactly one of its
   new_ids — complete (every affected row appears) and disjoint (no
   row appears twice), so each affected row has exactly one
   destination. Merges need no row list: every row of each merged
   old_id goes to the single new_id. Every new_id is defined by its
   section; row citations are interpreted through the remap table
   plus, for splits, the split row assignment.

5. Orphan lists. After the remap table, two lists, both always
   present (explicitly "empty" if so):
   - List A — req_ids with zero rows: one entry per orphan req_id that
     includes [Rnnn] as its requirement citation; an assessment
     flagging whether this looks like a Lighthouse gap or a
     requirement-inventory gap (or out of scope for a CL client),
     with a one-clause reason, cited, and PROV-SPEC-tagged where the
     PROV-SPEC rule applies;
   - List B — rows with req_id NONE: one entry per orphan row,
     ending-citation [row_id]; a one-clause disposition (e.g.
     infrastructure, scaffolding, mapping gap), cited.
   Every entry in either list is resolved (its assessment fully
   explains the orphan, with citations) or logged (it ends with an
   existing [OPEN-Qn], or with the [[OPEN-Q-PENDING:<subject>]]
   placeholder that step 6 replaces with the durable ID).
   List membership must match the step-2 derivations exactly.

6. Open-question logging (the only write to "$OPEN_Q"). This file is
   append-only: never edit, reorder, or delete existing content, even
   during recovery; questions appended by a run whose later steps
   failed are durable — they are reused via the duplicate guard,
   never removed.
   - Record "$OPEN_Q"'s line count and SHA-256 now, before any
     append and even if none turns out to be needed (the receipt
     records them in every case; with zero appends the post-check is
     that the whole file still hashes to the recorded value).
   - Tail integrity: verify "$OPEN_Q" ends with a newline and, if a
     Phase 2c provenance marker appears in the file, that every such
     entry is complete and well-formed (ID line through provenance
     marker). A trailing or truncated Phase 2c entry from an
     interrupted earlier append means: stop and report the exact
     tail state — append-only forbids repairing it; the user
     decides. The duplicate guard matches only complete entries.
   - Pending subjects are every distinct [[OPEN-Q-PENDING:<subject>]]
     placeholder in "$TMP_MD", plus any other finding this phase must
     log (each under a stable subject identifier: req_id, row_id, or
     finding slug — never a lead identifier or a reference to the
     lead register, per the lead-register rule). For each subject:
     - Duplicate guard: search "$OPEN_Q" for an existing question
       whose provenance marker names Phase 2c and the same subject
       identifier. If a complete such entry exists — including one
       appended by an interrupted earlier run — reuse its ID; do not
       append again.
     - Otherwise append exactly one question with a single append
       command writing the complete entry (one entry per command;
       check its exit code before the next append), assigning the
       next monotonic [OPEN-Qn] ID strictly greater than every ID
       present in the file at append time (IDs are never reused or
       renumbered, and are assigned only at this write — never
       inline earlier). The entry's final line is its provenance
       marker naming Phase 2c, today's date, and the subject
       identifier, so the duplicate guard, placeholder resolution,
       and the audit can match it and detect truncation.
   - After the last append (if any), verify the first <recorded line
     count> lines still hash to the recorded value (append-only
     proof). Record both hashes and the full subject → [OPEN-Qn]
     mapping (appended and reused) in the receipt. If the prefix
     check fails: stop and report; do not attempt to repair the
     file.
   - Resolve placeholders: replace every [[OPEN-Q-PENDING:<subject>]]
     in "$TMP_MD" with its durable [OPEN-Qn] from the mapping.
   Run this step before final citation validation, so every [OPEN-Qn]
   cited in "$TMP_MD" exists in "$OPEN_Q" and no placeholder remains
   when checked.

7. Validation (all against "$TMP_MD"; record each check and result
   for the receipt):
   - Structure: one section per final cluster; each nonempty section
     has all five schema elements in order; proposal implications
     contain bullets only; remap table present and well-formed; both
     orphan lists present.
   - Citations: every claim bullet, coverage statement, confidence
     basis, implication bullet, and orphan entry ends with a valid
     citation token; every [row_id] and [Rnnn] exists in its matrix;
     every [raw:<path>] exists under notes/raw/ (existence test
     only); every [OPEN-Qn] exists in "$OPEN_Q"; no
     [[OPEN-Q-PENDING:<subject>]] placeholder remains anywhere in
     the document.
   - Citation suitability: every claim asserting Lighthouse
     implementation, behavior, test coverage or gap, evidence depth,
     or a confidence basis carries at least one [row_id]; no such
     claim rests on [Rnnn] or requirements-side [raw:] pointers
     alone (citation-suitability rule).
   - Orphan completeness: List A and List B membership and counts
     equal the step-2 derivations; every entry resolved or carrying
     an [OPEN-Qn].
   - Cluster coverage and partition: every lh-files.tsv row maps
     through the remap table (and, for splits, the split row
     assignment) to exactly one final cluster that has a section;
     each split old_id's rows appear exactly once across its
     new_ids' assignment lists; the per-final-cluster counts sum to
     the lh-files.tsv data-row count (counts recorded in the
     receipt); every cluster ID used in the document is a taxonomy
     ID or a remap new_id.
   - PROV-SPEC: every claim depending on spec-snapshot-derived
     requirements carries the tag; no such claim concludes stronger
     than needs-input.
   - Confidence: every value is H, M, or L with a basis clause
     consistent with the confidence rule.
   - Hygiene: no reference to files under .work/ or /tmp anywhere in
     the document.
   - Lead-register hygiene and disposition: no reference to the lead
     register (its path or register-specific labels) and no lead
     identifier present in the register appears anywhere in "$TMP_MD"
     or in any open-question entry this phase appended (subject to
     the collision handling in the lead-register rule); every lead in
     the register has exactly one recorded disposition (ESTABLISHED
     with its document location, NO-EFFECT with its one-clause
     reason, or UNVERIFIABLE) for the receipt; the register file is
     unmodified (its SHA-256 still matches the value recorded when
     it was read).
   If any check fails: fix the specific error in "$TMP_MD" and re-run
   the failed checks, or — if the failure traces to the inputs —
   stop and report. Never publish an unvalidated document. On a stop
   before publication, preserve "$TMP_MD" and "$WORK_DIR" and report
   which staged paths exist; remove "$TMP_MD" only if this session
   created it AND the stop reason makes its content worthless (e.g.
   wrong inputs), stating so in the report.

8. Publication (only after every validation check passes), in this
   order:
   a. Confirm "$FINAL_MD" still does not exist; if it now exists,
      stop and report (no-clobber — never overwrite).
   b. cp "$TMP_MD" "$FINAL_MD"; check the exit code.
   c. cmp "$TMP_MD" "$FINAL_MD"; check the exit code (byte-identical
      proof; "$TMP_MD" is retained until the receipt is written so an
      interruption here is recoverable).
   d. Write the receipt (contents below) to "$WORK_DIR"/receipt.tmp,
      never directly to "$RECEIPT"; its last line is the literal
      end-marker "END-OF-RECEIPT P2c". Check the exit code and that
      the end-marker is the file's final line.
   e. cp "$WORK_DIR"/receipt.tmp "$RECEIPT"; check the exit code.
      cmp "$WORK_DIR"/receipt.tmp "$RECEIPT"; check the exit code.
   f. rm "$TMP_MD"; check the exit code.
   If b–f fails at any point: preserve everything, report exactly
   which of "$TMP_MD", "$FINAL_MD", "$RECEIPT", and
   "$WORK_DIR"/receipt.tmp exist and the failing step, and do not
   claim completion.

## Recovery from interrupted publication

If "$FINAL_MD" exists and "$RECEIPT" is absent or lacks the final
end-marker line:
- If "$TMP_MD" exists and cmp shows it byte-identical to "$FINAL_MD":
  the copy succeeded; resume at step 8d (write the staged receipt
  from the preserved derivations, lead dispositions, and validation
  results — re-running step 7 checks against "$FINAL_MD" first, and
  re-deriving the lead dispositions per the lead-register rule, if
  the prior run's records were not preserved), then 8e–8f. At 8e, an
  existing incomplete
  "$RECEIPT" may be overwritten only if it is a byte prefix of the
  complete staged receipt (an interrupted copy of the same content);
  otherwise stop and report both files' states and wait for the
  user's decision.
- If "$TMP_MD" is missing, or differs from "$FINAL_MD": stop, report
  the exact state (which files exist; the cmp result; whether
  "$RECEIPT" exists and whether it carries the end-marker), change
  nothing, and wait for the user's decision.
Open-question appends from the interrupted run are handled by the
step-6 duplicate guard, never by editing "$OPEN_Q". Placeholder
tokens cannot appear here: a placeholder-bearing draft never passes
validation, so it is never published.

## Remediation R1 — sole authorized corrective execution (2026-07-16)

### R1 trigger and scope

The 2026-07-16 Phase 2c completion audit reported BLOCK on one
criterion: a sentence in the introductory prose of "$FINAL_MD"
asserts that lh-files.tsv records per-row test evidence but has no
column recording that implementation code was read, uses that
assertion as the document-wide confidence-cap basis, and ends with
no citation token — violating citation termination and the
citation-suitability rule (an evidence-depth or confidence-basis
claim must carry at least one [row_id]). The execution-time and
initial audit checks scanned bullet and labeled-element lines only,
so the substantive prose paragraph escaped both.

R1 corrects exactly that defect and re-establishes auditable
completion. It:
- edits "$FINAL_MD" only within the defective sentence, under the
  guards below, staging and validating the corrected document before
  a guarded in-place replacement;
- extends citation-termination and citation-suitability checking to
  substantive prose paragraphs (the R1 extended scan below), both
  for R1's validation and for the later audit;
- publishes exactly one new durable artifact, "$R1_RECEIPT";
- preserves "$RECEIPT" unmodified as the historical record of the
  original execution — it is never rewritten, appended to, or
  restated;
- re-runs no task step 1–8; reassesses no cluster content,
  confidence value, orphan disposition, remap entry, or open
  question; and appends nothing to "$OPEN_Q";
- reads no repo clone and no network source; never reads the lead
  register or notes/refs.md (each is hashed only, to prove it
  unchanged); and creates, modifies, and deletes nothing under
  notes/raw/ ([raw:] citations are existence-tested only).
If any R1 step appears to require more than this, stop and report:
a defect beyond the one described above is a new audit finding for
the user, never a silent extension of this remediation.

R1 declared inputs — read these and nothing else: "$FINAL_MD" (the
correction target), "$RECEIPT" (read-only cross-check source),
lh-files.tsv and requirements.tsv (token resolution and the
exemplar row), and "$OPEN_Q" (read-only, [OPEN-Qn] resolution).
Hash-only, never read: notes/leads/p2b-lead-register.md,
notes/refs.md, and files under notes/raw/ (aggregate digest and
existence tests only). The Column identification, Citation rules,
and Command success and stderr policy sections of this specification
govern R1, with stderr files under "$R1_WORK"/stderr/.

### R1 extended scan (definition; used by R1.2 and R1.4)

Segment the document into blank-line-separated blocks. Heading
lines, table rows (including the remap table), and the split
row-assignment block are structural. Bullet and labeled-element
lines are checked exactly as in task step 7. Every remaining prose
paragraph is split into sentences, and each sentence is classified
substantive or purely connective/structural; every exemption
(connective/structural sentence) is listed in the R1 receipt with a
one-clause justification — an unlisted exemption is a scan failure.
Every substantive sentence must end with a valid citation token that
resolves per the citation rules, and the citation-suitability rule
applies to it: a sentence asserting Lighthouse implementation or
behavior, test coverage or gap, evidence depth, or a confidence
basis must include at least one [row_id]. Structural-annotation
treatment (row-count statements; the column-identification note
produced by R1.3) applies only where this specification grants it
and its mechanical basis is recorded in a receipt.

### R1 procedure

R1.0 Entry classification (the sole R1 entry point; the resume
rules' R1 dispatch always routes here).

Classification is read-only over the classified R1 state: the R1
working files ("$R1_TMP", "$R1_PRISTINE", "$R1_BASELINE",
"$R1_RECEIPT_STAGE", "$R1_RECEIPT") and "$FINAL_MD". Until a branch
below has been selected, R1 must not create, modify, move, or
delete any of those files, so an interrupted run is never
re-baselined or overwritten before it has been classified. Stderr
and diagnostic files under "$R1_WORK"/stderr/ are session
by-products, never part of the classified working state; writing
them does not breach this rule.

Stderr bootstrap — before the first classification command, and the
sole R1 exception to the normal stderr location, because
"$R1_WORK"/stderr may not yet exist:
  R1_BOOT_ERR=/tmp/p2c-r1-bootstrap.stderr
  mkdir -p "$R1_WORK"/stderr 2>"$R1_BOOT_ERR"
Check the exit code; on failure stop and report the command, its
exit code, and "$R1_BOOT_ERR". On success, move the bootstrap file
into the declared temporary area:
  mv "$R1_BOOT_ERR" "$R1_WORK"/stderr/bootstrap.stderr \
    2>"$R1_WORK"/stderr/bootstrap-move.stderr
and check that exit code. mkdir -p and this mv neither read nor
alter any classified file. Thereafter every R1 command — including
each R1.0 existence test, byte comparison, and hash check — follows
the normal stderr and exit-code policy.

Stale-state reset — not part of read-only classification: where a
branch below orders it, and only after classification has selected
that branch, relocate everything under "$R1_WORK" except the
current session's stderr directory into
"$R1_WORK"/superseded-<UTC timestamp>/ by rename — file contents
unmodified, nothing edited, overwritten, or deleted (temporary
files, never evidence, never deleted by R1). No branch that stops
for the user's decision performs the reset.

Take exactly one branch:
- "$R1_RECEIPT" exists (with or without its end-marker): apply
  R1.7. Do not run R1.1.
- "$R1_RECEIPT" absent and "$R1_TMP" exists: an interrupted
  remediation. Apply the receipt-absent branches of R1.7. Do not
  run R1.1.
- "$R1_RECEIPT" and "$R1_TMP" absent, but any of "$R1_PRISTINE",
  "$R1_BASELINE", or "$R1_RECEIPT_STAGE" exists: a run interrupted
  before the correction was staged, or an out-of-procedure state.
  If "$R1_PRISTINE" exists, "$FINAL_MD" is byte-identical to it,
  and — where "$R1_BASELINE" exists with its end-marker — the
  SHA-256 of "$FINAL_MD" equals its recorded PRE_SHA: no
  publication write has occurred; perform the stale-state reset
  above and start fresh at R1.1. Otherwise: stop and report the
  exact state (which R1 files exist; the comparison results);
  change nothing; the user decides.
- None of "$R1_RECEIPT", "$R1_TMP", "$R1_PRISTINE", "$R1_BASELINE",
  or "$R1_RECEIPT_STAGE" exists: a fresh remediation. If "$R1_WORK"
  contains anything besides the current session's stderr directory
  (stray diagnostics only), perform the stale-state reset above.
  Run R1.1 onward.

R1.1 State guard (any failure: stop, report, change nothing).
- Verify the last line of "$RECEIPT" is the literal end-marker
  "END-OF-RECEIPT P2c"; record ORIG_RECEIPT_SHA, its SHA-256.
- Parse from "$RECEIPT" the recorded SHA-256 values of lh-files.tsv
  and requirements.tsv; recompute both; each must match. A mismatch
  means a matrix drifted since the original execution: stop and
  report the delta; the user decides.
- Record PRE_SHA (the SHA-256 of "$FINAL_MD") and its byte count;
  copy "$FINAL_MD" to "$R1_PRISTINE"; cmp the copy (byte-identical;
  check the exit code).
- Record baseline SHA-256 values of "$OPEN_Q", the lead register
  (hash-only), notes/refs.md, and both matrices, and the baseline
  aggregate raw digest via the staged raw-digest procedure below
  with tag "pre".
- Persist the baselines before any later step runs: write
  ORIG_RECEIPT_SHA, PRE_SHA with its byte count, and every baseline
  hash above (including the baseline aggregate raw digest) to
  "$R1_BASELINE", one label TAB value TAB path line per entry, with
  the literal final line "END-OF-BASELINES P2c-R1". Check the exit
  code and that the final line is that end-marker. A baseline file
  lacking the end-marker is incomplete and must never be used.
  "$R1_BASELINE" is a process record for recovery only, never
  evidence; once "$R1_RECEIPT" is published it supersedes
  "$R1_BASELINE" as the durable record.

Staged raw-digest procedure (<tag> is pre or post; no pipelines —
each numbered command captures stderr under "$R1_WORK"/stderr/ and
has its exit code checked individually per the command success
policy before the next runs):
1. find "$STUDY_ROOT"/notes/raw -type f -print0
     > "$R1_WORK"/raw-files.<tag>.z
2. LC_ALL=C sort -z "$R1_WORK"/raw-files.<tag>.z
     > "$R1_WORK"/raw-sorted.<tag>.z
3. xargs -0 -r sha256sum < "$R1_WORK"/raw-sorted.<tag>.z
     > "$R1_WORK"/raw-sums.<tag>.txt
4. sha256sum "$R1_WORK"/raw-sums.<tag>.txt — its digest value is
   the aggregate raw digest for <tag>.
Any nonzero exit code in 1–4 is a failure: stop per the command
success policy. On a pre/post aggregate-digest mismatch, diff the
pre and post raw-sums files and report the differing paths.

R1.2 Defect confirmation (against "$R1_PRISTINE"). Run the R1
extended scan. The result must be exactly one violating sentence,
located in the introductory prose (before the document body's first
"## " heading), that (a) asserts lh-files.tsv records per-row test
evidence, (b) asserts it has no column recording that implementation
code was read, (c) presents that as the document-wide confidence-cap
basis, and (d) contains no citation token. Zero violations, more
than one, or any violation not matching (a)–(d): stop, report every
finding verbatim, change nothing; the user decides (any additional
violation is a new audit finding outside R1's authorization).

R1.3 Correction (staged only; never applied directly to
"$FINAL_MD").
- Identify from lh-files.tsv (columns per the Column identification
  section) at least one data row whose fields bear the sentence's
  evidence-depth statement: test evidence recorded for the row, and
  nothing recording that implementation code was read. If no such
  row exists, the sentence misdescribes the matrix — a defect beyond
  R1's authorization: stop and report. Record the verbatim header
  row and the qualifying row(s) verbatim for the R1 receipt.
- Copy "$R1_PRISTINE" to "$R1_TMP" and apply the minimal edit, to
  that sentence only, such that the corrected text (one sentence,
  or two if citation placement requires it):
  - asserts the same content — the same elements (a)–(c) of R1.2 —
    introducing, strengthening, weakening, or removing no other
    factual or interpretive content;
  - presents the column-level observation explicitly as the
    column-identification result for lh-files.tsv, whose mechanical
    basis (the verbatim header row) is recorded in the R1 receipt;
  - ends each substantive corrected sentence with a valid citation,
    including at least one [row_id] naming a qualifying row, per
    the citation-suitability rule.
- Nothing else in the document changes: no other sentence, bullet,
  heading, table, count, confidence value, or citation.

R1.4 Validation (all against "$R1_TMP"; record each check, its
command, and its result for the R1 receipt; on any failure preserve
"$R1_WORK", stop, report, and publish nothing).
- diff -u "$R1_PRISTINE" "$R1_TMP" produces exactly one hunk,
  confined to the corrected sentence's line(s); record the unified
  diff verbatim.
- R1 extended scan of "$R1_TMP": zero violations; the exemption
  list is recorded.
- Full-document token resolution: every [row_id] and [Rnnn] in
  "$R1_TMP" exists in its matrix; every [raw:<path>] exists under
  notes/raw/ (existence test only); every [OPEN-Qn] exists in
  "$OPEN_Q"; no [[OPEN-Q-PENDING:<subject>]] placeholder remains
  anywhere; no reference to .work/ or /tmp; the diff introduces no
  reference to the lead register (its path or register-specific
  labels).
- The corrected sentence's [row_id] token(s) name exactly the
  verified qualifying row(s) from R1.3.

R1.5 Publication (guarded in-place replacement — the only write to
"$FINAL_MD" that R1 permits, and R1's sole authorized exception to
treating the published document as fixed), in this order:
a. Recompute the SHA-256 of "$FINAL_MD"; it must equal PRE_SHA. If
   not, the document changed outside this procedure: stop, report
   both hashes, publish nothing.
b. cp "$R1_TMP" "$FINAL_MD"; check the exit code.
c. cmp "$R1_TMP" "$FINAL_MD"; check the exit code.
d. Record POST_SHA (the SHA-256 of "$FINAL_MD") and its byte count.
e. Recompute every R1.1 baseline hash (the untouched inputs, plus
   the aggregate raw digest via the staged raw-digest procedure
   with tag "post") and the SHA-256 of "$RECEIPT"; each must equal
   the corresponding value read from "$R1_BASELINE", whose final
   line must be the baseline end-marker. Any difference, or a
   missing or incomplete "$R1_BASELINE": stop and report; do not
   write the R1 receipt, and never substitute freshly recorded
   values for the original baselines.
f. Write the R1 receipt (contents below) to "$R1_RECEIPT_STAGE",
   never directly to "$R1_RECEIPT"; its last line is the literal
   end-marker "END-OF-RECEIPT P2c-R1". Check the exit code and that
   the end-marker is the final line.
g. No-clobber publication: if "$R1_RECEIPT" already exists, cmp it
   against "$R1_RECEIPT_STAGE" — identical means already published
   (continue to h); different: stop, report both states, and never
   overwrite (the byte-prefix case in R1.7 is the sole exception).
   Otherwise cp "$R1_RECEIPT_STAGE" "$R1_RECEIPT" and cmp; check
   both exit codes.
h. rm "$R1_TMP"; check the exit code. "$R1_PRISTINE" and the rest
   of "$R1_WORK" remain temporary diagnostics, never evidence.
If b–h fails at any point: preserve everything; report exactly which
of "$R1_TMP", "$R1_PRISTINE", "$R1_RECEIPT_STAGE", and "$R1_RECEIPT"
exist and whether "$FINAL_MD" hashes to PRE_SHA, POST_SHA, or
neither; do not claim completion.

### R1 receipt (durable; required for the audit)

"$R1_RECEIPT" records, at minimum:
- date; "Phase 2c — Remediation R1"; the trigger: the 2026-07-16
  completion-audit BLOCK and the defect description from the R1
  trigger section;
- ORIG_RECEIPT_SHA, with confirmation that "$RECEIPT" ends with
  "END-OF-RECEIPT P2c" and hashed unchanged through R1.5e;
- the matrix-hash cross-check results against the values recorded
  in "$RECEIPT";
- PRE_SHA and POST_SHA with byte counts;
- the verbatim lh-files.tsv header row and the qualifying exemplar
  row(s) verbatim (the corrected sentence's mechanical basis);
- the exact changed text: the defective sentence verbatim, the
  corrected text verbatim, and the R1.4 unified diff verbatim;
- every R1.1, R1.2, and R1.4 check with its command and result —
  including the "$R1_BASELINE" write and its end-marker
  verification — and the full exemption list with one-clause
  justifications;
- the untouched-input verification: baseline and post-publication
  hashes for "$OPEN_Q", the lead register, notes/refs.md, both
  matrices, and the aggregate raw digest, shown equal, with the
  baseline side read from "$R1_BASELINE" (execution-time values
  persisted before any publication write, never re-recorded
  afterward);
- publication steps R1.5a–f with their exit codes. Steps g–h run
  after the receipt's content is fixed: their success is checked
  in-session and verified by the audit from final state (the
  end-marker as "$R1_RECEIPT"'s last line; "$R1_TMP" absent).
The last line is the literal end-marker "END-OF-RECEIPT P2c-R1"; a
file without it is incomplete and never establishes remediation.
The R1 receipt is a process record, never study evidence, and is
never cited from synthesis documents.

### R1.7 Completed-state handling and interruption recovery

- "$R1_RECEIPT" exists and its last line is the end-marker:
  remediation completed. Compare the SHA-256 of "$FINAL_MD" with
  the receipt's recorded POST_SHA. Equal: report the remediated
  completed state (still subject to the resume rules' lead-register
  comparison); if "$R1_TMP" still exists and is byte-identical to
  "$FINAL_MD", cleanup was interrupted — remove it, check the exit
  code, and report; if it exists and differs, stop and report.
  POST_SHA mismatch: stop and report both hashes — the document
  changed after remediation, and the user decides. Never report an
  unqualified remediated completion without this comparison.
- "$R1_RECEIPT" exists without the end-marker: interrupted receipt
  publication. If "$R1_RECEIPT_STAGE" exists, carries the
  end-marker as its last line, and "$R1_RECEIPT" is a byte prefix
  of it: first verify that the SHA-256 of "$FINAL_MD" equals the
  POST_SHA recorded in "$R1_RECEIPT_STAGE". A mismatch means the
  document changed after receipt staging: stop, report both
  hashes, and never publish a completed receipt over a document
  state it does not record; the user decides. On a match, redo
  R1.5g (this byte-prefix case is the sole permitted overwrite of
  "$R1_RECEIPT"), continue at R1.5h, and then finish through the
  completed-state branch above, whose POST_SHA comparison and
  reporting rules apply to this recovery unchanged. If
  "$R1_RECEIPT_STAGE" is absent, lacks its end-marker, or
  "$R1_RECEIPT" is not a byte prefix of it: stop and report both
  files' states; the user decides.
- "$R1_RECEIPT" absent and "$R1_TMP" exists:
  - "$R1_TMP" byte-identical to "$FINAL_MD": the replacement
    succeeded before the receipt was staged. Require both
    "$R1_PRISTINE" and a complete "$R1_BASELINE" (final line the
    baseline end-marker), and verify that the SHA-256 of
    "$R1_PRISTINE" equals the PRE_SHA recorded in "$R1_BASELINE".
    If either file is absent or incomplete, or those values
    disagree: stop and report — the execution-time baselines cannot
    be recovered, post-publication state must never be recorded in
    their place, and the user decides. Otherwise verify that diff
    of "$R1_PRISTINE" against "$FINAL_MD" is exactly one hunk
    confined to the corrected sentence, re-run the R1.4 checks
    against "$FINAL_MD", then resume at R1.5d with the
    "$R1_BASELINE" values as the R1.5e baselines.
  - "$R1_TMP" differs from "$FINAL_MD" and "$FINAL_MD" hashes equal
    to "$R1_PRISTINE" (pre-correction state): interrupted before
    publication. If "$R1_BASELINE" is complete, verify that the
    SHA-256 of "$FINAL_MD" equals its recorded PRE_SHA (mismatch:
    stop and report) and use it unchanged. Only if "$R1_BASELINE"
    is absent or incomplete — permissible solely because no
    publication write has occurred in this state — re-run R1.1 in
    full, rewriting "$R1_BASELINE"; this pre-publication restart is
    never a substitute for lost post-publication baselines. Then
    re-run every R1.4 check against "$R1_TMP"; all pass — continue
    at R1.5; any failure — preserve and report per R1.4.
  - "$R1_TMP" differs and "$FINAL_MD" matches neither: stop and
    report the inconsistent state; change nothing.
- "$R1_RECEIPT" absent, "$R1_TMP" absent, and the R1.2 scan finds
  zero violations: an unexpected document state (for example an
  unrecorded prior correction). Stop and report; never write a
  remediation receipt for work this session did not perform.

### R1 report (session output)

- The resolved R1 paths; the R1.1 guard results and baselines;
- the R1.2 finding and the R1.4 unified diff, verbatim;
- each validation check and result, and the publication exit codes;
- confirmation that "$RECEIPT", "$OPEN_Q", the lead register, both
  matrices, notes/refs.md, and everything under notes/raw/ are
  unchanged; that no repo clone or network source was read; and
  that nothing was committed;
- the complete list of files created or changed, for user review.

## Validation receipt (durable; required for the audit)

"$RECEIPT" records, at minimum:
- date; phase name; the declared input paths with their SHA-256
  checksums and data-row counts as read;
- the column-identification result for both matrices;
- the step-2 derivation commands with per-cluster row counts and both
  orphan lists (IDs and counts);
- the post-remap per-final-cluster row counts, the split
  row-assignment totals (if any splits), and the partition-check
  result (every row in exactly one final cluster; counts sum to the
  data-row total);
- each step-7 validation check, how it was performed, and its result;
- lead-register handling: the register path with its SHA-256 and lead
  count as read; the full per-lead disposition record (ESTABLISHED
  with document location, NO-EFFECT with its one-clause reason, or
  UNVERIFIABLE); any identifier collisions recorded under the
  lead-register rule; the register-hygiene check result; and
  confirmation the register was not modified. Lead
  identifiers may appear here: the receipt is a process record, never
  cited from synthesis documents;
- open-questions handling: "$OPEN_Q" pre-append line count and
  SHA-256 (recorded even when nothing was appended), the
  tail-integrity result, the post-append prefix verification result
  (or the unchanged-hash verification when zero appends), and the
  full subject → [OPEN-Qn] mapping of IDs appended and/or reused
  (possibly none);
- the publication steps through receipt staging (8a–8d) with their
  exit codes and the 8c cmp result. Steps 8e–8f run after the
  receipt's content is fixed: their success is checked in-session,
  reported in session output, and verified by the audit from final
  state (the end-marker present as "$RECEIPT"'s last line; "$TMP_MD"
  absent).
The receipt's last line is the literal end-marker "END-OF-RECEIPT
P2c"; a receipt file without it is incomplete and never establishes
completion. The receipt is a process record, not study evidence: it
is never cited from synthesis documents.

## Also produce (in session output, not in the artifacts)

- Any input anomalies observed but not blocking (may be empty).
- The list of open-question IDs appended and reused (may be empty).
- The list of register leads consulted but unverifiable from the
  declared evidence (may be empty), for the user's promotion
  decision.
- The complete list of files created or changed, for user review.
Interpretive statements in session output carry citations per the
citation rules; pure filesystem facts need none.

## Done when

- "$FINAL_MD" exists, with one section per final (post-remap)
  cluster; every nonempty section contains req_ids, cited claim
  bullets, a cited test-coverage-or-gap statement, an H/M/L
  confidence with basis, and cited bullet-only proposal implications.
- Every substantive claim in "$FINAL_MD" ends with a [row_id],
  [Rnnn], [raw:<path>], or [OPEN-Qn] citation that resolves per the
  citation rules ([raw:] paths exist under notes/raw/; row IDs exist
  in their matrices; [OPEN-Qn] IDs exist in "$OPEN_Q"); no
  [[OPEN-Q-PENDING:<subject>]] placeholder remains; every claim
  about Lighthouse implementation, behavior, test coverage, or
  confidence carries at least one [row_id] per the
  citation-suitability rule.
- The remap table is present with `old_id → new_id, date` rows or the
  explicit identity-mapping row; every split old_id has a complete,
  disjoint split row assignment; every cluster ID used in the
  document is a taxonomy ID or a remap new_id; every lh-files.tsv
  row resolves to exactly one final cluster, with the per-cluster
  counts in the receipt summing to the data-row total.
- Both orphan lists are present (explicitly empty if so), their
  membership matches the receipt's mechanical derivations, and every
  entry is resolved with citations or logged with an [OPEN-Qn].
- "$OPEN_Q" was only appended to: the receipt's prefix check (or
  unchanged-hash check when zero appends) passed, and every appended
  ID is monotonic, unreused, complete through its provenance-marker
  final line, and carries the Phase 2c provenance marker with its
  subject identifier.
- Every claim depending on the provisional spec baseline carries
  PROV-SPEC and concludes no stronger than needs-input.
- The lead register was used only as a discovery index: "$FINAL_MD"
  and every open-question entry appended by this phase contain no
  reference to the register and no lead identifier (subject to the
  recorded collision handling); the receipt records the register's
  SHA-256 and lead count as read, exactly one disposition
  (ESTABLISHED with document location, NO-EFFECT with a one-clause
  reason, or UNVERIFIABLE) per lead, and the passing
  register-unmodified check from validation time. A register change
  observed after publication does not invalidate the published
  synthesis, but it removes independent re-auditability of the
  lead-disposition accounting: the resume rules report it as a
  qualified completed state for the user's decision, never as
  unqualified completion.
- "$RECEIPT" exists, ends with the end-marker line "END-OF-RECEIPT
  P2c", and records the inputs, derivations, partition check,
  validation results, open-question handling (including the
  subject → ID mapping), and publication exit codes listed above.
- "$TMP_MD" no longer exists; nothing under notes/raw/ was created,
  modified, or deleted; no repo clone or network source was read.
- Nothing committed.

## Done when — Remediation R1 (2026-07-16 amendment)

Phase 2c is audit-complete only when every criterion above holds,
verified against the corrected "$FINAL_MD", AND all of the following
hold. The R1 receipt's confined unified diff establishes that every
original-execution validation over unchanged content still applies
to the corrected document; only the corrected sentence needs fresh
verification.

- "$R1_RECEIPT" exists at
  notes/receipts/P2c-R1-remediation-receipt.md, its last line is
  the literal end-marker "END-OF-RECEIPT P2c-R1", and it records
  the trigger, ORIG_RECEIPT_SHA, the matrix-hash cross-check,
  PRE_SHA and POST_SHA with byte counts, the exact changed text
  with its verbatim unified diff, the R1.1, R1.2, and R1.4 commands
  and results (including the pre-publication baseline persistence
  to "$R1_BASELINE" and its end-marker verification) with the full
  exemption list, the untouched-input verification, and the
  publication exit codes defined in the R1 receipt section.
- The SHA-256 of "$FINAL_MD" equals the POST_SHA recorded in
  "$R1_RECEIPT", and the receipt's unified diff — confined to the
  formerly uncited introductory sentence — is the only difference
  between the PRE_SHA document state and "$FINAL_MD".
- The corrected sentence presents the column-level observation as
  the column-identification result for lh-files.tsv, whose
  mechanical basis (the verbatim header row and qualifying exemplar
  row(s)) is recorded in "$R1_RECEIPT", and ends with at least one
  [row_id] that exists in lh-files.tsv and names a row bearing the
  recorded evidence depth.
- Under the R1 extended scan, "$FINAL_MD" contains no uncited
  substantive sentence and no citation-suitability violation in any
  prose paragraph, bullet, or labeled element; every connective or
  structural exemption is listed in "$R1_RECEIPT" with a one-clause
  justification.
- "$RECEIPT" is preserved unmodified as the historical record of
  the original execution: it still ends with "END-OF-RECEIPT P2c"
  and its SHA-256 equals the ORIG_RECEIPT_SHA recorded in
  "$R1_RECEIPT", so the original execution and this correction are
  independently verifiable, each from its own receipt.
- "$OPEN_Q", notes/leads/p2b-lead-register.md, both matrices, and
  notes/refs.md hash equal to the baselines recorded in
  "$R1_RECEIPT"; the aggregate notes/raw digest is unchanged
  (nothing under notes/raw/ was created, modified, or deleted by
  R1).
- "$R1_TMP" no longer exists; no repo clone or network source was
  read; nothing committed.
