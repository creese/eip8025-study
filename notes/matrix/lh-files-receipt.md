# Phase 2b — Lighthouse classification completion receipt

date: 2026-07-15
validation: OK

## Inputs verified
- lh-manifest.tsv data rows: 172
- lh-name-status.txt lines: 172

## Reconciliation
Reconciliation was performed against notes/raw/lh-manifest.tsv (with the
notes/raw/lh-name-status.txt rename-safe cross-check), NOT against
notes/raw/lh-diffstat.txt. Result: every manifest index 001-172 has at
least one FINAL_TSV row; no FINAL_TSV row bears an index outside
001-172. PASS.

## Totals
- total data rows: 250

### By classification
- LH-arch-choice: 209
- spec-driven: 15
- CI-devnet: 12
- dependency: 11
- scaffolding: 3
- unclear: 0
- reverted-partial: 0
- reviewer-contested: 0

### By cluster_id
- C1: 4
- C2: 32
- C3: 21
- C4: 7
- C5: 5
- C6: 30
- C7: 12
- C8: 16
- C9: 26
- C10: 87
- C-unassigned: 10

### By change_type
- added: 60
- modified: 188
- deleted: 2
- renamed: 0

## Other counts
- req_ids = NONE rows: 235
- PROV-SPEC rows: 10

## Unclear rate
- unclear rows / total = 0 / 250 = 0.00%
- disposition: <= 15%
