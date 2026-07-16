# Phase 2c design brief — cluster synthesis

**Status:** Authoritative drafting source until
`phases/P2c-cluster-synthesis.md` exists as an executable phase
specification under the rules in `CLAUDE.md`.

Applicable workflow inputs:
- `workflow/PROVISIONAL-CLUSTER-TAXONOMY.md`

The cluster taxonomy, remap rule, and WIP-scaffolding warning are
defined in `workflow/PROVISIONAL-CLUSTER-TAXONOMY.md`.

**`notes/02-clusters.md`** — per cluster: req_ids; claim bullets, each *claim* ending `[row_id]`, `[raw:<path>]`, or `[OPEN-Qn]` (headings/structure exempt); test coverage or gap; confidence H/M/L (H = code+tests read; M = code read; L = inferred); proposal implications as bullets, no prose paragraphs. Ends with remap table and two orphan lists: req_ids with no rows; rows with req_id NONE.

## Lead-register discovery input (workflow amendment, 2026-07-16)

Phase 2bq produces `notes/leads/p2b-lead-register.md`, a durable
non-evidence register of candidate leads distilled from the Phase 2b
session reports. For Phase 2c it is a discovery and investigation
index only:

- The register may be consulted solely to direct attention within
  Phase 2c's declared evidence (the two matrices). It is not
  evidence: neither the register, its lead IDs, nor the underlying
  session reports may ever be cited or otherwise support any claim.
- A lead affects `notes/02-clusters.md` only after it is
  independently established from Phase 2c's declared inputs and cited
  under the phase's normal citation rules; the resulting claim must
  stand exactly as it would had the lead never existed.
- Phase 2c's open-question authority is exercised exactly as its
  specification already defines it: a question it logs must be
  motivated and cited from Phase 2c's declared evidence alone, and
  its entry never references the register. That the register contains
  a similar lead neither permits nor prevents such a question; it is
  an independently derived Phase 2c question, not a promotion.
- Promotion proper — carrying a register lead into
  `notes/open-questions.md` on the authority of the register or the
  Phase 2b session reports, without that independent evidence
  grounding — is prohibited to Phase 2c and reserved to the user.
- A lead that Phase 2c's declared evidence cannot verify enters no
  claim and no question; it remains in the register, and session
  output lists it as consulted but unverifiable, for the user.

This adds a constrained discovery input only: Phase 2c's substantive
scope, evidence standard, orphan analysis, cluster taxonomy, and
open-question authority are unchanged.

Note: the executable specification `phases/P2c-cluster-synthesis.md`
predates this amendment and does not declare the register as an
input. This section is drafting source for a future user-authorized
revision of that specification; until such a revision is reviewed and
authorized under `CLAUDE.md`, Phase 2c executes without reading the
register.

## Retained v3.2 drafting source
**P2c — Cluster synthesis (Chat):**
> Loaded: `notes/matrix/lh-files.tsv`, `notes/matrix/requirements.tsv`. Produce `notes/02-clusters.md` per schema — every claim cites `[row_id]`, `[raw:<path>]`, or `[OPEN-Qn]`. You may split/merge provisional clusters — append the remap table. End with both orphan lists: req_ids with zero rows (Lighthouse gap or my inventory gap — flag which you think and why, logging open questions via `notes/open-questions.md`), and rows with req_id NONE.

## Retained v3.2 completion goal
Every claim cited per the invariant; remap table present; both orphan lists present, each orphan resolved or logged via `notes/open-questions.md`
