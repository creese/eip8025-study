# Phase 2b — Lighthouse classification (batched synthesis sessions)

This is a SYNTHESIS session type, not a harvest: reading saved diffs,
judging change type and classification, and citing signals are the
task. The phase runs as a series of bounded batch sessions, each
executing this specification. The binding rules are: read only the
declared inputs; every classification cites a concrete signal; output
is the matrix defined below. Do not commit. Do not perform live
repository or network inspection of any kind; in particular, never
inspect live Lighthouse or Grandine sources. Per CLAUDE.md, do not
execute this specification until the user has reviewed its git diff
and explicitly authorized execution.

STUDY_ROOT=/work

## Fixed paths

- FINAL_TSV="$STUDY_ROOT"/notes/matrix/lh-files.tsv
- BATCH_LOG="$STUDY_ROOT"/notes/matrix/lh-files-batchlog.md
- RECEIPT="$STUDY_ROOT"/notes/matrix/lh-files-receipt.md
- WORK_DIR="$STUDY_ROOT"/.work/p2b

## Declared inputs — read these and nothing else

- "$STUDY_ROOT"/notes/raw/lh-manifest.tsv — the name-status-derived
  harvest manifest; TSV header exactly:
  `index	path	status	old_path_if_renamed	diff_file`,
  followed by 172 data rows with zero-padded contiguous indices
  001–172.
- "$STUDY_ROOT"/notes/raw/lh-files/ — one saved diff per manifest row,
  at the path named in that row's `diff_file` column.
- "$STUDY_ROOT"/notes/raw/lh-name-status.txt — used only by the
  completion reconciliation cross-check.
- "$STUDY_ROOT"/notes/raw/lh-threads.md — sole source for
  reviewer-contested comment citations.
- "$STUDY_ROOT"/notes/matrix/requirements.tsv — sole source of req_ids
  (column 1, `R`-prefixed) and of each requirement's `source` value
  (column 2), used for the PROV-SPEC rule below.
- "$STUDY_ROOT"/notes/refs.md — context only: pins, the Phase 2a diff
  base decision, PROVISIONAL-SPEC-BASELINE, [OPEN-Q1].
- "$FINAL_TSV", "$BATCH_LOG", and "$RECEIPT" — this phase's own
  outputs, read for resume and for receipt-state classification.

Do not read other notes files, other matrices, other raw artifacts,
repository clones, phase briefs, other phase specifications, or the
live web. "$STUDY_ROOT"/notes/raw/lh-diffstat.txt is explicitly NOT an
input and must never be used for reconciliation or coverage claims —
renames slip through --stat output. If a required declared input is
missing, stop and report; never substitute another source.

## Preconditions (verify at the start of every session; stop if unmet)

1. Every immutable declared input above exists: lh-manifest.tsv,
   lh-files/, lh-name-status.txt, lh-threads.md, requirements.tsv,
   and refs.md. FINAL_TSV, BATCH_LOG, and RECEIPT are phase-owned
   state files, not required inputs: their presence depends on
   execution state, and their existence and consistency are judged
   only by the resume and Completion rules, never by this
   precondition.
2. lh-manifest.tsv has exactly the header above plus 172 data rows;
   indices are zero-padded, unique, and contiguous 001–172; every
   `diff_file` value names an existing file. Verify by command (e.g.
   awk field/count checks plus a per-row existence test) and check
   exit codes.
3. lh-name-status.txt has exactly 172 lines.
4. requirements.tsv exists, is non-empty, and its first header field
   is `req_id`.
5. Never edit, repair, or delete anything under notes/raw/; a failed
   precondition on a raw artifact is reported, not fixed.

## Resume behavior (run before processing; deterministic, idempotent)

0. Receipt-state classification — runs before every other resume step
   and before any repair-capable action: if RECEIPT exists, this
   invocation is read-only. Skip all repair, rollback, cleanup,
   reconstruction, and staging-file removal below and write nothing —
   the sole write permitted in a receipt-present invocation is the
   benign receipt.tmp cleanup defined in Completion step 0 — and go
   directly to Completion step 0. Any condition that would
   otherwise trigger recovery — a malformed FINAL_TSV or BATCH_LOG
   line, an unconfirmed tail, a leftover staging file — is, in this
   state, reported as an inconsistency for user decision, never
   repaired.
1. If FINAL_TSV is absent: this is the first batch. Create it
   exclusively and interruption-safely: mkdir -p "$WORK_DIR" (check
   the exit code); write the header row (schema below) to
   "$WORK_DIR"/header.tmp, removing a stale copy first; verify the
   content; publish with `ln` to FINAL_TSV — which fails atomically
   if FINAL_TSV has appeared — check the exit code; then remove
   header.tmp. FINAL_TSV therefore either does not exist or carries
   a complete valid header; a partial header cannot exist. A
   leftover header.tmp byte-identical to the header row is benign:
   remove it. Resume index = 001; next row_id = LH0001.
2. If FINAL_TSV exists and contains only the correct header (zero
   data rows): a prior session stopped after creation and before any
   batch publication.

   Classify "$WORK_DIR"/header.tmp before continuing:
   - If it is absent, continue.
   - If it exists and is byte-identical to the header-only FINAL_TSV,
     or shares the same inode, it is the benign residue of a stop
     between successful exclusive-link publication and temporary-file
     cleanup. Remove it, check the exit code, and record the cleanup.
   - If it exists but does not match FINAL_TSV, stop and report both
     paths; do not remove or modify either file.

   Resume index = 001; next row_id = LH0001. Step 4 still runs; in
   this state BATCH_LOG must not cover any index — if it does, stop
   and report the inconsistency.
3. Otherwise validate FINAL_TSV: correct header; every data row has
   exactly 11 tab-separated fields — a malformed final line is not an
   immediate failure here; it is handled as part of the unconfirmed
   tail in step 4; row_ids unique and strictly increasing;
   manifest_index values non-decreasing with each index's rows
   contiguous; all enum fields valid. Any failure other than a
   malformed final line: stop and report; do not repair, do not
   process rows.
4. Publication-confirmation recovery — the only permitted
   self-repair. A batch is confirmed only by its BATCH_LOG entry,
   which is written only after successful publication and
   re-validation, so unlogged rows are never trusted as complete.

   Repair publication rule — applies to every repair below that
   changes FINAL_TSV or BATCH_LOG: never edit or truncate the
   authoritative file in place. Build the complete post-repair
   content as a candidate file under WORK_DIR; validate the
   candidate fully; verify the authoritative file is still
   byte-identical to the version read at the start of this recovery;
   then publish with a single atomic same-filesystem rename (mv) of
   the candidate onto the authoritative path, checking the exit
   code. Before the first such rename in a session, verify that
   WORK_DIR and the destination directory report the same filesystem
   device ID (e.g. stat -c %d); if they differ, stop and report — a
   cross-device mv silently degrades to a non-atomic copy, and
   candidates must stay under .work/ per artifact governance. An
   interrupted repair therefore leaves the authoritative file
   untouched; a stale candidate under WORK_DIR is removed and
   rebuilt at the next attempt.

   a. If BATCH_LOG exists, check every entry is a single well-formed
      line (entry format in the publication section). A malformed
      final line is provisionally set aside — not removed — as a
      suspected interrupted log write; steps 4b–4d run on the
      well-formed entries only. The set-aside line may be repaired
      only in step 4f, where the TSV tail and staging file
      corroborate it; in every other outcome — including an empty
      tail — a set-aside line has no supported origin: stop and
      report with BATCH_LOG untouched. Any other malformed entry:
      stop and report.
   b. Semantic log validation — before any frontier is trusted: the
      batch entries, in file order, must begin at 001 and be
      contiguous and non-overlapping (the first entry's <first> is
      001; each later entry's <first> is the previous entry's <last>
      + 1); each entry's `rows` value must equal the number of
      FINAL_TSV data rows with manifest_index in its range, and its
      `total` must equal the number with manifest_index ≤ its <last>.
      A syntactically valid but semantically inconsistent log is
      never trusted as a frontier: stop and report; this is not
      self-repairable.
   c. frontier = the <last> index of the final remaining batch
      entry, or 000 if BATCH_LOG is absent or has no batch entries.
   d. tail = all FINAL_TSV data rows with manifest_index > frontier,
      including any malformed final line. At most one unconfirmed
      batch can exist, because this recovery runs at the start of
      every session.
   e. If the tail is empty: state is clean.
   f. If the tail is non-empty, a staging file matching
      batch-<frontier+1>-*.tsv exists under WORK_DIR, its content is
      byte-identical (cmp) to the tail, and the tail passes full
      batch validation: the publication completed and only its log
      entry is missing or was interrupted. Apply the duplicate-entry
      guard, then publish per the repair publication rule — the
      candidate log is BATCH_LOG minus any set-aside malformed final
      line plus the reconstructed entry with the marker
      `reconstructed`. Then remove the staging file. This
      reconstructs the missing entry exactly once.
   g. Evidence-backed rollback — permitted only with positive
      evidence of an interrupted append: exactly one staging file
      matching batch-<frontier+1>-*.tsv exists, the tail's complete
      rows are byte-identical to the initial lines of that file, and
      any malformed final partial line is a prefix of the file's next
      line. The tail is then a confirmed partial write of that batch
      (it may omit later rows, including part of one index's intended
      row set): remove the entire tail per the repair publication
      rule — the candidate is FINAL_TSV minus the tail; before
      publishing, verify the candidate validates and its highest
      manifest_index equals the frontier (header-only when the
      frontier is 000). Then report and remove the now-stale staging
      file, and record the rollback in the session report and in
      this session's eventual BATCH_LOG entry.
   h. Any other non-empty-tail state — the staging file is missing,
      more than one candidate exists, or its content neither equals
      nor prefix-contains the tail — has no supported origin under
      the publication rules and gives insufficient evidence for a
      deterministic rollback: stop and report the full state (tail
      index range and row count, staging files present, comparison
      results) for user decision. Remove nothing.
   i. Independently of the tail: a staging file under WORK_DIR whose
      name range <first>-<last> exactly matches a remaining BATCH_LOG
      batch entry is a confirmed-batch leftover — a stop occurred
      between the log write and staging removal, possibly after the
      final batch. Remove it and record the cleanup. Any other file
      under WORK_DIR is reported and left in place; a file named for
      the batch about to be staged is handled by the staging
      procedure.
   j. Notwithstanding step 4i: either
      "$WORK_DIR"/refinement.tsv or
      "$WORK_DIR"/refinement-recovery.tsv marks an interrupted
      refinement pass (Completion step 3). Stop and report for user
      decision, including which files exist, whether each is
      byte-identical to FINAL_TSV, whether the candidate and recovery
      files are byte-identical to each other, and which `refinement`
      entries BATCH_LOG carries. Do not remove, publish, or
      reconstruct anything. Refinement is a user-supervised path; its
      interrupted states are resolved by the user.
5. Resume index = frontier + 1. After recovery this must equal
   (highest manifest_index in FINAL_TSV) + 1, treating a header-only
   file as frontier 000; if they disagree, stop and report. Next
   row_id = the last row_id in FINAL_TSV + 1, or LH0001 if there are
   no data rows.
6. If the resume index exceeds 172, all indices are classified:
   proceed to Completion. RECEIPT is absent here (resume step 0
   routes receipt-present invocations directly to Completion step 0),
   so Completion runs steps 1–4 and writes the receipt.
7. Completed rows are never revisited, except (a) the recovery rule
   above and (b) a user-authorized unclear-refinement pass (see
   Completion).

## Batch size

Each session processes up to 25 consecutive manifest indices starting
at the resume index, fewer only when index 172 is reached, the
workload bound below is hit, or a reported failure stops the session
early. The invoking task prompt may state smaller bounds; it may not
raise them.

Workload bound: before starting each manifest index after the first,
if the cumulative `wc -l` line count of the saved diffs already
processed this session is 3000 or more, end the batch at the previous
index. A batch always ends at a manifest-index boundary; one index's
diff is never split across batches, however large.

## Task — per manifest index, in index order

1. Read the manifest row: index, path, status, old_path_if_renamed,
   diff_file. Read the saved diff at diff_file.
2. Produce one or more matrix rows for this index, per the schema
   below. Cover the whole diff: every substantive change in the file
   is covered by some row for its index (multiple symbols or concerns
   in one file may be split across rows; a small or single-purpose
   file may be one row). A deleted file still gets a row.
3. To fill test_evidence, you may read any other manifest-listed diff
   under notes/raw/lh-files/ (e.g. a test file changed by the same
   PR); you may not read anything else.
4. To fill req_ids, match against requirements.tsv. Use NONE where
   nothing matches — NONE rows feed the Phase 2c orphan check and are
   not errors. Do not stretch a match to avoid NONE.
5. Frame every free-text finding as describing one reference
   implementation: PR #39's approach is the choice of one draft
   reference implementation, never "the" EIP-8025 design, and notes
   must not present an implementation choice as spec-required unless
   the row cites a req_id imposing it.
6. Never treat WIP scaffolding — devnet hacks, TODO markers, stubbed
   or reverted code — as Lighthouse's intended design; classify it by
   its signal (scaffolding, CI-devnet, or reverted-partial), and keep
   any design inference out of notes.

## Provisional cluster taxonomy (fixed for this phase)

Assign each row exactly one provisional cluster_id from:
C1 SSZ/types · C2 gossip validation · C3 Req/Resp · C4 proof engine ·
C5 EL integration · C6 block import/forkchoice · C7 config/CLI ·
C8 validator/prover service · C9 sync · C10 tests/CI · C-unassigned.
Use C-unassigned when no cluster fits. Do not invent, split, or merge
clusters — remapping is Phase 2c's job, via its remap table.

## Output schema

TSV, not Markdown. Tab-separated header row (11 columns):

row_id	manifest_index	file	symbol_lines	change_type	cluster_id	req_ids	classification	classification_signal	test_evidence	notes

- row_id: LH0001, LH0002, ... zero-padded to 4 digits, unique,
  strictly increasing in append order across all batches.
- manifest_index: the 3-digit manifest index; repeats allowed
  (multi-row files); all rows for one index are contiguous and
  written in the same batch.
- file: the manifest `path` value, verbatim.
- symbol_lines: the symbol(s), hunk(s), or line ranges this row
  covers, as seen in the saved diff (e.g. `fn verify_proof_quorum`,
  `hunks @@ -0,0 +1,120 @@`, `whole file`). Free text, no tabs.
- change_type: added | modified | deleted | renamed. Must be
  consistent with the manifest status letter for this index
  (A→added, M→modified, D→deleted, R*→renamed); a renamed row's
  notes name the old path from old_path_if_renamed.
- cluster_id: one taxonomy value from the list above.
- req_ids: comma-separated req_ids from requirements.tsv (e.g.
  `R012,R047`), or NONE.
- classification, one of:
  - spec-driven — implements or enforces one or more cited
    requirements; req_ids must not be NONE, and the signal names the
    matched requirement behavior.
  - LH-arch-choice — an implementation-specific design choice of this
    one reference implementation, not dictated by any extracted
    requirement.
  - scaffolding — WIP, placeholder, stubbed, or temporary code; the
    signal quotes the concrete marker (TODO text, stub, placeholder
    value).
  - CI-devnet — CI workflow, kurtosis/devnet wiring, or hardcoded
    devnet value; the signal names the concrete value or workflow.
  - dependency — dependency, lockfile, or build-metadata change.
  - reverted-partial — added then reverted, or explicitly partial,
    within the PR; the signal must show this from the saved evidence,
    not inference.
  - reviewer-contested — a specific saved reviewer comment objects to
    this change; permitted only with that comment cited from
    lh-threads.md per the rule below.
  - unclear — signal insufficient to classify confidently. Use
    freely; never force a confident-looking label.
- classification_signal: required, non-empty; names the concrete
  observable signal for the classification (quoted TODO or hardcoded
  value, matched requirement, comment locator, dependency-file
  nature), and ends with at least one [raw:<path>] pointer — for most
  rows [raw:lh-files/<index>.diff].
- test_evidence: [raw:<path>] pointer(s) to saved diff(s) showing
  test coverage touching this change, or NONE.
- notes: optional except where a rule requires it; any factual or
  interpretive claim in it ends with [raw:<path>] or an existing
  [OPEN-Qn]. Never assign a new [OPEN-Qn] ID.
- Field hygiene: no tab or newline characters inside any field;
  collapse internal whitespace runs to single spaces; every data row
  has exactly 11 fields.
- The TSV contains classification rows only — no commentary, open
  questions, or batch markers.

## reviewer-contested rule

classification=reviewer-contested requires, in classification_signal
or notes: (1) metadata locating one specific comment uniquely by
search within lh-threads.md — author and timestamp, thread
path/position, comment identifier, or a short distinctive quoted
phrase — and (2) the pointer [raw:lh-threads.md]. Thread resolution
state or other metadata alone never establishes contested. An
objection in a thread marked resolved, or withdrawn in-thread, is
recorded in notes but is not by itself reviewer-contested. If
lh-threads.md contains no qualifying comment for a change, the row
simply takes another classification.

## PROV-SPEC rule

The spec snapshot behind requirements.tsv is a
PROVISIONAL-SPEC-BASELINE ([OPEN-Q1]; see refs.md).

Mechanical rule: a row whose req_ids field cites one or more req_ids,
all of which have `source` = spec-snapshot in requirements.tsv, must
contain in its notes field both the literal tag `PROV-SPEC` and the
literal reference `[OPEN-Q1]`. Rows citing at least one req_id with
source EIP-PR or both, and rows with req_ids = NONE, are not
tag-required; the tag (with both literals) may still be added where
the classification rationale otherwise depends on the provisional
snapshot.

Meaning of the tag: a PROV-SPEC row supports no conclusion stronger
than needs-input, in this or any later phase, until the baseline is
confirmed; the tag itself carries this limitation and no extra prose
is required.

Audit check: the set of tag-required rows is computed exactly by
joining each row's req_ids against the requirements.tsv `source`
column; every tag-required row must contain both literals in notes.

## Staging, publication, and failure handling (per batch)

1. mkdir -p "$WORK_DIR"; check the exit code.
2. BATCH_TSV="$WORK_DIR"/batch-<first>-<last>.tsv, named for the
   batch's manifest index range. If a file of that name already
   exists, it is stale from a failed prior attempt: report it,
   remove it, and continue.
3. Write matrix rows only to BATCH_TSV until validation passes. Never
   write data rows directly to FINAL_TSV. Write BATCH_TSV in small
   bounded chunks — never the whole batch in one shell command,
   heredoc, or generated program — and after each chunk verify every
   completed row has exactly 11 tab-separated fields.
4. Batch validation before publication: every row has 11 fields;
   enums valid; change_type consistent with the manifest status;
   row_ids continue strictly from the last row_id in FINAL_TSV;
   manifest_index values cover exactly the indices <first>–<last>,
   each with at least one row and none outside the range;
   classification_signal non-empty and carrying [raw:<path>];
   spec-driven rows have req_ids ≠ NONE; every req_id exists in
   requirements.tsv; every [raw:<path>] names an existing file under
   notes/raw/; PROV-SPEC rule satisfied.
5. On any failure before publication: remove BATCH_TSV only if this
   session created it, report the failure and the resume point
   (unchanged), and stop. FINAL_TSV is untouched.
6. Record FINAL_TSV's pre-append line count. Publish with a single
   append: `cat "$BATCH_TSV" >> "$FINAL_TSV"`; capture and check the
   exit code immediately.
7. Re-validate FINAL_TSV in full. If this fails: restore FINAL_TSV
   per the resume repair publication rule — the candidate is exactly
   the recorded pre-append content (the first pre-append-count
   lines); verify the candidate reproduces the pre-append line count
   and validates before publishing. Preserve BATCH_TSV for
   diagnosis, report both paths and the failure, and stop. Do not
   claim publication.
8. On success: append one entry to BATCH_LOG (create it if absent).
   Entry format — exactly one line per entry:
   `<date> | batch <first>-<last> | rows <n> | total <running total> | repair: <none or short description> | validation: OK`
   with the suffix ` | reconstructed` only on entries written by
   resume-recovery step 4f, and `refinement` in place of
   `batch <first>-<last>` on a refinement-pass entry.
   Duplicate-entry guard: before appending, check BATCH_LOG for an
   existing entry covering any index in this range; if found, report
   the discrepancy instead of appending a duplicate. Check the append
   exit code. If the BATCH_LOG write fails: do not roll back the
   published batch; preserve BATCH_TSV in place, report both paths
   and the failure, and stop — the next session's resume recovery
   (step 4f) reconstructs the missing entry exactly once from the
   preserved staging file.
9. Remove BATCH_TSV (session-created staging) only after its
   BATCH_LOG entry is confirmed written.

This phase publishes nothing under notes/raw/ and must not modify
notes/refs.md, notes/open-questions.md, or any existing raw artifact.

## Session output (every batch session)

- The batch range processed and per-batch counts by classification,
  cluster_id, and change_type; count of req_ids=NONE rows; count of
  PROV-SPEC rows.
- Running totals for the same groupings across FINAL_TSV.
- Any proposed open questions, unnumbered, each ending with
  supporting [raw:<path>] citations; do NOT write to
  open-questions.md — the user assigns IDs and appends manually.
  Existing questions may be referenced as [OPEN-Qn].
- Any observations (e.g. borderline classifications), each cited.
- End by stating the resume point: the last manifest index processed
  and the next index (or "complete: 172/172").

## Completion (the session that publishes index 172, or a later one)

0. Completed-state invocation: if RECEIPT already exists (reached via
   resume step 0, so no repair, rollback, or cleanup has been
   performed), this session is read-only. Independently recheck every
   mechanically checkable `Done when` condition: run steps 1 and 2 as
   verification only; validate BATCH_LOG syntax and semantics per
   resume steps 4a and 4b, reporting rather than repairing; confirm
   exactly one entry per batch, batch entries beginning at 001,
   contiguous, non-overlapping, covering 001–172, with rows and total
   values consistent with FINAL_TSV; recompute the counts and unclear
   rate from FINAL_TSV and compare them with the receipt's recorded
   values and required contents; confirm no staging files remain
   under WORK_DIR — with one benign exception: a leftover
   "$WORK_DIR"/receipt.tmp that is byte-identical to RECEIPT (or the
   same inode) is the residue of a stop between receipt publication
   and its cleanup; remove it — this removal is the sole write
   permitted in a receipt-present invocation — record the cleanup,
   and continue verification. If everything is consistent: report that the phase
   is complete and verified read-only, and stop, writing nothing
   else. On
   any inconsistency: stop and report the specific mismatch; never
   overwrite, regenerate, or delete RECEIPT, FINAL_TSV, or BATCH_LOG
   to resolve it — that is a user decision.
1. Reconciliation, against the manifest and never lh-diffstat.txt:
   verify every manifest index 001–172 has at least one FINAL_TSV
   row and no FINAL_TSV row bears an index outside 001–172; verify
   lh-manifest.tsv still has 172 data rows and lh-name-status.txt
   still has 172 lines (rename-safe cross-check). Run these as
   commands and check exit codes; include the outputs in session
   output.
2. Full-file validation of FINAL_TSV per the batch-validation rules,
   applied to every row.
3. Compute the unclear rate: rows with classification=unclear divided
   by total data rows.
   - If ≤ 15%: proceed to the receipt.
   - If > 15%: stop and report the rate and the affected rows. The
     user decides: accept the rate, or authorize one
     unclear-refinement pass. A refinement pass is a separate
     user-supervised session under this specification that
     re-examines only classification=unclear rows and may revise
     only those rows' cluster_id, req_ids, classification,
     classification_signal, test_evidence, and notes (never row_id,
     manifest_index, file, symbol_lines beyond correction, or any
     non-unclear row). It never edits FINAL_TSV in place. It builds
     the complete revised file as "$WORK_DIR"/refinement.tsv and
     validates it fully: the row count is unchanged, and differences
     from FINAL_TSV are confined to the permitted fields of rows that
     were classified unclear before the pass.

     Before publication, require
     "$WORK_DIR"/refinement-recovery.tsv to be absent, then create it
     as a hard link to "$WORK_DIR"/refinement.tsv:

     ln "$WORK_DIR"/refinement.tsv \
       "$WORK_DIR"/refinement-recovery.tsv

     Check the exit code and verify that the two paths are
     byte-identical or share the same inode. Publish refinement.tsv
     over FINAL_TSV by atomic rename per the repair publication rule,
     including the device-ID and unchanged-original checks. The
     recovery link therefore remains under WORK_DIR after the rename
     and contains the exact published refinement.

     Append the BATCH_LOG entry marked `refinement` and check its exit
     code. Remove refinement-recovery.tsv only after that log entry is
     confirmed written. If publication or the log append fails, or
     execution stops before cleanup, preserve every remaining
     refinement file and stop; resume step 4j routes the state to the
     user. An interrupted refinement therefore leaves corroborating
     evidence whether publication occurred before or after the stop.
	 The user verifies the pass through the actual git
     diff. After it, rerun this Completion section. Do not create the receipt while the rate
     exceeds 15% without a recorded user acceptance in the invoking
     task prompt.
4. Publish the completion receipt interruption-safely (RECEIPT is
   absent at this point, by step 0):
   a. RECEIPT_TMP="$WORK_DIR"/receipt.tmp. Ensure WORK_DIR exists
      (mkdir -p; check the exit code). If RECEIPT_TMP already exists
      it is stale from an interrupted attempt: report and remove it.
   b. Write the receipt content to RECEIPT_TMP and check the exit
      code. The receipt records: date; manifest row count and
      name-status line count as verified; the reconciliation result
      and the statement that reconciliation used lh-manifest.tsv, not
      lh-diffstat.txt; total rows; counts by classification,
      cluster_id, and change_type; req_ids=NONE count; PROV-SPEC row
      count; the unclear rate and either "≤ 15%" or the user's
      recorded acceptance / completed refinement; and
      `validation: OK`.
   c. Validate RECEIPT_TMP: every required item above is present and
      its counts equal the values just recomputed from FINAL_TSV.
   d. Publish with `ln "$RECEIPT_TMP" "$RECEIPT"` — a hard link,
      which fails atomically if the destination already exists, so
      no separate absence check can race with publication — and
      check the exit code immediately. WORK_DIR and notes/matrix/
      are both under STUDY_ROOT and expected on one filesystem; a
      cross-filesystem or destination-exists failure surfaces as a
      failed exit code. On success, remove RECEIPT_TMP. RECEIPT is
      therefore either absent or complete and validated; a partial
      receipt cannot exist, so receipt presence is a reliable
      completed-state marker for resume step 0. If publication
      fails: preserve RECEIPT_TMP, report both paths, and do not
      claim completion.

## Done when

- FINAL_TSV exists and every manifest index 001–172 has at least one
  row, reconciled against lh-manifest.tsv (with the lh-name-status.txt
  cross-check) and never against lh-diffstat.txt.
- For each manifest index, its rows collectively cover every
  substantive hunk, symbol, or distinct concern in that index's saved
  diff (Task step 2). This universal condition is verified read-only
  by comparing rows against every index's saved diff under
  notes/raw/lh-files/; the verifying audit may work in bounded
  batches of indices, but a sample alone does not establish the
  criterion, and any discovered coverage gap fails it.
- Every data row passes full validation: 11 tab-separated fields;
  valid enums; change_type consistent with the manifest status;
  unique strictly-increasing row_ids; every classification carries a
  non-empty cited classification_signal; every spec-driven row has
  req_ids ≠ NONE that exist in requirements.tsv; every
  reviewer-contested row cites a specific comment per the
  reviewer-contested rule; every [raw:<path>] names an existing file
  under notes/raw/; the PROV-SPEC rule is satisfied.
- The unclear rate is ≤ 15%, or the user's acceptance or authorized
  refinement outcome is recorded in RECEIPT.
- BATCH_LOG exists with exactly one entry per published batch — a
  reconstructed entry counts as that batch's entry; refinement
  entries are additional — every entry well-formed per the entry
  format; batch entries begin at 001, are contiguous and
  non-overlapping, cover 001–172, and carry rows and total values
  that agree with FINAL_TSV (resume step 4b).
- RECEIPT exists with all required contents and `validation: OK`.
- No batch staging files remain under "$WORK_DIR" for published
  batches.
- notes/raw/, notes/refs.md, and notes/open-questions.md are
  unmodified by this phase.
- Nothing committed.
