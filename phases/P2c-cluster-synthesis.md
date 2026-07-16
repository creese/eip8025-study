# Phase 2c — cluster synthesis (synthesis session)

This is a SYNTHESIS session, not a harvest: reading the two declared
matrices, judging, grouping, and writing the cluster document are the
task, together with a constrained, non-evidence consultation of the
Phase 2bq lead register per the lead-register rule below. The binding
rules are: read only the declared inputs; every substantive claim
cites evidence; outputs are the cluster document, the open-question
appends, and the validation receipt defined below.
Do not commit.

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

Create "$WORK_DIR" (and "$WORK_DIR"/stderr, "$WORK_DIR"/derived) and
"$STUDY_ROOT"/notes/receipts/ if absent. Everything under "$WORK_DIR"
is temporary or diagnostic, is never evidence, and is never cited.

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
  - If "$TMP_MD" does not exist: the phase already completed. Stop
    and report; change nothing.
  - If "$TMP_MD" exists and cmp shows it byte-identical to
    "$FINAL_MD": publication completed but cleanup was interrupted.
    Remove "$TMP_MD", check the exit code, report the recovered
    completed state, and change nothing else.
  - If "$TMP_MD" exists but differs from "$FINAL_MD": stop and
    report the inconsistent state; change nothing.
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
     flagging whether this 
     looks like a Lighthouse gap or a requirement-inventory gap (or
     out of scope for a CL client), with a one-clause reason, cited,
     and PROV-SPEC-tagged where the PROV-SPEC rule applies;
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
