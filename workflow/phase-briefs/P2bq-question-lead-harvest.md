# Phase 2bq design brief — P2b question-lead harvest

**Status:** Authoritative drafting source until
`phases/P2bq-question-lead-harvest.md` exists as an executable phase
specification under the rules in `CLAUDE.md`.

## Purpose and position

Phase 2b's bounded batch sessions each ended with session output,
saved locally as `.work/p2b/session-*.log`, that may contain
unnumbered proposed open questions, reported divergences, borderline
judgments, and other analytical leads. Those reports are
model-generated process records under `.work/`: they are not study
evidence, are never citable, and are at risk of deletion. Phase 2b is
complete and is not reopened by this phase.

Phase 2bq is a required handoff between Phase 2b and Phase 2c. It
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

## Declared inputs (read these and nothing else)

- `.work/p2b/session-*.log` — the Phase 2b session reports. As of
  2026-07-16 the set is `session-000.log` through `session-016.log`
  (17 files). The executable specification pins the exact filename
  list; the session enumerates the pinned files and accounts for
  every one of them.
- The phase's own outputs, for resume.

Explicitly excluded: matrices under `notes/matrix/`, anything under
`notes/raw/`, `notes/open-questions.md`, `notes/refs.md`, repository
clones, other phases' briefs and specifications, and the network.
Reading any of these would let extraction drift into verification or
synthesis. Identifiers mentioned in the logs — row_ids, req_ids,
`[raw:<path>]` pointers, `[OPEN-Qn]` references — are copied verbatim
and are not checked for existence or validity.

## Inclusion boundary and disposition

A log passage is in scope when it (a) appears in a log's
proposed-open-questions or observations reporting, or (b) anywhere in
the log poses a question, reports a divergence or inconsistency, or
characterizes a judgment, classification, or verification as
borderline, uncertain, or unresolved. Purely mechanical report
content — counts, running totals, batch ranges, resume points, and
routine validation or publication status lines — is out of scope
unless it itself reports such a divergence or uncertainty. When
inclusion is doubtful, extract.

Every in-scope passage receives exactly one disposition, recorded in
the receipt's per-log accounting: `extracted`, naming the lead entry
it appears under, or `not-a-lead`, with a one-clause reason (for
example, a bare verbatim reference to an existing `[OPEN-Qn]` that
adds no new content). A later audit that re-reads the logs must be
able to re-derive the in-scope set and find a disposition for every
member; an in-scope passage with no recorded disposition is a silent
omission and fails the phase.

## Authority boundary (beyond the global rules in CLAUDE.md)

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
  artifact.

## Outputs

**`notes/leads/p2b-lead-register.md`** — the durable lead register.

It begins with a fixed non-evidence banner stating: the register and
its source logs are not study evidence; nothing in the register may be
cited or otherwise support any claim; every lead is an unverified
candidate that requires independent verification against declared
evidence in an authorized later phase before it affects any synthesis
or is promoted; direct promotion from this register to
`notes/open-questions.md` is a user decision; lead IDs are not
`[OPEN-Qn]` identifiers and never become citation tokens.

One entry per distinct lead, `LQ-001, LQ-002, ...` (monotonic,
unbracketed), each with labeled fields:

- **Type:** exactly one of `proposed-question` (the log explicitly
  poses a question), `reported-divergence` (the log reports a
  divergence or mismatch), `borderline-judgment` (the log flags its
  own classification or judgment as borderline or uncertain), or
  `other-observation` (the passage meets the inclusion boundary but
  fits none of the first three types — an unresolved uncertainty or
  anomaly not phrased as a question, divergence, or borderline
  judgment; never a catch-all for out-of-scope content).
- **Sources:** every occurrence, as `<log filename>:<line(s)>`.
- **Excerpt:** the verbatim canonical formulation; a materially
  different variant wording is quoted with its own source.
- **Mentioned identifiers:** row_ids, req_ids, `[raw:<path>]`
  pointers, and `[OPEN-Qn]` references copied verbatim, or `none`.
- **Log-reported status:** any verification, citation, or resolution
  status the log itself claims, quoted and marked as log-reported
  only; or `none`.

Consolidation rule: a repeated formulation of the same lead becomes
one entry listing all of its occurrences; nothing is silently
dropped — every extracted occurrence appears under exactly one lead.
Repetition is captured by the multi-source listing, not by a type;
log-claimed verification is captured only in the log-reported status
field.

**`notes/receipts/P2bq-extraction-receipt.md`** — the durable process
record, recording at minimum: date; the pinned log list with each
file's SHA-256 and line count as read; the per-log disposition
accounting of every in-scope passage (line range, disposition, and
lead ID or one-clause reason); per-log extracted occurrence counts
(explicitly zero where a log yielded none); total occurrences,
total consolidated leads, and the occurrence-to-lead accounting; the
banner-presence and ID-monotonicity check results; and the statement
that no input beyond the declared logs was read and no existing study
artifact was modified. The receipt is a process record, not study
evidence, and is never cited from synthesis documents.

## Completion goal

Every pinned session log is enumerated and accounted for in the
receipt; every passage meeting the inclusion boundary carries exactly
one recorded disposition (extracted under a named lead, or not-a-lead
with its reason); every extracted occurrence appears under exactly one
lead entry; every entry carries a type, complete source locators, and a
verbatim excerpt; the non-evidence banner is present; lead IDs are
monotonic and unbracketed; `notes/open-questions.md`, `notes/refs.md`,
`notes/matrix/`, and `notes/raw/` are untouched; nothing committed.

## Audit handoff

The Phase 2bq completion audit (per `workflow/PHASE-AUDIT.md`) should
run while the source logs still exist under `.work/p2b/`, since
re-verifying log accounting requires them; once the logs are deleted,
the accounting criteria are `NOT RE-VERIFIABLE` with the receipt as
the surviving state. The `.work/p2b/` session logs should therefore
be preserved until the P2bq audit has reported. Downstream
consumption rules for the register are defined in the Phase 2c design
brief's lead-register section.
