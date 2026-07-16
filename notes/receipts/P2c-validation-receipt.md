# P2c validation receipt

Date: 2026-07-16
Phase: P2c — cluster synthesis

## Declared inputs as read

| input | sha256 | data rows |
|---|---|---|
| notes/matrix/lh-files.tsv | a078c94a719e29538c5e3eb54622d46f347e67a0faa2d8116d77dfd8f5ab7097 | 250 (251 lines incl. header) |
| notes/matrix/requirements.tsv | c213005c2c28a96c6bb5da850e9995cbf10869da9b8ea82d6acce6d4b6978975 | 36 (37 lines incl. header) |
| notes/leads/p2b-lead-register.md | e958ca77af1d1cefec60c170fb140058f5767d242be52eb4987da02212143426 | 35 leads (LQ-001..LQ-035); discovery input only |
| notes/open-questions.md (pre-append) | a60c06bc1bf9211cbc86b6b9c6053a9c5c512b81e8417548e43af421fc7df944 | 2 lines (Q1, Q2) |
| notes/refs.md | b7cbd580e54843e0675e21c3a62ff8cb480019265d2250001058684943191624 | context only (PROV-SPEC decision, [OPEN-Q1]) |

## Column identification

requirements.tsv header: req_id (identifier, R-prefixed zero-padded)
and source (provenance: EIP-PR / spec-snapshot / both) identified
unambiguously.

lh-files.tsv header: row_id (identifier, LH-prefixed), req_ids
(requirement mapping: NONE or comma-separated R-tokens), cluster_id
(taxonomy IDs) identified unambiguously. Evidence depth: test_evidence
records a raw test-evidence pointer or NONE per row, but no column
records that implementation code was read, so the confidence-rule
fallback applied: confidence capped at M for rows demonstrating
code-level description, L otherwise; every per-cluster basis clause
states the cap. WIP markers: classification value "scaffolding"
(LH0052, LH0209, LH0232) plus TODO markers recorded in row notes.

## Step 1 — mechanical input validation (all pass, empty stderr)

- Field counts: awk NF check vs header; lh-files.tsv exit 0,
  requirements.tsv exit 0 (11 and 9 fields respectively).
- Cluster IDs: awk check col 6 in {C1..C10, C-unassigned}; exit 0.
- req_ids tokens: awk split on comma, format ^R[0-9]{3}$ or NONE,
  membership in requirements.tsv req_id set; exit 0.

## Step 2 — mechanical derivations (commands under .work/p2c/derived)

Commands: awk projections of cluster_id/row_id (cluster-rows.tsv,
cluster-counts.tsv); awk referenced-req set vs requirements list
(orphan-A.txt); awk req_ids==NONE filter (orphan-B.txt). All exit 0,
empty stderr.

Per-cluster row counts (= post-remap counts; identity mapping):
C1 4, C2 32, C3 21, C4 7, C5 5, C6 30, C7 12, C8 16, C9 26, C10 87,
C-unassigned 10. Sum = 250 = lh-files.tsv data-row count. Partition
check pass: cluster_id is a single-valued column, so each row maps to
exactly one final cluster; every section heading matches a taxonomy
ID; no split/merge, no split row assignment required.

Orphan list A (19): R002 R003 R004 R007 R008 R010 R014 R017 R021
R022 R023 R024 R025 R026 R029 R030 R031 R033 R034.
Requirements referenced by at least one row (17): R001 R005 R006
R009 R011 R012 R013 R015 R016 R018 R019 R020 R027 R028 R032 R035
R036.
Orphan list B: 235 rows (req_ids = NONE); full list in the published
document; membership diff vs derivation: identical.

Rows with non-NONE mappings (15): LH0024, LH0026, LH0027, LH0029,
LH0034, LH0041, LH0045, LH0122, LH0129, LH0132, LH0133, LH0138,
LH0161, LH0248, LH0250.

## Step 7 — validation checks (all against the staged draft; all pass)

- Structure: 11 cluster sections (C1..C10, C-unassigned); awk element
  scan shows req_ids/Claims/Test coverage/Confidence/Proposal
  implications present in order in every section; remap table present
  with the identity-mapping row and date; both orphan lists present.
- Citation termination: grep over every substantive bullet/statement
  line (excluding structural req_ids:/Claims:/Proposal implications:
  headers) for a terminal [LHnnnn]/[Rnnn]/[OPEN-Qn]/[raw:] token:
  zero violations.
- Citation existence: 250 distinct [row_id] cited, all present in
  lh-files.tsv; 21 distinct [Rnnn] cited, all present in
  requirements.tsv; [OPEN-Q1..Q6] all present in
  notes/open-questions.md; zero [raw:] pointers used; zero
  [[OPEN-Q-PENDING:...]] placeholders remain.
- Citation suitability: grep for substantive lines lacking any
  [LHnnnn] token returned none — every implementation/behavior/test/
  confidence line carries at least one [row_id].
- Orphan completeness: List A doc membership diff vs orphan-A.txt:
  identical (19); List B doc membership diff vs orphan-B.txt:
  identical (235); every entry resolved with citations or ending in
  an [OPEN-Qn].
- Cluster coverage and partition: identity remap; counts above sum to
  250; only taxonomy IDs used as sections.
- PROV-SPEC: grep for R010-R036 references without PROV-SPEC found
  only structural req_ids: lines after one fix (the C3 R004/R011
  claim was tagged PROV-SPEC [OPEN-Q1] and rechecked); no
  PROV-SPEC-dependent claim concludes stronger than needs-input.
- Confidence: 11 Confidence lines, all M with a cap-stating basis
  clause (grep count check).
- Hygiene: grep for .work/, /tmp, LQ-, lead-register references in
  the document: zero matches.
- Lead-register hygiene: no lead identifier or register reference in
  the document or in the appended Q3-Q6 entries; no identifier
  collisions exist (LQ- prefix matches no row_id/req_id); register
  SHA-256 re-checked at validation time:
  e958ca77af1d1cefec60c170fb140058f5767d242be52eb4987da02212143426
  (unmodified).

## Lead-register handling

Register: notes/leads/p2b-lead-register.md, SHA-256
e958ca77af1d1cefec60c170fb140058f5767d242be52eb4987da02212143426,
35 leads, read once in full after step 2. Consulted as a discovery
index only; no claim or question rests on it. Identifier collisions:
none. Register unmodified (hash re-verified at validation time).
Per-lead dispositions (process record, never evidence):

LQ-001: UNVERIFIABLE — PR-thread/review-comment state is not recorded in either declared matrix; no claim, no question entered.
LQ-002: ESTABLISHED — upstream base-branch drift coverage in cluster sections C2/C6/C9/C10 claim bullets and List B dispositions, established from row notes/signals.
LQ-003: ESTABLISHED — List A entry R002 (status-only retention gap) via LH0023/LH0150; also C3 claim bullet and C3/C9 implications.
LQ-004: ESTABLISHED — C3 serve-range lower-bound divergence claim via LH0027/LH0132/LH0133, PROV-SPEC.
LQ-005: ESTABLISHED — C5 claim bullet: fire-and-forget dispatch on both notify paths mapped to R001/R009 per LH0034/LH0045.
LQ-006: ESTABLISHED — List B entry LH0052 (WIP scaffolding disposition per its classification); C10 scaffolding claim bullet.
LQ-007: ESTABLISHED — List B entries LH0046/LH0049 (neutral wiring dispositions mirroring row notes).
LQ-008: ESTABLISHED — C1 claim bullet on request_root()/SSZ derives, no mandated requirement, via LH0072.
LQ-009: ESTABLISHED — C5 claim + implication: test-mock branch in production from_config via LH0073; echoed in C10 (LH0078/LH0073).
LQ-010: ESTABLISHED — C2 claim bullet: HTTP submission endpoint delegates validation, via LH0084/LH0086.
LQ-011: ESTABLISHED — List B entry LH0085 (non-EIP-8025 per row notes).
LQ-012: ESTABLISHED — C2 claim bullet notes the decision/delegation split (LH0122 with LH0024).
LQ-013: ESTABLISHED — C3 claim bullet: status reply built at LH0129, returned by router LH0138.
LQ-014: ESTABLISHED — C2 claim bullet: ENR eproof advertisement as implementation mechanism, via LH0095.
LQ-015: ESTABLISHED — List B entry LH0097 (test-only helper; no drift assertion).
LQ-016: ESTABLISHED — List B entries LH0094/LH0111/LH0113 and C7 claim (enable-mplex unrelated upstream).
LQ-017: ESTABLISHED — C3 claim bullets separating R035 status reply (LH0129/LH0138) from routing wiring (LH0139).
LQ-018: ESTABLISHED — C9 claims: ProofSync as implementation catch-up mechanism (LH0150/LH0160) and C9 implication tying backfill to R002 retention.
LQ-019: ESTABLISHED — List B entry LH0141 (drift disposition per row notes).
LQ-020: ESTABLISHED — C9 claim bullet R005/R032/R036 with PROV-SPEC, via LH0161.
LQ-021: ESTABLISHED — C9 claim bullet: requester-side window filter mirrors snapshot form, via LH0162 (kept implementation-side).
LQ-022: ESTABLISHED — C7/C1 domain-collision claims via LH0184/LH0186/LH0188 and new open question with subject LH0184 (independently grounded in the matrix rows).
LQ-023: ESTABLISHED — C1 (LH0188) and C4 (LH0189) sections cover both halves of the split.
LQ-024: ESTABLISHED — List B entry LH0191 (test-infra gating disposition).
LQ-025: ESTABLISHED — C10 claim bullet: narrowed ef-test exclusions with TODO, via LH0199/LH0203.
LQ-026: ESTABLISHED — C9 claim bullet: ignored proof-sync test as WIP scaffolding, via LH0209.
LQ-027: ESTABLISHED — List B entries LH0201/LH0202 (unrelated test changes per row notes).
LQ-028: ESTABLISHED — C1 claim/implication and List A entry R017 (MaxProofSize divergence) via LH0188/LH0104, plus new open question with subject R017.
LQ-029: ESTABLISHED — List B entries LH0216 (CI-devnet) / LH0217 (test fixture) per their classifications.
LQ-030: ESTABLISHED — List B entries LH0220-LH0223 (CI-devnet fork schedule vs plumbing dispositions).
LQ-031: ESTABLISHED — List B entry LH0232 (WIP scaffolding disposition); C10 scaffolding claim bullet.
LQ-032: ESTABLISHED — C8 claim bullet: signing primitive/impl cited without stretching R001 to a mandate (LH0237/LH0250 per matrix mapping).
LQ-033: ESTABLISHED — List B entry LH0239 (implementation wiring; not scaffolding).
LQ-034: ESTABLISHED — C8 section: R001 only on LH0248/LH0250 claims; plumbing rows carried as NONE in List B.
LQ-035: ESTABLISHED — C8 section covers the whole proof service; taxonomy kept as identity mapping (remap table).

## Open-questions handling

- Pre-append: 2 lines, SHA-256
  a60c06bc1bf9211cbc86b6b9c6053a9c5c512b81e8417548e43af421fc7df944.
- Tail integrity: file ends with a newline; no Phase 2c provenance
  marker existed pre-append (grep exit 1), so no truncated prior
  entry; duplicate guard found no reusable Phase 2c entries.
- Appends (one printf per entry, each exit 0, empty stderr), IDs
  assigned monotonically above the maximum present (Q2):
  - subject:R010  -> Q3 (appended)
  - subject:R014  -> Q4 (appended)
  - subject:R017  -> Q5 (appended)
  - subject:LH0184 -> Q6 (appended)
  Reused: none. Each entry is a single line naming Phase 2c,
  2026-07-16 and its subject identifier (the line is the entry's
  provenance marker).
- Post-append prefix verification: head -n 2 | sha256sum =
  a60c06bc1bf9211cbc86b6b9c6053a9c5c512b81e8417548e43af421fc7df944
  (matches recorded value; append-only proven). Post-append file:
  6 lines, SHA-256
  8e896ef0a8175bcf39ab39aaed3a6d8a4ae9f1db3eb2a563f67a8621f7f4b2ad.
- Placeholder resolution: sed replaced all four
  [[OPEN-Q-PENDING:...]] subjects with [OPEN-Q3..Q6]; residual
  placeholder count 0.

## Publication (8a-8d)

- 8a: notes/02-clusters.md confirmed absent before copy (no-clobber
  pass).
- 8b: cp staged draft -> notes/02-clusters.md, exit 0.
- 8c: cmp staged draft vs notes/02-clusters.md, exit 0
  (byte-identical).
- 8d: this receipt staged at .work/p2c/receipt.tmp; write exit codes
  0 with empty stderr; end-marker verified as the final line before
  8e. Steps 8e-8f run after this receipt's content is fixed; their
  exit codes are checked in-session and verified by the audit from
  final state (end-marker present in the published receipt;
  staged draft removed).

END-OF-RECEIPT P2c
