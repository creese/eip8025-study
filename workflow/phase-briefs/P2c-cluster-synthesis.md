# Phase 2c design brief — cluster synthesis

**Status:** Authoritative drafting source until
`phases/P2c-cluster-synthesis.md` exists as an executable phase
specification under the rules in `CLAUDE.md`.

Applicable workflow inputs:
- `workflow/PROVISIONAL-CLUSTER-TAXONOMY.md`

The cluster taxonomy, remap rule, and WIP-scaffolding warning are
defined in `workflow/PROVISIONAL-CLUSTER-TAXONOMY.md`.

**`notes/02-clusters.md`** — per cluster: req_ids; claim bullets, each *claim* ending `[row_id]`, `[raw:<path>]`, or `[OPEN-Qn]` (headings/structure exempt); test coverage or gap; confidence H/M/L (H = code+tests read; M = code read; L = inferred); proposal implications as bullets, no prose paragraphs. Ends with remap table and two orphan lists: req_ids with no rows; rows with req_id NONE.

## Retained v3.2 drafting source
**P2c — Cluster synthesis (Chat):**
> Loaded: `notes/matrix/lh-files.tsv`, `notes/matrix/requirements.tsv`. Produce `notes/02-clusters.md` per schema — every claim cites `[row_id]`, `[raw:<path>]`, or `[OPEN-Qn]`. You may split/merge provisional clusters — append the remap table. End with both orphan lists: req_ids with zero rows (Lighthouse gap or my inventory gap — flag which you think and why, logging open questions via `notes/open-questions.md`), and rows with req_id NONE.

## Retained v3.2 completion goal
Every claim cited per the invariant; remap table present; both orphan lists present, each orphan resolved or logged via `notes/open-questions.md`
