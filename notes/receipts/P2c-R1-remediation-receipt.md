# P2c R1 remediation receipt

Date: 2026-07-16
Phase: Phase 2c — Remediation R1

## Trigger

The 2026-07-16 Phase 2c completion audit reported BLOCK on one
criterion: a sentence in the introductory prose of
notes/02-clusters.md asserts that lh-files.tsv records per-row test
evidence but has no column recording that implementation code was
read, uses that assertion as the document-wide confidence-cap basis,
and ends with no citation token — violating citation termination and
the citation-suitability rule (an evidence-depth or confidence-basis
claim must carry at least one [row_id]). The execution-time and
initial audit checks scanned bullet and labeled-element lines only,
so the substantive prose paragraph escaped both.

## R1.1 state guard

- Original receipt end-marker: tail -n 1
  notes/receipts/P2c-validation-receipt.md = "END-OF-RECEIPT P2c",
  exit 0. PASS.
- ORIG_RECEIPT_SHA =
  dd1bbbd63670d5d1149fb539291b50a461d6685e1f0503dfe1806d41ad0ac8c5
  (sha256sum, exit 0). Confirmed unchanged through R1.5e (recomputed
  value identical).
- Matrix-hash cross-check against values recorded in the original
  receipt (sha256sum, exit 0):
  - notes/matrix/lh-files.tsv recomputed
    a078c94a719e29538c5e3eb54622d46f347e67a0faa2d8116d77dfd8f5ab7097
    = recorded value. MATCH.
  - notes/matrix/requirements.tsv recomputed
    c213005c2c28a96c6bb5da850e9995cbf10869da9b8ea82d6acce6d4b6978975
    = recorded value. MATCH.
- PRE_SHA =
  27229dc56e823909015979c56e1c5d3d6a8cbc3b142d834d64dd5de68526e405,
  49924 bytes (sha256sum / wc -c, both exit 0).
- Pristine copy: cp notes/02-clusters.md .work/p2c-r1/02-clusters.pre.md
  exit 0; cmp exit 0 (byte-identical).
- Baseline hashes recorded (sha256sum, exit 0):
  - notes/open-questions.md
    8e896ef0a8175bcf39ab39aaed3a6d8a4ae9f1db3eb2a563f67a8621f7f4b2ad
  - notes/leads/p2b-lead-register.md (hash-only, never read)
    e958ca77af1d1cefec60c170fb140058f5767d242be52eb4987da02212143426
  - notes/refs.md (hash-only, never read)
    b7cbd580e54843e0675e21c3a62ff8cb480019265d2250001058684943191624
  - notes/matrix/lh-files.tsv
    a078c94a719e29538c5e3eb54622d46f347e67a0faa2d8116d77dfd8f5ab7097
  - notes/matrix/requirements.tsv
    c213005c2c28a96c6bb5da850e9995cbf10869da9b8ea82d6acce6d4b6978975
  - Aggregate raw digest, tag pre (staged raw-digest procedure:
    find -print0 / LC_ALL=C sort -z / xargs -0 -r sha256sum /
    sha256sum of the sums file; four commands, each exit 0, empty
    stderr; 221 files):
    5c6fb1e75b34390d2b96fef6b76a3020f5d1c1de09748ab473b31c0e99015370
- Baseline persistence: baselines written to
  .work/p2c-r1/baselines.txt before any later step (write exit 0);
  final line verified as the literal end-marker
  "END-OF-BASELINES P2c-R1" (tail -n 1, exit 0). PASS.

## R1.2 defect confirmation (against the pristine copy)

R1 extended scan: awk segmentation excluding headings, table rows,
blank lines and the structural labels (req_ids:/Claims:/Proposal
implications:); every other bullet or labeled-element line checked
for a terminal [LHnnnn]/[Rnnn]/[OPEN-Qn]/[raw:] token (step-7 form);
remaining prose lines extracted and classified sentence by sentence.
Scan command exit 0, empty stderr. Result: zero bullet/labeled-line
violations; three prose blocks (document lines 3-11, 206-207,
233-234).

Exactly one violating sentence, in the introductory prose (before
the first "## " heading), document lines 5-9:

  "Confidence values are capped at M throughout: lh-files.tsv
  records per-row test evidence but has no column recording that
  implementation code was read, so the confidence-rule fallback cap
  applies (mechanical basis in the Phase 2c validation receipt)."

It (a) asserts lh-files.tsv records per-row test evidence, (b)
asserts it has no column recording that implementation code was
read, (c) presents that as the document-wide confidence-cap basis,
and (d) contains no citation token. Matches the authorized defect
exactly; no other violation found. PASS.

## Exemption list (connective/structural sentences; identical in the
pristine and corrected scans)

1. Intro sentence 1 ("Synthesis of notes/matrix/lh-files.tsv (250
   classified rows) against notes/matrix/requirements.tsv (36
   requirements), grouped by the provisional cluster taxonomy shared
   with Phase 2b.") — connective document-scope statement; its row
   counts are structural annotations whose mechanical basis (input
   data-row counts) is recorded in the original validation receipt.
2. Intro sentence 3 ("Claims depending on spec-snapshot-derived
   requirements carry PROV-SPEC and conclude no stronger than
   needs-input [OPEN-Q1].") — not exempted: treated as a checked
   sentence; it ends with the valid resolving token [OPEN-Q1] and
   asserts no Lighthouse implementation/behavior/test/confidence
   content, so no [row_id] is required. Listed for completeness.
3. Remap-table follow-up sentence (document lines 206-207, "No
   splits or merges were made, so no split row assignment block is
   required; every lh-files.tsv row resolves to its recorded
   cluster_id.") — structural annotation restating the remap table's
   identity-mapping row and the partition check whose mechanical
   basis is recorded in the original validation receipt.
4. Orphan-list-B preamble (document lines 233-234, "One entry per
   row; the disposition restates the row's own classification and
   notes.") — purely connective format note describing the list's
   entry structure.

## R1.3 correction basis (mechanical basis for the corrected sentence)

Verbatim lh-files.tsv header row (head -n 1, exit 0):

row_id	manifest_index	file	symbol_lines	change_type	cluster_id	req_ids	classification	classification_signal	test_evidence	notes

Column-identification result: a test_evidence column exists and
records a raw test-evidence pointer or NONE per row; no column
records that implementation code was read.

Qualifying exemplar row, verbatim (grep '^LH0188<TAB>', exit 0) —
test_evidence non-NONE, and no field records a code read:

LH0188	124	consensus/types/src/execution/eip8025.rs	SSZ containers PublicInput/ExecutionProof/SignedExecutionProof/ProofByRootIdentifier; size/domain constants	added	C1	NONE	LH-arch-choice	defines Encode/Decode/TreeHash proof containers with MaxProofSize = 1344 KiB (1,376,256 bytes) and DOMAIN_EXECUTION_PROOF = [0x0D,0x00,0x00,0x00] [raw:lh-files/124.diff]	[raw:lh-files/124.diff]	PublicInput holds only new_payload_request_root; MaxExecutionProofsPerPayload = U4; MIN_REQUIRED_EXECUTION_PROOFS = 1 [raw:lh-files/124.diff]

## Changed text

Defective sentence (verbatim):

  "Confidence values are capped at M throughout: lh-files.tsv
  records per-row test evidence but has no column recording that
  implementation code was read, so the confidence-rule fallback cap
  applies (mechanical basis in the Phase 2c validation receipt)."

Corrected sentence (verbatim):

  "Confidence values are capped at M throughout: the
  column-identification result for lh-files.tsv is that it records
  per-row test evidence but has no column recording that
  implementation code was read, so the confidence-rule fallback cap
  applies (mechanical basis in the Phase 2c validation receipt)
  [LH0188]."

The corrected sentence asserts the same elements (a)-(c), presents
the observation explicitly as the column-identification result for
lh-files.tsv, and ends with the citation [LH0188] naming the
qualifying exemplar row, per the citation-suitability rule. No other
content was introduced, strengthened, weakened, or removed.

R1.4 unified diff (verbatim):

--- /work/.work/p2c-r1/02-clusters.pre.md	2026-07-16 10:02:29.226610946 +0000
+++ /work/.work/p2c-r1/02-clusters.corrected.md	2026-07-16 10:06:26.796505919 +0000
@@ -3,10 +3,11 @@
 Synthesis of notes/matrix/lh-files.tsv (250 classified rows) against
 notes/matrix/requirements.tsv (36 requirements), grouped by the
 provisional cluster taxonomy shared with Phase 2b. Confidence values
-are capped at M throughout: lh-files.tsv records per-row test
-evidence but has no column recording that implementation code was
-read, so the confidence-rule fallback cap applies (mechanical basis
-in the Phase 2c validation receipt). Claims depending on
+are capped at M throughout: the column-identification result for
+lh-files.tsv is that it records per-row test evidence but has no
+column recording that implementation code was read, so the
+confidence-rule fallback cap applies (mechanical basis in the
+Phase 2c validation receipt) [LH0188]. Claims depending on
 spec-snapshot-derived requirements carry PROV-SPEC and conclude no
 stronger than needs-input [OPEN-Q1].


## R1.4 validation (all against the corrected staged copy; all pass)

- diff -u pristine vs corrected: exit 1 (diff's documented
  files-differ result — the required outcome; exit 2 would be
  failure), empty stderr; exactly one hunk (grep -c '^@@' = 1,
  exit 0), confined to the corrected sentence's lines (document
  lines 6-9 pre / 6-10 post; the shared wrap lines carry only the
  unchanged adjoining sentence fragments). Diff recorded verbatim
  above. PASS.
- R1 extended scan of the corrected copy: zero bullet/labeled-line
  violations (awk scan, exit 0, empty stderr); the corrected
  sentence now ends with the valid token [LH0188] and, as an
  evidence-depth/confidence-basis claim, carries a [row_id]
  (citation suitability). Exemption list unchanged, recorded above.
  PASS.
- Full-document token resolution: 250 distinct [LHnnnn] tokens
  extracted, comm -23 vs the lh-files.tsv row_id column = 0
  unresolved (exit 0); 21 distinct [Rnnn] tokens, comm -23 vs the
  requirements.tsv req_id column = 0 unresolved (exit 0); 6 distinct
  [OPEN-Qn] tokens (Q1-Q6), each resolving to exactly one entry line
  "Qn | " in notes/open-questions.md (grep -c = 1, exit 0, for each
  of Q1..Q6); zero [raw:] pointers in the document (grep -o count 0),
  so no notes/raw existence tests were required; zero
  [[OPEN-Q-PENDING:...]] placeholders (grep exit 1); no reference to
  .work/ or /tmp (grep exit 1). PASS.
- Lead-register hygiene: the unified diff introduces no reference to
  the lead register — grep for 'LQ-' and lead-register labels over
  the diff text: exit 1, no matches. PASS.
- Corrected-sentence citation: its only [row_id] token is [LH0188],
  exactly the verified qualifying exemplar row from R1.3. PASS.

## Untouched-input verification (baseline read from
.work/p2c-r1/baselines.txt, persisted pre-publication; post values
recomputed at R1.5e; sha256sum, exit 0 in every case)

| input | baseline (pre) | post-publication | result |
|---|---|---|---|
| notes/open-questions.md | 8e896ef0a8175bcf39ab39aaed3a6d8a4ae9f1db3eb2a563f67a8621f7f4b2ad | 8e896ef0a8175bcf39ab39aaed3a6d8a4ae9f1db3eb2a563f67a8621f7f4b2ad | EQUAL |
| notes/leads/p2b-lead-register.md | e958ca77af1d1cefec60c170fb140058f5767d242be52eb4987da02212143426 | e958ca77af1d1cefec60c170fb140058f5767d242be52eb4987da02212143426 | EQUAL |
| notes/refs.md | b7cbd580e54843e0675e21c3a62ff8cb480019265d2250001058684943191624 | b7cbd580e54843e0675e21c3a62ff8cb480019265d2250001058684943191624 | EQUAL |
| notes/matrix/lh-files.tsv | a078c94a719e29538c5e3eb54622d46f347e67a0faa2d8116d77dfd8f5ab7097 | a078c94a719e29538c5e3eb54622d46f347e67a0faa2d8116d77dfd8f5ab7097 | EQUAL |
| notes/matrix/requirements.tsv | c213005c2c28a96c6bb5da850e9995cbf10869da9b8ea82d6acce6d4b6978975 | c213005c2c28a96c6bb5da850e9995cbf10869da9b8ea82d6acce6d4b6978975 | EQUAL |
| notes/raw aggregate digest (221 files) | 5c6fb1e75b34390d2b96fef6b76a3020f5d1c1de09748ab473b31c0e99015370 | 5c6fb1e75b34390d2b96fef6b76a3020f5d1c1de09748ab473b31c0e99015370 | EQUAL |
| notes/receipts/P2c-validation-receipt.md | dd1bbbd63670d5d1149fb539291b50a461d6685e1f0503dfe1806d41ad0ac8c5 | dd1bbbd63670d5d1149fb539291b50a461d6685e1f0503dfe1806d41ad0ac8c5 | EQUAL |

The baseline file's final line was re-verified as
"END-OF-BASELINES P2c-R1" (tail -n 1, exit 0) before comparison.
The original validation receipt is preserved unmodified as the
historical record of the original execution.

## Document hashes

- PRE_SHA:
  27229dc56e823909015979c56e1c5d3d6a8cbc3b142d834d64dd5de68526e405
  (49924 bytes)
- POST_SHA:
  558706d181a9ad081367ac6c762dfc96474111296b75a177d0c489e11a2b7c92
  (49981 bytes)

## Publication (R1.5)

- R1.5a: sha256sum notes/02-clusters.md = PRE_SHA, exit 0. PASS.
- R1.5b: cp corrected staged copy -> notes/02-clusters.md, exit 0.
- R1.5c: cmp staged copy vs notes/02-clusters.md, exit 0
  (byte-identical).
- R1.5d: POST_SHA and byte count recorded (sha256sum exit 0,
  wc -c exit 0).
- R1.5e: every baseline recomputed (including the aggregate raw
  digest via the staged raw-digest procedure, tag post: four
  commands, each exit 0, empty stderr) and each value equal to the
  corresponding entry in .work/p2c-r1/baselines.txt; the original
  receipt's SHA-256 recomputed and equal to ORIG_RECEIPT_SHA. PASS.
- R1.5f: this receipt staged at .work/p2c-r1/receipt.tmp; write and
  end-marker verification checked in-session. Steps R1.5g-h run
  after this receipt's content is fixed: their success is checked
  in-session and verified by the audit from final state (the
  end-marker as the published receipt's last line; the staged
  corrected copy removed).

END-OF-RECEIPT P2c-R1
