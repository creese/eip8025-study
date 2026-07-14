# Phase 3a design brief — Grandine harvest

**Status:** Authoritative drafting source until
`phases/P3a-grandine-harvest.md` exists as an executable phase
specification under the rules in `CLAUDE.md`.

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

## Retained v3.2 drafting source
For each cluster in `notes/02-clusters.md`, search for the Grandine
equivalent. Save the exact command and output to
`notes/raw/gr-search-<cluster>.txt`. Record the path and symbol for
positive findings. Never assert absence without a saved empty
transcript.

## Retained v3.2 completion goal
Grandine SHA verified at session open; every cluster has recorded
positive search evidence or a saved negative-search transcript.
