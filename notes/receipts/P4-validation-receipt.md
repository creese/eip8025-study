# Phase 4 — validation receipt

- Date (UTC): 2026-07-17
- Primary output: notes/matrix/gap.tsv
- Published matrix SHA-256: c250e8664a6e5aeb786f9f9dcc5c554284edb8a41fdffdbc4804b206f9947be7
- Session type: synthesis / schema-filling (no live research; no ref-verification gate; no notes/raw read)

## Step 1 — input hash verification (sha256sum, checked)
- notes/02-clusters.md   recorded=558706d181a9ad081367ac6c762dfc96474111296b75a177d0c489e11a2b7c92  recomputed=558706d181a9ad081367ac6c762dfc96474111296b75a177d0c489e11a2b7c92  RESULT=MATCH
- notes/matrix/gr-surfaces.tsv  recorded=ae40dac29ecba16782d0f4d36efdfcbdb6ba3b14e93a002b3ca560719cccd4fb  recomputed=ae40dac29ecba16782d0f4d36efdfcbdb6ba3b14e93a002b3ca560719cccd4fb  RESULT=MATCH
- Recorded hashes were read from notes/receipts/P3b-validation-receipt.md.

## Open-questions integrity record (notes/open-questions.md)
- pre-append  SHA-256=8e896ef0a8175bcf39ab39aaed3a6d8a4ae9f1db3eb2a563f67a8621f7f4b2ad
- pre-append  byte-length=2275
- appended entries (this phase): NONE — no new open questions were required; all references resolved to the existing [OPEN-Q1]; no NEWQ placeholders were used.
- reused IDs: NONE
- post-append byte-length (L)=2275
- post-append SHA-256 (S)=8e896ef0a8175bcf39ab39aaed3a6d8a4ae9f1db3eb2a563f67a8621f7f4b2ad
- Because no entry was appended, the post-append snapshot equals the pre-append snapshot and the SHA-256 of the first L=2275 bytes of the current file equals S.

## Step 4 — validation checks on the final published matrix
Validation harness (.work/p4/validate.py) run as a single checked command; exit code 0; captured output .work/p4/checks/validate-final.out.

- CHECK 1: PASS - header equals schema
- CHECK 2: PASS - every row has exactly 9 tab-separated fields (bad rows: [])
- CHECK 3: PASS - cluster_id multiset == Step 2 universe (['C1','C2','C3','C4','C5','C6','C7','C8','C9','C10','C-unassigned']; no dup/extra/missing)
- CHECK 4: PASS - every req_ids and lh_row_ids nonempty and matches the Step 2 mapping (NONE accepted as valid nonempty mapping)
- CHECK 5: PASS - every gr_surfaces_ref == gr-surfaces:<own cluster_id> and that cluster_id exists in gr-surfaces.tsv
- CHECK 6: PASS - every mismatch_description nonempty and carries >= 1 required citation (own lh_row_id / gr_surfaces_ref / [raw:...] / [OPEN-Qn])
- CHECK 7: PASS - [raw:...] pointers in matrix: 0 (none used); nothing to resolve
- CHECK 8: PASS - every port_difficulty in {low,medium,high}; every confidence in {low,medium,high}; confidence <= min(lh_confidence, gr_surfaces_confidence) for every row
- CHECK 9: PASS - every prov_spec empty or PROV-SPEC; PROV-SPEC set exactly for clusters provisional-tagged in 02-clusters.md or carrying PROV-SPEC in the referenced gr-surfaces notes field
- CHECK 10: PASS - every confidence=low row has nonempty open_q_ids
- CHECK 11: PASS - every open_q_ids entry is an existing [OPEN-Qn] in notes/open-questions.md (no placeholders remaining)
- CHECK 12: PASS - no NEWQ placeholder in any mismatch_description; every [OPEN-Qn] cited in a mismatch_description exists in notes/open-questions.md

All 12 checks PASS with no placeholders remaining.

## Row and category counts
- Row count (excl. header): 11
- By port_difficulty: high=9, medium=2, low=0
- By confidence: medium=9, low=2, high=0
- Rows with PROV-SPEC: 6 — C1, C2, C3, C8, C9, C10

## Fuzzy-cluster record
No cluster was designated fuzzy by the user, in the starting task prompt or interactively during the session. No fuzzy-cluster questions were asked and no answers were received. (Section intentionally empty; decision context only, not evidence.)

## Confidence-ceiling derivation (per row)
Lighthouse-side confidence is medium for every cluster (02-clusters.md records the M cap). Ceiling = min(lh_confidence, gr_surfaces_confidence):
- C1: gr=low  -> ceiling low  -> confidence low
- C2: gr=high -> ceiling medium -> confidence medium
- C3: gr=high -> ceiling medium -> confidence medium
- C4: gr=high -> ceiling medium -> confidence medium
- C5: gr=medium -> ceiling medium -> confidence medium
- C6: gr=high -> ceiling medium -> confidence medium
- C7: gr=high -> ceiling medium -> confidence medium
- C8: gr=high -> ceiling medium -> confidence medium
- C9: gr=high -> ceiling medium -> confidence medium
- C10: gr=low -> ceiling low  -> confidence low
- C-unassigned: gr=medium -> ceiling medium -> confidence medium
