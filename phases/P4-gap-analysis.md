# Phase 4 — gap analysis

## Purpose

For every behavior cluster, compare the Lighthouse-side requirement
and row mapping recorded in `notes/02-clusters.md` against the
Grandine surface recorded in `notes/matrix/gr-surfaces.tsv`, describe
the mismatch (or its cited absence), assess port difficulty, and
publish the result as the derived matrix `notes/matrix/gap.tsv`.

## Session type and scope

This is a synthesis / schema-filling session.

- No live research of any kind: no network access, no fetching, no
  cloning, no checkout, and no inspection of any live repository
  (Grandine, Lighthouse, consensus-specs, EIPs, or any other). All
  facts must come from the declared inputs. If a fact cannot be
  supported from those inputs, it is not recorded as a finding.
- No raw artifact under `notes/raw/` is read in this session. Raw
  pointers may appear in output only by transcription from the
  declared inputs (see "Matrix schema").
- Because this phase touches no live pinned repository content, no
  ref-verification gate is run. If any step appears to require live
  repository access or a re-pin decision, stop and report; do not
  perform the access or make the decision.
- This phase runs as a single bounded session covering all clusters.
  No batching and no combination with any other phase is permitted.
  Interrupted runs resume under "Resume and recovery" below.
- Model recollection of Grandine, Lighthouse, the EIP, or the
  consensus specs is never evidence. Prior conversation history is
  never evidence.

## Declared inputs

Read-only inputs except where "Open-question handling" defines the
guarded append to `notes/open-questions.md`. Load these and nothing
else — no other notes files, matrices, raw artifacts, phase briefs,
or phase specifications, and in particular not `notes/refs.md`. The
applicable provisional-spec baseline this phase needs is encoded
directly in this specification (see "Provisional-spec baseline
(encoded)"); this phase never reads `notes/refs.md` at execution time.

1. `notes/02-clusters.md` — control input. Sole source of the
   cluster universe: the set of cluster identifiers and, for each
   cluster, its associated requirement IDs (`req_ids`), its
   associated Lighthouse classification row identifiers
   (`lh_row_ids`), its cluster-level description, and any
   provisional (`PROV-SPEC` / PROVISIONAL-SPEC-BASELINE-dependent)
   tagging carried on the cluster or its requirements.
2. `notes/matrix/gr-surfaces.tsv` — the Phase 3b derived matrix.
   Sole source of Grandine-side facts: per-cluster surfaces,
   `CITED-ABSENT` determinations, evidence pointers, confidence,
   and notes.
3. `notes/receipts/P3b-validation-receipt.md` — control input used
   only to verify, by SHA-256, that inputs 1 and 2 are the exact
   files Phase 3b validated.
4. `notes/open-questions.md` — control input for the set of existing
   `[OPEN-Qn]` identifiers and the append target defined under
   "Open-question handling".
5. This specification.

Input gate (Step 1 of the procedure): if any of inputs 1–4 is
missing or empty, or the Step 1 hash verification fails, stop and
report the failing input by name. Never substitute another source.

If `notes/02-clusters.md` does not yield, for every cluster, an
unambiguous cluster identifier, a mapped `req_ids` value (distinct
requirement IDs or the explicit `NONE` mapping, per the "`req_ids`"
extraction rule), and a nonempty set of Lighthouse row identifiers,
stop and report the affected cluster or section verbatim. A cluster
whose `req_ids` resolves to `NONE` is not a missing mapping and is not
excluded. Do not open `notes/matrix/requirements.tsv`, any Lighthouse
matrix, or any other file to fill the gap; the user decides how to
proceed.

## Extraction from `notes/02-clusters.md`

All per-cluster facts drawn from `notes/02-clusters.md` are extracted
mechanically by the rules below. These rules are the sole definition of
the cluster universe and of each cluster's `lh_row_ids` and
Lighthouse-side confidence; no other reading of the file substitutes
for them.

### Cluster sections

- A cluster section begins at a Markdown heading line of the form
  `## <cluster_id>` optionally followed by additional heading text,
  where `<cluster_id>` is the first whitespace-delimited token after
  the `## ` prefix and is terminated by a space or the end of the
  line. The line is a cluster heading if and only if that token is
  exactly `C` followed by one or more digits (for example `C1`, `C10`)
  or exactly `C-unassigned`. Both actual forms are accepted: the
  `C[0-9]+` headings continue with ` — <label> (<n> rows)` (an em
  dash), and the `C-unassigned` heading continues with ` (<n> rows)`
  and contains no em dash. The `cluster_id` is that first token,
  verbatim.
- A cluster section ends at the byte before the next line beginning
  with `## ` (or end of file).
- Headings whose first token is neither `C[0-9]+` nor `C-unassigned`
  are not cluster sections and contribute nothing to any cluster. In
  particular the `## Remap table`, `## Orphan list A …`, and `## Orphan
  list B …` sections are excluded; tokens appearing in them are never
  attributed to any cluster.
- The cluster universe is exactly the ordered list of `cluster_id`
  values from the matching headings, in file order. A `cluster_id` must
  not repeat; if it does, stop and report.

### `req_ids`

- Within a cluster section, locate the single line whose content after
  any leading `- ` begins with `req_ids:`. Consider only the text
  following `req_ids:` on that line (the "mapping text").
- Collect every distinct bare requirement token matching the regex
  `R[0-9]+` in the mapping text, in first-appearance reading order.
  These tokens are written comma-separated, no spaces, no brackets
  (for example `R011,R012,R013`). This is the cluster's `req_ids`.
- If the mapping text yields no `R[0-9]+` token but explicitly records
  the `NONE` requirement mapping — i.e. the mapping text contains the
  literal token `NONE` (as in `none mapped (all C1 rows carry NONE)`)
  — the cluster's `req_ids` is the literal sentinel `NONE`. `NONE` is
  a valid, nonempty, mapped value, not a missing value.
- Stop and report the `cluster_id` verbatim only when neither can be
  derived unambiguously: the section has no `req_ids:` line, has more
  than one, or its mapping text yields no `R[0-9]+` token and does not
  contain the literal `NONE`.

### `lh_row_ids`

- Within a cluster section, a Lighthouse row token is any substring
  matching the regex `\[LH[0-9]+\]` (a literal `[LH`, one or more
  digits, then `]`). The recorded row identifier is the token with the
  surrounding brackets stripped (for example `[LH0188]` yields
  `LH0188`).
- Tokens matching `\[R[0-9]+\]` (requirement IDs) and `\[OPEN-Q[0-9]+\]`
  (open-question IDs) are excluded, as is any other bracketed token.
  Bare, unbracketed mentions (for example `R012` inside prose) are not
  tokens and are ignored.
- A cluster's `lh_row_ids` is the sequence of these row identifiers in
  first-appearance reading order top-to-bottom within the section,
  deduplicated by keeping only the first occurrence of each identifier.
- If a cluster section yields no `\[LH[0-9]+\]` token, stop and report
  that `cluster_id` verbatim; do not open any other file to fill the
  gap.

### Lighthouse-side confidence

- Within a cluster section, the Lighthouse-side confidence is read from
  the single line whose content after any leading `- ` begins with
  `Confidence:`. The confidence token is the first whitespace-delimited
  field following `Confidence:`; it must be exactly `H`, `M`, or `L`,
  mapped to `high`, `medium`, and `low` respectively.
- If a cluster section contains zero such lines, more than one, or a
  token other than `H`/`M`/`L`, stop and report that `cluster_id`
  verbatim.

## Provisional-spec baseline (encoded)

The applicable provisional-spec baseline for this study is recorded
here so this phase needs no execution-time read of `notes/refs.md`:

- The EIP-8025 consensus-specs spec target is treated as a
  **PROVISIONAL-SPEC-BASELINE (provisional: yes)**. The only
  consensus-specs version associated with Lighthouse PR #39 is the
  ef-tests pin `v1.7.0-alpha.8`, which is inherited from the PR base
  branch, unmodified by PR #39, and was not confirmed anywhere as the
  EIP-8025 spec target. It is retained as supporting metadata only, not
  as a confirmed spec target.
- This baseline remains provisional, and is tracked by, the existing
  open question `[OPEN-Q1]` in `notes/open-questions.md`. Any finding
  that depends on this provisional spec snapshot must be tagged
  `PROV-SPEC` and may not support a conclusion stronger than
  needs-input until `[OPEN-Q1]` is resolved.

Because this baseline is encoded here, the `prov_spec` tagging required
by the schema is derived entirely from the declared inputs and this
rule: a cluster's row depends on the provisional baseline exactly when
the declared inputs mark it as such (a `PROV-SPEC` token or an
`[OPEN-Q1]` reference in the cluster's section of
`notes/02-clusters.md`, or a `PROV-SPEC` token in the referenced
`gr-surfaces` row's `notes` field — see the `prov_spec` field rule).

## Fuzzy-cluster interaction

The user may designate one or more clusters as fuzzy, either in the
task prompt that starts execution or interactively during the
session. For each cluster so designated, before filling its row: ask
the user questions about that cluster and wait for answers, then
fill the row incorporating the user's decisions. User answers govern
scope and interpretation decisions for that row but are not
evidence: every factual claim in the row must still be supported by
the declared inputs under the rules below. Clusters not designated
fuzzy are filled directly without asking. Only the user creates the
fuzzy designation; the model never self-designates a cluster as
fuzzy (uncertainty is expressed through `confidence` and open
questions instead).

For every user-designated fuzzy cluster, the exact fuzzy designation,
the questions asked, and the answers received are recorded in
`notes/receipts/P4-validation-receipt.md` (Step 5) as decision
context, explicitly marked as not evidence. When no cluster is
designated, the receipt records an explicit empty fuzzy-cluster
section.

## Output

- Primary output: `notes/matrix/gap.tsv` (derived artifact).
- Control-file change: zero or more new question entries appended to
  `notes/open-questions.md`, exactly as defined under "Open-question
  handling". No other control-file change is permitted.
- Validation receipt: `notes/receipts/P4-validation-receipt.md`
  (durable, required for the completion audit).

This phase publishes no raw evidence: it must not create, modify, or
delete anything under `notes/raw/`. It must not modify
`notes/refs.md`, `notes/02-clusters.md`,
`notes/matrix/gr-surfaces.tsv`, or any file other than the three
outputs named above. If execution ever appears to require another
modification, stop and report instead.

## Staging

All intermediate files go under `.work/p4/` (create it if absent):

- `.work/p4/gap.tsv` — staged matrix.
- `.work/p4/new-questions.txt` — staged texts of proposed new open
  questions (see "Open-question handling").
- `.work/p4/checks/` — validation command outputs and captured
  stderr.

Nothing under `.work/` is ever evidence and nothing from `.work/` is
ever cited. No staging file may be created under `notes/raw/`.

## Command success and stderr policy

Every command run by this phase must have its exit code checked
before its output is used or a dependent step begins. Capture stderr
for validation commands to `.work/p4/checks/`. On a nonzero exit or
stderr indicating an error: stop, report the command, exit code, and
stderr location, and preserve `.work/p4/` for diagnosis. Output from
an unchecked command is never treated as a passed check.

## Matrix schema

Tab-separated file. Exact header line (single tab between fields):

```
cluster_id	req_ids	lh_row_ids	gr_surfaces_ref	mismatch_description	port_difficulty	confidence	prov_spec	open_q_ids
```

No field may contain a tab or newline. Field rules:

- `cluster_id` — the cluster identifier exactly as it appears in
  `notes/02-clusters.md`.
- `req_ids` — the cluster's requirement mapping extracted from its
  `- req_ids:` line in `notes/02-clusters.md` by the "`req_ids`" rule
  under "Extraction from `notes/02-clusters.md`": either the distinct
  bare `R[0-9]+` tokens in first-appearance order, comma-separated, no
  spaces, or the literal sentinel `NONE` when the line explicitly
  records the `NONE` mapping. Never empty; `NONE` is a valid nonempty
  mapped value, and clusters with a `NONE` mapping are never excluded
  or left blank.
- `lh_row_ids` — the cluster's Lighthouse classification row
  identifiers extracted from its section of `notes/02-clusters.md` by
  the "`lh_row_ids`" rule under "Extraction from
  `notes/02-clusters.md`" (bracket-stripped `\[LH[0-9]+\]` tokens, in
  first-appearance order, deduplicated), comma-separated, no spaces.
  Never empty (that extraction rule stops the phase if a cluster
  section yields no such token).
- `gr_surfaces_ref` — exactly `gr-surfaces:<cluster_id>`, naming
  this cluster's row in `notes/matrix/gr-surfaces.tsv`. The
  `<cluster_id>` part must equal the row's own `cluster_id`, and a
  row with that `cluster_id` must exist in
  `notes/matrix/gr-surfaces.tsv`.
- `mismatch_description` — nonempty free text describing the gap
  between the cluster's Lighthouse-side behavior (as recorded in
  `notes/02-clusters.md`) and the Grandine state recorded in the
  referenced `gr-surfaces` row, or stating explicitly that the
  declared inputs show no mismatch. Citation rules:
  - Every factual or interpretive claim in this field must be
    supported by the row's cited references: its `lh_row_ids`, its
    `gr_surfaces_ref`, a `[raw:notes/raw/<file>]` pointer, or an
    existing `[OPEN-Qn]`.
  - Each `mismatch_description` must contain at least one citation:
    one of the row's own `lh_row_ids`, its `gr_surfaces_ref` value,
    a `[raw:...]` pointer, or an `[OPEN-Qn]`.
  - A `[raw:notes/raw/<file>]` pointer may be used only by
    transcription: the identical pointer or path must appear
    verbatim in `notes/02-clusters.md` or in the `evidence` field
    of the referenced cluster's row in
    `notes/matrix/gr-surfaces.tsv`, and the file must exist under
    `notes/raw/`. The raw file itself is never opened.
  - Every `[OPEN-Qn]` token appearing in this field must exist in
    `notes/open-questions.md` at publication time. A `NEWQ-<k>`
    placeholder never appears in this field; placeholders live only in
    the `open_q_ids` field during staging (see "Open-question
    handling").
  - No new claims from memory.
- `port_difficulty` — one of `low`, `medium`, `high`, judged only
  from the declared inputs: `low` = the referenced `gr-surfaces`
  row records surfaces closely matching the cluster's behavior with
  little adaptation implied; `medium` = partial surface or module
  present but notable adaptation implied; `high` = the referenced
  row records `CITED-ABSENT`, or the recorded surfaces imply major
  structural divergence.
- `confidence` — one of `high`, `medium`, `low`, reflecting how
  strongly the declared inputs support this row's mismatch
  determination. It must not exceed the weaker (lower, under the
  ordering `low` < `medium` < `high`) of two ceilings: the cluster's
  Lighthouse-side confidence extracted from `notes/02-clusters.md` by
  the "Lighthouse-side confidence" rule under "Extraction from
  `notes/02-clusters.md`", and the `confidence` recorded on the
  referenced `gr-surfaces` row. Equivalently, `confidence` ≤
  `min(lh_confidence, gr_surfaces_confidence)`.
- `prov_spec` — either empty or exactly `PROV-SPEC`. Must be
  `PROV-SPEC` when either of the following holds: the cluster's
  section in `notes/02-clusters.md` carries a `PROV-SPEC` token or an
  `[OPEN-Q1]` reference (marking a claim that depends on the encoded
  provisional-spec baseline — see "Provisional-spec baseline
  (encoded)"); or the referenced `gr-surfaces` row carries `PROV-SPEC`
  in its `notes` field. Both triggers are evaluated from the declared
  inputs only; the provisional baseline itself is the one encoded in
  this specification, not read from `notes/refs.md`. A `PROV-SPEC` row
  must not be worded as a confirmed proposal finding and may not
  support a conclusion stronger than needs-input until the baseline is
  confirmed.
- `open_q_ids` — comma-separated `[OPEN-Qn]` identifiers, no
  spaces, or empty. Every listed identifier must exist in
  `notes/open-questions.md` at publication time. Every row with
  `confidence` = `low` must have a nonempty `open_q_ids`.

## Open-question handling

Numbered `[OPEN-Qn]` identifiers are assigned only at the moment of
writing to `notes/open-questions.md`, which is append-only; existing
entries are never edited, renumbered, or reused.

1. While filling rows (Step 3), a row needing a question first
   reuses an applicable existing `[OPEN-Qn]` from
   `notes/open-questions.md` when one covers the issue. Otherwise
   the row's `open_q_ids` field carries a placeholder token
   `NEWQ-<k>` (`k` = 1, 2, 3, … in first-use order), and the full
   question text for each token is recorded in
   `.work/p4/new-questions.txt` as one block per token. Placeholder
   tokens never appear outside staging.
2. At publication (Step 5), for each `NEWQ-<k>` in order: search
   `notes/open-questions.md` for the exact question text
   (fixed-string). If present — from an earlier interrupted run —
   reuse that entry's existing ID; do not append a duplicate.
   Otherwise determine the highest existing question number,
   append the new entry at the end of the file following the
   file's existing entry format with the next number, and record
   the assigned ID. If the existing numbering or entry format
   cannot be determined unambiguously, stop and report; do not
   guess.
3. After all appends, substitute each placeholder in the staged
   matrix with its assigned `[OPEN-Qn]` and re-run the Step 4
   checks before publishing the matrix. The append to
   `notes/open-questions.md` always precedes publication of
   `notes/matrix/gap.tsv`, so the published matrix only ever
   references identifiers that exist.

## Procedure

### Step 1 — input gate and input verification

1. Verify `notes/02-clusters.md`, `notes/matrix/gr-surfaces.tsv`,
   `notes/receipts/P3b-validation-receipt.md`, and
   `notes/open-questions.md` exist and are nonempty.
2. From `notes/receipts/P3b-validation-receipt.md`, extract the
   recorded SHA-256 of `notes/02-clusters.md` and the recorded
   SHA-256 of the published `notes/matrix/gr-surfaces.tsv`.
   Recompute both files' SHA-256 with `sha256sum` as checked
   commands and compare. A mismatch on either file fails the gate.
3. Save to `.work/p4/checks/input-verification.txt`: both recorded
   and recomputed hashes, the comparison results, and the SHA-256 and
   byte length of `notes/open-questions.md` at session start. (The
   authoritative pre-append snapshot for the integrity record is
   captured immediately before the append in Step 5.)

Failure handling: stop and report the missing or empty input or
each hash mismatch by name; do not begin synthesis. A hash mismatch
is an input discrepancy for the user to resolve; never proceed past
it and never re-derive or repair an input. Nothing has been staged
beyond `.work/p4/checks/`; nothing to clean up.

### Step 2 — extract the cluster universe

From `notes/02-clusters.md`, applying the rules under "Extraction from
`notes/02-clusters.md`", extract the ordered list of (`cluster_id`,
`req_ids`, `lh_row_ids`, `lh_confidence`, provisional-tag) tuples,
where `lh_row_ids` and `lh_confidence` come from those rules and the
provisional-tag is whether the cluster's section carries a `PROV-SPEC`
token or an `[OPEN-Q1]` reference. From `notes/matrix/gr-surfaces.tsv`,
extract the list of its `cluster_id` values and, per cluster, its
`grandine_surface`, `evidence`, `confidence`, and `notes` fields.
Verify the two cluster-ID sets are identical. Record both extractions
(including per-cluster `lh_row_ids` and `lh_confidence`) and the
comparison result to `.work/p4/checks/cluster-universe.txt`.

Failure handling: if any cluster lacks an identifier, a derivable
`req_ids` value (distinct requirement IDs or the explicit `NONE`
mapping), or Lighthouse row IDs, stop and report it verbatim (see
"Declared inputs" and the "`req_ids`" extraction rule). A `NONE`
mapping is a valid value, not a lacking one. If the cluster-ID sets
differ, stop and report the difference as an input discrepancy.
Preserve `.work/p4/` as-is.

### Step 3 — fill the staged matrix

For each cluster, in the order extracted in Step 2, write exactly
one row to `.work/p4/gap.tsv` under the schema and citation rules
above, honoring "Fuzzy-cluster interaction" for any user-designated
fuzzy cluster and "Open-question handling" for question references.

Failure handling: if a row cannot be filled within the schema rules
from the declared inputs (and, for a fuzzy cluster, the user's
answers), stop, report the affected `cluster_id` and the specific
rule that cannot be satisfied, and preserve `.work/p4/` including
the partial staged matrix. Do not publish a partial matrix and do
not perform live research to fill the gap.

### Step 4 — validate the staged matrix

Run each check as a command with a checked exit code; save outputs
under `.work/p4/checks/`:

1. Header equals the exact schema header.
2. Every row has exactly 9 tab-separated fields.
3. The multiset of `cluster_id` values equals exactly the Step 2
   universe — one row per cluster, no duplicates, no extras, none
   missing.
4. Every `req_ids` and `lh_row_ids` value is nonempty and matches
   the Step 2 mapping for its cluster; a `req_ids` value of the
   literal `NONE` is accepted as a valid nonempty mapping and matches
   only clusters whose Step 2 `req_ids` is `NONE`.
5. Every `gr_surfaces_ref` equals `gr-surfaces:<cluster_id>` for
   its own row's `cluster_id`, and that `cluster_id` exists in
   `notes/matrix/gr-surfaces.tsv`.
6. Every `mismatch_description` is nonempty and contains at least
   one required citation (one of the row's own `lh_row_ids`, its
   `gr_surfaces_ref` value, a `[raw:...]` pointer, or an
   `[OPEN-Qn]`).
7. Every `[raw:notes/raw/<file>]` pointer anywhere in the staged
   matrix names a file that exists under `notes/raw/`, and its
   pointer or path appears verbatim (fixed-string search) in
   `notes/02-clusters.md` or in the referenced cluster's `evidence`
   field in `notes/matrix/gr-surfaces.tsv`.
8. Every `port_difficulty` is `low`, `medium`, or `high`; every
   `confidence` is `high`, `medium`, or `low`; no row's `confidence`
   exceeds either the cluster's `lh_confidence` extracted in Step 2 or
   the `confidence` recorded on its referenced `gr-surfaces` row —
   i.e. `confidence` ≤ `min(lh_confidence, gr_surfaces_confidence)`
   (ordering low < medium < high).
9. Every `prov_spec` is empty or exactly `PROV-SPEC`, and it is
   `PROV-SPEC` for every cluster that is provisional-tagged in
   `notes/02-clusters.md` or whose referenced `gr-surfaces` row
   carries `PROV-SPEC` in its `notes` field.
10. Every row with `confidence` = `low` has a nonempty
    `open_q_ids`.
11. Every `open_q_ids` entry is an existing `[OPEN-Qn]` present in
    `notes/open-questions.md` or a `NEWQ-<k>` placeholder with a
    corresponding block in `.work/p4/new-questions.txt`; after the
    Step 5 substitution, the re-run of this check requires every
    entry to be an existing `[OPEN-Qn]` with no placeholders
    remaining.
12. No `NEWQ-<k>` placeholder appears in any `mismatch_description`,
    and every `[OPEN-Qn]` token appearing in any `mismatch_description`
    is an existing identifier in `notes/open-questions.md`. Before the
    Step 5 append this is checked against the current file; the re-run
    after publication requires every `[OPEN-Qn]` token in any
    `mismatch_description`, together with every `open_q_ids` entry, to
    exist in the final `notes/open-questions.md`.

Failure handling: on any failed check, stop before publication,
report the failed check and offending row(s), and preserve
`.work/p4/` for diagnosis. `notes/` is untouched at this point.

### Step 5 — publish

Publication order (open-questions append first, matrix next,
receipt last, so the matrix only references existing IDs and the
receipt always attests already-published files):

1. If `notes/matrix/gap.tsv` already exists, follow "Resume and
   recovery" instead of overwriting.
2. Immediately before appending, capture the pre-append snapshot of
   `notes/open-questions.md`: its SHA-256 and byte length, with
   checked commands, saved to
   `.work/p4/checks/open-questions-integrity.txt`. Then append new
   open questions and substitute placeholders exactly as defined in
   "Open-question handling", including the duplicate-entry guard.
   Immediately after the append, capture the post-append snapshot: the
   byte length and the SHA-256 of the complete file, plus the exact
   text of every entry appended by this phase and the assigned
   `[OPEN-Qn]` IDs (and any reused IDs), all saved to the same file.
   If no entry was appended, the post-append snapshot equals the
   pre-append snapshot and the appended-entry list is empty. Then
   re-run the Step 4 checks on the final staged matrix (all must pass
   with no placeholders remaining).
3. Copy `.work/p4/gap.tsv` to `notes/matrix/gap.tsv` with a checked
   command.
4. Compute the SHA-256 of the published file with a checked
   command.
5. Write `notes/receipts/P4-validation-receipt.md` containing: the
   date; the recorded-vs-recomputed hash results from Step 1 for
   `notes/02-clusters.md` and `notes/matrix/gr-surfaces.tsv`; the
   open-questions integrity record — the pre-append SHA-256 and byte
   length, the post-append byte length and SHA-256 of the complete
   file, the exact text of each entry appended by this phase with its
   assigned `[OPEN-Qn]` ID, and the list of reused IDs (all empty or
   equal where no append occurred); the result and exit code of every
   Step 4 check on the final matrix; the row count; the counts of
   rows by `port_difficulty`, by `confidence`, and with `PROV-SPEC`;
   the fuzzy-cluster record — for every user-designated fuzzy cluster,
   its exact fuzzy designation, the questions asked, and the answers
   received, explicitly labelled as decision context and not evidence,
   or an explicit empty section when no cluster was designated; and
   the SHA-256 of the published `notes/matrix/gap.tsv`.

Failure handling: if publication fails partway, report exactly
which of the three writes (open-questions append, matrix, receipt)
completed; the staged matrix and check outputs remain under
`.work/p4/` and the rerun follows "Resume and recovery". A
partially completed open-questions append leaves only whole
appended entries; the duplicate-entry guard makes the rerun safe.

### Step 6 — report and stop

Report the files created or changed (`notes/matrix/gap.tsv`,
`notes/receipts/P4-validation-receipt.md`, and
`notes/open-questions.md` if appended), the row count, the counts
by `port_difficulty` and `confidence`, the clusters tagged
`PROV-SPEC`, and the appended or reused `[OPEN-Qn]` IDs. Do not
commit; stop for user review.

## Resume and recovery

A rerun must either complete the remaining work cleanly or stop
without corrupting or duplicating state:

- If `notes/matrix/gap.tsv` exists and
  `notes/receipts/P4-validation-receipt.md` exists and the
  receipt's recorded SHA-256 matches the current file: before
  reporting the phase already complete, recompute the SHA-256 of
  `notes/02-clusters.md` and `notes/matrix/gr-surfaces.tsv` and
  compare them to the values recorded in the receipt. For
  `notes/open-questions.md`, run the append-compatible integrity
  check instead of a whole-file equality: read the recorded
  post-append byte length `L` and post-append SHA-256 `S`; if the
  current file is shorter than `L` bytes, fail; otherwise hash the
  first `L` bytes of the current file and require the result to equal
  `S`; additionally require that each `[OPEN-Qn]` ID appended by this
  phase still appears exactly once in the current file with its
  recorded entry text intact. This permits later phases to have
  appended further whole entries after byte `L` while proving this
  phase's prefix and entries are unchanged and non-duplicated. If the
  `02-clusters`/`gr-surfaces` hashes match and this integrity check
  passes, the phase output is already published: report this and stop;
  change nothing. If any differs, stop and report the mismatch as an
  input or control-file discrepancy; the user decides how to proceed.
- If the matrix exists but the receipt is missing, or the receipt's
  SHA-256 does not match (so no valid durable open-questions integrity
  record exists): treat as an interrupted or tampered publication. Do
  not edit any published file in place, and do not reconstruct a
  pre-append `notes/open-questions.md` by removing entries or by any
  other inference from the current file state. Recovery may complete
  only if the staging integrity record written before the append —
  `.work/p4/checks/open-questions-integrity.txt`, holding the
  pre-append snapshot, the post-append snapshot, and the exact
  appended entries and IDs — is still available and verifies directly
  against the current files: the current `notes/open-questions.md`
  passes the append-compatible prefix check against the recorded
  post-append byte length `L` and SHA-256 `S` (its first `L` bytes
  hash to `S`), each recorded appended `[OPEN-Qn]` ID appears exactly
  once with its recorded entry text intact, and a staged matrix
  rebuilt and validated from the declared inputs (Steps 1–4, reusing
  IDs for question text already present in `notes/open-questions.md`)
  is byte-identical to the published `notes/matrix/gap.tsv`. If all of
  that holds, write the receipt from the verified records
  (Step 5.4–5.5) and finish. Otherwise preserve all files unchanged
  and stop for the user's decision.
- If `notes/open-questions.md` contains entries appended by an
  earlier interrupted run but no published matrix exists: proceed
  from Step 1; the duplicate-entry guard reuses those entries'
  IDs instead of appending again.
- If only staging under `.work/p4/` exists (no published matrix):
  staged files may be deleted or rebuilt freely; restart from
  Step 1 (fuzzy-cluster answers must be re-obtained from the user,
  not recalled from prior conversation history).
- Never delete, edit, or replace anything under `notes/raw/`; never
  edit or renumber existing entries in `notes/open-questions.md`
  during recovery.

## Done when

Each criterion is verifiable read-only from the named durable
artifacts and the declared inputs; a model's session report is not
evidence for any of them.

1. `notes/matrix/gap.tsv` exists, is nonempty, and its header is
   exactly
   `cluster_id	req_ids	lh_row_ids	gr_surfaces_ref	mismatch_description	port_difficulty	confidence	prov_spec	open_q_ids`.
2. The file contains exactly one row per cluster defined in
   `notes/02-clusters.md` — no missing clusters, no duplicates, no
   extra rows — and every row has exactly 9 tab-separated fields.
3. Every `req_ids` and `lh_row_ids` value is nonempty and matches
   the cluster's mapping in `notes/02-clusters.md` (per the
   "`req_ids`" and "`lh_row_ids`" extraction rules); a `req_ids` of
   the literal `NONE` is a valid nonempty mapping for clusters whose
   `- req_ids:` line records the `NONE` mapping, and no cluster is
   omitted or left with an empty `req_ids`.
4. Every `gr_surfaces_ref` equals `gr-surfaces:<cluster_id>` for
   its own row, and that cluster has a row in
   `notes/matrix/gr-surfaces.tsv`.
5. Every `mismatch_description` is nonempty and contains at least
   one of: one of its row's `lh_row_ids`, its `gr_surfaces_ref`
   value, a `[raw:notes/raw/<file>]` pointer, or an `[OPEN-Qn]`.
   Every `[raw:...]` pointer names a file that exists under
   `notes/raw/` and appears verbatim (pointer or path) in
   `notes/02-clusters.md` or in the referenced cluster's `evidence`
   field in `notes/matrix/gr-surfaces.tsv`.
6. Every `port_difficulty` is `low`, `medium`, or `high`; every
   `confidence` is `high`, `medium`, or `low`; no row's `confidence`
   exceeds either the cluster's Lighthouse-side confidence extracted
   from `notes/02-clusters.md` or the `confidence` on its referenced
   `gr-surfaces` row — i.e. `confidence` ≤
   `min(lh_confidence, gr_surfaces_confidence)`.
7. Every `prov_spec` is empty or exactly `PROV-SPEC`, and is
   `PROV-SPEC` for every cluster provisional-tagged in
   `notes/02-clusters.md` or whose referenced `gr-surfaces` row
   carries `PROV-SPEC` in its `notes` field.
8. Every row with `confidence` = `low` has a nonempty `open_q_ids`;
   every identifier listed in any `open_q_ids` field or cited in
   any `mismatch_description` exists in `notes/open-questions.md`;
   no `NEWQ-` placeholder appears anywhere in the published matrix.
9. Any change to `notes/open-questions.md` is append-only: the git
   diff shows only added lines forming whole new entries at the end
   of the file, with question numbers strictly increasing and no
   existing line modified, deleted, or renumbered, and no duplicate
   question IDs in the resulting file.
10. `notes/receipts/P4-validation-receipt.md` exists and records:
    input hash verifications matching the current
    `notes/02-clusters.md` and `notes/matrix/gr-surfaces.tsv` (as
    verified by `sha256sum`); the open-questions integrity record —
    the pre-append SHA-256 and byte length, and the post-append byte
    length `L` and SHA-256 `S` such that the SHA-256 of the first `L`
    bytes of the current `notes/open-questions.md` equals `S` (a
    prefix match, permitting later whole append-only entries past byte
    `L`); the exact text and assigned `[OPEN-Qn]` ID of each entry
    this phase appended, each still present exactly once in the
    current file, plus any reused IDs; the per-check validation
    results with exit codes; the row and category counts; the
    fuzzy-cluster record (designations, questions, and answers, or an
    explicit empty section); and a SHA-256 matching the current
    `notes/matrix/gap.tsv`.
11. The git status/diff for the phase shows that, apart from
    `.work/`, the session changed only: `notes/matrix/gap.tsv`
    added, `notes/receipts/P4-validation-receipt.md` added, and
    (only if questions were appended) `notes/open-questions.md`
    appended — and nothing else; in particular nothing under
    `notes/raw/`, and no change to `notes/refs.md`,
    `notes/02-clusters.md`, or `notes/matrix/gr-surfaces.tsv`.
12. No commit was made by the session.
