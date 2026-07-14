# Phase 2a — Lighthouse PR #39 mechanical harvest

Refs pinned in notes/refs.md. This is a HARVEST session: run the fixed
commands and save output verbatim — no interpretation, summarization,
or synthesis. Do not commit. Never inspect Grandine sources in this
session. PR-thread harvesting runs in live mode only; live harvesting
and manual fallback are mutually exclusive modes, and no
manual-fallback export was recorded for PR #39. This specification is
executable only after the user has reviewed its git diff and
explicitly authorized execution.

## Variables

Set all of the following, echo all of them, run the path-confirmation
checks, and proceed only if they pass. No human pause is required
mid-session; stop only on a failed check.

STUDY_ROOT=/work
LH_CLONE=/work/repos/lighthouse
REFS="$STUDY_ROOT/notes/refs.md"
RAW_DIR="$STUDY_ROOT/notes/raw"
WORK_DIR="$STUDY_ROOT/.work/p2a-harvest"
STAGE_DIR="$WORK_DIR/staging"
RECEIPT="$STAGE_DIR/lh-harvest-receipt.txt"
RESUME_LOG="$WORK_DIR/resume-validation.log"
BASE=dfb259171a65cacd6db57b8874af8f543cabcb7a
PR_HEAD=0dd6c3b8cf3b1eece82a0a7ee87282a222d93bf5
PIN_SIGP_UNSTABLE=7d2b64341bcabaed85332fa59e7be28d3740e88a
PIN_SIGP_MERGE_BASE=494b00a3491e2c5e281f6972aa00694b17f16722

BASE is the Lighthouse PR harvest diff base fixed by the 2026-07-13
Phase 2a decision-log entry in refs.md (the stated PR base commit, not
the merge-base vs sigp/lighthouse@unstable). PR_HEAD is the pinned
head of eth-act/lighthouse PR #39. PIN_SIGP_UNSTABLE and
PIN_SIGP_MERGE_BASE are the 2026-07-12 check-time values recorded in
refs.md, used only for the drift record in step E.

STAGE_DIR is this phase's declared staging location. Every harvest
artifact is written there first, validated there as a complete set,
and only then published into "$RAW_DIR" (the append-only
published-evidence store) by the no-clobber procedure in step D.
Everything under "$WORK_DIR" is temporary — staging, stderr,
resume-validation, and diagnostic files live there, are never created
under notes/raw/, and are never cited as evidence.

RESUME_LOG receives the results of every verification run in the
resume, already-complete, and recovery branches (A.2, A.3, B.7, F).
Those branches must never create, append to, or modify the staged or
published receipt: after step D1 finishes the receipt, it is closed,
and the published copy is immutable raw evidence.

Never use HEAD as a variable name; always $PR_HEAD. No command in
this session may depend on the current working directory: every git
command uses `git -C "$LH_CLONE"`. Except for the single
BOOTSTRAP_ERR path declared in Path confirmation step 4, every file
write path starts with "$STUDY_ROOT". All fetches use explicit URLs
and forced explicit refspecs; never rely on the clone's configured
remotes for fetching.

Command success and stderr policy: check the exit code of every
command before dependent work continues. Each gate, harvest,
validation, and publication command redirects stderr to a file under
"$WORK_DIR"/stderr/ (stdout of harvest commands goes only to the
staged artifact). On a nonzero exit code: stop, report the failed
command, its exit code, and the stderr file path; preserve
"$WORK_DIR" for diagnosis; publish nothing further. Output from an
unchecked command is never treated as successful harvest, validation,
or publication.

Final published artifacts (all under notes/raw/):
- "$RAW_DIR"/lh-name-status.txt
- "$RAW_DIR"/lh-manifest.tsv
- "$RAW_DIR"/lh-diffstat.txt
- "$RAW_DIR"/lh-log.txt
- "$RAW_DIR"/lh-files/<index>.diff (one per manifest row)
- "$RAW_DIR"/lh-deps.diff
- "$RAW_DIR"/lh-threads.md
- "$RAW_DIR"/lh-harvest-receipt.txt (published last; its presence in
  notes/raw/ marks the raw set as completely published)

Plus exactly one merge-base drift record appended to "$REFS" (step E),
only after raw publication succeeds.

## Path confirmation — before any other step
1. test -d "$LH_CLONE" and git -C "$LH_CLONE" rev-parse --git-dir
   If either fails: stop, report (do not guess another path).
2. git -C "$LH_CLONE" remote get-url origin
   Must reference a github.com lighthouse repository
   (github.com/eth-act/lighthouse or github.com/sigp/lighthouse).
   If not: stop, report the observed URL.
3. Confirm "$REFS" exists and "$RAW_DIR" exists and is a directory.
   If not: stop, report.
4. Bootstrap the temporary command-log directory. This command is the
   sole exception to the normal stderr-location policy because
   "$WORK_DIR"/stderr does not yet exist:

   BOOTSTRAP_ERR=/tmp/p2a-harvest-bootstrap.stderr
   mkdir -p "$WORK_DIR"/stderr 2>"$BOOTSTRAP_ERR"

   Capture and check the exit code. On failure: stop and report the
   command, exit code, and "$BOOTSTRAP_ERR"; do not continue.

   On success, move the bootstrap stderr file into the declared
   temporary area:

   mv "$BOOTSTRAP_ERR" "$WORK_DIR"/stderr/bootstrap.stderr \
     2>"$WORK_DIR"/stderr/bootstrap-move.stderr

   Capture and check that exit code. After this step, the normal
   stderr policy applies to every later command. "$RESUME_LOG" may
   then be created under "$WORK_DIR" when its branch requires it.

## A. Session-state classification — before gate and harvest

Evaluate exactly these observations:
- RECEIPT_PUBLISHED: "$RAW_DIR"/lh-harvest-receipt.txt exists.
- OTHER_PUBLISHED: any of the other seven final paths exists
  (lh-files/ counts as existing only if it exists AND is non-empty;
  an existing empty directory does not count).
- The drift-record classification below.

### Drift-record classification (used by A, E, and F)
Let DRIFT_COUNT be the number of lines in "$REFS" equal to the
heading "## Phase 2a merge-base drift record". Heading presence alone
never proves the record is complete.
- DRIFT_COUNT = 0: the record is ABSENT.
- DRIFT_COUNT = 1: extract the section from that heading to the next
  "## " heading or end of file. The record is COMPLETE only if the
  section contains, in order, the three list items of the step E
  template: the observed-HEAD item carrying the check-time literal
  7d2b64341bcabaed85332fa59e7be28d3740e88a; the merge-base item
  carrying the check-time literal
  494b00a3491e2c5e281f6972aa00694b17f16722; and the "Context only:"
  item carrying the literal BASE
  dfb259171a65cacd6db57b8874af8f543cabcb7a — with each substituted
  observed value a 40-character hex string. Otherwise the record is
  PARTIAL/MALFORMED: stop, report the section verbatim, and await the
  user's decision. Never edit or delete refs.md content.
- DRIFT_COUNT > 1: DUPLICATE records: stop, report all occurrences,
  and await the user's decision. Never edit or delete refs.md content.

### Published-set verification — read-only (used by A.2, A.3, and F)
Record every command, output, and exit code in "$RESUME_LOG"; never
create, append to, or modify the staged or published receipt.
1. "$RAW_DIR"/lh-harvest-receipt.txt exists and contains exactly one
   BYTE INVENTORY section and exactly one SHA256 INVENTORY section in
   the marker-delimited formats defined in D1. Each section is sorted
   by canonical final path, contains no duplicate path, and excludes
   the receipt itself.
2. Every expected artifact exists: the six single files, the receipt,
   and every diff_file listed in the published lh-manifest.tsv.
3. Manifest reconciliation from the published files: lh-manifest.tsv
   data-row count (excluding header) equals the lh-name-status.txt
   line count; every data row has exactly the five schema fields;
   "$RAW_DIR"/lh-files/ contains exactly the manifest-listed diff
   files — none missing, none extra.
4. Parse checksum rows only between `BEGIN SHA256 INVENTORY` and
   `END SHA256 INVENTORY`. Recompute sha256 of every published
   artifact except the receipt; each value must equal the inventory
   value for its canonical final path. The parsed inventory must list
   exactly those artifacts — none missing, none extra. Perform the
   analogous exact-set comparison against the marker-delimited BYTE
   INVENTORY using recomputed byte counts.
5. lh-threads.md contains the five exact-command headings from C.7,
   each followed by its fenced output; the headRefOid in the saved
   `gh pr view` output equals $PR_HEAD; every hasNextPage value in
   the saved GraphQL output is false.
On any failure: stop, report the mismatching path(s) and both values,
and await the user's decision.

Then take exactly one branch:
1. Fresh run — no RECEIPT_PUBLISHED, no OTHER_PUBLISHED, drift record
   ABSENT: if "$STAGE_DIR" exists and is non-empty, move it aside to
   "$WORK_DIR"/staging-superseded-<UTC timestamp> (temporary files,
   never evidence), then mkdir -p "$STAGE_DIR"/lh-files and run steps
   B, C, D, E in order.
2. Already published — RECEIPT_PUBLISHED, all seven other final paths
   exist, drift record COMPLETE: run the published-set verification
   above; only if it passes, report that the Phase 2a outputs exist
   and verified against the receipt, list them, and stop. Never
   report the phase as already published on path existence alone.
   Re-run nothing; completion is established by the phase-completion
   audit, not by this report.
3. Resume, drift record only — RECEIPT_PUBLISHED, all seven other
   final paths exist, drift record ABSENT: run the published-set
   verification above; if it passes, run the reduced gate in B.7,
   skip C and D, and run step E only.
4. Interrupted partial publication — any other combination in which
   RECEIPT_PUBLISHED or OTHER_PUBLISHED is true and the drift record
   is ABSENT: run the recovery procedure in step F. Do not assume
   that publishing the missing files alone is safe.
5. Inconsistent — drift record COMPLETE but not (RECEIPT_PUBLISHED
   and all seven other final paths exist): stop and report; the drift
   record claims a completed publication that is not present. Await
   the user's decision. (A PARTIAL/MALFORMED or DUPLICATE record
   already stopped inside the classification.)

## B. Gate — all checks must pass before any harvest step

Checks B.1–B.6 run only in the fresh-run branch A.1. Record every
command in those checks, its verbatim output, and its exit code in
"$RECEIPT" as it runs; create "$RECEIPT" at the start of B.1.

Check B.7 runs only in the drift-only resume branch A.3. Record every
B.7 command, output, and exit code in "$RESUME_LOG"; B.7 must never
create, append to, or modify the staged or published receipt.

1. Pin verification against refs.md — derived from the variables,
   never from separately duplicated literals:
   BASE_CONTEXT=$(grep -nF -C 2 -- "$BASE" "$REFS"); BASE_RC=$?
   HEAD_CONTEXT=$(grep -nF -C 2 -- "$PR_HEAD" "$REFS"); HEAD_RC=$?
   Validation:
   - Both rc values must be 0 (hash present in refs.md).
   - BASE_CONTEXT must identify BASE's role as the Lighthouse PR
     harvest diff base per the 2026-07-13 Phase 2a decision-log entry.
   - HEAD_CONTEXT must identify PR_HEAD's role as the head of
     eth-act/lighthouse PR #39.
   - Report both captured contexts verbatim.
   If either rc is nonzero, or either role cannot be confirmed from
   its context: stop, report both contexts and rc values.
2. Fetch the PR head by explicit URL and forced explicit refspec:
   git -C "$LH_CLONE" fetch https://github.com/eth-act/lighthouse.git +refs/pull/39/head:refs/remotes/harvest/pr-39
3. PR head equality check:
   PR_FETCHED=$(git -C "$LH_CLONE" rev-parse refs/remotes/harvest/pr-39)
   test "$PR_FETCHED" = "$PR_HEAD"
   If the test fails, guard the discrepancy entry with a stable
   mismatch identity so repeated failures never append duplicates:
   DELTA_ID="P2a-delta gate-pr39-head observed=$PR_FETCHED pinned=$PR_HEAD"
   If grep -qF -- "$DELTA_ID" "$REFS" succeeds, the identical mismatch
   is already recorded — append nothing. Otherwise append to the
   discrepancy/notes block of "$REFS" one dated entry containing the
   DELTA_ID line verbatim. In both cases: report both values, stop,
   and await the user's re-pin decision. Never continue past this
   failed gate. Nothing is published.
4. Base object existence (NOT equality with any current branch tip —
   branches may have advanced; only the pinned object must exist):
   git -C "$LH_CLONE" cat-file -e "$BASE"^{commit}
   If missing: stop, report.
5. Ancestry integrity check (per refs.md the PR head is a straight
   descendant of the stated base; deterministic given both objects —
   a mismatch means a shallow/corrupt local clone or a wrong pin, not
   ordinary upstream drift):
   git -C "$LH_CLONE" merge-base "$BASE" "$PR_HEAD"
   Must equal $BASE. If not: stop, report the observed value.
6. Drift-record guard: run the drift-record classification; it must
   report ABSENT (state classification should already have routed
   this case; this is a final guard so the record is written exactly
   once).
7. Reduced gate for the drift-only resume branch (A.3): run checks 1
   and 6, then confirm the pinned head object exists locally:
   git -C "$LH_CLONE" cat-file -e "$PR_HEAD"^{commit}
   Record these results in "$RESUME_LOG" — never in the staged or
   published receipt. Do not refetch or re-check the live PR head:
   the published harvest is fixed at the pinned SHAs, and later
   movement of the PR does not invalidate it. On any failure: stop,
   report.

## C. Harvest — run exactly, in order, into "$STAGE_DIR"
1. Primary evidence — the raw name-status output; the manifest in
   step 2 is derived from this file:
   git -C "$LH_CLONE" diff --name-status -M $BASE..$PR_HEAD > "$STAGE_DIR"/lh-name-status.txt
2. Build "$STAGE_DIR"/lh-manifest.tsv mechanically and
   deterministically from lh-name-status.txt. The manifest is the
   reconciliation source of truth for the Done-when criteria.
   The file must be TSV, not Markdown. Use this tab-separated header row:
   index	path	status	old_path_if_renamed	diff_file

   Indexes are zero-padded, starting at 001; if the data-row count
   exceeds 999, pad every index to the digit count of the total
   data-row count (all indexes the same width).
   Rules: record paths exactly as emitted by name-status; quote paths
   only when constructing shell commands; record the status field
   exactly as emitted (A, M, D, or Rxx with its score digits); for
   Rxx rows, path = new path and old_path_if_renamed = old path; for
   A, M, and D rows, old_path_if_renamed is empty; for D rows, path =
   deleted path; diff_file = notes/raw/lh-files/<index>.diff (path
   relative to the study root, matching the final published location).
   One data row per name-status line; data-row count, excluding the
   header, must equal the line count of lh-name-status.txt.
   If any path field begins with a double quote — Git's C-style
   quoted representation of a path containing special characters —
   stop and report that line verbatim: this specification does not
   define quoted-path decoding, and no manifest row or diff command
   may be improvised from it.
   If any name-status line carries a status other than A, M, D, or
   Rxx, or is otherwise malformed (an A/M/D line must be exactly
   status TAB path; an Rxx line must be exactly status TAB old path
   TAB new path): stop and report that line verbatim; do not
   improvise a manifest row or diff command form for it.
3. Human-readable summary only, not used for reconciliation:
   git -C "$LH_CLONE" diff --stat $BASE..$PR_HEAD > "$STAGE_DIR"/lh-diffstat.txt
4. git -C "$LH_CLONE" log --oneline $BASE..$PR_HEAD > "$STAGE_DIR"/lh-log.txt
5. Per manifest row, write the per-file diff. Each command form ends
   with explicit redirection to that row's diff file under
   "$STAGE_DIR"/lh-files/:
   A/M rows:
   git -C "$LH_CLONE" diff $BASE..$PR_HEAD -- "<path>" > "$STAGE_DIR"/lh-files/<index>.diff
   D rows:
   git -C "$LH_CLONE" diff $BASE..$PR_HEAD -- "<deleted path>" > "$STAGE_DIR"/lh-files/<index>.diff
   Rxx rows:
   git -C "$LH_CLONE" diff -M $BASE..$PR_HEAD -- "<old path>" "<new path>" > "$STAGE_DIR"/lh-files/<index>.diff
6. Dependency-manifest diff:
   git -C "$LH_CLONE" diff $BASE..$PR_HEAD -- Cargo.toml Cargo.lock '**/Cargo.toml' > "$STAGE_DIR"/lh-deps.diff
   An empty file is a valid harvest result (it is itself the saved
   evidence that the diff touched no dependency manifests) but must be
   reported explicitly as empty; its byte count is recorded during set
   validation and in the session report.
7. Build "$STAGE_DIR"/lh-threads.md — live mode only: start from an
   empty file; before each command below, append a heading line with
   the exact command; then append its raw output verbatim in a fenced
   block. (gh commands are directory-independent; run as written.)
   - gh pr view 39 --repo eth-act/lighthouse --json title,state,body,baseRefName,headRefName,headRefOid,baseRefOid,isDraft,mergedAt,closed
   - gh api repos/eth-act/lighthouse/issues/39/comments --paginate
   - gh api repos/eth-act/lighthouse/pulls/39/comments --paginate
   - gh api repos/eth-act/lighthouse/pulls/39/reviews --paginate
   - gh api graphql -f query='query{repository(owner:"eth-act",name:"lighthouse"){pullRequest(number:39){reviewThreads(first:100){pageInfo{hasNextPage endCursor},nodes{isResolved,comments(first:50){pageInfo{hasNextPage endCursor},nodes{author{login},body,path,createdAt}}}}}}}'
   If gh or the network is unavailable, or any command fails: stop and
   report — no manual-fallback export was recorded for PR #39, so
   there is no fallback path; never invent, guess, or create one, and
   do not continue with partial PR-thread evidence. The staged
   lh-threads.md stays under "$STAGE_DIR" for diagnosis and is not
   published.
   After saving the `gh pr view` output: the headRefOid it reports
   must equal $PR_HEAD. If not, guard the discrepancy entry with a
   stable mismatch identity so repeated failures never append
   duplicates:
   DELTA_ID="P2a-delta threads-pr39-headRefOid observed=<headRefOid> pinned=$PR_HEAD"
   If grep -qF -- "$DELTA_ID" "$REFS" succeeds, the identical mismatch
   is already recorded — append nothing. Otherwise append to the
   discrepancy/notes block of "$REFS" one dated entry containing the
   DELTA_ID line verbatim. In both cases: report both values, stop,
   and await the user's re-pin decision. Nothing is published; the
   staged content stays under "$STAGE_DIR" only.
   After saving the GraphQL output: every hasNextPage value in the
   saved output must be false; if any is true, stop and report it (do
   not improvise pagination); staged output stays under "$STAGE_DIR",
   unpublished.
   Caveat carried into the record for downstream phases:
   resolved/unresolved thread state may be incomplete regardless of
   method; a reviewer-contested classification must cite the specific
   saved comment, never an inferred thread state.

## D. Set validation, receipt, and publication

### D1. Set validation — in staging, before any publication
Run each check below; append each command, its verbatim output, and
its exit code to "$RECEIPT". Any failed check: stop, report, preserve
"$STAGE_DIR", publish nothing.

The receipt's two artifact-inventory sections use this exact
machine-readable format:

BEGIN BYTE INVENTORY
<decimal byte count><TAB><canonical final path>
END BYTE INVENTORY

BEGIN SHA256 INVENTORY
<64 lowercase hexadecimal characters><TAB><canonical final path>
END SHA256 INVENTORY

Each marker line must occur exactly once. Inventory rows are sorted
lexicographically by canonical final path. Each canonical final path
appears exactly once in each inventory, with no duplicate, missing,
or additional path. The receipt itself is excluded from both
inventories.

Command transcripts and other receipt content must appear outside
these marker-delimited sections. Later verification parses inventory
rows only from between the corresponding markers.

1. N=$(wc -l < "$STAGE_DIR"/lh-name-status.txt); record N.
2. Manifest data-row count (excluding the header) equals N.
3. Every manifest data row has exactly the five schema fields:
   awk -F'\t' 'NR>1 && NF!=5' "$STAGE_DIR"/lh-manifest.tsv
   must produce no output.
4. "$STAGE_DIR"/lh-files/ contains exactly N files, each named as a
   diff_file in the manifest — none missing, none extra.
5. All six staged single files exist: lh-name-status.txt,
   lh-manifest.tsv, lh-diffstat.txt, lh-log.txt, lh-deps.diff,
   lh-threads.md.
6. Build "$WORK_DIR"/byte-inventory.tsv from every staged artifact
   except the receipt. Each row must have exactly:

   <decimal byte count><TAB><canonical final path>

   Compute the count from the staged content but label it with its
   final path under notes/raw/. Sort rows lexicographically by
   canonical final path and verify that no path is duplicated.

   Append to "$RECEIPT", in order:

   BEGIN BYTE INVENTORY
   <the complete sorted contents of byte-inventory.tsv>
   END BYTE INVENTORY

   Record separately in the command transcript whether lh-deps.diff
   is empty. The receipt itself is excluded because its byte count
   changes while it is being written.
7. lh-threads.md contains all five exact-command headings from C.7,
   each followed by its fenced output; the headRefOid check from C.7
   passed; every hasNextPage value in the saved GraphQL output is
   false.

8. Build "$WORK_DIR"/sha256-inventory.tsv from every staged artifact
   except the receipt. Each row must have exactly:

   <64 lowercase hexadecimal characters><TAB><canonical final path>

   Compute each checksum from the staged content but label it with its
   final path under notes/raw/. Sort rows lexicographically by
   canonical final path and verify that no path is duplicated.

   Append to "$RECEIPT", in order:

   BEGIN SHA256 INVENTORY
   <the complete sorted contents of sha256-inventory.tsv>
   END SHA256 INVENTORY

After all checks pass, finish "$RECEIPT": it now durably records the
gate and set-validation commands with their verbatim outputs and exit
codes — row counts, per-file diff count, byte counts, and checksums of
the other staged artifacts keyed by their final paths — sufficient for
a later read-only audit. The receipt is closed before publication, is
published unmodified, and is never appended to or modified afterward.

### D2. Publication — no-clobber, fixed order, receipt last
Publication is no-clobber: it must fail rather than overwrite any
existing raw artifact; existing raw artifacts are never edited,
replaced, or deleted.

Before publication:

1. Confirm "$RAW_DIR" is a real directory and not a symbolic link.
2. If "$RAW_DIR"/lh-files exists, confirm it is a real directory and
   not a symbolic link.
3. If "$RAW_DIR"/lh-files does not exist, create it, check the exit
   code, and then confirm it is a real directory and not a symbolic
   link.
4. Confirm `python3` is available; it is the declared interpreter for
   the exclusive-create helper below.

Then publish in this fixed order: lh-name-status.txt,
lh-manifest.tsv, lh-diffstat.txt, lh-log.txt, each
lh-files/<index>.diff in ascending index order, lh-deps.diff,
lh-threads.md, and lh-harvest-receipt.txt last:
1. Create the final path with this exact Python helper, which uses
   `O_CREAT | O_EXCL` so creation itself fails if the destination
   already exists:

   python3 - "<staged path>" "<final path>" <<'PY'
   import os
   import shutil
   import sys

   source, destination = sys.argv[1], sys.argv[2]
   flags = os.O_WRONLY | os.O_CREAT | os.O_EXCL
   fd = os.open(destination, flags, 0o644)

   try:
       with open(source, "rb") as src, os.fdopen(fd, "wb") as dst:
           shutil.copyfileobj(src, dst, length=1024 * 1024)
   except BaseException:
       try:
           os.close(fd)
       except OSError:
           pass
       raise
   PY

   Redirect stderr according to the phase stderr policy and check the
   exit code. On failure, stop and report; a destination may have
   been created only partially, so a rerun must enter recovery under
   A.4.

   Exception: in a recovery run, a final path whose checksum was
   already verified identical to its staged counterpart is skipped,
   not re-created.
2. cmp -s "<staged path>" "<final path>"; check the exit code.
On any failure: stop; report which artifacts were published and which
remain unpublished; preserve "$STAGE_DIR". This leaves an interrupted
partial publication; a rerun resumes only through step F. The receipt
is copied last so that its presence under notes/raw/ marks a
completely published, validated set.

## E. Merge-base drift record — only after D2 succeeds, exactly once
1. Guard: run the drift-record classification (step A). It must
   report ABSENT; if a COMPLETE record exists, stop and report (the
   record is written exactly once; never append a duplicate). A
   PARTIAL/MALFORMED or DUPLICATE result already stops inside the
   classification and awaits the user's decision.
2. Fetch the current sigp unstable tip by explicit URL and forced
   explicit refspec:
   git -C "$LH_CLONE" fetch https://github.com/sigp/lighthouse.git +refs/heads/unstable:refs/remotes/harvest/sigp-unstable
3. OBSERVED_UNSTABLE=$(git -C "$LH_CLONE" rev-parse refs/remotes/harvest/sigp-unstable)
   OBSERVED_MB=$(git -C "$LH_CLONE" merge-base "$PR_HEAD" refs/remotes/harvest/sigp-unstable)
   Capture and check both exit codes; on failure, stop and report the
   failed command and rc. The published harvest artifacts stay; the
   session is NOT done, and a rerun resumes at A.3 (drift record
   only).
4. Construct the record first, append it once, then verify the
   appended bytes exactly:
   a. Before constructing or appending the record, verify that
      "$REFS" is empty or ends with a line-feed byte. Use this exact
      check:

      python3 - "$REFS" <<'PY'
      from pathlib import Path
      import sys

      content = Path(sys.argv[1]).read_bytes()
      raise SystemExit(
          0 if not content or content.endswith(b"\n") else 1
      )
      PY

      Redirect stderr according to the phase stderr policy and check
      the exit code. If the check fails: stop and report that refs.md
      lacks a terminal newline; do not normalize, edit, or append to
      it.

      If "$REFS" is non-empty, the append uses its existing terminal
      line feed as the separator. If "$REFS" is empty, the record
      begins at byte zero. Do not insert any additional separator
      before the staged record. The byte-exact verification below
      continues to compare the appended tail directly with drift-record.txt.
   
   b. Write the complete section below to "$WORK_DIR"/drift-record.txt,
      substituting the run date and captured values. Confirm the file
      contains exactly one heading line and all three list items
      before touching "$REFS".
   c. Append it in a single command and check the exit code:
      cat "$WORK_DIR"/drift-record.txt >> "$REFS"
   d. Verify the append exactly: the tail of "$REFS" must be
      byte-identical to the constructed record —
      tail -c "$(wc -c < "$WORK_DIR"/drift-record.txt)" "$REFS" | cmp -s - "$WORK_DIR"/drift-record.txt
      — and re-running the drift-record classification must now
      report exactly one COMPLETE record. On any verification
      failure: stop and report; never edit, retry, or re-append
      ("$REFS" may hold a partial append) — await the user's
      decision.

   ## Phase 2a merge-base drift record
   - <date> | Phase 2a | sigp/lighthouse@unstable observed HEAD:
     <OBSERVED_UNSTABLE> (2026-07-12 check-time value:
     7d2b64341bcabaed85332fa59e7be28d3740e88a; moving branch, drift
     expected).
   - merge-base of PR #39 head vs observed unstable: <OBSERVED_MB>
     (2026-07-12 check-time value:
     494b00a3491e2c5e281f6972aa00694b17f16722).
   - Context only: the harvest diff base remains the pinned BASE
     dfb259171a65cacd6db57b8874af8f543cabcb7a per the 2026-07-13
     decision-log entry. Drift in sigp/lighthouse@unstable does not
     invalidate this harvest and is not a pin mismatch.

   This record is metadata in refs.md, not a re-pin, and requires no
   user decision unless the user later chooses to act on it.

## F. Recovery — interrupted partial publication (from A.4)
Publishing the missing files alone is not assumed safe; recovery
publishes a file only after proving the published subset matches the
validated staged set exactly. Record every recovery check and result
in "$RESUME_LOG"; never create, append to, or modify the staged or
published receipt.
1. Require "$STAGE_DIR" to exist and contain a staged artifact set,
   including a finished "$RECEIPT". If it does not: stop and report;
   await the user's decision (never re-harvest over a partial
   publication, and never delete or replace published files).
2. Re-run the validation checks represented by D1 against the staged
   set, but do not execute any D1 instruction that creates, appends
   to, finishes, or otherwise modifies "$RECEIPT".

   Parse the existing receipt's marker-delimited inventories and
   independently recompute the staged row counts, field counts, exact
   diff-file set, thread checks, byte counts, and checksums. Verify
   that the recomputed values and exact artifact set match the closed
   receipt.

   Record every recovery command, output, and exit code only in
   "$RESUME_LOG". If validation fails: stop and report; await the
   user's decision.
3. Verify the published subset against the exact expected set defined
   by the staged manifest and the final-artifact list:
   - every file present under "$RAW_DIR"/lh-files/ must be a
     diff_file named in the staged manifest; any unexpected file:
     stop, report it; await the user's decision;
   - Phase 2a owns exactly these top-level destinations under
     notes/raw/:

     lh-name-status.txt
     lh-manifest.tsv
     lh-diffstat.txt
     lh-log.txt
     lh-files/
     lh-deps.diff
     lh-threads.md
     lh-harvest-receipt.txt

     Recovery inspects only these owned destinations and the files
     inside lh-files/. Other notes/raw/lh-* paths are outside this
     phase's recovery scope and must not cause recovery to fail;
   - sha256 of every already-published artifact must equal its staged
     counterpart's; any mismatch: stop, report the path and both
     checksums; await the user's decision.
4. Only if 1–3 pass: complete publication per D2 (skipping the
   checksum-verified already-published files; the receipt still
   published last), then continue with step E under its guard.

## Report (session output)
Capture and check the exit code of every reporting command (wc -l,
wc -c, ls, etc.). If a reporting command fails after the artifacts
are written: preserve the artifacts, report the failed command and
exit code, and do NOT claim completion — the report commands can be
re-run by hand.
- Resolved: STUDY_ROOT, LH_CLONE, BASE, PR_HEAD, RAW_DIR, WORK_DIR,
  STAGE_DIR, RESUME_LOG
- Session-state classification result (which branch of A ran), and
  the drift-record classification result
- Pin verification: BASE_CONTEXT and HEAD_CONTEXT verbatim
- PR_FETCHED value and its equality with PR_HEAD; headRefOid
  cross-check result from lh-threads.md
- lh-name-status.txt line count; manifest data-row count (excluding
  header); lh-files/ diff-file count — all three values, shown equal
- Result of the five-field manifest validation; confirmation that no
  Git-quoted path was encountered
- Byte count of lh-deps.diff, with an explicit statement if it is
  empty
- Confirmation that set validation passed and the receipt records it,
  and that every published artifact compared identical to its staged
  counterpart; in resume or recovery branches, the "$RESUME_LOG" path
  and its results
- The appended Phase 2a merge-base drift record, verbatim, and the
  result of its byte-exact append verification
- Confirmation that the only paths created under notes/raw/ by this
  session are the eight listed output artifacts (lh-files/ counted as
  one), and that no existing raw artifact was edited, replaced, or
  deleted
- Confirmation that nothing was summarized, interpreted, or committed
- List of all files created or changed (full paths), including
  temporary files under "$WORK_DIR"
- Any deviation already stopped the session per its rule
Then stop.

## Done when
Each criterion below is a final condition that a later read-only
audit can verify independently from the published artifacts, the
receipt, and notes/refs.md. Session-behavior rules — verbatim
harvesting, stopping on failed gates, not committing, keeping
temporary files out of notes/raw/ — are governed by the sections
above and CLAUDE.md, are confirmed in the session report, and are not
completion criteria here.
- All eight published artifacts exist under notes/raw/:
  lh-name-status.txt, lh-manifest.tsv, lh-diffstat.txt, lh-log.txt,
  lh-files/<index>.diff (one per manifest row), lh-deps.diff,
  lh-threads.md, lh-harvest-receipt.txt
- lh-manifest.tsv data-row count, excluding header, equals the
  lh-name-status.txt line count; every data row has exactly the five
  schema fields; no path field is a Git-quoted representation
- notes/raw/lh-files/ contains exactly the manifest-listed diff
  files — one per data row, none missing, none extra
- lh-deps.diff exists; its byte count is recorded in the receipt,
  with an explicit statement there if it is empty
- lh-threads.md contains, for each of the five listed commands, the
  exact-command heading followed by its verbatim fenced output; the
  headRefOid in the saved `gh pr view` output equals PR_HEAD; every
  hasNextPage value in the saved GraphQL output is false
- lh-harvest-receipt.txt records the gate and set-validation commands
  with their verbatim outputs and exit codes; every recorded exit
  code is 0. It contains exactly one marker-delimited BYTE INVENTORY
  and exactly one marker-delimited SHA256 INVENTORY in the D1 formats.
  Both inventories are sorted by canonical final path, contain each
  non-receipt artifact exactly once, contain no duplicate or
  additional path, and match the byte counts and checksums recomputed
  from the published artifacts
- notes/refs.md contains exactly one Phase 2a merge-base drift
  record, and it is COMPLETE per the drift-record classification
  (heading plus all three template list items with 40-hex observed
  values)
