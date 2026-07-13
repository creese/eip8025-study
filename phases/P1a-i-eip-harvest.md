# Phase 1a-i — EIP PR #11604 mechanical harvest

Refs pinned in notes/refs.md. This is a harvest session: save output
verbatim, no summarization or interpretation. Do not commit.

## Variables
Pinned merge-base (BASE) and pinned PR head (PR_HEAD) below. Set all
four, echo all four, run the path-confirmation checks, and proceed
only if they pass. No human pause is required; stop only on a failed
check.

STUDY_ROOT=/work
EIPS_CLONE=/work/repos/EIPs
BASE=2215c17cde2c7ee0bb5068f2beb573c4776e92ac
PR_HEAD=d5653bc4d9b86997e069567dcd1eb8766b0c8a55

Never use HEAD as a variable name; always $PR_HEAD.
No command in this session may depend on the current working
directory: every git command uses `git -C "$EIPS_CLONE"`, every
file write path starts with "$STUDY_ROOT".

## Path confirmation — before any gate or harvest step
1. git -C "$EIPS_CLONE" remote get-url origin
   Must reference github.com/ethereum/EIPs. If not: stop, report.
2. Confirm "$STUDY_ROOT"/notes/refs.md exists. If not: stop, report.

## Gate — all checks must pass before any harvest step
1. Fetch with forced explicit refspecs:
   git -C "$EIPS_CLONE" fetch origin +master:refs/remotes/origin/master
   git -C "$EIPS_CLONE" fetch origin +pull/11604/head:refs/remotes/origin/pr/11604
2. PR head equality check:
   PR_FETCHED=$(git -C "$EIPS_CLONE" rev-parse refs/remotes/origin/pr/11604)
   test "$PR_FETCHED" = "$PR_HEAD"

If the test fails: stop and report both values:
- PR_FETCHED
- PR_HEAD
3. Base object existence (NOT equality with current origin/master —
   master may have advanced; only the pinned object must exist):
   git -C "$EIPS_CLONE" cat-file -e d2fb2b2e6104e7484f552bf142c500a9d2a6ef4e^{commit}
   If missing: stop, report.
4. Merge-base integrity check (deterministic given both objects; a
   mismatch means a shallow/corrupt local clone, not upstream drift):
   git -C "$EIPS_CLONE" merge-base d2fb2b2e6104e7484f552bf142c500a9d2a6ef4e $PR_HEAD
   Must equal $BASE. If not: stop, report.
5. Overwrite protection (raw/ is append-only). Two distinct checks:
a. Stop and report if any of these FILES already exists:
	"$STUDY_ROOT"/notes/raw/eip-name-status.txt
	"$STUDY_ROOT"/notes/raw/eip-manifest.tsv
	"$STUDY_ROOT"/notes/raw/eip-diffstat.txt
	"$STUDY_ROOT"/notes/raw/eip-log.txt
	"$STUDY_ROOT"/notes/raw/eip-threads.md
b. Stop and report if "$STUDY_ROOT"/notes/raw/eip-files/ exists
	AND is non-empty. (An existing empty directory is fine.)
6. Only after 5a and 5b pass:
   mkdir -p "$STUDY_ROOT"/notes/raw/eip-files

## Diff-base decision (log before harvesting)
Append to `## Decision log` in "$STUDY_ROOT"/notes/refs.md; create that section if absent:
"2026-07-12 | Phase 1a-i | EIP PR harvest diff base =
merge-base 2215c17cde2c7ee0bb5068f2beb573c4776e92ac (not master
baseRefOid, which has advanced past the fork point per the Phase 0
decision-input note)."

## Harvest — run exactly, in order
1. Primary evidence — the raw name-status output; the manifest in
   step 2 is derived from this file:
   git -C "$EIPS_CLONE" diff --name-status -M $BASE..$PR_HEAD > "$STUDY_ROOT"/notes/raw/eip-name-status.txt
2. Build "$STUDY_ROOT"/notes/raw/eip-manifest.tsv mechanically from
   eip-name-status.txt. 
   The file must be TSV, not Markdown. Use this tab-separated header row:
   index	path	status	old_path_if_renamed	diff_file
   
   Use zero-padded indexes starting at 001.
   Rules: record paths exactly as emitted by name-status; quote paths only
   when constructing shell commands; for Rxx rows, path = new path and
   old_path_if_renamed = old path; for D rows, path = deleted path;
   diff_file = notes/raw/eip-files/<index>.diff (path relative to the
   study root, matching the file written under "$STUDY_ROOT").
   One data row per name-status line; data-row count, excluding the header,
   must equal the line count of eip-name-status.txt.
3. Human-readable summary only, not used for reconciliation:
   git -C "$EIPS_CLONE" diff --stat $BASE..$PR_HEAD > "$STUDY_ROOT"/notes/raw/eip-diffstat.txt
4. git -C "$EIPS_CLONE" log --oneline $BASE..$PR_HEAD > "$STUDY_ROOT"/notes/raw/eip-log.txt
5. Per manifest row, write the per-file diff. Each command form ends
   with explicit redirection to that row's diff_file:
   A/M rows:
   git -C "$EIPS_CLONE" diff $BASE..$PR_HEAD -- "<path>" > "$STUDY_ROOT"/notes/raw/eip-files/<index>.diff
   D rows:
   git -C "$EIPS_CLONE" diff $BASE..$PR_HEAD -- "<deleted path>" > "$STUDY_ROOT"/notes/raw/eip-files/<index>.diff
   Rxx rows:
   git -C "$EIPS_CLONE" diff -M $BASE..$PR_HEAD -- "<old path>" "<new path>" > "$STUDY_ROOT"/notes/raw/eip-files/<index>.diff
6. Build "$STUDY_ROOT"/notes/raw/eip-threads.md: start from an empty
   file; before each command below, append a heading line with the
   exact command; then append its raw output verbatim in a fenced
   block. (gh commands are directory-independent; run as written.)
   - gh pr view 11604 --repo ethereum/EIPs --json title,state,body,baseRefName,headRefName,headRefOid,baseRefOid,isDraft,mergedAt,closed
   - gh api repos/ethereum/EIPs/issues/11604/comments --paginate
   - gh api repos/ethereum/EIPs/pulls/11604/comments --paginate
   - gh api repos/ethereum/EIPs/pulls/11604/reviews --paginate
   - gh api graphql -f query='query{repository(owner:"ethereum",name:"EIPs"){pullRequest(number:11604){reviewThreads(first:100){pageInfo{hasNextPage   endCursor},nodes{isResolved,comments(first:50){pageInfo{hasNextPage endCursor},nodes{author{login},body,path,createdAt}}}}}}}'
   If gh/network is unavailable: stop — I'll export manually.
   After saving the GraphQL output: if any hasNextPage is true, stop
   and report it (do not improvise pagination); output so far stays.

## Done when
- eip-name-status.txt exists; manifest data-row count, excluding header,
  equals eip-name-status.txt line count
- eip-files/ diff count = manifest data-row count, excluding header
- eip-diffstat.txt, eip-log.txt, eip-threads.md exist per rules above
- Decision-log entry written in "$STUDY_ROOT"/notes/refs.md
- No hasNextPage=true unreported
- Nothing summarized, nothing committed. List files created (full
  paths) and stop.
