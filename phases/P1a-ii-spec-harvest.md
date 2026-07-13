# Phase 1a-ii — consensus-specs snapshot harvest (PROVISIONAL-SPEC-BASELINE)

Refs pinned in notes/refs.md. This is a harvest session: save output
verbatim, no summarization or interpretation. Do not commit.

This is a SNAPSHOT harvest at one pinned commit — there is no PR, no
diff, no base/head pair, and no threads file in this phase.

## Variables
Set both, echo both, run the path-confirmation checks, and proceed
only if they pass. No human pause is required; stop only on a failed
check.

STUDY_ROOT=/work
SPECS_CLONE=/work/repos/consensus-specs
SPEC_PIN=932c6d691e0d5ed4a003c8bfb9c1c6731ce01924

SPEC_PIN is the dereferenced commit of tag v1.7.0-alpha.8, pinned in
refs.md as PROVISIONAL-SPEC-BASELINE (see [OPEN-Q1]).
No command in this session may depend on the current working
directory: every git command uses `git -C "$SPECS_CLONE"`, every
file write path starts with "$STUDY_ROOT".

## Exit-status rule for every grep / git grep in this session
Capture rc=$? immediately after the command, before anything else runs.
- rc=0: matches found — save output verbatim.
- rc=1: NO MATCHES — a valid result, NOT a failure. Save the (empty)
  output file and continue.
- rc>=2 (incl. 128): FAILURE — report the command, exit code, and
  stderr verbatim; stop the session.

## Path confirmation — before any gate or harvest step
1. Clone existence and validity (do not guess another path):
   test -d "$SPECS_CLONE"
   git -C "$SPECS_CLONE" rev-parse --git-dir
   If either fails: stop, report.
2. git -C "$SPECS_CLONE" remote get-url origin
   Must reference github.com/ethereum/consensus-specs. If not: stop, report.
3. Confirm "$STUDY_ROOT"/notes/refs.md exists. If not: stop, report.

## Gate — all checks must pass before any harvest step
1. Pinned object existence:
   git -C "$SPECS_CLONE" cat-file -e "$SPEC_PIN"^{commit}
   If missing: git -C "$SPECS_CLONE" fetch origin +refs/tags/v1.7.0-alpha.8:refs/tags/v1.7.0-alpha.8
   then re-check; if still missing: stop, report.
2. Tag-to-pin integrity check:
   git -C "$SPECS_CLONE" rev-parse v1.7.0-alpha.8^{commit}
   Must equal $SPEC_PIN. If not: stop, report both values.
3. Overwrite protection (raw/ is append-only). Two distinct checks:
   a. Stop and report if any of these FILES already exists:
      "$STUDY_ROOT"/notes/raw/spec-pin.txt
      "$STUDY_ROOT"/notes/raw/spec-tree.txt
      "$STUDY_ROOT"/notes/raw/spec-search-8025.txt
      "$STUDY_ROOT"/notes/raw/spec-search-execution-proof.txt
      "$STUDY_ROOT"/notes/raw/spec-manifest.txt
      "$STUDY_ROOT"/notes/raw/spec-todos.txt
   b. Stop and report if "$STUDY_ROOT"/notes/raw/spec-files/ exists
      AND is non-empty. (An existing empty directory is fine.)
4. Only after 3a and 3b pass:
   mkdir -p "$STUDY_ROOT"/notes/raw/spec-files

## Harvest — run exactly, in order
All git reads use "$SPEC_PIN" directly (no checkout needed).
1. Record the pin:
   git -C "$SPECS_CLONE" rev-parse "$SPEC_PIN" > "$STUDY_ROOT"/notes/raw/spec-pin.txt
2. Full top-two-level tree at the pin (orientation evidence):
   git -C "$SPECS_CLONE" ls-tree -r --name-only "$SPEC_PIN" -- specs/ | head -500 > "$STUDY_ROOT"/notes/raw/spec-tree.txt
3. Relevance search, transcripts saved. Absence is a valid,
   first-class outcome: if a search returns nothing, the empty
   transcript IS the evidence (CITED-ABSENT); do not widen the search
   or improvise alternatives beyond the two commands given. The
   exit-status rule above applies to both commands.
   For each `git grep` command below, capture `rc=$?` immediately after
   the command and apply the session grep exit-status rule before running
   any later command. 
   a. git -C "$SPECS_CLONE" grep -il "8025" "$SPEC_PIN" > "$STUDY_ROOT"/notes/raw/spec-search-8025.txt
   b. git -C "$SPECS_CLONE" grep -il -e "execution proof" -e "execution_proof" -e "execution-proof" "$SPEC_PIN" > "$STUDY_ROOT"/notes/raw/spec-search-execution-proof.txt
   These two transcripts are raw evidence: never edit them (they keep
   the "$SPEC_PIN": prefix git grep adds).
4. Build "$STUDY_ROOT"/notes/raw/spec-manifest.txt from the two
   transcripts, in this deterministic order:
   first, every path from spec-search-8025.txt in file order; then,
   every path from spec-search-execution-proof.txt not already
   present, in that file's order. Exact-line dedup only.
   Before the union, strip the exact literal prefix "$SPEC_PIN:" from
   each line using literal prefix removal (shell "${line#"$SPEC_PIN:"}"),
   not cut or an unescaped sed regex — the SHA-prefix strip must not
   touch path content. The strip happens only while building the
   manifest; the transcripts themselves stay untouched.
   One path per line. If both searches were empty, write a file
   containing exactly one line:
   NO-MATCHES (see spec-search-*.txt)
5. Copy matched files verbatim: for each path in spec-manifest.txt
   (skip if it is the NO-MATCHES line):
   git -C "$SPECS_CLONE" show "$SPEC_PIN:<path>" > "$STUDY_ROOT"/notes/raw/spec-files/<index>-<basename>
   using zero-padded indexes starting at 001, in manifest order.
6. Build spec-todos.txt as a marker transcript: append raw grep matches verbatim
   when present, and append explicit NO-MARKERS / NOT-APPLICABLE lines for absence
   cases.
   TODO/WIP/DRAFT markers within matched files only. For each copied
   file in spec-files/, in manifest order:
   grep -HnE "TODO|WIP|DRAFT" "$STUDY_ROOT"/notes/raw/spec-files/<index>-<basename> >> "$STUDY_ROOT"/notes/raw/spec-todos.txt
   The exit-status rule applies per file: rc=1 (no markers in that
   file) is valid — append the line
   NO-MARKERS: <index>-<basename> (exit 1)
   to spec-todos.txt and continue; rc>=2 stops the session.
   Do not tighten or loosen the pattern; substring matches (e.g.
   DRAFTING) are captured as-is for later phases to assess.
   If spec-manifest.txt is NO-MATCHES, write exactly one line:
   NOT-APPLICABLE (no matched files)

## Done when
- spec-pin.txt contains exactly $SPEC_PIN
- spec-tree.txt, both spec-search files, spec-manifest.txt,
  spec-todos.txt exist (empty search files are valid)
- spec-files/ count = manifest path count (0 if NO-MATCHES)
- Nothing summarized, nothing committed. List files created (full
  paths) and stop.
