# Phase 3a — Grandine harvest (executable specification)

## Purpose and session type

Harvest, verbatim, the Grandine-side evidence needed to later assess
per-cluster equivalents of the EIP-8025 Lighthouse changes: repository
layout, README/docs, a wiring case study of the most recently added
fork, and one search transcript per cluster.

Remediation R (Steps R1–R4) closes an evidence gap identified after
the original harvest passed audit: the pinned Grandine checkout
contains an uninitialized `eth2_libp2p` submodule, so the working
tree covered by Step 4's wiring searches and Step 5's per-cluster
searches did not include that submodule's content. The remediation
initializes the submodule at the exact gitlink SHA recorded by the
pinned commit, replays the affected search commands verbatim,
records an explicitly submodule-scoped variant of each, and
publishes supplemental artifacts plus a separate remediation
receipt. All original Phase 3a artifacts and the original receipt
are preserved unchanged.

This is a **harvest session**. It runs fixed, recorded commands and
saves source material verbatim. It performs no interpretation,
summarization, synthesis, or classification. Recording a matching
file path and symbol name for a positive search hit is permitted
mechanical recording; deciding what a hit *means* (including any
CITED-ABSENT classification) belongs to a later phase.

This session inspects live Grandine sources only. It must not inspect
Lighthouse sources (live or checked out) in any form. Network access
is permitted solely to clone/fetch the pinned Grandine repository as
defined in Step 1 and, for Remediation R, to fetch the `eth2_libp2p`
submodule at its recorded gitlink SHA as defined in Step R1.

## Declared inputs

1. `notes/refs.md` — read only the `## Pin: Grandine` block (pin
   verification gate, Step 1).
2. `notes/02-clusters.md` — defines the authoritative cluster set for
   Step 5.
3. This specification.
4. For Remediation R only: the published original Phase 3a artifacts
   under `notes/raw/` — `gr-harvest-receipt.txt` and every artifact
   it lists (`gr-pin-verification.txt`, `gr-layout.txt`,
   `gr-readme-docs.txt`, `gr-fork-case-study.txt`, and every
   `gr-search-<slug>.txt`). All of them may be read to recompute
   `sha256sum` and byte size for the Step R1 and Step R3 integrity
   checks; the receipt may additionally be read for its recorded
   file list and hashes, and `gr-fork-case-study.txt` and the
   `gr-search-<slug>.txt` files for the recorded command lines to
   replay (Step R2). No other raw artifact may be read, and no
   published artifact is ever modified.

No other notes files, matrices, raw artifacts, phase briefs, phase
specifications, or repositories may be read. Prior conversation
history is never evidence or project state. If a declared input is
absent or unreadable, stop and report; never substitute another
source.

## Working locations

- Working copy of Grandine: `.work/p3a/repo/`
- Staging for new artifacts: `.work/p3a/staging/`
- Staging for Remediation R artifacts: `.work/p3a/staging-r1/`
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

Every Remediation R artifact (Steps R1–R3) additionally carries,
immediately after the `# Repo:` line:

```
# Submodule: eth2_libp2p @ <full gitlink SHA verified in Step R1>
```

In a Step R2 supplemental artifact, each replayed command block
carries, immediately after its `## Command <n>` heading line and
before the `$` line, the mechanical mapping line
`replays: <replay source artifact filename> Command <m>`; the
replayed command line itself must be byte-identical to the command
line recorded in that source block. Each submodule-scoped variant
block (Step R2.3) carries, in the same position, the mapping line
`scoped variant of: <replay source artifact filename> Command <m>`
instead.

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

## Remediation R — `eth2_libp2p` submodule evidence gap

Steps R1–R4 run only after the original artifact set
(`gr-pin-verification.txt` through `gr-harvest-receipt.txt`) is fully
published and receipt-verified; Step R1 checks this. They stage into
`.work/p3a/staging-r1/`, publish only new `gr-sub-*` files, and never
modify, re-list, or supersede any published artifact. All original
rules — command success policy, stderr policy, artifact format,
verbatim output, negative-evidence preservation, end-marker,
no-clobber publication, and the general step-failure handling — apply
unchanged unless a step below says otherwise.

### Step R1 — Remediation gate: original set, parent pin, submodule

Run the Step 1 pin verification gate first, exactly as written (the
gate always runs, in every session, including a remediation-only
session). Then, capturing every transcript for
`gr-sub-pin-verification.txt`:

1. Original-set check and pre-remediation baseline:
   `notes/raw/gr-harvest-receipt.txt` must exist and end with the
   `# END OF ARTIFACT` marker, and every artifact it lists must exist
   under `notes/raw/` with a matching `sha256sum` (recompute and
   record). Record the receipt's own `sha256sum` and byte size as
   the **pre-remediation baseline**, captured before the remediation
   stages or publishes anything: the original receipt records no
   self-hash and the original artifacts are untracked, so this
   baseline is the earliest durable record of the receipt's own
   hash, and Step R3 and the audit verify the receipt against it. If
   this check fails, the original harvest is incomplete or damaged:
   do not start the remediation; stop and follow the Resume rules
   for the original harvest instead.
2. Submodule inventory, inside `.work/p3a/repo/`:
   - `git ls-tree -r HEAD | grep -E '^160000 '` — enumerates the
     actual mode-160000 gitlink entries of the pinned tree (the
     recorded exit code is the pipeline's, i.e. `grep`'s). The
     pipeline must exit 0 and its output must include an
     `eth2_libp2p` entry; exit 1 or a missing `eth2_libp2p` line
     means the remediation premise does not match the pinned tree:
     stop and report.
   - `git config -f .gitmodules --get-regexp '^submodule\..*\.(path|url)$'`
     — records every declared submodule path and URL;
   - `git ls-tree HEAD -- eth2_libp2p` — must show mode `160000`
     (a gitlink); its object ID is the **recorded gitlink SHA**, the
     only revision at which the submodule may be checked out.
   If `.gitmodules` has no entry for `eth2_libp2p`, stop and report:
   the submodule cannot be initialized from recorded values, and the
   user decides how to proceed. If the gitlink enumeration shows any
   mode-160000 entry other than `eth2_libp2p` (whether or not
   `.gitmodules` declares it), stop **before initializing anything
   or replaying any search**: stage nothing further, preserve
   `.work/p3a/` intact, report the complete gitlink inventory
   transcript verbatim, and wait for the user's decision on the
   remediation's scope. Never proceed past additional gitlinks by
   initializing only `eth2_libp2p` and deferring the rest to the
   final session report.
3. Initialize: `git submodule update --init -- eth2_libp2p`, using
   only the URL recorded in `.gitmodules` and the recorded gitlink
   SHA (never `--remote`, never a searched, guessed, or discovered
   branch, ref, or URL). Network access is permitted solely for this
   fetch. No manual initialization step is required or permitted:
   this recorded command performs the entire initialization.
4. Verify:
   - `git -C eth2_libp2p rev-parse HEAD` equals the recorded gitlink
     SHA exactly;
   - `git submodule status -- eth2_libp2p` reports that same SHA with
     no `+` or `-` prefix;
   - `git rev-parse HEAD` still equals the pinned parent SHA, and
     `git status --porcelain` output is empty;
   - `git -C eth2_libp2p status --porcelain` output is empty;
   - populated-tree evidence:
     `find eth2_libp2p -maxdepth 2 -type d -not -path '*/.git*' | sort`
     (nonempty output required);
   - ignore-scope coverage probe: from the repository root,
     `rg --files -g 'eth2_libp2p/**'` must exit 0 with nonempty
     output, establishing that the repository's ignore rules do not
     exclude the submodule's files from root-wide `rg` searches;
     empty output or failure is a gate failure. If `rg` is
     unavailable in this session, record that fact; the probe may be
     skipped only if every command extracted in Step R2 invokes
     `grep`, which does not honor ignore files.
5. Stage `gr-sub-pin-verification.txt` in `.work/p3a/staging-r1/`
   containing, in the standard format with the `# Submodule:` header
   line: the original-set check transcript with the pre-remediation
   baseline, the inventory transcripts, the initialization command
   with its exit code, and the verification transcripts.

Failure handling: if the gitlink enumeration shows any mode-160000
entry other than `eth2_libp2p`, if initialization fails, if any SHA
does not match, if either tree is not clean, or if the submodule
tree is empty after initialization, stop; stage nothing further;
preserve `.work/p3a/` intact; report the observed state versus the
recorded values (for the gitlink case, the complete inventory
transcript); wait for the user's decision. Never continue past a failed gate, never
check out any other submodule revision, and never delete or reset the
submodule state to force progress. Steps R2–R4 run only after this
gate has passed in the current session.

### Step R2 — Replay of affected searches → supplemental artifacts

Replay source artifacts: the published
`notes/raw/gr-fork-case-study.txt` (the case-study wiring searches,
including the networking domains the submodule affects) and, for
every cluster derived from `notes/02-clusters.md` under Step 5's slug
rule, the published `notes/raw/gr-search-<slug>.txt`. If the derived
slug list does not match, one-to-one, the `gr-search-*.txt` set
listed in the original receipt, stop and report the mismatch.

For each replay source artifact, in order:

1. Extract, in recorded order, every `## Command` block whose command
   line (its `$` line) invokes `rg` or `grep`. These working-tree
   search commands are the ones whose coverage the uninitialized
   submodule invalidated; other recorded commands (`git log`, `tree`,
   `find`, `cat`, `ls`, `cargo`, …) query history or fixed paths that
   submodule initialization does not change, and are not replayed. If
   a replay source artifact contains no such command, stop and report
   — that contradicts the remediation premise.
2. Replay each extracted command line byte-identically — the same
   command line, unmodified — from `.work/p3a/repo/` (the same
   working directory as the original harvest), with the submodule now
   initialized. Record every replay, including those with empty
   output, in the standard format with its `replays:` mapping line,
   into the corresponding supplemental artifact in
   `.work/p3a/staging-r1/`:
   - `gr-sub-fork-case-study.txt` for the case study;
   - `gr-sub-search-<slug>.txt` for each cluster, with the exact
     cluster identifier in the header.
3. Immediately after each replay, record its **submodule-scoped
   variant**: the same command line with ` eth2_libp2p` appended as
   one additional path argument (if the command line ends in a
   stderr-redirection clause, insert the path argument immediately
   before it). A byte-identical replay only covers the submodule if
   the original command's path, glob, and ignore scope happens to
   include it; appending the path argument adds `eth2_libp2p` to the
   search scope regardless, so the variant is the explicit submodule
   evidence in every case. The variant block carries the
   `scoped variant of:` mapping line. If an extracted command line
   contains a shell operator other than a trailing
   stderr-redirection clause (`|`, `;`, `&`, `<`, `>`, `$(`,
   backquote), a path argument cannot be appended mechanically: stop
   and report the exact command line; the user decides how to scope
   it.
4. All original recording rules apply unchanged to replays and
   scoped variants alike: exit code 0 or 1 is
   valid for `rg`/`grep` and ≥ 2 is a step failure; an empty-output
   transcript is preserved as the only permissible negative evidence
   and absence is never asserted; for each positive hit retained as a
   finding — including hits under `eth2_libp2p/` — record the
   matching file path and symbol name next to (not inside) the output
   block; no truncation, paraphrase, reordering, annotation inside
   the output markers, or classification.

### Step R3 — Remediation staging validation

Before anything is published:

1. Expected remediation set = `gr-sub-pin-verification.txt`,
   `gr-sub-fork-case-study.txt`, and one `gr-sub-search-<slug>.txt`
   for every cluster in `notes/02-clusters.md` (re-derive the slug
   list from the file at validation time and compare — no cluster may
   be missing or extra).
2. Every staged remediation artifact passes the Step 6 per-file
   checks (nonempty; required header recording the pinned parent SHA;
   at least one `## Command` block with a recorded exit code; ends
   with the `# END OF ARTIFACT` marker) and additionally carries the
   `# Submodule:` header line with the gitlink SHA verified in
   Step R1.
3. Replay-fidelity check, per supplemental artifact: every replayed
   command line is byte-identical to a `rg`/`grep` command line
   recorded in its replay source artifact; its `replays:` mapping
   names that source block; no `rg`/`grep` command line of the source
   is missing from the replay set; every replay is immediately
   followed by its submodule-scoped variant, whose command line is
   the source line with `eth2_libp2p` appended per Step R2's rule
   and whose `scoped variant of:` mapping names the same source
   block; and every `rg`/`grep` block records exit code 0 or 1 (any
   code ≥ 2 is a validation failure).
4. Preservation check: recompute the `sha256sum` and byte size of
   `notes/raw/gr-harvest-receipt.txt` and compare them against the
   pre-remediation baseline recorded by Step R1 in
   `gr-sub-pin-verification.txt`; recompute `sha256sum` for every
   artifact the receipt lists and confirm each still matches its
   receipt-recorded hash; record all recomputed values. Any mismatch
   is a validation failure (report; modify nothing).
5. Write the validation receipt `gr-sub-remediation-receipt.txt` in
   `.work/p3a/staging-r1/`, containing: the standard header with the
   `# Submodule:` line; the gate results (pinned parent SHA and
   recorded gitlink SHA, each verified equal); the first line of
   `--version` output for each tool used in the remediation (`git`,
   `rg` or `grep`, and `find`), captured at validation time; the
   cluster-identifier → slug → filename mapping; the full expected
   remediation set with per-file sha256 and byte size; the
   preservation-check outcome with the pre-remediation baseline and
   the recomputed original hashes;
   and the validation outcome for checks 1–4. The receipt itself ends
   with the `# END OF ARTIFACT` marker, written after all other
   receipt content.

Failure handling: on any validation failure, publish nothing,
preserve staging and logs, report exactly which check failed and for
which artifact, and stop.

### Step R4 — Remediation publication

Publish the remediation set into `notes/raw/` under Step 7's rules
unchanged: per-artifact no-clobber copy in the Step R3 order with
post-copy hash verification; an existing file with an equal hash is
recorded `already-published` (resume case); an existing file with a
different hash stops the session with a conflict report — nothing
under `notes/raw/` is ever overwritten, edited, or deleted.
`gr-sub-remediation-receipt.txt` is published **last**, only after
every other remediation artifact is published and hash-verified; only
its marker-terminated presence under `notes/raw/` signals that
remediation publication completed. The original receipt and artifacts
are not rewritten, re-listed, or superseded: the remediation receipt
supplements the original receipt, and both remain in force for the
audit. Keep `.work/p3a/` intact after publication.

## Resume and idempotency

Any rerun starts at Step 1 (the gate always runs, against the pin
currently recorded in `notes/refs.md`). Before reusing or
supplementing any previously staged or published Phase 3a artifact,
check the SHA recorded in its header: if any such artifact records a
SHA different from the current pin, stop and report — a re-pin has
occurred mid-phase, the artifact set must not mix snapshots, and the
user decides how to proceed. For remediation artifacts this check
covers both recorded SHAs: the parent SHA must equal the current pin
and the submodule SHA must equal the gitlink SHA recorded by that
pinned commit. Then:

- If `notes/raw/gr-harvest-receipt.txt` exists: first check that it
  ends with the `# END OF ARTIFACT` marker. Marker absent → the
  receipt is a truncated interrupted copy, not a completion signal:
  stop and report; change nothing under `notes/raw/` (it is never
  edited); the user decides how to proceed. Marker present →
  publication completed previously: verify every artifact listed in
  the receipt exists under `notes/raw/` with a matching sha256 and
  its own end-marker. All match → the original harvest is published;
  continue with Remediation R (Step R1, or the remediation resume
  rules below if any `gr-sub-*` state already exists). Any mismatch
  or missing file → stop and report the discrepancy; change nothing
  under `notes/raw/`.
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

Remediation resume rules (reached only when the original set is fully
published and receipt-verified):

- If `notes/raw/gr-sub-remediation-receipt.txt` exists: first check
  that it ends with the `# END OF ARTIFACT` marker. Marker absent →
  truncated interrupted copy, not a completion signal: stop and
  report; change nothing under `notes/raw/`; the user decides how to
  proceed. Marker present → remediation publication completed
  previously: verify every artifact it lists exists under
  `notes/raw/` with a matching sha256 and its own end-marker, and
  re-run the Step R3 preservation check over the original set,
  using the pre-remediation baseline recorded in the published
  `gr-sub-pin-verification.txt`. All
  match → report "Phase 3a original and remediation already
  published; awaiting audit" and stop with no changes. Any mismatch
  or missing file → stop and report the discrepancy; change nothing
  under `notes/raw/`.
- If the remediation receipt is absent but some `gr-sub-*` artifacts
  exist under `notes/raw/`: an earlier remediation publication was
  interrupted. Apply the original interrupted-publication rules to
  the remediation set: never touch published copies; where a file
  exists both in `.work/p3a/staging-r1/` and under `notes/raw/`,
  equal hashes → the published copy governs (`already-published`),
  different hashes → stop, report both copies' path, byte size, and
  sha256, and modify nothing. Otherwise, after the Step R1 gate,
  replay (Step R2) only the supplemental artifacts not yet present in
  either staging or `notes/raw/`, then run Step R3 validation over
  the union of published and staged remediation files (hashing
  published files in place) and Step R4 publication for the remainder
  plus the remediation receipt.
- Staged files in `.work/p3a/staging-r1/` may be reused only if they
  pass the Step R3 per-file checks and record both the current pinned
  parent SHA and the current recorded gitlink SHA.

A rerun must either cleanly complete the remaining work or stop
without modifying or duplicating any published state.

## Prohibitions

- No interpretation, summarization, synthesis, or classification.
- No inspection of Lighthouse sources, live or local.
- No network access other than Step 1's clone/fetch of the pinned
  Grandine repo and Step R1's `git submodule update --init` fetch of
  `eth2_libp2p` at its recorded gitlink SHA.
- No initialization, fetch, or checkout of any submodule other than
  `eth2_libp2p`, and of any submodule revision other than the
  recorded gitlink SHA.
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
9. `notes/raw/gr-sub-pin-verification.txt` exists and contains: the
   original-set check transcript (original receipt marker-terminated,
   every listed artifact hash-matched) with the pre-remediation
   baseline (the original receipt's own sha256 and byte size); the
   gitlink enumeration transcript (`git ls-tree -r HEAD` filtered to
   mode-160000 entries) showing `eth2_libp2p` as the only gitlink in
   the pinned tree; the `.gitmodules`
   inventory transcript; a `git ls-tree HEAD -- eth2_libp2p`
   transcript showing a mode-160000 gitlink and its SHA; the
   `git submodule update --init -- eth2_libp2p` command with exit
   code 0; and verification transcripts showing the submodule checked
   out at exactly the recorded gitlink SHA, clean parent and
   submodule trees, the nonempty ignore-scope coverage probe (or its
   recorded grep-only exception), and a nonempty populated-tree
   listing.
10. `notes/raw/gr-sub-fork-case-study.txt` exists, and every
    `rg`/`grep` command line recorded in
    `notes/raw/gr-fork-case-study.txt` is replayed in it
    byte-identically, each with its `replays:` mapping line, an exit
    code of 0 or 1, and verbatim output, and each replay is
    immediately followed by its submodule-scoped variant per
    Step R2's construction rule, with its `scoped variant of:`
    mapping line and exit code 0 or 1, with file path and symbol
    recorded for each retained positive hit.
11. For every cluster identified in `notes/02-clusters.md` there is a
    `notes/raw/gr-sub-search-<slug>.txt` conforming to the slug rule
    and remediation artifact format, in which every `rg`/`grep`
    command line of the corresponding original
    `gr-search-<slug>.txt` is replayed byte-identically and paired
    with its submodule-scoped variant under the same rules, and
    negative results are preserved as empty-output transcripts. No
    extra `gr-sub-search-*.txt` exists.
12. `notes/raw/gr-sub-remediation-receipt.txt` exists; its recorded
    cluster mapping, file list, and sha256 values match the published
    remediation artifacts byte-for-byte and cover exactly the
    remediation set; it records the preservation check with the
    pre-remediation baseline and recomputed hashes for every artifact
    listed in the original receipt; and it and every published
    remediation artifact end with the `# END OF ARTIFACT` marker line
    and record both the pinned parent SHA and the recorded gitlink
    SHA.
13. Every artifact listed in `notes/raw/gr-harvest-receipt.txt`, and
    that receipt itself, is unchanged across the remediation: the
    receipt's recomputed sha256 and byte size match the
    pre-remediation baseline recorded in
    `notes/raw/gr-sub-pin-verification.txt`, and every listed
    artifact's recomputed hash matches the receipt, as recorded in
    `notes/raw/gr-sub-remediation-receipt.txt`. (The original
    artifacts are untracked, so git history cannot evidence their
    preservation; the Step R1 baseline is the earliest durable
    record of the original receipt's own hash.)

Criteria 1–8 (original harvest) and 9–13 (Remediation R) are
cumulative: the next completion audit verifies all thirteen, and the
phase is complete only when every criterion holds.
