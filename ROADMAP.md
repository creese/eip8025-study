# EIP-8025 Grandine Proposal Study — Roadmap

## Authority

For a phase with a specification under phases/, that phase
specification is authoritative for execution details.

For a phase whose specification has not yet been written, its design
brief in this file is the authoritative drafting source for its
intended scope, constraints, outputs, and completion goals.

When a phase specification is finalized, migrate the applicable
details from its design brief into the phase file. Then reduce the
roadmap entry to phase navigation and any rationale that remains
useful.

## Phase completion audits

Before advancing, run a dedicated audit of the completed phase against
every criterion in its `Done when` section, using the published outputs
themselves.

The audit session must not create or revise the next phase
specification.

If the phase does not pass, report the unmet criterion and stop.

After the audit reports PASS, the user reviews and accepts the result
before starting specification authoring.

## Specification authoring

If the next phase specification does not exist, run a separate session
to create it.

Follow `CLAUDE.md` and use:

* the corresponding design brief and applicable shared design material
  in this file;
* applicable decisions recorded in `notes/refs.md`;
* existing phase specifications only as structural references.

The new phase specification must be self-contained and executable.

A specification-authoring session must not execute the phase, inspect
the target repository, use network access, or modify study evidence.

If an unresolved decision prevents a safe executable specification,
stop and report the blocker.

After creating or revising a phase specification, inspect the actual
git diff and report:

* whether the target specification was created or changed;
* any unresolved drafting blocker;
* all files changed.

Commit behavior is governed by `CLAUDE.md`.

## Phase map

| Phase | Purpose | Primary output | Next step |
|---|---|---|---|
| 0 | Verify the environment and pin all required refs | `notes/refs.md` | Phase 1a-i |
| 1a-i | Harvest EIP PR evidence | `notes/raw/eip-manifest.tsv`, `notes/raw/eip-files/`, `notes/raw/eip-threads.md` | Phase 1a-ii |
| 1a-ii | Harvest the consensus-spec snapshot | `notes/raw/spec-manifest.txt`, `notes/raw/spec-files/`, `notes/raw/spec-todos.txt` | Phase 1a-iii |
| 1a-iii | Harvest complete pre-PR and post-PR EIP text | `notes/raw/eip-8025-pre-pr.md`, `notes/raw/eip-8025-post-pr.md` | Phase 1b |
| 1b | Build the normative requirement inventory | `notes/matrix/requirements.tsv` | Phase 2a |
| 2a | Harvest Lighthouse PR evidence | `notes/raw/lh-*` | Phase 2b |
| 2b | Classify Lighthouse files and map them to requirements | `notes/matrix/lh-files.tsv` | Phase 2c |
| 2c | Synthesize behavior clusters and reconcile orphan evidence | `notes/02-clusters.md` | Phase 3a |
| 3a | Harvest Grandine layout and search evidence | `notes/raw/gr-*` | Phase 3b |
| 3b | Map Grandine implementation surfaces or cited absences | `notes/matrix/gr-surfaces.tsv` | Phase 4 |
| 4 | Compare requirements, Lighthouse evidence, and Grandine surfaces | `notes/matrix/gap.tsv` | Phase 5 |
| 5 | Harvest ecosystem risks, then synthesize the risk register | `notes/raw/risks-*`, `notes/05-risks.md` | Phase 6 |
| 6 | Classify implementation scope | `notes/matrix/scope.tsv` | Checkpoint G |
| G | Make the human go/no-go and maintainer-question decisions | Decision in `notes/refs.md` | Phase 7, narrower scope, or stop |
| 7 | Critique and verify the human-written proposal | `notes/draft/proposal.md` | Final submission review |

## Model and effort guidance

| Phase | Tool, default effort | Adjust |
|---|---|---|
| 0, 1a-i, 1a-ii, 1a-iii, 2a, 3a, 5-harvest | Claude Code, low effort (fixed commands) | Never upgrade — mechanical only |
| 1b | Chat, medium | Higher if spec-vs-EIP reconciliation is messy |
| 2b | Claude Code, medium | Low for obvious test/CI-only batches |
| 2c, 4, 5-synthesis, 6 | Chat, medium | Higher for load-bearing clusters/tradeoffs; low for bookkeeping clusters |
| 3b | Claude Code, low→medium | Medium if Grandine structure needs judgment |
| 7 | Chat, medium | One high-effort adversarial pass pre-submission; model never writes proposal prose |

## Shared design material — PR thread harvesting

This procedure is shared drafting material for phase specifications
that harvest pull-request evidence. When every relevant phase
specification contains or references an authoritative procedure, this
section may be reduced or removed.

Preferred (REST, all three endpoints, paginated):
```
gh pr view <n> --repo <owner/repo> --json title,state,body,baseRefName,headRefName,headRefOid
gh api repos/<owner/repo>/issues/<n>/comments --paginate
gh api repos/<owner/repo>/pulls/<n>/comments --paginate
gh api repos/<owner/repo>/pulls/<n>/reviews --paginate
```
For thread resolution state, prefer GraphQL:
```
gh api graphql -f query='query{repository(owner:"<owner>",name:"<repo>"){pullRequest(number:<n>){reviewThreads(first:100){nodes{isResolved,comments(first:50){nodes{author{login},body,path,createdAt}}}}}}}'
```
Fallback: manual browser export into `notes/raw/` before the session,
as decided in Phase 0. If neither the preferred commands nor the
recorded fallback is available, stop and report; do not continue with
partial PR-thread evidence.
**Rule:** resolved/unresolved state may be incomplete regardless of method. A `reviewer-contested` classification must cite the specific comment present in raw evidence — never an inferred thread state.

## Shared design material — diff manifests

**Diff manifests** (`notes/raw/lh-manifest.tsv`, `notes/raw/eip-manifest.tsv`): `index | path | status (A/M/D/Rxx) | old_path_if_renamed | diff_file`. Built from `git diff --name-status -M`; the reconciliation source for done criteria.

## Phase 2a design brief — Lighthouse harvest

**Status:** Phase specification not yet written. This brief is
authoritative drafting source until phases/P2a-lighthouse-harvest.md
is finalized.
1. Gate: `git fetch`; compare head SHA to pin; on mismatch, stop, log delta in `notes/refs.md`, await your re-pin decision.
2. Run the following harvest operations exactly. The phase
   specification may replace placeholders with variables, but must not
   omit or substitute an operation:
   - `git diff --name-status -M <base>..<head>` → build `notes/raw/lh-manifest.tsv` (reconciliation source of truth)
   - `git diff --stat <base>..<head>` (human-readable summary only)
   - `git log --oneline <base>..<head>`
   - per-manifest-row `git diff <base>..<head> -- <path> > notes/raw/lh-files/<index>.diff`
   - `git diff <base>..<head> -- Cargo.toml Cargo.lock '**/Cargo.toml' > notes/raw/lh-deps.diff`
   - PR metadata/threads per `Shared design material — PR thread harvesting`
3. Record merge-base drift vs `sigp/lighthouse@unstable` once in
   `notes/refs.md`. 

### Retained v3.2 completion goal
`notes/raw/lh-manifest.tsv` exists; per-file diff count = manifest row count; `notes/raw/lh-deps.diff` exists; threads harvested 

## Phase 2b design brief — Lighthouse classification

**Status:** Phase specification not yet written. This brief is
authoritative drafting source until
phases/P2b-lighthouse-classification.md is finalized.
1. Classify (2b, batched): change type + classification per schema, each with a cited signal (TODO, hardcoded devnet value, specific reviewer comment); frame all findings as "one reference implementation."

**Provisional cluster taxonomy (fixed before 2b):** C1 SSZ/types · C2 gossip validation · C3 Req/Resp · C4 proof engine · C5 EL integration · C6 block import/forkchoice · C7 config/CLI · C8 validator/prover service · C9 sync · C10 tests/CI · C-unassigned. 2c may split/merge but must append a remap table (`old_id → new_id, date`) to `notes/02-clusters.md`; row citations are interpreted through the remap.

**`notes/matrix/lh-files.tsv`**: `row_id | manifest index | file | symbol/lines | change type | provisional cluster_id | req_id(s) or NONE | classification (spec-driven / LH-arch-choice / scaffolding / CI-devnet / dependency / reverted-partial / reviewer-contested / unclear) | classification signal | test evidence | notes`. `req_id = NONE` rows feed the 2c orphan check, not errors.

### Retained v3.2 drafting source
**P2b — Batched classification (Claude Code):**
> Read `notes/matrix/lh-files.tsv` for the resume point (last manifest index processed). Process the next `<N>` manifest rows from `notes/raw/lh-files/`. Append rows per schema using the provisional taxonomy C1-C10/C-unassigned; cite a concrete signal for every classification; `reviewer-contested` only with a specific comment from `notes/raw/lh-threads.md`; use `unclear` freely; `req_id = NONE` where nothing matches. End by stating the resume point. Do not revisit completed rows.

### Retained v3.2 completion goal
Every manifest row has a table row (reconcile against the manifest, not --stat); every row classified with a signal; `unclear` ≤ ~15% or a follow-up batch scheduled 

### Mistakes to avoid
- Treating WIP scaffolding (devnet hacks, TODOs, reverted code) as Lighthouse's intended design.
- Reconciling 2b against `--stat` instead of the name-status manifest (renames will slip through).

## Phase 2c design brief — cluster synthesis
**Status:** Phase specification not yet written. This brief is
authoritative drafting source until the corresponding phase
specification is finalized.

**Provisional cluster taxonomy (fixed before 2b):** C1 SSZ/types · C2 gossip validation · C3 Req/Resp · C4 proof engine · C5 EL integration · C6 block import/forkchoice · C7 config/CLI · C8 validator/prover service · C9 sync · C10 tests/CI · C-unassigned. 2c may split/merge but must append a remap table (`old_id → new_id, date`) to `notes/02-clusters.md`; row citations are interpreted through the remap.

**`notes/02-clusters.md`** — per cluster: req_ids; claim bullets, each *claim* ending `[row_id]`, `[raw:<path>]`, or `[OPEN-Qn]` (headings/structure exempt); test coverage or gap; confidence H/M/L (H = code+tests read; M = code read; L = inferred); proposal implications as bullets, no prose paragraphs. Ends with remap table and two orphan lists: req_ids with no rows; rows with req_id NONE.

### Retained v3.2 drafting source
**P2c — Cluster synthesis (Chat):**
> Loaded: `notes/matrix/lh-files.tsv`, `notes/matrix/requirements.tsv`. Produce `notes/02-clusters.md` per schema — every claim cites `[row_id]`, `[raw:<path>]`, or `[OPEN-Qn]`. You may split/merge provisional clusters — append the remap table. End with both orphan lists: req_ids with zero rows (Lighthouse gap or my inventory gap — flag which you think and why, logging open questions via `notes/open-questions.md`), and rows with req_id NONE.

### Retained v3.2 completion goal
Every claim cited per the invariant; remap table present; both orphan lists present, each orphan resolved or logged via `notes/open-questions.md` 

### Mistakes to avoid
- Treating WIP scaffolding (devnet hacks, TODOs, reverted code) as Lighthouse's intended design.

## Phase 3a design brief — Grandine harvest

**Status:** Phase specification not yet written. This brief is
authoritative drafting source until phases/P3a-grandine-harvest.md is
finalized.
1. Gate: verify that the checked-out Grandine SHA matches the pin in
   `notes/refs.md`; on mismatch, stop and await the user's re-pin
   decision.
2. Layout: `cargo metadata` / `tree -L 2`, README/docs; most recent fork addition as the wiring case study.
3. Per-cluster equivalent search: gossip topic/validation
   registration, Req/Resp registration, ENR/metadata fields,
   storage/pruning, EL boundary, config/CLI/feature flags, and the
   spec-test harness.
4. Save each cluster's exact search command and output to
   `notes/raw/gr-search-<cluster>.txt`. For positive results, record
   the matching path and symbol. Preserve empty output as evidence for
   a possible CITED-ABSENT classification.

### Retained v3.2 drafting source
For each cluster in `notes/02-clusters.md`, search for the Grandine
equivalent. Save the exact command and output to
`notes/raw/gr-search-<cluster>.txt`. Record the path and symbol for
positive findings. Never assert absence without a saved empty
transcript.

### Retained v3.2 completion goal
Grandine SHA verified at session open; every cluster has recorded
positive search evidence or a saved negative-search transcript.

## Phase 3b design brief — Grandine surface mapping

**Status:** Phase specification not yet written. This brief is
authoritative drafting source until
phases/P3b-grandine-surface-mapping.md is finalized.
1. Mark a cluster CITED-ABSENT only when the corresponding
   `notes/raw/gr-search-<cluster>.txt` exists and is cited as evidence.

**`notes/matrix/gr-surfaces.tsv`**:
`cluster_id | req_ids | grandine path/symbol OR CITED-ABSENT |
evidence pointer (notes/raw/gr-* file) | confidence | notes`.

### Retained v3.2 drafting source
Using the saved Phase 3a evidence, fill `notes/matrix/gr-surfaces.tsv`.
Record a positive Grandine path/symbol or mark CITED-ABSENT only when
the corresponding saved negative-search transcript exists and is
cited.

### Retained v3.2 completion goal
Every cluster has a positive Grandine surface row or a CITED-ABSENT
row citing its saved negative-search transcript.

## Phase 4 design brief — gap analysis
**Status:** Phase specification not yet written. This brief is
authoritative drafting source until the corresponding phase
specification is finalized.

**`notes/matrix/gap.tsv`**: `cluster_id | req_ids | lh row_ids | gr-surfaces ref | mismatch description | port difficulty | confidence | PROV-SPEC tag if applicable | open_q ids`.

### Retained v3.2 drafting source
**P4 — Gap analysis (Chat):**
> Loaded: `notes/02-clusters.md`, `notes/matrix/gr-surfaces.tsv`. Fill `notes/matrix/gap.tsv` per schema, tagging PROV-SPEC where reasoning depends on the provisional baseline. Every mismatch description cites row/cluster IDs or raw pointers. If I say a cluster feels fuzzy, switch to asking me questions about it before filling its row; otherwise proceed directly.

### Retained v3.2 completion goal
Every cluster in gap.tsv; every L-confidence row has an open_q; PROV-SPEC tags applied where applicable 

## Phase 5 design brief — risk harvest and synthesis
**Status:** Phase specification not yet written. This brief is
authoritative drafting source until the corresponding phase
specification is finalized.

**`notes/05-risks.md`**: `risk_id | description | likelihood (H/M/L) | impact (H/M/L) | evidence ([raw:<path>] or matrix row) | mitigation or 'accept' | proposal section it affects`.

### Retained v3.2 drafting source
**P5 — Risk harvest then synthesis (Claude Code, then Chat):**
> (Harvest, Claude Code) You may use network, GitHub, and web searches for this session, but every finding must be saved to a raw file before any synthesis: consensus-specs test-vector status for EIP-8025 → `notes/raw/risks-spec-tests.txt`; Grandine issue-tracker search for EIP-8025/execution-proof work → `notes/raw/risks-grandine-issues.txt`; Grandine PR search → `notes/raw/risks-grandine-prs.txt`; L1-zkEVM breakout-call notes/status → `notes/raw/risks-breakout-notes.txt`; dependency analysis of `notes/raw/lh-deps.diff` (new crates, versions, licenses) → `notes/raw/risks-prover-deps.txt`. Save search queries alongside results. No conclusions in this session.
> (Synthesis, Chat) Loaded: the five `notes/raw/risks-*` files plus matrices. Fill `notes/05-risks.md` per schema; every evidence cell is `[raw:<path>]` or a matrix row.

### Retained v3.2 completion goal
All five `notes/raw/risks-*` files exist (with saved queries) before synthesis; risk table covers at minimum spec-status/stagnancy, fork drift, scaffolding, test-vector absence, prover dependencies, duplicate-effort check; every evidence cell cites `[raw:<path>]` or a matrix row 

### Mistakes to avoid
- Letting Phase 5 web findings reach Chat synthesis without first landing in `notes/raw/risks-*`.

## Phase 6 design brief — scope decomposition
**Status:** Phase specification not yet written. This brief is
authoritative drafting source until the corresponding phase
specification is finalized.

**`notes/matrix/scope.tsv`**: `req_id/surface | MVP / stretch / non-goal / needs-input | justification (row/cluster/risk ids) | PROV-SPEC tag if applicable | maintainer question verbatim (if needs-input)`. PROV-SPEC-tagged rows may not be classified stronger than needs-input until the baseline is confirmed.

### Retained v3.2 drafting source
**P6 — Scope (Chat):**
> Loaded: `notes/matrix/gap.tsv` and `notes/05-risks.md`. Classify every gap row: MVP / stretch / explicit non-goal / needs-maintainer-input, with justification IDs (row, cluster, or risk_id). PROV-SPEC rows: needs-input at strongest. Draft each needs-input question verbatim, ready to post.

### Retained v3.2 completion goal
Every gap row classified; PROV-SPEC rows ≤ needs-input; every needs-input row has a verbatim question; justifications cite gap/cluster/risk IDs 

### Mistakes to avoid
- Running scope decomposition before the risk table exists.

## Checkpoint G design brief
**Status:** This brief is authoritative until the checkpoint procedure
is finalized.

### Retained v3.2 completion goal
Go/narrow/park decision + post-questions-first decision logged in `notes/refs.md` 

### Mistakes to avoid
- Skipping the go/no-go checkpoint or the maintainer-question decision before drafting.

## Phase 7 design brief — proposal critique
**Status:** Phase specification not yet written. This brief is
authoritative drafting source until the corresponding phase
specification is finalized.

### Retained v3.2 drafting source
**P7 — Draft critique (Chat):**
> Here's `notes/draft/proposal.md` section `<X>` plus the matrices. Do not rewrite. List every claim not traceable to a matrix row, a `[raw:<path>]` pointer, or an `OPEN-Q` tag. After I confirm or fix citations, you may tighten prose by editing the file — I will review `git diff` myself; do not summarize your changes.

### Retained v3.2 completion goal
`git diff` review of every model edit shows zero new/strengthened claims; final draft has zero claims lacking a row ID, raw pointer, or OPEN-Q tag 
