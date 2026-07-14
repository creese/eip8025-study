# Phase 3b design brief — Grandine surface mapping

**Status:** Authoritative drafting source until
`phases/P3b-grandine-surface-mapping.md` exists as an executable phase
specification under the rules in `CLAUDE.md`.

1. Mark a cluster CITED-ABSENT only when the corresponding
   `notes/raw/gr-search-<cluster>.txt` exists and is cited as evidence.

**`notes/matrix/gr-surfaces.tsv`**:
`cluster_id | req_ids | grandine path/symbol OR CITED-ABSENT |
evidence pointer (notes/raw/gr-* file) | confidence | notes`.

## Retained v3.2 drafting source
Using the saved Phase 3a evidence, fill `notes/matrix/gr-surfaces.tsv`.
Record a positive Grandine path/symbol or mark CITED-ABSENT only when
the corresponding saved negative-search transcript exists and is
cited.

## Retained v3.2 completion goal
Every cluster has a positive Grandine surface row or a CITED-ABSENT
row citing its saved negative-search transcript.
