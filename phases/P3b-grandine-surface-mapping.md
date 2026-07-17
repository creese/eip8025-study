# Phase 3b — Grandine surface mapping

## Purpose

Map every behavior cluster to a Grandine implementation surface
(path/symbol) or to a cited absence, using only the saved Phase 3a
evidence, and publish the result as the derived matrix
`notes/matrix/gr-surfaces.tsv`.

## Session type and scope

This is a synthesis / schema-filling session.

- No live research of any kind: no network access, no fetching, no
  cloning, no checkout, and no inspection of any live repository
  (Grandine, Lighthouse, consensus-specs, EIPs, or any other). All
  Grandine facts must come from the declared saved artifacts. If a
  fact cannot be supported from those artifacts, it is not recorded
  as a positive surface.
- Because this phase touches no live pinned repository content, no
  ref-verification gate is run. If any step appears to require live
  repository access or a re-pin decision, stop and report; do not
  perform the access or make the decision.
- This phase runs as a single bounded session covering all clusters.
  No batching and no combination with any other phase is permitted.
  Interrupted runs resume under "Resume and recovery" below.
- Model recollection of Grandine, Lighthouse, or the EIP is never
  evidence. Prior conversation history is never evidence.

## Declared inputs

Read-only inputs. Load these and nothing else — no other notes
files, matrices, raw artifacts, phase briefs, or phase
specifications.

1. `notes/02-clusters.md` — control input. Sole source of the
   cluster universe: the set of cluster identifiers and, for each
   cluster, its associated requirement IDs (`req_ids`) and any
   provisional (`PROV-SPEC`) tagging carried on the cluster or its
   requirements.
2. The Phase 3a receipts `notes/raw/gr-harvest-receipt.txt` and
   `notes/raw/gr-sub-remediation-receipt.txt` — control inputs that
   define the authoritative Phase 3a evidence set.
3. The verified Phase 3a evidence inventory: exactly the artifacts
   listed by the two receipts, after they pass Step 1 verification.
   An unrestricted `notes/raw/gr-*` wildcard is never treated as the
   authoritative set; a `gr-*` file not listed in either receipt is
   not evidence for this phase and must not be read or cited. The
   inventory includes per-cluster search transcripts named
   `notes/raw/gr-search-<slug>.txt` (original) and
   `notes/raw/gr-sub-search-<slug>.txt` (remediation), with `<slug>`
   defined under "Cluster slug rule".
4. This specification.

Input gate (Step 1 of the procedure): if `notes/02-clusters.md` or
either Phase 3a receipt is missing or empty, or receipt verification
fails, stop and report the failing input by name. Never substitute
another source.

If `notes/02-clusters.md` does not yield, for every cluster, an
unambiguous cluster identifier and a nonempty set of requirement
IDs, stop and report the affected cluster or section verbatim. Do
not open `notes/matrix/requirements.tsv` or any other file to fill
the gap.

## Cluster slug rule

`<slug>` for a cluster is derived from its exact `cluster_id` as it
appears in `notes/02-clusters.md`:

1. lowercase the exact identifier;
2. replace every maximal run of characters outside `[a-z0-9]` with a
   single hyphen;
3. trim any leading or trailing hyphens.

This rule is self-contained; never guess, search for, or substitute
alternative spellings. Every reference to
`notes/raw/gr-search-<slug>.txt` and
`notes/raw/gr-sub-search-<slug>.txt` in this specification uses this
derivation.

## Retained-positive annotation

A search transcript's retained positive evidence is defined
mechanically as the annotation line
`# Retained positive hits — file path : symbol` together with the
entries listed under it, each recording a surface in the
evidence-native form `file path : symbol`. A transcript containing
this annotation line contains retained positive evidence; a
transcript without it does not. This definition is used wherever
this specification refers to retained positive evidence.

## Output

- Primary output: `notes/matrix/gr-surfaces.tsv` (derived artifact).
- Validation receipt: `notes/receipts/P3b-validation-receipt.md`
  (durable, required for the completion audit).

This phase publishes no raw evidence: it must not create, modify,
or delete anything under `notes/raw/`. It must not modify
`notes/refs.md`, `notes/open-questions.md`, or any other control
file. The evidence/control-file publication-ordering rule is
therefore satisfied vacuously; if execution ever appears to require
such a modification, stop and report instead.

## Staging

All intermediate files go under `.work/p3b/` (create it if absent):

- `.work/p3b/gr-surfaces.tsv` — staged matrix.
- `.work/p3b/checks/` — validation command outputs and captured
  stderr.

Nothing under `.work/` is ever evidence and nothing from `.work/`
is ever cited. No staging file may be created under `notes/raw/`.

## Command success and stderr policy

Every command run by this phase must have its exit code checked
before its output is used or a dependent step begins. Capture
stderr for validation commands to `.work/p3b/checks/`. On a nonzero
exit or stderr indicating an error: stop, report the command, exit
code, and stderr location, and preserve `.work/p3b/` for diagnosis.
Output from an unchecked command is never treated as a passed check.

## Matrix schema

Tab-separated file. Exact header line (single tab between fields):

```
cluster_id	req_ids	grandine_surface	evidence	confidence	notes
```

Field rules:

- `cluster_id` — the cluster identifier exactly as it appears in
  `notes/02-clusters.md`.
- `req_ids` — the cluster's requirement IDs exactly as mapped in
  `notes/02-clusters.md`, comma-separated, no spaces. Never empty.
- `grandine_surface` — either one or more entries
  (semicolon-separated), or the literal token `CITED-ABSENT`. No
  other values. Each entry is a Grandine file path, or
  `path::symbol`, where `::` is matrix notation joining a path and
  a symbol. The path and the symbol are each transcribed exactly as
  they appear in the cited saved evidence (Phase 3a
  retained-positive annotations record them as
  `file path : symbol`). The path — and the symbol, when present —
  must each appear verbatim, as separate fixed strings, in at least
  one artifact cited in the row's `evidence` field; the combined
  `path::symbol` string is matrix notation and is not required to
  appear in the evidence.
- `evidence` — the schema-defined citation field. One or more
  pointers of the form `[raw:notes/raw/<file>]`, semicolon-separated.
  Every cited file must be part of the Step 1 verified evidence
  inventory. For a `CITED-ABSENT` row, the citation must include
  both `[raw:notes/raw/gr-search-<slug>.txt]` and
  `[raw:notes/raw/gr-sub-search-<slug>.txt]` for that row's own
  cluster, and both files must exist and pass Step 1 receipt
  verification.
- `confidence` — one of `high`, `medium`, `low`.
  - Positive rows: `high` = the cited evidence shows the exact path
    and symbol implementing the cluster's behavior; `medium` = the
    cited evidence shows the relevant path/module but the
    symbol-level match is partial; `low` = the cited evidence is
    indirect (e.g., naming or layout only).
  - `CITED-ABSENT` rows: confidence reflects the search coverage
    recorded in the cited original and remediation transcripts taken
    together (`high` = together they record searches covering the
    cluster's obvious terms and locations; lower otherwise).
- `notes` — free text; may be empty. Any factual or interpretive
  claim in `notes` must be supported by the file(s) already cited in
  this row's `evidence` field or must reference an existing
  `[OPEN-Qn]`. No new claims from memory. Do not assign new
  `[OPEN-Qn]` identifiers.

Provisional baseline: if a cluster or any of its requirement IDs
carries a provisional (`PROV-SPEC` /
PROVISIONAL-SPEC-BASELINE-dependent) tag in `notes/02-clusters.md`,
the row's `notes` field must carry `PROV-SPEC`, and the row must
not be worded as a confirmed proposal finding.

## Procedure

### Step 1 — input gate and Phase 3a receipt verification

1. Verify `notes/02-clusters.md`,
   `notes/raw/gr-harvest-receipt.txt`, and
   `notes/raw/gr-sub-remediation-receipt.txt` exist and are
   nonempty.
2. Verify each receipt is complete: its final line must be exactly
   the literal end-marker line `# END OF ARTIFACT`. A receipt whose
   final line is anything else is incomplete.
3. Extract from the two receipts the artifacts they list and the
   SHA-256 hash each records. Recompute every listed artifact's
   SHA-256 with `sha256sum` as a checked command and compare
   against the recorded value. A listed artifact that is missing,
   unreadable, or hash-mismatched fails the gate.
4. The verified evidence inventory is exactly the union of the
   artifacts listed by the two receipts. Compute the SHA-256 of each
   receipt with `sha256sum` as a checked command, then save to
   `.work/p3b/checks/gr-inventory.txt`: both receipt hashes, the
   full inventory, and the per-artifact hash-comparison results.
   Files matching `notes/raw/gr-*` that are not listed in either
   receipt are outside the evidence set: do not read or cite them.

Failure handling: stop and report the missing or empty input, the
missing end marker, or each missing or hash-mismatched artifact by
name; do not begin synthesis. Nothing has been staged; nothing to
clean up.

### Step 2 — extract the cluster universe

From `notes/02-clusters.md`, extract the ordered list of
(`cluster_id`, `req_ids`, provisional-tag) tuples. Record the
extracted list to `.work/p3b/checks/cluster-universe.txt`.

Failure handling: if any cluster lacks an identifier or requirement
IDs, stop and report it (see "Declared inputs"). Preserve
`.work/p3b/` as-is.

### Step 3 — fill the staged matrix

For each cluster, in the order extracted in Step 2, write exactly
one row to `.work/p3b/gr-surfaces.tsv`:

- Positive row: record a Grandine surface only when its path — and
  its symbol, when recorded — each appear verbatim in the content
  of an artifact in the Step 1 verified inventory and that artifact
  is cited in `evidence`. Retained-positive annotations record
  surfaces in the form `file path : symbol` (see
  "Retained-positive annotation").
- `CITED-ABSENT` row: only after considering the cluster's complete
  saved search evidence — read both
  `notes/raw/gr-search-<slug>.txt` (original) and
  `notes/raw/gr-sub-search-<slug>.txt` (remediation) in full. Both
  files must exist, be part of the Step 1 verified inventory, and
  be cited in the row's `evidence`. File existence alone never
  establishes absence: if either transcript contains a
  retained-positive annotation (see "Retained-positive
  annotation"), record a positive row citing it instead of
  `CITED-ABSENT`. An unsaved search or model impression is never
  evidence of absence.
- If a cluster has neither positive evidence in the verified
  inventory nor both of its search transcripts
  (`gr-search-<slug>.txt` and `gr-sub-search-<slug>.txt`), this is
  a Phase 3a coverage gap: finish staging rows for the remaining
  clusters where possible, then stop without publishing. Report
  every uncoverable `cluster_id`, preserve `.work/p3b/` (including
  the partial staged matrix), and wait for the user's decision. Do
  not perform live searches to fill the gap and do not publish a
  partial matrix.

### Step 4 — validate the staged matrix

Run each check as a command with a checked exit code; save outputs
under `.work/p3b/checks/`:

1. Header equals the exact schema header.
2. Every row has exactly 6 tab-separated fields.
3. The multiset of `cluster_id` values equals exactly the Step 2
   universe — one row per cluster, no duplicates, no extras, none
   missing.
4. Every `req_ids` value is nonempty and matches the Step 2 mapping
   for its cluster.
5. `grandine_surface` is nonempty on every row; rows are either
   positive or exactly `CITED-ABSENT`.
6. Every file cited in any `evidence` field is part of the Step 1
   verified evidence inventory (and therefore exists under
   `notes/raw/`).
7. Every `CITED-ABSENT` row cites both
   `notes/raw/gr-search-<slug>.txt` and
   `notes/raw/gr-sub-search-<slug>.txt` for its own cluster's slug,
   and both files are in the Step 1 verified inventory.
8. For every positive row, each `grandine_surface` entry's path —
   and its symbol, when present (split on the `::` matrix notation)
   — appears verbatim as a separate fixed-string match (`grep -F`)
   in at least one artifact cited by that row. The combined
   `path::symbol` string is not required to appear in any artifact.
9. For every `CITED-ABSENT` row, neither
   `notes/raw/gr-search-<slug>.txt` nor
   `notes/raw/gr-sub-search-<slug>.txt` for its cluster contains
   the retained-positive annotation line (fixed-string search for
   `# Retained positive hits`).
10. Every `confidence` value is `high`, `medium`, or `low`.
11. Every provisional-tagged cluster's row carries `PROV-SPEC` in
    `notes`.

Failure handling: on any failed check, stop before publication,
report the failed check and offending row(s), and preserve
`.work/p3b/` for diagnosis. `notes/` is untouched at this point.

### Step 5 — publish

Publication order (matrix first, receipt last, so the receipt
always attests an already-published file):

1. If `notes/matrix/gr-surfaces.tsv` already exists, follow "Resume
   and recovery" instead of overwriting.
2. Copy `.work/p3b/gr-surfaces.tsv` to
   `notes/matrix/gr-surfaces.tsv` with a checked command.
3. Compute the SHA-256 of the published file with a checked
   command.
4. Write `notes/receipts/P3b-validation-receipt.md` containing: the
   date; the SHA-256 of `notes/02-clusters.md`; the SHA-256 of each
   verified Phase 3a receipt (`gr-harvest-receipt.txt` and
   `gr-sub-remediation-receipt.txt`); the exact verified evidence
   inventory used, with the per-artifact hash-verification results
   from Step 1; the result and exit code of every Step 4 check; the
   row count; the count of positive vs `CITED-ABSENT` rows; and the
   SHA-256 of the published `notes/matrix/gr-surfaces.tsv`.

Failure handling: if publication fails partway (e.g., matrix copied
but receipt not yet written), report exactly which of the two files
was written; the staged matrix and check outputs remain under
`.work/p3b/` and the rerun follows "Resume and recovery".

### Step 6 — report and stop

Report the files created (`notes/matrix/gr-surfaces.tsv`,
`notes/receipts/P3b-validation-receipt.md`), the row counts, any
clusters marked `CITED-ABSENT`, and any proposed unnumbered open
questions (proposals only — do not write to
`notes/open-questions.md`). Do not commit; stop for user review.

## Resume and recovery

A rerun must either complete the remaining work cleanly or stop
without corrupting or duplicating state:

- If `notes/matrix/gr-surfaces.tsv` exists and
  `notes/receipts/P3b-validation-receipt.md` exists and the
  receipt's recorded SHA-256 matches the current file: before
  reporting the phase already complete, recompute the current
  SHA-256 of both `notes/raw/gr-harvest-receipt.txt` and
  `notes/raw/gr-sub-remediation-receipt.txt` with `sha256sum` and
  compare them to the Phase 3a receipt hashes recorded in the
  Phase 3b validation receipt. If both match, the phase output is
  already published: report this and stop; change nothing. If
  either differs, stop and report the mismatch as a
  Phase 3a-evidence discrepancy; the user decides how to proceed.
- If the matrix exists but the receipt is missing, or the receipt's
  SHA-256 does not match: treat as an interrupted or tampered
  publication. Do not edit the published file in place. Rebuild the
  staged matrix from the declared inputs (Steps 1–4, including the
  Step 1 Phase 3a receipt and hash verification). If the
  validated staged matrix is byte-identical to the published file,
  write the receipt (Step 5.3–5.4) and finish. If it differs, stop
  and report the difference; the user decides how to proceed.
- If only staging under `.work/p3b/` exists (no published matrix):
  staged files may be deleted or rebuilt freely; restart from
  Step 1. Staging is fully derivable from the declared inputs, so
  this is idempotent.
- Never delete, edit, or replace anything under `notes/raw/` during
  recovery.

## Done when

Each criterion is verifiable read-only from the named durable
artifacts and the declared inputs; a model's session report is not
evidence for any of them.

1. `notes/matrix/gr-surfaces.tsv` exists, is nonempty, and its
   header is exactly
   `cluster_id	req_ids	grandine_surface	evidence	confidence	notes`.
2. The file contains exactly one row per cluster defined in
   `notes/02-clusters.md` — no missing clusters, no duplicates, no
   extra rows.
3. Every row either records a positive Grandine surface with an
   `evidence` field citing at least one artifact listed in
   `notes/raw/gr-harvest-receipt.txt` or
   `notes/raw/gr-sub-remediation-receipt.txt`, or records
   `CITED-ABSENT` with an `evidence` field citing both existing
   files `notes/raw/gr-search-<slug>.txt` and
   `notes/raw/gr-sub-search-<slug>.txt` for that row's own cluster,
   with `<slug>` derived per "Cluster slug rule".
4. Every file cited anywhere in the `evidence` column exists under
   `notes/raw/` and is listed in one of the two Phase 3a receipts.
5. For every positive `grandine_surface` entry, its path — and its
   symbol, when present — each appear verbatim as separate fixed
   strings in at least one artifact cited by its own row; the
   combined `path::symbol` matrix notation is not required to
   appear in any artifact.
6. For every `CITED-ABSENT` row, neither its
   `notes/raw/gr-search-<slug>.txt` nor its
   `notes/raw/gr-sub-search-<slug>.txt` contains the
   retained-positive annotation line `# Retained positive hits`.
7. Every `req_ids` value is nonempty and matches the cluster's
   mapping in `notes/02-clusters.md`; every `confidence` value is
   `high`, `medium`, or `low`; every provisional-tagged cluster's
   row carries `PROV-SPEC` in `notes`.
8. `notes/receipts/P3b-validation-receipt.md` exists and records
   the SHA-256 of each Phase 3a receipt, the exact verified
   evidence inventory used with per-artifact hash-verification
   results, the per-check validation results with exit codes, and a
   SHA-256 that matches the current `notes/matrix/gr-surfaces.tsv`;
   the recorded Phase 3a receipt hashes match the current
   `notes/raw/gr-harvest-receipt.txt` and
   `notes/raw/gr-sub-remediation-receipt.txt` as verified by
   `sha256sum`.
9. The git status/diff for the phase shows that, apart from
   `.work/`, the session changed only two paths:
   `notes/matrix/gr-surfaces.tsv` and
   `notes/receipts/P3b-validation-receipt.md` were added, and
   nothing else — in particular nothing under `notes/raw/`, and no
   change to `notes/refs.md` or `notes/open-questions.md`.
10. No commit was made by the session.
