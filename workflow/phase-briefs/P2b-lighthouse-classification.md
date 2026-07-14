# Phase 2b design brief — Lighthouse classification

**Status:** Authoritative drafting source until
`phases/P2b-lighthouse-classification.md` exists as an executable phase
specification under the rules in `CLAUDE.md`.

1. Classify (2b, batched): change type + classification per schema, each with a cited signal (TODO, hardcoded devnet value, specific reviewer comment); frame all findings as "one reference implementation."

Applicable workflow inputs:
- `workflow/PROVISIONAL-CLUSTER-TAXONOMY.md`

Cluster taxonomy and remap rules are defined in
`workflow/PROVISIONAL-CLUSTER-TAXONOMY.md`.

**`notes/matrix/lh-files.tsv`**: `row_id | manifest index | file | symbol/lines | change type | provisional cluster_id | req_id(s) or NONE | classification (spec-driven / LH-arch-choice / scaffolding / CI-devnet / dependency / reverted-partial / reviewer-contested / unclear) | classification signal | test evidence | notes`. `req_id = NONE` rows feed the 2c orphan check, not errors.

## Retained v3.2 drafting source
**P2b — Batched classification (Claude Code):**
> Read `notes/matrix/lh-files.tsv` for the resume point (last manifest index processed). Process the next `<N>` manifest rows from `notes/raw/lh-files/`. Append rows per schema using the provisional taxonomy C1-C10/C-unassigned; cite a concrete signal for every classification; `reviewer-contested` only with a specific comment from `notes/raw/lh-threads.md`; use `unclear` freely; `req_id = NONE` where nothing matches. End by stating the resume point. Do not revisit completed rows.

## Retained v3.2 completion goal
Every manifest row has a table row (reconcile against the manifest, not --stat); every row classified with a signal; `unclear` ≤ ~15% or a follow-up batch scheduled

## Mistakes to avoid
- The WIP-scaffolding warning in
  `workflow/PROVISIONAL-CLUSTER-TAXONOMY.md`.
- Reconciling 2b against `--stat` instead of the name-status manifest (renames will slip through).
