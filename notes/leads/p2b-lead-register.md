# P2b lead register

> **NON-EVIDENCE REGISTER.** This register and its source session
> logs under `.work/p2b/` are not study evidence. Nothing in this
> register may be cited or may otherwise support any claim. Every
> lead below is an unverified candidate: it requires independent
> verification against declared evidence in an authorized later
> phase before it affects any synthesis or is promoted. Direct
> promotion of a lead from this register to
> `notes/open-questions.md` is a user decision. Lead IDs are not
> `[OPEN-Qn]` identifiers and never become citation tokens.

## LQ-001

- **Type:** borderline-judgment
- **Sources:** session-006.log:14
- **Excerpt:** "Per the spec I continued into processing. The workload bound deterministically ends the batch at index 019 (cumulative diff lines reach 3030 ≥ 3000 before index 020), reproducing the prior session's batch name. I read all 19 saved diffs and classified them into 33 draft rows (notably: `lh-threads.md` records zero review comments/threads on PR #39, so no reviewer-contested classification is possible; indices 009–011, 013, 015–017 are largely blob-gossip-retirement refactoring with no EIP-8025 signal, classified `unclear`)."
- **Mentioned identifiers:** none
- **Log-reported status:** log-reported only:
  - session-006.log:14: "`lh-threads.md` records zero review comments/threads on PR #39"

## LQ-002

- **Type:** proposed-question
- **Sources:** session-007.log:30, session-008.log:18, session-009.log:39, session-010.log:28, session-011.log:46, session-012.log:35
- **Excerpt:** "- **Upstream base-branch drift dominates indices 008–017.** ~14 rows (e.g. LH0011, LH0012, LH0013, LH0016, LH0018, LH0020, LH0021) are the gloas/EIP-7732/PeerDAS blob→column transition, swept into the diff because the Phase 2a harvest base is the stated PR base (`dfb259171a`), which lags the PR head's upstream commits (refs.md decision log). Classified `LH-arch-choice` with explicit "appears upstream drift, not EIP-8025" notes. *Proposed question:* should Phase 2c treat upstream-drift rows as a distinct bucket rather than folding them into LH-arch-choice? [raw:lh-files/008.diff] [raw:lh-files/009.diff] [raw:lh-files/017.diff]"
  Variant (session-008.log:18): "Upstream-base-drift was preserved explicitly in row notes (PeerDAS blob→data-column and gloas/EIP-7732 churn: LH0035–0037, 0040, 0048, 0050, 0052–0062) using existing taxonomy values only; no new taxonomy value, no open-questions edit."
  Variant (session-009.log:39): "- Upstream base-branch drift (per refs.md decision log, PR head carries commits past the pinned base): preserved with the existing taxonomy on `getBlobsV1` removals (047, 048, 050, 052, 055), PeerDAS blob→data-column changes (062, 063), and gloas ePBS / payload-attestation additions (056, 057, 058, 061, 064) — all noted "not EIP-8025 proof work.""
  Variant (session-010.log:28): "Evidence-supported drift rows tagged "Appears upstream base-branch drift … not EIP-8025 proof work" for the PeerDAS blob→data-column and gloas transitions: LH0107 (075), LH0109 (076), LH0114 (078), LH0117 (080), LH0119 (081), LH0121 (083), LH0124/LH0125/LH0126 (084), LH0131 (085)."
  Variant (session-011.log:46): "Reused the established `Appears upstream base-branch drift (…), not EIP-8025 proof work [raw:…]` phrasing for the blob-decoupling / PeerDAS and `disable-backfill` feature-gating changes: LH0136, LH0137, LH0141, LH0142, LH0148, LH0149, LH0152 (and the block-lookups rework rows LH0143–LH0147 noted as refactor, not proof work). No design inference drawn from WIP/scaffolding."
  Variant (session-012.log:35): "- The bulk of 097–121 is **upstream base-branch drift** (PeerDAS blob-lookup removal, Gloas ePBS fork-choice/`ParentImportStatus`, `proto_array` `find_head` rewrite + children cache, `proposer_score_boost` `Option→u64`), classified LH-arch-choice with notes marking it "not EIP-8025 proof work.""
- **Mentioned identifiers:** LH0011, LH0012, LH0013, LH0016, LH0018, LH0020, LH0021, [raw:lh-files/008.diff], [raw:lh-files/009.diff], [raw:lh-files/017.diff], LH0035, LH0107, LH0109, LH0114, LH0117, LH0119, LH0121, LH0124, LH0125, LH0126, LH0131, LH0136, LH0137, LH0141, LH0142, LH0148, LH0149, LH0152, LH0143, LH0147, [raw:…]
- **Log-reported status:** none

## LQ-003

- **Type:** proposed-question
- **Sources:** session-007.log:31
- **Excerpt:** "- **Status-only proof retention vs R002.** `ExecutionProofStatusCache` stores proof *status* in bounded LRUs (8192) and explicitly does not durably retain proof bytes, which is narrower than the EIP retention MUST (R002). *Proposed question:* does the LRU status-only design satisfy R002's back-to-finalized retention obligation, or is it a divergence? [raw:lh-files/019.diff]"
- **Mentioned identifiers:** R002, [raw:lh-files/019.diff]
- **Log-reported status:** none

## LQ-004

- **Type:** reported-divergence
- **Sources:** session-007.log:32, session-011.log:38
- **Excerpt:** "- **Serve-window lower bound (PROV-SPEC).** `register_execution_proof_request_window` uses `finalized_checkpoint.epoch.start_slot`, matching the provisional snapshot form rather than the EIP `finalized_checkpoint.slot` text (R006 divergence, [OPEN-Q1]). [raw:lh-files/019.diff]"
  Variant (session-011.log:38): "Index 086 was classified on its own evidence as the proof **serving / RPC transport** mechanism (`handle_execution_proofs_by_range/root_request`, `send_response`, `proof_serve_range`), kept distinct from the index-085 status-**reply construction** (LH0129, R035). The `proof_serve_range` lower-bound uses `finalized_checkpoint.epoch.start_slot` — the provisional-snapshot form, an R006 divergence — so both serving rows carry PROV-SPEC per the LH0027 convention."
- **Mentioned identifiers:** R006, [raw:lh-files/019.diff], [OPEN-Q1], LH0129, LH0027, R035
- **Log-reported status:** none

## LQ-005

- **Type:** proposed-question
- **Sources:** session-008.log:29
- **Excerpt:** "- Should the fire-and-forget `task_executor.spawn("eip8025_proof_request")` proof-request wiring be read as *enforcing* R009 (validators MUST NOT delay block validation/attestation awaiting a proof), or only as an implementation convenience? The saved diffs show the async spawn but carry no comment tying it to the non-delay requirement; this governs the spec-driven vs LH-arch-choice call for LH0034/LH0045. [raw:lh-files/022.diff] [raw:lh-files/030.diff]"
- **Mentioned identifiers:** LH0034, LH0045, R009, [raw:lh-files/022.diff], [raw:lh-files/030.diff]
- **Log-reported status:** none

## LQ-006

- **Type:** borderline-judgment
- **Sources:** session-008.log:32
- **Excerpt:** "- LH0052 sits on the scaffolding / LH-arch-choice boundary: it is temporary test-disabling code but also upstream gloas churn; classified `scaffolding` because the signal is a literal `TODO(gloas)` marker. [raw:lh-files/034.diff]"
- **Mentioned identifiers:** LH0052, [raw:lh-files/034.diff]
- **Log-reported status:** none

## LQ-007

- **Type:** borderline-judgment
- **Sources:** session-008.log:33
- **Excerpt:** "- LH0046/LH0049 (`versioned_hashes` → fixed-length `try_into` for `NewPayloadRequestGloas`) may be gloas-upstream rather than EIP-8025; classified LH-arch-choice with neutral notes rather than asserting drift, since the diff alone doesn't disambiguate. [raw:lh-files/030.diff] [raw:lh-files/031.diff]"
- **Mentioned identifiers:** LH0046, LH0049, [raw:lh-files/030.diff], [raw:lh-files/031.diff]
- **Log-reported status:** none

## LQ-008

- **Type:** borderline-judgment
- **Sources:** session-009.log:36
- **Excerpt:** "- `LH0072` (049, `request_root`): the `NewPayloadRequest` gains `SszEncode`/`TreeHash` derives and `request_root()=tree_hash_root()`, computing the `new_payload_request_root` that the gossip rules key on; I classified it LH-arch-choice/NONE because no extracted requirement mandates the SSZ encoding of `NewPayloadRequest` itself — the root is the mechanism, not a cited obligation [raw:lh-files/049.diff]."
- **Mentioned identifiers:** LH0072, [raw:lh-files/049.diff]
- **Log-reported status:** none

## LQ-009

- **Type:** other-observation
- **Sources:** session-009.log:37
- **Excerpt:** "- `LH0073` (050, `from_config`): a test-mock branch (`parse_mock_index`/`get_mock_proof_engine`) is wired into the production `ExecutionLayer::from_config` constructor — a test hook on the production path, flagged as a design observation rather than reclassified [raw:lh-files/050.diff]."
- **Mentioned identifiers:** LH0073, [raw:lh-files/050.diff]
- **Log-reported status:** none

## LQ-010

- **Type:** borderline-judgment
- **Sources:** session-009.log:38
- **Excerpt:** "- `LH0084` (059, execution_proofs endpoint): the POST endpoint delegates gossip IGNORE/REJECT validation to `verify_and_observe_execution_proof` (spec-driven work already classified at index 019); the HTTP submission API itself is not spec-mandated, so LH-arch-choice/NONE [raw:lh-files/059.diff]."
- **Mentioned identifiers:** LH0084, [raw:lh-files/059.diff]
- **Log-reported status:** none

## LQ-011

- **Type:** borderline-judgment
- **Sources:** session-009.log:40
- **Excerpt:** "- `LH0085` (060, block_id blob filtering): a local blobs-API refactor; I did **not** label it drift (insufficient evidence), only "not EIP-8025 proof work" [raw:lh-files/060.diff]."
- **Mentioned identifiers:** LH0085, [raw:lh-files/060.diff]
- **Log-reported status:** none

## LQ-012

- **Type:** borderline-judgment
- **Sources:** session-010.log:31
- **Excerpt:** "- **LH0122 spec-driven** attributes the gossip forward/reject *decision* to R011/R015/R016/R018, while the underlying checks are delegated to `verify_and_observe_execution_proof` (LH0024/LH0029) — borderline vs. double-attribution [raw:lh-files/084.diff]."
- **Mentioned identifiers:** LH0122, LH0024, LH0029, R011, R015, R016, R018, [raw:lh-files/084.diff]
- **Log-reported status:** none

## LQ-013

- **Type:** borderline-judgment
- **Sources:** session-010.log:32
- **Excerpt:** "- **LH0129 spec-driven R035** builds the status reply; the actual RPC send lives in `rpc_methods` (index 086, next batch) [raw:lh-files/085.diff]."
- **Mentioned identifiers:** LH0129, R035, [raw:lh-files/085.diff]
- **Log-reported status:** none

## LQ-014

- **Type:** borderline-judgment
- **Sources:** session-010.log:33
- **Excerpt:** "- **LH0095** (066 ENR `eproof`) placed in **C2** as the networking bucket — no discovery-specific cluster exists; C-unassigned was the alternative [raw:lh-files/066.diff]."
- **Mentioned identifiers:** LH0095, [raw:lh-files/066.diff]
- **Log-reported status:** none

## LQ-015

- **Type:** borderline-judgment
- **Sources:** session-010.log:34
- **Excerpt:** "- **LH0097** (068 peerdb custody-subnet test helpers) classified LH-arch-choice/C10 as test-only, *not* tagged drift — it concerns PeerDAS custody but I have no positive evidence it is inherited vs PR-local [raw:lh-files/068.diff]."
- **Mentioned identifiers:** LH0097, [raw:lh-files/068.diff]
- **Log-reported status:** none

## LQ-016

- **Type:** borderline-judgment
- **Sources:** session-010.log:35
- **Excerpt:** "- `enable_mplex` transport changes (**LH0111/LH0113**) treated as non-proof work, consistent with LH0094's treatment of the `enable_mplex` config flag [raw:lh-files/077.diff] [raw:lh-files/078.diff]."
- **Mentioned identifiers:** LH0111, LH0113, LH0094, [raw:lh-files/077.diff], [raw:lh-files/078.diff]
- **Log-reported status:** none

## LQ-017

- **Type:** borderline-judgment
- **Sources:** session-011.log:41
- **Excerpt:** "- **Router (089)** split into a spec-driven status-reply row (R035) and pure req/resp + gossip **routing/wiring** rows classified LH-arch-choice (dispatch to the 086 handlers and forwarding to sync/gossip is plumbing, not itself a requirement)."
- **Mentioned identifiers:** R035
- **Log-reported status:** none

## LQ-018

- **Type:** borderline-judgment
- **Sources:** session-011.log:42
- **Excerpt:** "- **ProofSync integration (096, LH0150)** left LH-arch-choice/NONE: the optional execution-proof **catch-up sync** is a requester-side implementation mechanism supporting the backfill that R002's *retention* obligation enables; no requirement mandates the consumer side, and it is gated on `config.enable_execution_proof`."
- **Mentioned identifiers:** LH0150, R002
- **Log-reported status:** none

## LQ-019

- **Type:** borderline-judgment
- **Sources:** session-011.log:43
- **Excerpt:** "- **LH0141 (089)** mixes blob-lookup (req/resp) and blob-gossip removal; assigned C9 as the dominant blob-lookup-removal concern."
- **Mentioned identifiers:** LH0141
- **Log-reported status:** none

## LQ-020

- **Type:** borderline-judgment
- **Sources:** session-012.log:32
- **Excerpt:** "- **LH0161 (103, spec-driven R005,R032,R036):** proof-sync peer/type selection. `best_peer` selecting from cached `ExecutionProofStatus` (R036) and `needed_types` filtering on `status.proof_types` (R032) are clear matches; `add_peer` sending status on peer-add mapped to R005. Because R005 is source `both`, the row is *not* mechanically PROV-SPEC-tag-required, but I added `PROV-SPEC`/`[OPEN-Q1]` anyway since R032/R036 are provisional-snapshot."
- **Mentioned identifiers:** LH0161, R005, R032, R036, [OPEN-Q1]
- **Log-reported status:** none

## LQ-021

- **Type:** borderline-judgment
- **Sources:** session-012.log:33
- **Excerpt:** "- **LH0162 (103):** `servable_missing_proofs`/`finalized_request_start_slot` derive the requester serve-window lower bound as `finalized_checkpoint.epoch.start_slot` — the snapshot `proof_serve_range` form. Kept **LH-arch-choice** (requester-side catch-up filter, not a serving obligation) rather than spec-driven, to avoid over-claiming."
- **Mentioned identifiers:** LH0162
- **Log-reported status:** none

## LQ-022

- **Type:** proposed-question
- **Sources:** session-012.log:34, session-012.log:38, session-013.log:38, session-014.log:42, session-015.log:50
- **Excerpt:** "- Should the EIP-8025 execution-proof signing domain be a distinct value? PR #39 sets `domain_execution_proof = 0x0D`, colliding with `domain_proposer_preferences = 0x0D` [raw:lh-files/121.diff]."
  Variant (session-012.log:34): "- **LH0184 (121):** the new `domain_execution_proof = 0x0D` **duplicates `domain_proposer_preferences = 0x0D`**. Classified LH-arch-choice and flagged in notes as a possible not-yet-finalized value; no `TODO`/placeholder marker exists in the diff, so I did not classify it scaffolding."
  Variant (session-013.log:38): "- **Domain-type collision (preserved per instruction):** `DOMAIN_EXECUTION_PROOF = [0x0D,0x00,0x00,0x00]` (0x0D000000) is defined in `eip8025.rs` [raw:lh-files/124.diff] and exported alongside `domain_proposer_preferences` in the config/preset map [raw:lh-files/122.diff]. Proposed question: does `domain_execution_proof = 0x0D` collide with `domain_proposer_preferences = 0x0D`? (The `domain_proposer_preferences = 0x0D` value is taken as given from the task prompt; I did not independently verify it from a declared raw input this session.)"
  Variant (session-014.log:42): "2. Whether `DOMAIN_EXECUTION_PROOF = 0x0D000000` collides with the proposer-preferences domain. This batch shows `Domain::ExecutionProof` being used for proof signing (index 162) alongside `ProposerPreferences` in the same `Domain` enum imports [raw:lh-files/162.diff], but **the numeric value `0x0D000000` and the proposer-preferences domain value were NOT observed in this batch's declared evidence — the comparison was not independently verified here.**"
  Variant (session-015.log:50): "2. **Whether `DOMAIN_EXECUTION_PROOF = 0x0D000000` collides with the proposer-preferences domain.** *Entirely carried-forward* — the domain constant does **not appear** in this batch's diffs. This batch shows only epoch-scoped signing (`sign_execution_proof(..., signing_epoch)`, epoch derived from the block slot) [raw:lh-files/171.diff] [raw:lh-files/172.diff], which does not expose the domain value. **No new verified evidence** on the collision here."
- **Mentioned identifiers:** LH0184, [raw:lh-files/121.diff], [raw:lh-files/124.diff], [raw:lh-files/122.diff], [raw:lh-files/162.diff], [raw:lh-files/171.diff], [raw:lh-files/172.diff]
- **Log-reported status:** log-reported only:
  - session-013.log:38: "The `domain_proposer_preferences = 0x0D` value is taken as given from the task prompt; I did not independently verify it from a declared raw input this session."
  - session-014.log:42: "the numeric value `0x0D000000` and the proposer-preferences domain value were NOT observed in this batch's declared evidence — the comparison was not independently verified here."
  - session-015.log:50: "**No new verified evidence** on the collision here."

## LQ-023

- **Type:** borderline-judgment
- **Sources:** session-013.log:31
- **Excerpt:** "- **124 (`eip8025.rs`)** split into two rows: SSZ containers + constants (C1) and the `ProofStatus` verification enum/accessors (C4). Both `LH-arch-choice`, `req_ids=NONE` — the containers define shape but enforce no cited behavioral requirement."
- **Mentioned identifiers:** none
- **Log-reported status:** none

## LQ-024

- **Type:** borderline-judgment
- **Sources:** session-013.log:32
- **Excerpt:** "- **126 (`environment/Cargo.toml`, `[features] test-utils`)** — classified `LH-arch-choice` (feature gating a test module) rather than `dependency`; it is build-metadata but its purpose is test-infra design, not dependency churn."
- **Mentioned identifiers:** none
- **Log-reported status:** none

## LQ-025

- **Type:** borderline-judgment
- **Sources:** session-013.log:33
- **Excerpt:** "- **134 (`check_all_files_accessed.py`)** — classified `LH-arch-choice`, not `scaffolding`: the substantive effect is narrowing the blanket `networking/.*` exclusion so implemented gossip topics run; the retained `# TODO: Remaining gossip validation topics not yet implemented` is noted in-row."
- **Mentioned identifiers:** none
- **Log-reported status:** none

## LQ-026

- **Type:** borderline-judgment
- **Sources:** session-013.log:34
- **Excerpt:** "- **141 (`test_proof_engine_sync`)** split out as its own row and classified `scaffolding` (over `reverted-partial`), cluster C9 — the `#[ignore = "…proof-sync peer selection needs rework"]` marker flags a WIP proof-sync path; the other five integration tests are a passing suite (`LH-arch-choice`, C10)."
- **Mentioned identifiers:** none
- **Log-reported status:** none

## LQ-027

- **Type:** borderline-judgment
- **Sources:** session-013.log:35
- **Excerpt:** "- **136 (`fork_choice.rs`)** split into the KZG-verified-blob API migration and the relaxed `if valid && !success` assertion ("blob-DA failure cases are expected to import now"); both are drive-by test changes unrelated to execution proofs."
- **Mentioned identifiers:** none
- **Log-reported status:** none

## LQ-028

- **Type:** proposed-question
- **Sources:** session-013.log:39, session-014.log:41, session-015.log:49
- **Excerpt:** "- **Proof-size divergence:** Lighthouse sets `MaxProofSize = 1344 KiB (1,376,256 bytes)` [raw:lh-files/124.diff], which differs from the `MAX_PROOF_SIZE = 409600` (400 KiB) bound behind requirement R017. Proposed question: is the 1344 KiB cap an intentional implementation choice or a divergence from the (provisional) spec constant? (R017 is PROV-SPEC / [OPEN-Q1].)"
  Variant (session-014.log:41): "1. Lighthouse `MaxProofSize = 1344 KiB` versus provisional **R017** `MAX_PROOF_SIZE = 409600`. *(Carried from prior batches; the `1344 KiB` value was not present in this batch's declared evidence, so it was not re-verified here.)*"
  Variant (session-015.log:49): "1. **Lighthouse `MaxProofSize = 1344 KiB` vs provisional R017 / `MAX_PROOF_SIZE = 409600`.** *Carried-forward* from earlier batches — the numeric `1344 KiB` constant was recorded from an earlier index and is **not** restated in this batch. This batch's only bearing evidence is that the VC enforces *some* upper bound at proof completion: `ProofData::new(proof_bytes)` fails with "Proof data exceeds max size" [raw:lh-files/171.diff]. The 1344 KiB↔409600 mismatch itself is **not re-verified here**; only the existence of an enforced bound is visible."
- **Mentioned identifiers:** R017, [raw:lh-files/124.diff], [OPEN-Q1], [raw:lh-files/171.diff]
- **Log-reported status:** log-reported only:
  - session-013.log:39: "R017 is PROV-SPEC / [OPEN-Q1]."
  - session-014.log:41: "Carried from prior batches; the `1344 KiB` value was not present in this batch's declared evidence, so it was not re-verified here."
  - session-015.log:49: "The 1344 KiB↔409600 mismatch itself is **not re-verified here**; only the existence of an enforced bound is visible."

## LQ-029

- **Type:** borderline-judgment
- **Sources:** session-014.log:34
- **Excerpt:** "- **147 chain_config.json → CI-devnet** (not plain test data): classified on the hardcoded devnet `chainId 3151908` (kurtosis devnet id) [raw:lh-files/147.diff]. The paired witness fixture **148** was kept **LH-arch-choice** because its payload carries no devnet identifier [raw:lh-files/148.diff]."
- **Mentioned identifiers:** [raw:lh-files/147.diff], [raw:lh-files/148.diff]
- **Log-reported status:** none

## LQ-030

- **Type:** borderline-judgment
- **Sources:** session-014.log:35
- **Excerpt:** "- **151/152 basic_sim/fallback_sim split into two rows each**: fork-schedule changes classified **CI-devnet** (sim fork activation, carries the `TODO(gloas)` blocker comment), while the NodeType/proof-node-param plumbing (counts set to 0) classified **LH-arch-choice** [raw:lh-files/151.diff][raw:lh-files/152.diff]."
- **Mentioned identifiers:** [raw:lh-files/151.diff], [raw:lh-files/152.diff]
- **Log-reported status:** none

## LQ-031

- **Type:** borderline-judgment
- **Sources:** session-014.log:36
- **Excerpt:** "- **157 `#[ignore]`d `test_network_fixture_build` → scaffolding** (separate row) on the concrete marker "Ignore this for now because it conflicts with the proof_engine testing crate" [raw:lh-files/157.diff]; the rest of mod.rs is LH-arch-choice."
- **Mentioned identifiers:** [raw:lh-files/157.diff]
- **Log-reported status:** none

## LQ-032

- **Type:** borderline-judgment
- **Sources:** session-014.log:37
- **Excerpt:** "- **162 sign_execution_proof → LH-arch-choice / NONE, not spec-driven**: R001 only *permits* (MAY) proof-generating mode and imposes no signing obligation, so citing it would misrepresent a permission as a mandate [raw:lh-files/162.diff]."
- **Mentioned identifiers:** R001, [raw:lh-files/162.diff]
- **Log-reported status:** none

## LQ-033

- **Type:** borderline-judgment
- **Sources:** session-014.log:38
- **Excerpt:** "- **164 web3signer**: the `TODO(gloas) verify w/ web3signer specs` marker applies to the adjacent `ExecutionPayloadEnvelope` line, not the clean `ExecutionProof` addition — so this row is not scaffolding [raw:lh-files/164.diff]."
- **Mentioned identifiers:** [raw:lh-files/164.diff]
- **Log-reported status:** none

## LQ-034

- **Type:** borderline-judgment
- **Sources:** session-015.log:53
- **Excerpt:** "- **spec-driven R001 vs LH-arch-choice split within the proof service:** I tagged only the substantive produce/sign/submit path (LH0248) and the signing primitive (LH0250) as spec-driven under R001 (opted-in validator producing/broadcasting proofs; EIP-PR source, so no PROV-SPEC tag). The SSE/HTTP plumbing, block-event→request path, and outstanding-request bookkeeping are LH-arch-choice with req_ids NONE — the mode's existence is spec-described but its mechanism is not spec-mandated (did not stretch to a match)."
- **Mentioned identifiers:** LH0248, LH0250, R001
- **Log-reported status:** none

## LQ-035

- **Type:** borderline-judgment
- **Sources:** session-015.log:54
- **Excerpt:** "- **Cluster:** assigned all of `proof_service.rs` plus the two supporting files to **C8** (validator/prover service) rather than splitting the proof-engine-facing request path into C4 — the code is VC-side prover-service orchestration; cluster remapping is Phase 2c's job."
- **Mentioned identifiers:** none
- **Log-reported status:** none
