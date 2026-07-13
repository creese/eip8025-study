# Phase 1a-iii — EIP-8025 full-text snapshot harvest (pre/post PR)

This is a HARVEST session, not synthesis: save file contents exactly
as stored, no paraphrasing, no interpretation, no Markdown fences, no
added headers or trailing text. No live web. Do not modify the
repository clone. Do not commit. This session runs before Phase 1b,
which consumes its two output artifacts.

## Variables
STUDY_ROOT=/work
EIPS_CLONE=/work/repos/EIPs
PRE_REV=2215c17cde2c7ee0bb5068f2beb573c4776e92ac
POST_REV=d5653bc4d9b86997e069567dcd1eb8766b0c8a55
REFS="$STUDY_ROOT/notes/refs.md"
RAW_DIR="$STUDY_ROOT/notes/raw"
WORK_ROOT="$STUDY_ROOT/.work"
WORK_DIR="$WORK_ROOT/P1a-iii"
POST_FINAL="$RAW_DIR/eip-8025-post-pr.md"
PRE_FINAL="$RAW_DIR/eip-8025-pre-pr.md"

PRE_REV is the pinned merge-base fixed as the EIP PR diff base by the
Phase 1a-i decision-log entry in refs.md; POST_REV is the pinned EIP
PR head. Every git command uses `git -C "$EIPS_CLONE"`; every write
path starts with "$STUDY_ROOT" except the /tmp status snapshots.
No step may depend on the current working directory.

Staging and scratch names (declared here; NOTHING is created until
the allocation step after all gates pass):
POST_TMP="$WORK_DIR/eip-8025-post-pr.md.tmp"
PRE_TMP="$WORK_DIR/eip-8025-pre-pr.md.tmp"
POST_ERR="$WORK_DIR/eip-8025-post-pr.stderr.tmp"
PRE_ERR="$WORK_DIR/eip-8025-pre-pr.stderr.tmp"
STATUS_BEFORE, STATUS_AFTER: mktemp-allocated /tmp files for
clone-state snapshots.

notes/raw/ is the append-only published-evidence store. Existing
artifacts there are immutable. This session may add POST_FINAL and
PRE_FINAL, but it must not create any temporary, stderr, status,
staging, or recovery file under notes/raw/, and it must never edit,
replace, or delete an existing raw artifact.

WORK_DIR contains non-authoritative, session-owned staging and
diagnostics. It is never cited as evidence and must not be committed.
The empty WORK_DIR directory may exist before or after the session;
only files inside it indicate session state.

Stderr policy: for harvest commands (git show), success requires
rc=0 AND an empty stderr capture. For publication commands (ln and
staged-name rm), rc is authoritative for success and the filesystem
checks in cleanup verification confirm the outcome; their stderr is
captured into POST_ERR/PRE_ERR for FAILURE DIAGNOSTICS ONLY. The
diagnostics-ordering rule below prevents overwriting unreported
diagnostics.

## Shared pre-publication failure rule
For EVERY failure at any step before publication (step P below):
first report the diagnostics that step requires; then remove any of
POST_TMP, PRE_TMP, POST_ERR, PRE_ERR, STATUS_BEFORE, STATUS_AFTER
that this session created, checking each rm's exit code; then stop.
Never remove a file that existed before this session. Overwrite and
interrupted-run protection guarantees that the four named work files
did not pre-exist; the /tmp files are created by this session by
construction. Never remove POST_FINAL, PRE_FINAL, or any other path
under notes/raw/. Do not remove WORK_DIR itself; it may remain as an
empty directory. Publication failures (step P) have their own
preservation rules and are excluded from this cleanup rule.

## Path confirmation — before any gate or harvest step
1. test -d "$EIPS_CLONE" and git -C "$EIPS_CLONE" rev-parse --git-dir
If either fails: stop, report (do not guess another path).
2. git -C "$EIPS_CLONE" remote get-url origin
Must reference github.com/ethereum/EIPs. If not: stop, report.
3. Confirm these exist; if not: stop, report:
   "$REFS"
   "$RAW_DIR"
   "$RAW_DIR/eip-manifest.tsv"
   RAW_DIR must be a directory. WORK_ROOT and WORK_DIR are created
   only during scratch allocation; do not create them during path
   confirmation.

## Gate
1. Pin verification against refs.md — derived from the variables,
never from separately duplicated literals:
PRE_PIN_CONTEXT=$(grep -nF -C 2 -- "$PRE_REV" "$REFS"); PRE_PIN_RC=$?
POST_PIN_CONTEXT=$(grep -nF -C 2 -- "$POST_REV" "$REFS"); POST_PIN_RC=$?
Validation:
- Both rc values must be 0 (hash present in refs.md).
- PRE_PIN_CONTEXT must identify PRE_REV's role as the EIP PR
	merge-base / harvest diff base.
- POST_PIN_CONTEXT must identify POST_REV's role as the EIP PR
	head.
- Report both captured contexts verbatim.
If either rc is nonzero, or either role cannot be confirmed from
its context: stop, report both contexts and rc values.
2. Object existence:
git -C "$EIPS_CLONE" cat-file -e "$PRE_REV"^{commit}
git -C "$EIPS_CLONE" cat-file -e "$POST_REV"^{commit}
If either missing: stop, report.
3. Overwrite and interrupted-run protection:
   - notes/raw/ is append-only; existing artifacts are immutable.
     Stop and report if either published destination already exists:
     "$POST_FINAL"  "$PRE_FINAL"
   - Stop and report if any named work file already exists:
     "$POST_TMP"  "$PRE_TMP"  "$POST_ERR"  "$PRE_ERR"
   - If WORK_DIR exists and contains any other entry, stop and report
     a complete listing of its contents; do not delete or reuse those
     entries.
   Existing files under WORK_DIR indicate a previously interrupted
   or externally modified run. Preserve them for inspection.
   An existing empty WORK_DIR is permitted.

## Scratch allocation — only after all gates pass
Create the work directories:

mkdir -p "$WORK_ROOT"; capture rc immediately
mkdir -p "$WORK_DIR"; capture rc immediately

If either command returns nonzero: report the failed command and rc,
then stop. Do not create or remove anything under notes/raw/.

After creation, verify that WORK_DIR contains no entries. If it is
not empty: report a complete listing and stop; do not delete or reuse
the contents.

Publication uses hard links, so verify that WORK_DIR and RAW_DIR are
on the same filesystem:

WORK_DEV=$(stat -c %d "$WORK_DIR"); WORK_DEV_RC=$?
RAW_DEV=$(stat -c %d "$RAW_DIR"); RAW_DEV_RC=$?

Capture each rc immediately. Both rc values must be 0, and WORK_DEV
must equal RAW_DEV. If either stat fails or the device IDs differ:
report both commands, rc values, and captured device values; then
stop. Do not harvest and do not fall back to mv, cp, or another
publication method.

The same-device check establishes only that a hard link is possible
at the filesystem level. The publication step still checks the exit
code of each ln command.

STATUS_BEFORE=$(mktemp /tmp/eip-status-before.XXXXXX); check rc
STATUS_AFTER=$(mktemp /tmp/eip-status-after.XXXXXX); check rc
Capture each rc immediately; if either mktemp fails, apply the shared
failure rule (diagnostics: the failed command and rc).
Then take the before-snapshot with optional-lock suppression, to
avoid optional index-refresh writes:
GIT_OPTIONAL_LOCKS=0 git -C "$EIPS_CLONE" status --porcelain=v1 > "$STATUS_BEFORE"; check rc
(nonzero rc: shared failure rule).

## EIP path resolution — from study artifacts only, no assumptions
1. Read "$RAW_DIR/eip-manifest.tsv" (data rows only).
2. Select rows whose path basename is exactly `eip-8025.md`
   (the path matches `(^|/)eip-8025\.md$`).
- Exactly one row: use it. EIP_PATH = its path column.
- Zero rows or more than one: stop (shared failure rule), report
  all candidate rows — do not choose silently.
3. Status handling from that row:
- M: PRE_PATH = EIP_PATH.
- Rxx: EIP_PATH = new path; PRE_PATH = old_path_if_renamed.
- A, D, or anything else: stop (shared failure rule), report the
	row verbatim — per refs.md the EIP predates this PR, so these
	statuses indicate a manifest or resolution anomaly.

## Harvest — validated staging
1. git -C "$EIPS_CLONE" show "$POST_REV:$EIP_PATH" > "$POST_TMP" 2> "$POST_ERR"
Capture rc=$? immediately. If rc != 0: shared failure rule
(diagnostics: command, rc, full content of "$POST_ERR", staged
file byte count).
2. git -C "$EIPS_CLONE" show "$PRE_REV:$PRE_PATH" > "$PRE_TMP" 2> "$PRE_ERR"
Same rc rule as step 1 (diagnostics from "$PRE_ERR").
3. Staged-content validation:
test -s "$POST_TMP" && test -s "$PRE_TMP"
(either empty: shared failure rule; diagnostics: which file)
test ! -s "$POST_ERR" && test ! -s "$PRE_ERR"
(either non-empty despite rc=0: shared failure rule; diagnostics:
its content verbatim — do not treat warnings as clean)
The harvest stderr requirement is now satisfied; both captures
are verified empty and are reused by publication for failure
diagnostics only.
4. Clone-state after-snapshot and comparison:
GIT_OPTIONAL_LOCKS=0 git -C "$EIPS_CLONE" status --porcelain=v1 > "$STATUS_AFTER"; check rc
(nonzero rc: shared failure rule)
cmp -s "$STATUS_BEFORE" "$STATUS_AFTER"; capture rc immediately:
- rc=0: continue.
- rc=1 (snapshots differ): report BOTH snapshot files verbatim
	FIRST, then apply the shared failure rule.
- rc>1 (cmp operational error): report the command and rc, then
	apply the shared failure rule.
Scope of this check: it confirms the session did not change the
index or the tracked/untracked worktree state reported by
git status --porcelain=v1. It does not inspect ignored files or
all repository metadata, and it does NOT require the clone to
have been clean.

## P. Publication — atomic no-clobber publication, run separately
Publication uses hard links: `ln` fails if the destination already
exists, so an existing final artifact can never be replaced. This is
the enforcement mechanism; the recheck in step 0 is only a fast-fail
courtesy. Publication of the PAIR is still not transactional; the
per-file failure states below govern partial publication. 
Staged and final paths are in different directories. The
same-filesystem check performed during scratch allocation establishes
that WORK_DIR and RAW_DIR have the same device ID. If an ln
nevertheless fails because hard links are unsupported, prohibited, or
otherwise unavailable: report it as an environment finding and stop.
Do NOT fall back to mv, cp, or another replacing publication method.
Success for every ln and staged-name rm below is determined by its
exit code; stderr is captured for failure diagnostics only.
Diagnostics ordering rule: whenever a stderr capture holds diagnostics
from a failed command, report its content verbatim BEFORE any cleanup
that removes it, and never redirect another command into it first.
0. Fast-fail recheck (not the enforcement mechanism):
test ! -e "$POST_FINAL" && test ! -e "$PRE_FINAL"
If either exists: report it and apply the shared pre-publication
failure rule. Do not publish.
1. ln "$POST_TMP" "$POST_FINAL" 2> "$POST_ERR"; capture rc immediately.
If rc != 0: report the rc and the full content of "$POST_ERR"
verbatim, and the existence + byte count of both staged files;
then remove POST_ERR, PRE_ERR, STATUS_BEFORE, STATUS_AFTER
(checking each rm rc); PRESERVE both staged files for manual
inspection; stop.
If rc = 0: rm "$POST_TMP" 2> "$POST_ERR"; capture rc.
If this rm fails (rc != 0): the final artifact already exists and
is valid — PRESERVE it, report the rm failure and the content of
"$POST_ERR" verbatim, and that POST_TMP remains (same inode as
the final); remove POST_ERR, PRE_ERR, STATUS_BEFORE, STATUS_AFTER
(checking each rm rc); stop; the session is NOT done. Do not
retry publication and do not remove the final artifact.
2. ln "$PRE_TMP" "$PRE_FINAL" 2> "$PRE_ERR"; capture rc immediately.
If rc != 0: report the rc and the full content of "$PRE_ERR"
verbatim, and exactly which final and staged files now exist;
PRESERVE the published post-PR final file and the remaining
staged file; remove POST_ERR, PRE_ERR, STATUS_BEFORE,
STATUS_AFTER (checking each rm rc); stop.
A subsequent run stops under overwrite protection until the
partial-publication state is resolved manually.
If rc = 0: rm "$PRE_TMP" 2> "$PRE_ERR"; capture rc. If this rm
fails: same rule as the post-PR case — PRESERVE the final, report
the failure and "$PRE_ERR" verbatim, clean scratch (checking each
rm rc), stop, NOT done.
3. Cleanup on success — verified:
Remove POST_ERR, PRE_ERR, STATUS_BEFORE, STATUS_AFTER separately,
capturing and checking every rm exit code. 
Then verify that NONE of POST_TMP, PRE_TMP, POST_ERR, PRE_ERR,
STATUS_BEFORE, STATUS_AFTER exists. Also verify that WORK_DIR contains
no entries. The empty WORK_DIR directory itself may remain.
Any rm failure or surviving file: report it;
the session is NOT done. (In the publication-failure states
above, this verification applies only to the files those states
say to remove — deliberately preserved staged/final files are
excluded.) These filesystem checks, together with the per-command
exit codes, are what establish publication success.

## Report (session output)
Capture and check the exit code of every reporting command
(sha256sum, wc -c, etc.). If a reporting command fails after
successful publication: preserve the published artifacts, report the
failed command and exit code, and do NOT claim completion — manual
review resolves it (the report commands can be re-run by hand).
- Resolved: EIPS_CLONE, EIP_PATH, PRE_PATH, PRE_REV, POST_REV,
  RAW_DIR, WORK_DIR
- Pin verification: PRE_PIN_CONTEXT and POST_PIN_CONTEXT verbatim
- sha256sum and byte count (wc -c) of both final output files
- Confirmation: neither final file empty; both harvest commands
completed with empty captured stderr; every publication command
returned rc=0 and cleanup verification passed (publication stderr
captures are failure diagnostics only); clone-state comparison
passed (git status --porcelain=v1 scope, as defined above)
- Confirmation that the only paths created under notes/raw/ by this
  session are POST_FINAL and PRE_FINAL; no staging, stderr, status,
  temporary, or recovery path was created there
- Confirmation that WORK_DIR is empty after successful cleanup; in a
  publication-failure state, list every deliberately preserved path
  under WORK_DIR
- Any deviation already stopped the session per its rule

## Done when
- Pin verification against refs.md passed for both revisions, roles
confirmed from the captured contexts
- Both final files exist, non-empty, containing exact `git show`
output — no fences, no added text
- Both harvest commands completed with empty captured stderr; every
publication command returned rc=0, confirmed by the cleanup
verification filesystem checks
- Clone-state comparison passed (git status --porcelain=v1 scope;
session changed nothing it measures)
- Cleanup verification passed: no staged, stderr, or status scratch
  files remain; WORK_DIR contains no entries
- The only files created under notes/raw/ by this session are
  POST_FINAL and PRE_FINAL; no existing raw artifact was edited,
  replaced, or deleted
- Report section complete, every reporting command rc-checked
- Nothing committed. List the final files created, and separately
  report whether WORK_ROOT or WORK_DIR was created by this session.
  Stop.
