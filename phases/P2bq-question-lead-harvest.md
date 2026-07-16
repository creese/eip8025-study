# Phase P2bq — question-lead harvest

## Purpose and position

Phase 2b's bounded batch sessions each ended with a session report,
saved locally under `.work/p2b/`, that may contain unnumbered proposed
open questions, reported divergences, borderline judgments, and other
analytical leads. Those reports are model-generated process records
under `.work/`: they are not study evidence, are never citable, and
are at risk of deletion. Phase 2b is complete and is not reopened by
this phase.

Phase P2bq is a required handoff between Phase 2b and Phase 2c. It
distills the leads in those reports into one durable, explicitly
non-evidence lead register, so the user and later phases can locate
candidate leads without rediscovering them — and without the source
reports or the register ever being treated as evidence.

## Session type

Extraction only — neither an evidence harvest into `notes/raw/` nor a
synthesis session. The session copies lead formulations verbatim,
classifies each lead by its surface form in the log, and consolidates
repeated formulations transparently. It performs no verification, no
investigation, no resolution, and no live research of any kind.

This phase runs in a single bounded session. Interruption is handled
by the resume procedure in Step 0, not by predefined batches.

## Pinned inputs (read these and nothing else)

The pinned Phase 2b session-log set, pinned 2026-07-16, is exactly
these 17 files under `.work/p2b/`:

```
session-000.log   session-001.log   session-002.log
session-003.log   session-004.log   session-005.log
session-006.log   session-007.log   session-008.log
session-009.log   session-010.log   session-011.log
session-012.log   session-013.log   session-014.log
session-015.log   session-016.log
```

Also readable, for resume only (Step 0):

- this phase's own staging files under `.work/p2bq/`;
- this phase's own outputs, `notes/leads/p2b-lead-register.md` and
  `notes/receipts/P2bq-extraction-receipt.md`, if they exist.

Explicitly excluded — do not read, even if a log mentions them:
matrices under `notes/matrix/`, anything under `notes/raw/`,
`notes/open-questions.md`, `notes/refs.md`, repository clones, other
phases' briefs and specifications, and the network. Reading any of
these would let extraction drift into verification or synthesis.
Identifiers mentioned in the logs — row_ids, req_ids, `[raw:<path>]`
pointers, `[OPEN-Qn]` references — are copied verbatim and are not
checked for existence or validity.

## Prohibitions (beyond the global rules in CLAUDE.md)

The session must not:

- resolve, investigate, or rank leads by merit, or conclude that a
  suspected issue is real or not real;
- read any evidence a lead cites or refers to;
- assign `[OPEN-Qn]` identifiers or write to
  `notes/open-questions.md`;
- treat a log's claim that something was verified or cited as
  independent verification — such claims are recorded only as
  log-reported status;
- edit or delete the source logs, or modify any existing study
  artifact;
- delete anything under `.work/p2b/`; the source logs must be
  preserved until the P2bq completion audit has reported, because the
  audit re-derives the in-scope set from them.

## Locations

- Staging directory: `.work/p2bq/staging/` (created by this phase).
- Diagnostics (saved stderr, gate transcripts): `.work/p2bq/diag/`.
- Published outputs: `notes/leads/p2b-lead-register.md` and
  `notes/receipts/P2bq-extraction-receipt.md`.

Nothing is written under `notes/raw/`, `notes/matrix/`,
`notes/open-questions.md`, or `notes/refs.md`. Staging and diagnostic
files are process records, never evidence, and are never cited.

## Command success policy

Every command run by this phase (enumeration, hashing, line counts,
directory creation, copies, validation greps) must be checked before
dependent work continues: exit code must be the expected success code,
and stderr must be empty. Exception: a `grep`-style check whose
documented purpose is to confirm absence may exit 1 with empty output;
any exit code above 1 is a failure. On any command failure: save the
command line, exit code, and stderr to `.work/p2bq/diag/`, stop, and
report. Do not publish, and do not treat the command's output as
usable.

## Step 0 — resume check

Determine the phase state before doing anything else. Let REG =
`notes/leads/p2b-lead-register.md`, RCPT =
`notes/receipts/P2bq-extraction-receipt.md`.

- **Both REG and RCPT exist:** two distinct conditions are checked,
  both read-only, and completion is reported only when both hold.

  *Validated published artifact set:* `.work/p2bq/staging/VALIDATED`
  (Step 4) exists and the SHA-256 values it records equal the SHA-256
  values of the published REG and RCPT as they exist now. If the
  marker is missing, or either published file's hash differs from the
  marker (including a truncated or partial copy), stop and report the
  discrepancy; do not modify or delete the published files. The user
  decides how to proceed.

  *Currently re-auditable phase state:* every pinned log still exists
  under `.work/p2b/` and its recomputed SHA-256 equals the value
  recorded in the published receipt. This is a hash computation over
  the declared inputs, not a re-extraction.

  If both conditions hold, report that the phase outputs already
  exist, match the validated artifact set, and remain re-auditable,
  and stop — a rerun makes no changes. If the artifact set is valid
  but any pinned log is missing or its hash differs, do not report
  completion: the published outputs stand unmodified, but stop and
  report the exact log delta and that the accounting and
  excerpt-fidelity criteria would be `NOT RE-VERIFIABLE` for the
  completion audit, which requires the source logs preserved until it
  has reported. The user decides how to proceed.
- **REG exists, RCPT does not:** interrupted publication in the
  specified order. Resume at Step 5 — which skips the
  already-published register and publishes only the receipt — only if
  all of the following hold: `VALIDATED` exists; its recorded hashes
  match the current staged register and staged receipt; and the
  published REG is byte-identical to the staged register. Otherwise
  stop and report the partial state, preserving everything; the user
  decides how to proceed.
- **RCPT exists, REG does not:** this state cannot be produced by the
  Step 5 publication order (register always first). Treat it as an
  anomaly, not a resumable interruption: stop and report the observed
  state; publish nothing and modify nothing. The user decides how to
  proceed.
- **Neither exists, but `.work/p2bq/staging/` exists:** a prior run
  was interrupted before publication. Per-log accounting files under
  `.work/p2bq/staging/occ/` that end with their completion marker
  (Step 2) may be reused after the Step 1 gate confirms the
  corresponding log's SHA-256 matches the hash recorded in
  `.work/p2bq/staging/inputs.tsv` from the prior run; any accounting
  file without its completion marker, or whose log hash does not
  match, is moved to `.work/p2bq/diag/` and redone. Draft register and
  receipt files in staging without the `VALIDATED` marker are
  regenerated in Step 3. Then continue from the first incomplete step.
- **Nothing exists:** fresh run; continue with Step 1.

## Step 1 — input gate

1. Create `.work/p2bq/staging/`, `.work/p2bq/staging/occ/`, and
   `.work/p2bq/diag/` if absent.
2. Enumerate `.work/p2b/session-*.log`. The enumerated set must equal
   the pinned 17-file list exactly — no missing files, no extra
   matching files. If it does not, stop and report the delta (missing
   and extra filenames); the pin decision belongs to the user. Never
   continue past a failed gate.
3. For each pinned file, record in `.work/p2bq/staging/inputs.tsv`
   one line: filename, SHA-256, line count (`wc -l`). On a fresh run,
   write this file anew; on resume, a pre-existing `inputs.tsv` is
   kept for the Step 0 hash comparison and then overwritten with the
   freshly computed values.
4. Failure handling: any unreadable file, hash or count failure stops
   the phase per the command success policy. Nothing is published;
   staging is preserved for diagnosis.

## Step 2 — per-log extraction

Process the pinned logs one at a time, in ascending filename order.
For each log, read the full file and identify every in-scope passage.

**Inclusion boundary.** A log passage is in scope when it (a) appears
in the log's proposed-open-questions or observations reporting, or
(b) anywhere in the log poses a question, reports a divergence or
inconsistency, or characterizes a judgment, classification, or
verification as borderline, uncertain, or unresolved. Purely
mechanical report content — counts, running totals, batch ranges,
resume points, and routine validation or publication status lines —
is out of scope unless it itself reports such a divergence or
uncertainty. When inclusion is doubtful, extract.

**Passage.** A passage is a maximal contiguous run of lines expressing
one candidate lead or one excludable item. Passages within a log must
not overlap. Line numbers use the file's numbering as read (consistent
with the `wc -l` count recorded in Step 1).

**Disposition.** Every in-scope passage receives exactly one
disposition:

- `extracted`, naming the occurrence key it is recorded under (and,
  after Step 3, the lead entry); or
- `not-a-lead`, with a one-clause reason (for example, a bare verbatim
  reference to an existing `[OPEN-Qn]` that adds no new content).

An in-scope passage with no recorded disposition is a silent omission
and fails the phase.

**Per-passage record.** For each in-scope passage, record in the log's
accounting file `.work/p2bq/staging/occ/<log filename>.occ.md`:

- source locator: `<log filename>:<line(s)>`;
- the verbatim excerpt (exact text, no paraphrase);
- provisional type, exactly one of:
  - `proposed-question` — the log explicitly poses a question;
  - `reported-divergence` — the log reports a divergence or mismatch;
  - `borderline-judgment` — the log flags its own classification or
    judgment as borderline or uncertain;
  - `other-observation` — the passage meets the inclusion boundary but
    fits none of the first three types: an unresolved uncertainty or
    anomaly not phrased as a question, divergence, or borderline
    judgment. Never a catch-all for out-of-scope content;
- mentioned identifiers (row_ids, req_ids, `[raw:<path>]` pointers,
  `[OPEN-Qn]` references) copied verbatim, or `none`;
- log-reported status: any verification, citation, or resolution
  status the log itself claims, quoted and marked as log-reported
  only; or `none`;
- disposition (`extracted` or `not-a-lead` + reason).

A log that yields no in-scope passages still gets an accounting file
stating that explicitly. When a log's accounting is complete, end its
file with the marker line `END-OF-LOG-ACCOUNTING <log filename>`. The
marker is the completion signal used by resume; never write it before
the log has been read in full and every in-scope passage recorded.

Failure handling: if a log cannot be read in full, stop and report;
the partial accounting file, lacking its marker, will be redone on
resume.

## Step 3 — consolidation and drafting

Run only after all 17 accounting files carry their completion markers.

1. **Consolidate.** A repeated formulation of the same lead becomes
   one entry listing all of its occurrences; nothing is silently
   dropped — every `extracted` occurrence appears under exactly one
   lead. Repetition is captured by the multi-source listing, not by a
   type; log-claimed verification is captured only in the log-reported
   status field. A materially different variant wording of a
   consolidated lead is quoted in the entry with its own source. If
   occurrences consolidated into one lead carry different provisional
   types, the entry takes the type of its canonical formulation and
   the variant quotes preserve the divergent wording.
2. **Assign IDs.** `LQ-001, LQ-002, ...` — monotonic, no gaps, no
   duplicates, unbracketed — in order of each lead's first occurrence
   (ascending log filename, then ascending start line). Lead IDs are
   not `[OPEN-Qn]` identifiers; never write them in brackets.
3. **Draft the register** at `.work/p2bq/staging/p2b-lead-register.md`
   in the register format below.
4. **Draft the receipt** at
   `.work/p2bq/staging/P2bq-extraction-receipt.md` in the receipt
   format below, deriving the per-log accounting directly from the
   `occ/` files and `inputs.tsv`.

Failure handling: an occurrence that cannot be placed under exactly
one lead, or an accounting inconsistency discovered while drafting,
stops the phase with a report of the specific occurrence; staging is
preserved.

## Register format

`notes/leads/p2b-lead-register.md` begins with the title line
`# P2b lead register` followed by this banner, verbatim:

```
> **NON-EVIDENCE REGISTER.** This register and its source session
> logs under `.work/p2b/` are not study evidence. Nothing in this
> register may be cited or may otherwise support any claim. Every
> lead below is an unverified candidate: it requires independent
> verification against declared evidence in an authorized later
> phase before it affects any synthesis or is promoted. Direct
> promotion of a lead from this register to
> `notes/open-questions.md` is a user decision. Lead IDs are not
> `[OPEN-Qn]` identifiers and never become citation tokens.
```

Then one entry per distinct lead, in ID order:

```
## LQ-nnn

- **Type:** <proposed-question | reported-divergence |
  borderline-judgment | other-observation>
- **Sources:** <log filename>:<line(s)>[, <log filename>:<line(s)> ...]
- **Excerpt:** "<verbatim canonical formulation>"
  [Variant (<log filename>:<line(s)>): "<verbatim variant wording>"]
- **Mentioned identifiers:** <verbatim identifiers | none>
- **Log-reported status:** <quoted log claim, log-reported only | none>
```

Every occurrence of the lead appears in **Sources**. Excerpts are
verbatim; identifiers are copied verbatim and unchecked.

## Receipt format

`notes/receipts/P2bq-extraction-receipt.md` opens by stating it is a
process record, not study evidence, and is never cited from synthesis
documents. It records at minimum:

- date of extraction;
- the pinned log list, with each file's SHA-256 and line count as
  read (from Step 1, re-confirmed in Step 4);
- per-log disposition accounting: for every in-scope passage, its
  line range, its disposition, and the lead ID (for `extracted`) or
  one-clause reason (for `not-a-lead`);
- per-log extracted occurrence counts, explicitly `0` where a log
  yielded none;
- total occurrences, total consolidated leads, and the
  occurrence-to-lead accounting (each lead ID with its occurrence
  count; the counts must sum to total occurrences);
- the results of the Step 4 banner-presence, ID-monotonicity, and
  excerpt-fidelity checks;
- the statement that no input beyond the declared logs (and this
  phase's own staging and outputs, for resume) was read, and that no
  existing study artifact was modified.

## Step 4 — staging validation

Validate the staged register and receipt as a complete artifact set.
All checks below must pass. Recording results works as a fixpoint:
run the full check suite; if the staged receipt's check section does
not yet record these results, update it and run the full suite again
against the updated files. Validation is achieved only by a run in
which every check passes and neither staged file is modified during
or after that run. The checks are:

1. **Input integrity:** recompute each pinned log's SHA-256; all 17
   must match `inputs.tsv`. A mismatch means a log changed mid-phase:
   stop and report.
2. **Banner presence:** the staged register begins with the banner
   text above, verbatim.
3. **ID monotonicity:** register entry IDs are exactly
   `LQ-001 .. LQ-NNN` in order, no gaps, no duplicates, and no
   bracketed `LQ` token appears anywhere in the register.
4. **Entry completeness:** every entry has all five labeled fields and
   a Type from the closed four-value set.
5. **Accounting completeness:** every pinned log appears in the
   receipt's per-log accounting; every recorded in-scope passage has
   exactly one disposition; every `extracted` disposition names a lead
   ID that exists in the register.
6. **Occurrence-to-lead consistency:** every source locator listed in
   the register appears as an `extracted` passage in the receipt and
   vice versa; each occurrence appears under exactly one lead; totals
   agree.
7. **Excerpt fidelity:** every **Excerpt** and quoted variant wording
   in the register occurs verbatim within the lines named by its
   source locator in the corresponding pinned log, and every quoted
   **Log-reported status** occurs verbatim within a log named in its
   entry's **Sources**. This is a textual comparison against the
   declared logs only — it verifies faithful copying, not the truth
   of any log claim.
8. **No stray writes:** `git status --porcelain
   --untracked-files=all` shows no changed or untracked path outside
   the two intended published-output paths, if either already exists
   during recovery. Separately verify that filesystem paths created
   outside Git are confined to `.work/p2bq/`.
   `notes/open-questions.md`, `notes/refs.md`, `notes/matrix/`, and
   `notes/raw/` are unmodified.

When and only when a full run of the suite passes, write the marker
file `.work/p2bq/staging/VALIDATED` containing the date and the
SHA-256 of the staged register and staged receipt, computed from
exactly the file contents that passed that run. If either staged file
is modified after the marker is written, the marker is stale: delete
it and re-run Step 4. Any failing check: do not write the marker, do
not publish, stop and report the failing check; staging and
diagnostics are preserved.

## Step 5 — publication

Run only when `.work/p2bq/staging/VALIDATED` exists and matches the
current staged files' hashes.

1. Create `notes/leads/` and `notes/receipts/` if absent.
2. Publish the register — unless Step 0 already established that the
   published register exists and is byte-identical to the staged
   register, in which case skip to publishing the receipt. The copy
   is no-clobber: if the destination exists at this point, that
   contradicts Step 0's determination; the copy must fail rather than
   overwrite, and the phase stops and reports the inconsistency.
3. Verify the published register is byte-identical to the staged
   register (compare SHA-256). Mismatch: stop and report; do not
   publish the receipt.
4. Publish the receipt the same way (no-clobber copy — failing rather
   than overwriting an existing destination, then stopping to
   report — followed by SHA-256 comparison against the staged
   receipt) to `notes/receipts/P2bq-extraction-receipt.md`.
5. Ordering guard: the register is always published before the
   receipt, so a partial publication is always "register without
   receipt", which Step 0 resolves deterministically; the reverse
   state is treated by Step 0 as an anomaly, not a resumable
   interruption. This phase
   modifies no control file (`notes/refs.md` is untouched), so no
   control-file coordination or duplicate-entry guard beyond the
   no-clobber rule is needed.
6. Do not delete staging, diagnostics, or the source logs; all are
   preserved for the completion audit.

## Reporting

After Step 5 (or on any stop), report to the user: the phase state
reached; files created (expected: the two published outputs plus
staging/diagnostic files under `.work/p2bq/`); total occurrences and
total consolidated leads; any stop condition and what was preserved.
Do not commit; stop for user review.

## Done when

1. `notes/leads/p2b-lead-register.md` exists; begins with the
   `# P2b lead register` title and the fixed non-evidence banner,
   verbatim as specified in the register format; and every entry has
   all five labeled fields with a Type from the closed set
   (`proposed-question`, `reported-divergence`, `borderline-judgment`,
   `other-observation`), complete source locators in
   `<log filename>:<line(s)>` form, and a verbatim excerpt.
2. Register lead IDs are exactly `LQ-001` through `LQ-NNN`, monotonic,
   without gaps or duplicates, and unbracketed throughout the
   register.
3. `notes/receipts/P2bq-extraction-receipt.md` exists and records:
   the date; all 17 pinned logs with SHA-256 and line count as read;
   per-log disposition accounting giving every recorded in-scope
   passage's line range and exactly one disposition (`extracted` with
   a lead ID present in the register, or `not-a-lead` with a
   one-clause reason); per-log extracted occurrence counts including
   explicit zeros; total occurrences, total consolidated leads, and
   occurrence-to-lead accounting whose per-lead counts sum to the
   occurrence total; the recorded passing results of the
   banner-presence, ID-monotonicity, and excerpt-fidelity checks; and
   the declared-inputs / no-modification statement.
4. The register and receipt are mutually consistent: every lead ID in
   the receipt's accounting exists in the register and vice versa, and
   every source locator in the register appears as an `extracted`
   passage in the receipt and vice versa.
5. Re-reading the pinned logs (still present under `.work/p2b/`, with
   SHA-256 values matching the receipt) re-derives the in-scope set
   under the inclusion boundary, and every member has a recorded
   disposition in the receipt — no silent omissions; and every
   excerpt, variant wording, and quoted log-reported status in the
   register matches the log text as defined by the Step 4
   excerpt-fidelity check.
6. `.work/p2bq/staging/VALIDATED` exists and records SHA-256 values
   matching the published register and receipt.
7. `notes/open-questions.md`, `notes/refs.md`, `notes/matrix/`, and
   `notes/raw/` are untouched; the source logs under `.work/p2b/` are
   unedited and undeleted; the only new tracked-path files are the two
   published outputs.
8. Nothing is committed.
