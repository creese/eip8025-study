# Phase 3a — Grandine harvest (executable specification)

## Purpose and session type

Harvest, verbatim, the Grandine-side evidence needed to later assess
per-cluster equivalents of the EIP-8025 Lighthouse changes: repository
layout, README/docs, a wiring case study of the most recently added
fork, and one search transcript per cluster.

This is a **harvest session**. It runs fixed, recorded commands and
saves source material verbatim. It performs no interpretation,
summarization, synthesis, or classification. Recording a matching
file path and symbol name for a positive search hit is permitted
mechanical recording; deciding what a hit *means* (including any
CITED-ABSENT classification) belongs to a later phase.

This session inspects live Grandine sources only. It must not inspect
Lighthouse sources (live or checked out) in any form. Network access
is permitted solely to clone/fetch the pinned Grandine repository as
defined in Step 1.

## Declared inputs

1. `notes/refs.md` — read only the `## Pin: Grandine` block (pin
   verification gate, Step 1).
2. `notes/02-clusters.md` — defines the authoritative cluster set for
   Step 5.
3. This specification.

No other notes files, matrices, raw artifacts, phase briefs, phase
specifications, or repositories may be read. Prior conversation
history is never evidence or project state. If a declared input is
absent or unreadable, stop and report; never substitute another
source.

## Working locations

- Working copy of Grandine: `.work/p3a/repo/`
- Staging for new artifacts: `.work/p3a/staging/`
- stderr, diagnostics, recovery notes: `.work/p3a/logs/`

Create these directories if absent. Nothing is ever created directly
under `notes/raw/` except by the publication procedure in Step 7.
Files under `.work/` or `/tmp` are never cited as evidence.

## Command success policy

Every evidence-producing or state-changing command must have its exit
code checked before dependent work continues.

- `git`, `tree`, `find`, `cargo`, `cp`, `sha256sum`, file writes:
  nonzero exit code = step failure.
- `rg` / `grep`: exit 0 = matches found; exit 1 = no matches, which is
  a **valid negative result** and must be preserved as evidence; exit
  code ≥ 2 = tool error = step failure.

stderr policy: run each evidence command with stderr redirected to
`.work/p3a/logs/<artifact-name>.stderr`. Nonempty stderr on a
successful command does not fail the step but must be noted in the
artifact (`stderr: nonempty, see logs` — the log itself stays under
`.work/` and is not evidence). stderr on a failed command is preserved
in the log and quoted in the failure report.

On any step failure not explicitly handled below: stop; preserve
`.work/p3a/` intact (staging, logs, repo); publish nothing; report the
failed command, its exit code, and stderr; wait for the user.

## Artifact format

Every staged artifact is plain text and begins with this header:

```
# EIP-8025 Phase 3a harvest artifact
# Artifact: <filename>
# Cluster: <exact cluster identifier from notes/02-clusters.md, or "n/a">
# Repo: <repository URL read from the Grandine pin block in Step 1> @ <full SHA from Step 1>
# Date (UTC): <output of date -u +%Y-%m-%dT%H:%M:%SZ>
```

Each recorded command appears as:

```
## Command <n>
$ <exact command line as executed>
exit code: <n>
stderr: <empty | nonempty, see logs>
--- output ---
<verbatim stdout, unedited>
--- end output ---
```

Every artifact, once complete in staging, ends with the literal
final line `# END OF ARTIFACT`, written only after all of the
artifact's content. A Phase 3a file that does not end with this line
is by definition an incomplete or truncated copy, never valid
evidence or a completion signal.

An empty stdout is preserved as an empty section between the markers,
plus the line `stdout: empty (0 bytes)` immediately after
`--- end output ---`. Output is never truncated, paraphrased,
reordered, or annotated inside the output markers. If a single
command's stdout exceeds 1 MiB, do not truncate silently: split the
harvest into narrower commands (e.g., per-directory) and record each,
or stop and report if no bounded command can capture the evidence.

## Step 1 — Pin verification gate

`notes/refs.md` is the sole authority for the Grandine pin; this
specification intentionally records no literal repo URL or SHA.

1. Read the `## Pin: Grandine` block of `notes/refs.md` and take the
   repository URL and SHA from it. If the block is absent, or does
   not record both a repository URL and a full commit SHA, stop and
   report; never substitute, guess, or discover a pin.
2. If `.work/p3a/repo/` does not exist or is not a git repository:
   clone the pinned repo URL into `.work/p3a/repo/` and check out the
   pinned SHA (detached HEAD). If it exists: `git fetch origin`
   (allowed to fail if the SHA is already present locally), then check
   out the pinned SHA.
3. Verify, capturing the transcript for the receipt artifact:
   - `git rev-parse HEAD` output equals the pinned SHA exactly;
   - `git status --porcelain` output is empty (clean tree).
4. Stage `gr-pin-verification.txt` containing, in the standard format:
   the pinned values as read from `notes/refs.md`, the clone/fetch and
   checkout commands with exit codes, and the two verification
   commands with their verbatim output.

Failure handling: if the SHA cannot be fetched or checked out, if
`git rev-parse HEAD` does not equal the pin, or if the tree is not
clean, stop; stage nothing further; preserve `.work/p3a/`; report the
observed state versus the pin; wait for the user's re-pin decision.
Never continue past a failed gate. Every later step runs only after
this gate has passed in the current session.

## Step 2 — Layout harvest → `gr-layout.txt`

Run inside `.work/p3a/repo/`, recording each command per the format:

1. `tree -L 2` (if `tree` is unavailable, record that fact and run
   `find . -maxdepth 2 -type d -not -path './.git*' | sort` instead).
   This command must succeed; its failure is a step failure.
2. `cargo metadata --format-version 1 --no-deps --offline`
   (best-effort: if it fails or `cargo` is unavailable, record the
   exact command, exit code, and a `stderr: nonempty, see logs` note
   in the artifact and continue — the tree/find listing then stands
   alone as the layout evidence, and the failure must be restated in
   the final report).

## Step 3 — README/docs harvest → `gr-readme-docs.txt`

Run inside `.work/p3a/repo/`:

1. `ls` of the repository root (to show which README/docs entries
   exist).
2. Verbatim `cat` of the top-level README file(s) found in that
   listing.
3. A docs-directory probe that succeeds whether or not one exists:
   `find . -maxdepth 1 -type d -iname 'doc*' | sort` (exit 0 with
   empty output when none exists — that empty transcript is the
   preserved negative evidence; running `find` on a nonexistent path
   would instead exit nonzero, which the success policy treats as a
   step failure). For each directory the probe lists, additionally
   record a recursive file listing (paths only), e.g.
   `find docs -type f | sort`.

## Step 4 — Wiring case study: most recent fork addition → `gr-fork-case-study.txt`

The case study must be selected from saved evidence, not from model
recollection:

1. Search the working copy for the fork/phase enumeration (for
   example: `rg -n "enum Phase" --type rust`, and/or a search for
   known fork-name identifiers). Record every command and its verbatim
   output.
2. Enumeration order shows protocol ordering, not addition history,
   so selection must rest on repository history. For each variant in
   the saved enumeration, record a transcript of
   `git log --reverse --format='%H %cI %s' -S'<Variant>' -- <enumeration file>`
   and take its first line as the commit that introduced that
   variant. The "most recent fork addition" is the variant whose
   introducing commit has the latest commit date. Record the selected
   fork name and the introducing-commit line for every candidate. If
   any variant yields no introducing commit, or two candidates tie
   for latest, stop and report with the saved transcripts; do not
   guess. The user decides how to proceed.
3. For the selected fork, harvest its wiring: search the repository
   for the fork's identifier across each of these domains, recording
   each command and output — gossip topic/validation registration,
   Req/Resp registration, ENR/metadata fields, storage/pruning, the EL
   boundary, config/CLI/feature flags, and the spec-test harness.
4. For each positive hit retained as a finding, record the matching
   file path and symbol name next to (not inside) the output block.
   Empty results are preserved as negative transcripts.

## Step 5 — Per-cluster equivalent search → `gr-search-<slug>.txt`

1. Read `notes/02-clusters.md`. The cluster set recorded there is
   authoritative and complete for this step. If the file does not
   allow each cluster to be identified with a distinct identifier,
   stop and report; do not invent a cluster set (the brief's domain
   list is search guidance, never a substitute cluster set).
2. Slug rule: take the cluster's identifier exactly as recorded in
   `notes/02-clusters.md`, lowercase it, replace every run of
   characters outside `[a-z0-9]` with a single hyphen, and trim
   leading/trailing hyphens. If two clusters produce the same slug,
   stop and report the collision; do not rename.
3. For each cluster, in `.work/p3a/repo/`, search for the Grandine
   equivalent of that cluster's recorded Lighthouse-side content.
   Search terms must come from the cluster's recorded symbols, topic
   names, field names, or concepts in `notes/02-clusters.md`, plus
   reasonable Grandine-idiomatic renderings of those exact terms
   (case variants, snake/camel case, hyphen/underscore variants). Use
   `rg -n` (or `grep -rn` if `rg` is unavailable — record which).
   Multiple commands per cluster are expected; record every command
   actually run, including those with empty output.
4. Stage one artifact per cluster: `gr-search-<slug>.txt`, with the
   exact cluster identifier in the header. For positive results,
   record the matching file path and symbol next to the output block.
   Never assert absence: an empty-output transcript showing exactly
   what was searched is the only permissible negative evidence, and
   classification (e.g. CITED-ABSENT) is out of scope for this phase.
5. Order clusters as they appear in `notes/02-clusters.md`. If the
   session is interrupted mid-step, completed staged artifacts remain
   valid; resume per the Resume section.

## Step 6 — Staging validation

Before anything is published, validate the complete staged set:

1. Expected set = `gr-pin-verification.txt`, `gr-layout.txt`,
   `gr-readme-docs.txt`, `gr-fork-case-study.txt`, and one
   `gr-search-<slug>.txt` for every cluster in `notes/02-clusters.md`
   (re-derive the slug list from the file at validation time and
   compare — no cluster may be missing or extra).
2. Every staged artifact is nonempty, begins with the required header,
   contains at least one `## Command` block with a recorded exit code,
   records the pinned SHA from Step 1 in its header, and ends with
   the `# END OF ARTIFACT` marker line.
3. Every `rg`/`grep` block records exit code 0 or 1; any code ≥ 2 in a
   staged artifact is a validation failure.
4. Compute `sha256sum` of every staged artifact and write the
   validation receipt `gr-harvest-receipt.txt` in staging, containing:
   the standard header; the gate result (pinned SHA, verified equal);
   the first line of `--version` output for each tool used in the
   harvest (`git`, `rg` or `grep`, and `tree`/`find`/`cargo` as
   applicable), captured at validation time; the cluster-identifier →
   slug → filename mapping; the full expected set with per-file
   sha256 and byte size; and the validation outcome for checks 1–3.
   The receipt itself ends with the `# END OF ARTIFACT` marker,
   written after all other receipt content.

Failure handling: on any validation failure, publish nothing, preserve
staging and logs, report exactly which check failed and for which
artifact, and stop.

## Step 7 — Publication (no-clobber, receipt last)

This phase modifies no control file: `notes/refs.md`,
`notes/open-questions.md`, and every file outside `notes/raw/` must be
left untouched, so no evidence/control-file ordering coordination is
required beyond the rules below.

1. For each staged artifact except the receipt, in the Step 6 order:
   - If `notes/raw/<name>` does not exist: copy the staged file to
     `notes/raw/<name>`, then verify with `sha256sum` that the
     published file matches the staged hash. A hash mismatch is a
     step failure (report; do not delete the published file).
   - If `notes/raw/<name>` already exists: compare hashes. Equal →
     already published (resume case), record `already-published` and
     continue. Different → stop and report the conflict; never
     overwrite, edit, or delete anything under `notes/raw/`.
2. Publish `gr-harvest-receipt.txt` **last**, under the same
   no-clobber rule, only after every other artifact is published and
   hash-verified. A receipt in `notes/raw/` that ends with the
   `# END OF ARTIFACT` marker is the durable signal that publication
   completed; a receipt without the marker is a truncated interrupted
   copy, not a completion signal.
3. Keep `.work/p3a/` intact after publication (diagnostic value for
   the audit); it is not evidence and needs no cleanup.

## Resume and idempotency

Any rerun starts at Step 1 (the gate always runs, against the pin
currently recorded in `notes/refs.md`). Before reusing or
supplementing any previously staged or published Phase 3a artifact,
check the SHA recorded in its header: if any such artifact records a
SHA different from the current pin, stop and report — a re-pin has
occurred mid-phase, the artifact set must not mix snapshots, and the
user decides how to proceed. Then:

- If `notes/raw/gr-harvest-receipt.txt` exists: first check that it
  ends with the `# END OF ARTIFACT` marker. Marker absent → the
  receipt is a truncated interrupted copy, not a completion signal:
  stop and report; change nothing under `notes/raw/` (it is never
  edited); the user decides how to proceed. Marker present →
  publication completed previously: verify every artifact listed in
  the receipt exists under `notes/raw/` with a matching sha256 and
  its own end-marker. All match → report "Phase 3a already published;
  awaiting audit" and stop with no changes. Any mismatch or missing
  file → stop and report the discrepancy; change nothing under
  `notes/raw/`.
- If the receipt is absent but some `gr-*` Phase 3a artifacts exist
  under `notes/raw/`: an earlier publication was interrupted. Do not
  regenerate or touch published artifacts. If an artifact exists both
  in staging and under `notes/raw/`, compare sha256: equal → the
  published copy governs and the staged copy is left in place (Step 7
  records it `already-published`); different → stop and report both
  copies' path, byte size, and sha256, modify nothing, and preserve
  both — the published copy may itself be a truncated interrupted
  copy, `notes/raw/` is never edited, and the user decides how to
  proceed. Otherwise harvest (Steps 2–5) only the artifacts not yet
  present in either staging or `notes/raw/`, reusing valid staged
  artifacts, then run Step 6 validation over the union of published
  and staged files (hashing published files in place, and validating
  the published copy where both exist) and Step 7 publication for the
  remainder plus the receipt.
- If nothing is published: reuse any staged artifacts that pass the
  Step 6 per-file checks and re-run only the missing harvest steps,
  then Steps 6–7.

A rerun must either cleanly complete the remaining work or stop
without modifying or duplicating any published state.

## Prohibitions

- No interpretation, summarization, synthesis, or classification.
- No inspection of Lighthouse sources, live or local.
- No network access other than Step 1's clone/fetch of the pinned
  Grandine repo.
- No edits, replacements, or deletions under `notes/raw/`.
- No changes to `notes/refs.md`, `notes/open-questions.md`, or any
  file outside `notes/raw/` (other than under `.work/`).
- No new `[OPEN-Qn]` identifiers. Ambiguities or candidate questions
  are raised, unnumbered, in the final session report only.
- No commits. After publication, report the files created and stop
  for user review.

## Done when

Each criterion is an observable final condition verifiable read-only
from the repository state and the declared inputs:

1. `notes/raw/gr-pin-verification.txt` exists and contains a
   `git rev-parse HEAD` transcript with exit code 0 whose output
   equals the Grandine SHA pinned in `notes/refs.md`, plus a clean
   `git status --porcelain` transcript.
2. `notes/raw/gr-layout.txt` exists and contains a successful `tree`
   (or recorded `find` fallback) transcript, and either a successful
   `cargo metadata` transcript or its recorded failure.
3. `notes/raw/gr-readme-docs.txt` exists and contains the root
   listing, verbatim README content, and the docs listing or its
   preserved negative transcript.
4. `notes/raw/gr-fork-case-study.txt` exists and contains the
   fork-enumeration transcript, per-variant introducing-commit
   transcripts, the selected fork with its recorded latest
   introducing commit, and per-domain wiring transcripts with file
   path and symbol recorded for each retained positive hit.
5. For every cluster identified in `notes/02-clusters.md` there is a
   `notes/raw/gr-search-<slug>.txt` conforming to the slug rule and
   artifact format, in which every search command records its exact
   command line, exit code (0 or 1), and verbatim output; positive
   results record path and symbol, and negative results are preserved
   as empty-output transcripts. No extra `gr-search-*.txt` exists.
6. `notes/raw/gr-harvest-receipt.txt` exists, its recorded
   cluster mapping, file list, and sha256 values match the published
   artifacts byte-for-byte and cover exactly the Phase 3a artifact
   set, and the receipt and every published Phase 3a artifact end
   with the `# END OF ARTIFACT` marker line.
7. Git state shows only added files under `notes/raw/` (plus untracked
   `.work/` content): no preexisting file under `notes/raw/` was
   modified or deleted, and no file outside `notes/raw/` was changed.
8. Nothing was committed.
