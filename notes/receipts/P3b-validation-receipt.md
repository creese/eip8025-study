# Phase 3b — validation receipt

- Date (UTC): 2026-07-17T17:13:59Z
- Primary output: notes/matrix/gr-surfaces.tsv
- Published matrix SHA-256: ae40dac29ecba16782d0f4d36efdfcbdb6ba3b14e93a002b3ca560719cccd4fb
- Row count (excl. header): 11  (positive: 4, CITED-ABSENT: 7)

## Control input hashes
- notes/02-clusters.md SHA-256: 558706d181a9ad081367ac6c762dfc96474111296b75a177d0c489e11a2b7c92
- notes/raw/gr-harvest-receipt.txt SHA-256: 5afa1a2cc844d42af136a02c340d9cd57b49514060606e6b5886bffa260ebba3
- notes/raw/gr-sub-remediation-receipt.txt SHA-256: a08379813cd7d805a89fd5f032cb0dbfb32e98c9db3d4b0b5e58f31058f2e7cc

## Step 1 — verified Phase 3a evidence inventory (union of both receipts)
Per-artifact recomputed SHA-256 vs receipt-recorded value (all MATCH):

# gr-fork-case-study.txt  expected=222af96f6c4d04bb93c9c3728ed0d6b5f5ebbc7d8e1ee7d439014fe33aaaa21f  got=222af96f6c4d04bb93c9c3728ed0d6b5f5ebbc7d8e1ee7d439014fe33aaaa21f  MATCH  [gr-sub-remediation-receipt.txt]
# gr-layout.txt  expected=cd769414181413ba8ca921eb1239ea97c03a0301831b948923c12d64b18467d5  got=cd769414181413ba8ca921eb1239ea97c03a0301831b948923c12d64b18467d5  MATCH  [gr-sub-remediation-receipt.txt]
# gr-pin-verification.txt  expected=ce222ad58c481fc5d19f435fc41abc997c01d8a3d88b3f0b9fd4ff5ad4e13579  got=ce222ad58c481fc5d19f435fc41abc997c01d8a3d88b3f0b9fd4ff5ad4e13579  MATCH  [gr-sub-remediation-receipt.txt]
# gr-readme-docs.txt  expected=164f9f2a2dae316ce31af5d8c18ceade93ec3b30f35eaca2a65adc778366e128  got=164f9f2a2dae316ce31af5d8c18ceade93ec3b30f35eaca2a65adc778366e128  MATCH  [gr-sub-remediation-receipt.txt]
# gr-search-c-unassigned.txt  expected=374c6e2f9aba8a33d2867898c55833d78d4c9d98485bab94be8e8b383080288d  got=374c6e2f9aba8a33d2867898c55833d78d4c9d98485bab94be8e8b383080288d  MATCH  [gr-sub-remediation-receipt.txt]
# gr-search-c1.txt  expected=a24e53460cb23390b48f37c5017b6bbcbed19167078b2b1af09296849e8128f1  got=a24e53460cb23390b48f37c5017b6bbcbed19167078b2b1af09296849e8128f1  MATCH  [gr-sub-remediation-receipt.txt]
# gr-search-c10.txt  expected=3267777793b989b8946bbb53993358b2264e38fcf863cbec59f449d8fd3d1272  got=3267777793b989b8946bbb53993358b2264e38fcf863cbec59f449d8fd3d1272  MATCH  [gr-sub-remediation-receipt.txt]
# gr-search-c2.txt  expected=6cd9b14554bbac895b85eaaa85b7a17c77a0ebaaf435f8b61cdcf2bdba2cc787  got=6cd9b14554bbac895b85eaaa85b7a17c77a0ebaaf435f8b61cdcf2bdba2cc787  MATCH  [gr-sub-remediation-receipt.txt]
# gr-search-c3.txt  expected=7748d304b864d4809a2da1d9ac79f6c878837be58a3dda8eba62c76920f9ae14  got=7748d304b864d4809a2da1d9ac79f6c878837be58a3dda8eba62c76920f9ae14  MATCH  [gr-sub-remediation-receipt.txt]
# gr-search-c4.txt  expected=3115ac64b6b94c1ff15e77ba5909f95326d7ed344b11378414fbc5a5d8f3542d  got=3115ac64b6b94c1ff15e77ba5909f95326d7ed344b11378414fbc5a5d8f3542d  MATCH  [gr-sub-remediation-receipt.txt]
# gr-search-c5.txt  expected=22a60e69f114e479e478c2ed785363c43118271872e44128901b5df99b663cac  got=22a60e69f114e479e478c2ed785363c43118271872e44128901b5df99b663cac  MATCH  [gr-sub-remediation-receipt.txt]
# gr-search-c6.txt  expected=4653c36ee794fb6885949b8b82319fb6bfd2fdf3198ae2af8094d34bf8dcb30e  got=4653c36ee794fb6885949b8b82319fb6bfd2fdf3198ae2af8094d34bf8dcb30e  MATCH  [gr-sub-remediation-receipt.txt]
# gr-search-c7.txt  expected=0d06e4fd734b0861f4b9973666b006184ad3095f8afaa49072a1f0980254089a  got=0d06e4fd734b0861f4b9973666b006184ad3095f8afaa49072a1f0980254089a  MATCH  [gr-sub-remediation-receipt.txt]
# gr-search-c8.txt  expected=5a56b3583b0ca6679b39e8caafa8d39d3454682372768da74094caa2a1b4945d  got=5a56b3583b0ca6679b39e8caafa8d39d3454682372768da74094caa2a1b4945d  MATCH  [gr-sub-remediation-receipt.txt]
# gr-search-c9.txt  expected=3f3a8e170446221c5417ae5be1e0684a57a046eb4c2d501f479659a4990e7cdb  got=3f3a8e170446221c5417ae5be1e0684a57a046eb4c2d501f479659a4990e7cdb  MATCH  [gr-sub-remediation-receipt.txt]
# gr-sub-fork-case-study.txt  expected=1da3507ecd1afbbbbd20c1d5ebd166983fb9f21664591bac6c61f1a3144a2408  got=1da3507ecd1afbbbbd20c1d5ebd166983fb9f21664591bac6c61f1a3144a2408  MATCH  [gr-sub-remediation-receipt.txt]
# gr-sub-pin-verification.txt  expected=3c46440c4a3841a62fae06c80babeb32e305dd6d6effe09a3ebcf53c11d72f97  got=3c46440c4a3841a62fae06c80babeb32e305dd6d6effe09a3ebcf53c11d72f97  MATCH  [gr-sub-remediation-receipt.txt]
# gr-sub-search-c-unassigned.txt  expected=89aad8148c05b2fb7efe8c8f7c83f9e062672d742b15b281104a8043b7179ae3  got=89aad8148c05b2fb7efe8c8f7c83f9e062672d742b15b281104a8043b7179ae3  MATCH  [gr-sub-remediation-receipt.txt]
# gr-sub-search-c1.txt  expected=a9299b7bb252cd3ec1daee139e02ad76b2e7b07ef0f08b99e42bfaed68ac9089  got=a9299b7bb252cd3ec1daee139e02ad76b2e7b07ef0f08b99e42bfaed68ac9089  MATCH  [gr-sub-remediation-receipt.txt]
# gr-sub-search-c10.txt  expected=f3d817f9dd98bb13b52ba291923b1e169e9dcb2f4e2f038ec1f8ac4c9b22a5c5  got=f3d817f9dd98bb13b52ba291923b1e169e9dcb2f4e2f038ec1f8ac4c9b22a5c5  MATCH  [gr-sub-remediation-receipt.txt]
# gr-sub-search-c2.txt  expected=027c699dc703e62600fb7727bd65eac95797139175f784dd0a6ff4dad3e2df26  got=027c699dc703e62600fb7727bd65eac95797139175f784dd0a6ff4dad3e2df26  MATCH  [gr-sub-remediation-receipt.txt]
# gr-sub-search-c3.txt  expected=6dc432b6943bfca28c4331e3f6fca3c48997b3b2c89a444fa6939dff6c8b67c2  got=6dc432b6943bfca28c4331e3f6fca3c48997b3b2c89a444fa6939dff6c8b67c2  MATCH  [gr-sub-remediation-receipt.txt]
# gr-sub-search-c4.txt  expected=a8d100692a4739bbdf91030def96773ae508eb851455e3ca9977f99e6d02beeb  got=a8d100692a4739bbdf91030def96773ae508eb851455e3ca9977f99e6d02beeb  MATCH  [gr-sub-remediation-receipt.txt]
# gr-sub-search-c5.txt  expected=9faaf62803116b46c1daafc8b314935c41f0662fcc202d7b2961785b46f1ba11  got=9faaf62803116b46c1daafc8b314935c41f0662fcc202d7b2961785b46f1ba11  MATCH  [gr-sub-remediation-receipt.txt]
# gr-sub-search-c6.txt  expected=67cfabed267b815e9b59f0c7a1de63a0069d896151cbafc949067cf1d9f48a62  got=67cfabed267b815e9b59f0c7a1de63a0069d896151cbafc949067cf1d9f48a62  MATCH  [gr-sub-remediation-receipt.txt]
# gr-sub-search-c7.txt  expected=8923b7b90fa53f03c597229042d833abe0cd16fb7b460bafb02fdfbac2915d13  got=8923b7b90fa53f03c597229042d833abe0cd16fb7b460bafb02fdfbac2915d13  MATCH  [gr-sub-remediation-receipt.txt]
# gr-sub-search-c8.txt  expected=c551dadcb52b4500fd8e094f1168d953c299a19134b3af560130c2030ee54569  got=c551dadcb52b4500fd8e094f1168d953c299a19134b3af560130c2030ee54569  MATCH  [gr-sub-remediation-receipt.txt]
# gr-sub-search-c9.txt  expected=055c13771d776bfecad9d68fff9619272cf4bec13938271f49cac1a552f68759  got=055c13771d776bfecad9d68fff9619272cf4bec13938271f49cac1a552f68759  MATCH  [gr-sub-remediation-receipt.txt]

Total listed artifacts verified: 28 (all MATCH; 0 failures).
Files matching notes/raw/gr-* not listed in either receipt are outside the
evidence set and were neither read nor cited.

## Step 4 — validation checks (each run as a checked command; see .work/p3b/checks/validate.out)
Validation harness exit code: 0; captured stderr (.work/p3b/checks/validate.stderr): empty.

- CHECK 1: PASS - header equals schema
- CHECK 2: PASS - every row has 6 tab fields :: bad rows idx []
- CHECK 3: PASS - cluster_id multiset == universe :: rows=['C1', 'C2', 'C3', 'C4', 'C5', 'C6', 'C7', 'C8', 'C9', 'C10', 'C-unassigned']
- CHECK 4: PASS - req_ids nonempty and match universe :: []
- CHECK 5: PASS - grandine_surface nonempty
- CHECK 6: PASS - every evidence file in verified inventory & exists :: []
- CHECK 7: PASS - CITED-ABSENT rows cite both own-slug files in inventory :: []
- CHECK 8: PASS - positive surface path/symbol appear verbatim (grep -F) in a cited artifact :: []
- CHECK 9: PASS - CITED-ABSENT slug files lack retained-positive annotation :: []
- CHECK 10: PASS - confidence in {high,medium,low} :: []
- CHECK 11: PASS - provisional-tagged rows carry PROV-SPEC in notes :: []

All 11 checks PASS.

## Resume verification support
The Phase 3a receipt SHA-256 values recorded above were computed with sha256sum
from the current notes/raw/ files at publication time and match the hashes used
for the Step 1 inventory verification.
