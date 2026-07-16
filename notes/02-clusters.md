# 02 — Lighthouse PR #39 cluster synthesis (Phase 2c)

Synthesis of notes/matrix/lh-files.tsv (250 classified rows) against
notes/matrix/requirements.tsv (36 requirements), grouped by the
provisional cluster taxonomy shared with Phase 2b. Confidence values
are capped at M throughout: the column-identification result for
lh-files.tsv is that it records per-row test evidence but has no
column recording that implementation code was read, so the
confidence-rule fallback cap applies (mechanical basis in the
Phase 2c validation receipt) [LH0188]. Claims depending on
spec-snapshot-derived requirements carry PROV-SPEC and conclude no
stronger than needs-input [OPEN-Q1].

## C1 — SSZ/types (4 rows)

- req_ids: none mapped (all C1 rows carry NONE).
- Claims:
  - The PR adds the SSZ proof containers PublicInput, ExecutionProof, SignedExecutionProof and ProofByRootIdentifier, with MaxProofSize = 1,376,256 bytes (1344 KiB), DOMAIN_EXECUTION_PROOF = [0x0D,0x00,0x00,0x00], MaxExecutionProofsPerPayload = U4 and MIN_REQUIRED_EXECUTION_PROOFS = 1 [LH0188]
  - PublicInput carries only new_payload_request_root and has no successful_validation field, so the container shape cannot express the EIP verifier check of R003; the provisional snapshot PublicInput also lacks that field, PROV-SPEC, needs-input [LH0188] [R003] [OPEN-Q1]
  - NewPayloadRequest gains SszEncode/TreeHash derives and request_root() = tree_hash_root(), the mechanism binding a proof to its engine new-payload request; no extracted requirement mandates this SSZ encoding [LH0072]
  - Module wiring re-exports the new eip8025 types [LH0190]
  - The remaining C1 row is an unrelated KZG cell/proof pairing simplification, not proof work [LH0187]
- Test coverage: unit round-trip tests are recorded as test evidence for the container-definition row [LH0188]; the other C1 rows record no test evidence [LH0072] [LH0187] [LH0190]
- Confidence: M — rows demonstrate code-level description but the matrix records no code-read evidence depth, so the M cap was applied [LH0188] [LH0072]
- Proposal implications:
  - MaxProofSize = 1,376,256 diverges from the provisional snapshot constant MAX_PROOF_SIZE = 409600 behind R017, PROV-SPEC, needs-input [LH0188] [R017] [OPEN-Q5]
  - The 0x0D execution-proof signing domain duplicates domain_proposer_preferences 0x0D per the matrix; a distinct pinned value looks needed [LH0188] [LH0184] [OPEN-Q6]
  - The request-root-only PublicInput matches the provisional snapshot shape rather than the EIP text, PROV-SPEC, needs-input [LH0188] [R003] [OPEN-Q1]

## C2 — gossip validation (32 rows)

- req_ids: R011, R012, R013, R015, R016, R018.
- Claims:
  - verify_and_observe_execution_proof enforces first-valid-proof-per-(request_root, proof_type) dedup (R012) and one-attempt-per-validator dedup (R013), BLS-verifies the proof signature (R015), and treats a proof-engine Invalid verdict as rejection (R018), PROV-SPEC [LH0024] [OPEN-Q1]
  - ObservedExecutionProofs implements the IGNORE-2/IGNORE-3 rules in bounded caches, plus an implementation-local invalid-proof cache keyed on hash_tree_root(proof_data) and finalization pruning, PROV-SPEC [LH0041] [LH0042] [OPEN-Q1]
  - verify_signed_execution_proof_signature rejects empty proof_data (R016) and invalid signatures (R015); the module carries TODO markers (integration into proof engine pending), so this path is in part provisional scaffolding, not finalized design, PROV-SPEC [LH0029] [OPEN-Q1]
  - process_gossip_execution_proof maps verification outcomes to gossip actions: Accept on Valid/Accepted, Ignore while Syncing when the payload request is not yet seen (R011), Reject with peer penalties on Invalid results (R015/R016/R018); the checks themselves are delegated to verify_and_observe_execution_proof, PROV-SPEC [LH0122] [LH0024] [OPEN-Q1]
  - The execution_proof gossip topic is registered, whitelisted and decoded as SignedExecutionProof, and is subscribed only when enable_execution_proof is set [LH0118] [LH0116] [LH0112] [LH0115]
  - Proof-capable peers advertise an eproof ENR field for discovery; an implementation mechanism, not a spec-mandated field [LH0095]
  - A POST beacon/pool/execution_proofs endpoint verifies submitted proofs and publishes accepted ones to gossip, delegating validation to verify_and_observe_execution_proof [LH0084] [LH0086]
  - Domain-separation and signing-root helpers support signature verification and validator-client signing [LH0030]
  - Three gossip-rule MUSTs from the requirement inventory have no mapped row — first-seen dedup R010, the active-validator check R014, and the MAX_PROOF_SIZE bound R017 (see List A), PROV-SPEC [LH0041] [OPEN-Q3] [OPEN-Q4] [OPEN-Q5]
  - A subset of C2 rows is upstream blob-gossip retirement drift, not proof work [LH0012] [LH0117] [LH0119]
- Test coverage: unit tests are recorded for signature/empty-proof verification and for the dedup caches [LH0029] [LH0032] [LH0041] [LH0043]; the gossip_validation ef-test harness added by the PR covers only proposer/attester slashing topics, with remaining topics excluded behind a TODO, so no ef-level execution_proof gossip coverage is recorded [LH0203] [LH0199]
- Confidence: M — code-level descriptions with recorded unit-test evidence on load-bearing rows, M cap applied (no code-read depth column) [LH0024] [LH0029] [LH0041]
- Proposal implications:
  - Gossip IGNORE/REJECT coverage is partial as mapped: R010 and R014 are unverified from the declared evidence, PROV-SPEC, needs-input [LH0041] [OPEN-Q3] [OPEN-Q4]
  - The verification path carries TODO scaffolding markers; readiness assessments should treat it as provisional [LH0029] [LH0022]
  - The gossip machinery is default-off via enable_execution_proof, preserving the optionality of R001 [LH0093] [R001]

## C3 — Req/Resp (21 rows)

- req_ids: R006, R019, R020, R027, R028, R035.
- Claims:
  - Three req/resp protocols — ExecutionProofsByRange, ExecutionProofsByRoot and ExecutionProofStatus (V1, ssz_snappy) — are registered with size limits derived from MaxProofSize/MaxExecutionProofsPerPayload and are offered only when enable_execution_proof is set [LH0104] [LH0103]
  - The request/response containers are SSZ containers with length bounds and max_requested = count times MaxExecutionProofsPerPayload [LH0101] [LH0102]
  - The codec encodes one SignedExecutionProof per successful chunk for ByRange/ByRoot [LH0098]
  - The serving handlers serve proofs within proof_serve_range and answer ResourceUnavailable outside it, implementing ByRange serving (R019/R020) and ByRoot serving (R027/R028), the combined EIP obligation R006, PROV-SPEC [LH0132] [LH0133] [OPEN-Q1]
  - The proof_serve_range lower bound is derived as finalized_checkpoint.epoch.start_slot — the provisional snapshot form, diverging from the EIP finalized_checkpoint.slot text (R006 divergence), PROV-SPEC, needs-input [LH0027] [LH0132] [LH0133] [OPEN-Q1]
  - The ExecutionProofStatus reply is built from the canonical head and configured proof types and returned by the router on inbound status requests (R035), PROV-SPEC [LH0129] [LH0138] [OPEN-Q1]
  - The status cache stores proof status in bounded LRUs; unfinalized proof bytes remain hot/prunable and are not durably tracked, narrower than the EIP retention obligation R002 [LH0023] [R002]
  - Request-root resolution helpers return ProofStatus::Syncing when the request root is unresolved, relating to the queue-until-payload permission R004/R011 while the gossip gate lives in the network layer, PROV-SPEC [LH0025] [OPEN-Q1]
  - Rate limits (default 128 per 10 seconds per protocol) and peer-scoring match arms are implementation values [LH0100] [LH0096]
  - RPC-received proofs are verified through the same verify path, with low-tolerance peer penalties on invalid proofs [LH0123]
  - Router dispatch, request-id types and serving-dispatch wrappers wire the handlers and forward responses to sync [LH0139] [LH0106] [LH0110] [LH0128]
  - Empty requested proof-type lists fall back to the execution-layer configured proof types, a local default-selection choice [LH0134]
- Test coverage: SSZ round-trip tests for the three request types are recorded [LH0098] [LH0099]; no test evidence is recorded on the serving-handler rows [LH0132] [LH0133]
- Confidence: M — code-level rows with partial test evidence, M cap applied (no code-read depth column) [LH0132] [LH0098]
- Proposal implications:
  - The response framing/ordering/limit requirements R022-R026, R029-R031 and R033-R034 have no mapped rows; the codec and container definitions cover framing in substance but per-requirement verification is absent, PROV-SPEC, needs-input [LH0098] [LH0101] [OPEN-Q1]
  - Serving without durable retention looks inconsistent: the status-only cache cannot back the serving obligations over the full serve range, a Lighthouse gap against R002 [LH0023] [R002]
  - The serve-window lower-bound divergence turns on the provisional baseline decision, PROV-SPEC, needs-input [LH0132] [OPEN-Q1]

## C4 — proof engine (7 rows)

- req_ids: none mapped (all C4 rows carry NONE).
- Claims:
  - HttpProofEngine provides the proof-generating (request_proofs) and proof-verifying (verify_execution_proof) transport, delegating to a boxed ProofNodeClient; the HTTP-to-proof-node mechanism is an implementation choice [LH0066]
  - The HTTP proof-node client defines endpoints /v1/execution_proof_requests, /v1/execution_proof_verifications and /v1/execution_proofs, a 1-second default timeout, and an EventSource-based proof-event stream [LH0067]
  - A ProofType registry fixes vendor proof types 0..6 (ethrex-risc0 through reth-zisk) with a default set; proof-node convention, not spec-mandated [LH0068]
  - ProofStatus is the verification status vocabulary: Valid, Invalid, Accepted, NotSupported, Syncing [LH0189]
  - A ProofEngineError taxonomy defines JSON-RPC error codes -39002 to -39004 [LH0064]
  - The eip8025 beacon-chain module carries a TODO (integrate into proof engine), so parts of this integration are provisional scaffolding, not finalized design [LH0022]
- Test coverage: test evidence is recorded for the proof-node client and the type definitions [LH0067] [LH0068] [LH0189]; ProofType parsing has unit tests [LH0069]
- Confidence: M — code-level rows with partial test evidence, M cap applied (no code-read depth column) [LH0067] [LH0066]
- Proposal implications:
  - The CL-to-proof-node interface (endpoints, event stream, proof-type registry, timeouts) is entirely implementation-defined; no extracted requirement covers it, a potential standardization gap for the proposal [LH0067] [LH0068]
  - Vendor-specific proof-type identifiers and defaults would need a registry or allocation decision [LH0068]

## C5 — EL integration (5 rows)

- req_ids: R001, R009.
- Claims:
  - Proof requests are dispatched fire-and-forget (task_executor.spawn) on both the notify_new_payload path and the gloas payload-envelope notify path, so block processing is not delayed awaiting proofs (R001 proof-generating mode; R009 non-delay) [LH0034] [LH0045]
  - The ExecutionLayer engine becomes optional, so a proof-verifying-only node can run without an execution endpoint [LH0073]
  - A test-mock branch (parse_mock_index/get_mock_proof_engine) is wired into the production ExecutionLayer::from_config constructor [LH0073]
  - The getBlobsV1 removal rows are upstream base-branch drift, not proof work [LH0070] [LH0071]
- Test coverage: no C5 row records test evidence [LH0034] [LH0045] [LH0073]; end-to-end coverage of the proof-request path is recorded only in the integration-test cluster [LH0208]
- Confidence: M — code-level rows without recorded test evidence, M cap applied (no code-read depth column) [LH0034] [LH0073]
- Proposal implications:
  - The R009 non-delay MUST is met by construction through async dispatch on both notify paths [LH0034] [LH0045]
  - The EL-optional refactor is a substantial architectural enabler for proof-verifying-only nodes (R001 proof-verifying mode) [LH0073] [R001]
  - The test hook in the production constructor merits cleanup before any finalized design reading [LH0073]

## C6 — block import / fork choice (30 rows)

- req_ids: R001.
- Claims:
  - Valid proofs are consumed as a supplementary payload-validity signal: when the execution_proof_quorum threshold is met, the payload/envelope is marked valid via fork choice (R001 proof-verifying mode); promotion happens only when explicitly enabled and defaults off [LH0026] [LH0017]
  - Block import and payload-envelope import register block_root to new_payload_request_root mappings for later proof status and serving [LH0009] [LH0044]
  - The bulk of C6 is upstream base-branch drift — blob-to-data-column availability transition, gloas ePBS parent-import status, the proto_array find_head rewrite and the proposer_score_boost type change — not proof work [LH0011] [LH0173] [LH0180] [LH0185]
- Test coverage: no test evidence is recorded on the proof-related C6 rows (quorum promotion, request-root registration) [LH0026] [LH0009] [LH0044]; the verified-proof pipeline is covered end to end only in the integration-test cluster [LH0208]
- Confidence: M — code-level rows without recorded test evidence on the proof path, M cap applied (no code-read depth column) [LH0026]
- Proposal implications:
  - The quorum threshold (min_valid_proof_types, default MIN_REQUIRED_EXECUTION_PROOFS) is an implementation knob; the extracted inventory pins no promotion threshold, which is the open proof-verification-threshold question [LH0017] [LH0026] [OPEN-Q2]
  - Default-off promotion keeps valid proofs a metadata-only supplementary signal, matching the MAY optionality of R001 [LH0017] [R001]

## C7 — config/CLI (12 rows)

- req_ids: none mapped (all C7 rows carry NONE).
- Claims:
  - Opt-in beacon-node flags proof-engine-endpoint, proof-types and execution-proof-quorum are added with requires() dependency chains and parsed into the EL and client configs [LH0165] [LH0167]
  - The network flag enable_execution_proof defaults false, gating the proof gossip/RPC protocols [LH0093]
  - ExecutionProofQuorumConfig defaults enabled=false with min = MIN_REQUIRED_EXECUTION_PROOFS, keeping proof-backed promotion off by default [LH0017]
  - Validator-client flags proof-engine-endpoint and proof-types (default 0,1,2,3) enable proof-generating mode [LH0240] [LH0241]
  - Domain::ExecutionProof (domain_execution_proof = 0x0D) is added to the chain spec and exposed in config/preset output; the matrix records that 0x0D duplicates domain_proposer_preferences, a possible not-yet-finalized value [LH0184] [LH0186] [OPEN-Q6]
  - Generated help documentation reflects the new flags [LH0169] [LH0170]
  - The enable-mplex rows are unrelated upstream work, not proof work [LH0094] [LH0166] [LH0168]
- Test coverage: a domain test is recorded for the chain-spec change [LH0184]; no other C7 row records test evidence [LH0165] [LH0167]
- Confidence: M — code-level rows, M cap applied (no code-read depth column) [LH0165] [LH0184]
- Proposal implications:
  - The whole feature is default-off across network, quorum and CLI, consistent with the EIP optionality of R001 [LH0093] [LH0017] [LH0165] [R001]
  - The execution-proof signing domain needs a pinned, collision-free allocation [LH0184] [OPEN-Q6]

## C8 — validator/prover service (16 rows)

- req_ids: R001.
- Claims:
  - A validator-client ProofService monitors block SSE events, requests proofs from the proof engine on non-optimistic blocks, and on ProofComplete builds, signs and submits the execution proof — the opted-in validator producing and broadcasting proofs of R001 [LH0247] [LH0248]
  - sign_execution_proof is added to the ValidatorStore trait and implemented via signing_context(Domain::ExecutionProof), with a SignableMessage variant and web3signer mapping (R001 signing primitive) [LH0250] [LH0237] [LH0238] [LH0239]
  - The submitted PublicInput carries only new_payload_request_root, and the proof is signed with the first only_safe voting pubkey [LH0248]
  - Outstanding-request bookkeeping uses a hardcoded 300-second stale timeout [LH0249]
  - Top-level wiring constructs and starts the service when proof_engine_endpoint is set, selecting a mock or HTTP engine [LH0242]
  - A vc_signed_execution_proofs_total metric tracks signing outcomes [LH0243]
- Test coverage: no C8 row records test evidence [LH0248] [LH0250]; the validator-client proof service is covered by the integration-test suite [LH0208]
- Confidence: M — code-level rows without recorded test evidence, M cap applied (no code-read depth column) [LH0248] [LH0247]
- Proposal implications:
  - Signing-key selection (first safe voting pubkey) interacts with the one-attempt-per-validator gossip rule R013 and may need explicit proposal treatment, PROV-SPEC, needs-input [LH0248] [R013] [OPEN-Q1]
  - Proof-generating mode activates only on explicit endpoint configuration, matching the MAY of R001 [LH0242] [R001]

## C9 — sync (26 rows)

- req_ids: R005, R032, R036.
- Claims:
  - A ProofSync catch-up engine for missing optional execution proofs polls per slot, pauses during range sync, and chooses ExecutionProofsByRange vs ByRoot by estimated SSZ request size [LH0160] [LH0150]
  - On adding a proof-capable peer it sends ExecutionProofStatus (R005), selects peers from cached status responses (R036), and filters needed proof types on status.proof_types (R032), PROV-SPEC [LH0161] [OPEN-Q1]
  - The requester-side window filter starts at the finalized epoch start slot, mirroring the snapshot proof_serve_range lower bound [LH0162]
  - Proof-capable peers are identified via the ENR execution-proof field, and cached peer statuses refresh on a 300-second threshold [LH0154]
  - The proof-sync integration test is ignored with the marker that a late-joining verifier cannot reliably discover the proof-capable peer yet and peer selection needs rework: proof-sync peer discovery is WIP scaffolding, not finalized design [LH0209]
  - Most other C9 rows are upstream base-branch drift (blob-lookup removal, the block-lookup rewrite, disable-backfill gating), not proof work [LH0152] [LH0143] [LH0146]
- Test coverage: a unit test on the serve-window filter is recorded [LH0162]; the end-to-end proof-sync test is disabled as WIP [LH0209]
- Confidence: M — code-level rows with minimal recorded test evidence, M cap applied (no code-read depth column) [LH0161] [LH0160]
- Proposal implications:
  - The requester-side catch-up mechanism is implementation-defined; no extracted requirement mandates the consumer side, while the retention obligation R002 that makes backfill serveable looks unimplemented (List A) [LH0150] [LH0023] [R002]
  - The WIP peer-discovery state makes proof sync unstable ground for cross-client comparison [LH0209]
  - The initial status exchange R005 is implemented on peer add, PROV-SPEC [LH0161] [OPEN-Q1]

## C10 — tests/CI (87 rows)

- req_ids: none mapped (all C10 rows carry NONE).
- Claims:
  - New CI workflows run a kurtosis eip8025 devnet asserting proof metrics, zkboost test suites, and a proof-engine-tests job with corresponding make targets [LH0001] [LH0004] [LH0003] [LH0007]
  - An integration-test crate drives proof generation, gossip and verification end to end across multi-node topologies, including a validator-client proof-service case [LH0208] [LH0210] [LH0211]
  - zkboost wire-compatibility tests run the HTTP proof-node client against a real zkBoost server with mock zkVM backends; the zkboost dependencies track a moving master branch [LH0214] [LH0215] [LH0213]
  - The simulator gains proof-generator/proof-verifier node types wired to mock proof engines [LH0225] [LH0227] [LH0228]
  - Test-observability hooks — an internal event bus, un-gated test harness types and a mock proof-node registry — are compiled into production crates [LH0010] [LH0038] [LH0130] [LH0077]
  - The mock proof-node client stubs verification as always-Valid and is consumable from the production ExecutionLayer constructor [LH0078] [LH0073]
  - Unit tests are recorded for the dedup caches, signature verification, ProofType parsing, req/resp codec round-trips and the status cache [LH0043] [LH0032] [LH0069] [LH0099] [LH0028]
  - The gossip_validation ef-test harness covers only proposer/attester slashing topics; other topics, including execution_proof, remain excluded behind a TODO [LH0203] [LH0199] [LH0206]
  - Scaffolding is present: TODO(gloas) test disables and an ignored fixture-build test are temporary states, not finalized design [LH0052] [LH0232]
  - A large share of C10 is upstream base-branch drift test migration (blob-to-column, gloas ePBS, fork-choice), not proof work [LH0089] [LH0163] [LH0137]
- Test coverage: this cluster is the recorded test surface itself; integration and unit coverage of the proof pipeline is recorded on the rows above, while the proof-sync end-to-end path is disabled [LH0208] [LH0209]
- Confidence: M — code-level rows describing test code, M cap applied (no code-read depth column) [LH0208] [LH0203]
- Proposal implications:
  - No ef-test or spec-test coverage exists for the execution_proof gossip rules; conformance testing would need consensus-spec test vectors, PROV-SPEC, needs-input for snapshot-derived rules [LH0203] [LH0199] [OPEN-Q1]
  - Devnet and zkboost harnesses depend on external moving artifacts (zkboost master branch, vendor prover images) [LH0213] [LH0196]
  - Test hooks compiled into production binaries merit cleanup before finalization [LH0010] [LH0130]

## C-unassigned (10 rows)

- req_ids: none mapped (all C-unassigned rows carry NONE).
- Claims:
  - Build metadata: lockfile and workspace-member additions and SSZ dependency bumps for the new crates [LH0005] [LH0006] [LH0063] [LH0175]
  - BeaconChain infrastructure wiring: the proof dedup/status cache fields and event bus, their builder initialization, and crate-root module registration [LH0008] [LH0015] [LH0039] [LH0120]
  - Unrelated or upstream items: a blobs-API refactor and gloas ePBS payload-bid client methods [LH0085] [LH0172]
- Test coverage: no C-unassigned row records test evidence [LH0008] [LH0005]
- Confidence: M — code-level rows, M cap applied (no code-read depth column) [LH0008]
- Proposal implications:
  - The cache-field infrastructure carries no requirement content of its own; enforcement lives in the gossip and req/resp modules [LH0008] [LH0024]

## Remap table

| old_id → new_id | date |
|---|---|
| no remaps — identity mapping | 2026-07-16 |

No splits or merges were made, so no split row assignment block is
required; every lh-files.tsv row resolves to its recorded cluster_id.

## Orphan list A — requirements with zero mapped rows (19 entries)

- R002 (retention of proofs back to the finalized checkpoint) [R002]: looks like a Lighthouse gap — the status cache stores proof status only, with unfinalized proof bytes hot/prunable and not durably tracked, narrower than the retention MUST [LH0023]; the catch-up sync presupposes peers that retain proofs [LH0150] [R002]
- R003 (verifier must check public_input.successful_validation) [R003]: the implemented PublicInput has no successful_validation field so the check cannot be expressed; the provisional snapshot also lacks the field, an EIP-vs-baseline divergence, PROV-SPEC, needs-input [LH0188] [R003] [OPEN-Q1]
- R004 (MAY queue a proof until its payload arrives) [R004]: covered in substance by Ignore-on-Syncing handling (gossip re-delivery rather than a local queue); a permission, not a gap, PROV-SPEC [LH0025] [LH0122] [OPEN-Q1]
- R007 (MAY descore a peer answering ResourceUnavailable) [R007]: generic RPC peer-scoring arms exist for the proof protocols; MAY-level mapping gap rather than an implementation gap [LH0096] [R007]
- R008 (guest must verify signatures and supplied public keys) [R008]: out of scope for a CL client — an execution-layer/zkVM-guest obligation; proving is delegated to the external proof node [LH0066] [R008]
- R010 (ignore any already-seen proof by hash_tree_root) [R010]: the observed caches implement first-valid and per-validator dedup and an invalid-proof cache, but no row records a plain already-seen IGNORE, PROV-SPEC, needs-input [LH0041] [LH0042] [OPEN-Q3]
- R014 (reject unless the signer is an active validator) [R014]: the error taxonomy includes InvalidValidatorIndex but no row records an active-in-current-epoch check, PROV-SPEC, needs-input [LH0031] [OPEN-Q4]
- R017 (reject proof_data larger than MAX_PROOF_SIZE) [R017]: a size bound exists in the types and RPC limits but the constant (1,376,256) diverges from the snapshot 409600, PROV-SPEC, needs-input [LH0188] [LH0104] [OPEN-Q5]
- R021 (MAY descore or disconnect peers that cannot serve) [R021]: same generic peer-scoring coverage as R007; MAY-level mapping gap, PROV-SPEC [LH0096] [OPEN-Q1]
- R022 (ByRange response framing, one proof per chunk) [R022]: covered in substance by the codec encoding, mapping gap, PROV-SPEC [LH0098] [OPEN-Q1]
- R023 (requester-side count bound) [R023]: covered in substance by max_requested = count times MaxExecutionProofsPerPayload, mapping gap, PROV-SPEC [LH0101] [OPEN-Q1]
- R024 (respond with at least one held proof, ByRange) [R024]: the handler serves held proofs in range but the at-least-one obligation is not distinctly recorded, PROV-SPEC, needs-input [LH0132] [OPEN-Q1]
- R025 (MAY limit returned proofs, ByRange) [R025]: MAY-level; no distinct row and none required beyond serving coverage, PROV-SPEC [LH0132] [OPEN-Q1]
- R026 (SHOULD return proofs in slot-ascending order) [R026]: ordering behavior is not recorded on any row; SHOULD-level, unverified from the declared evidence, PROV-SPEC, needs-input [LH0132] [OPEN-Q1]
- R029 (ByRoot response framing, one proof per chunk) [R029]: as R022, via the shared codec, mapping gap, PROV-SPEC [LH0098] [OPEN-Q1]
- R030 (respond with at least one held proof, ByRoot) [R030]: as R024 for the ByRoot handler, PROV-SPEC, needs-input [LH0133] [OPEN-Q1]
- R031 (MAY limit returned proofs, ByRoot) [R031]: as R025, PROV-SPEC [LH0133] [OPEN-Q1]
- R033 (status request/response encoded as SSZ container) [R033]: covered in substance by the SSZ container definitions and codec, mapping gap, PROV-SPEC [LH0101] [LH0098] [OPEN-Q1]
- R034 (status response is a single chunk) [R034]: the status codec and reply exist but single-chunk framing is not distinctly recorded, PROV-SPEC, needs-input [LH0098] [LH0129] [OPEN-Q1]

## Orphan list B — rows with requirement mapping NONE (235 entries)

One entry per row; the disposition restates the row's own
classification and notes.

- LH0001: CI/devnet harness infrastructure, not a spec obligation [LH0001]
- LH0002: CI/devnet harness infrastructure, not a spec obligation [LH0002]
- LH0003: CI/devnet harness infrastructure, not a spec obligation [LH0003]
- LH0004: CI/devnet harness infrastructure, not a spec obligation [LH0004]
- LH0005: build-manifest/dependency metadata [LH0005]
- LH0006: build-manifest/dependency metadata [LH0006]
- LH0007: CI/devnet harness infrastructure, not a spec obligation [LH0007]
- LH0008: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0008]
- LH0009: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0009]
- LH0010: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0010]
- LH0011: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0011]
- LH0012: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0012]
- LH0013: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0013]
- LH0014: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0014]
- LH0015: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0015]
- LH0016: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0016]
- LH0017: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0017]
- LH0018: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0018]
- LH0019: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0019]
- LH0020: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0020]
- LH0021: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0021]
- LH0022: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0022]
- LH0023: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0023]
- LH0025: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0025]
- LH0028: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0028]
- LH0030: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0030]
- LH0031: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0031]
- LH0032: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0032]
- LH0033: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0033]
- LH0035: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0035]
- LH0036: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0036]
- LH0037: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0037]
- LH0038: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0038]
- LH0039: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0039]
- LH0040: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0040]
- LH0042: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0042]
- LH0043: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0043]
- LH0044: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0044]
- LH0046: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0046]
- LH0047: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0047]
- LH0048: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0048]
- LH0049: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0049]
- LH0050: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0050]
- LH0051: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0051]
- LH0052: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0052]
- LH0053: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0053]
- LH0054: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0054]
- LH0055: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0055]
- LH0056: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0056]
- LH0057: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0057]
- LH0058: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0058]
- LH0059: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0059]
- LH0060: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0060]
- LH0061: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0061]
- LH0062: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0062]
- LH0063: build-manifest/dependency metadata [LH0063]
- LH0064: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0064]
- LH0065: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0065]
- LH0066: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0066]
- LH0067: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0067]
- LH0068: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0068]
- LH0069: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0069]
- LH0070: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0070]
- LH0071: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0071]
- LH0072: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0072]
- LH0073: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0073]
- LH0074: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0074]
- LH0075: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0075]
- LH0076: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0076]
- LH0077: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0077]
- LH0078: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0078]
- LH0079: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0079]
- LH0080: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0080]
- LH0081: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0081]
- LH0082: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0082]
- LH0083: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0083]
- LH0084: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0084]
- LH0085: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0085]
- LH0086: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0086]
- LH0087: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0087]
- LH0088: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0088]
- LH0089: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0089]
- LH0090: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0090]
- LH0091: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0091]
- LH0092: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0092]
- LH0093: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0093]
- LH0094: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0094]
- LH0095: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0095]
- LH0096: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0096]
- LH0097: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0097]
- LH0098: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0098]
- LH0099: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0099]
- LH0100: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0100]
- LH0101: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0101]
- LH0102: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0102]
- LH0103: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0103]
- LH0104: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0104]
- LH0105: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0105]
- LH0106: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0106]
- LH0107: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0107]
- LH0108: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0108]
- LH0109: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0109]
- LH0110: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0110]
- LH0111: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0111]
- LH0112: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0112]
- LH0113: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0113]
- LH0114: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0114]
- LH0115: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0115]
- LH0116: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0116]
- LH0117: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0117]
- LH0118: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0118]
- LH0119: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0119]
- LH0120: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0120]
- LH0121: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0121]
- LH0123: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0123]
- LH0124: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0124]
- LH0125: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0125]
- LH0126: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0126]
- LH0127: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0127]
- LH0128: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0128]
- LH0130: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0130]
- LH0131: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0131]
- LH0134: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0134]
- LH0135: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0135]
- LH0136: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0136]
- LH0137: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0137]
- LH0139: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0139]
- LH0140: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0140]
- LH0141: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0141]
- LH0142: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0142]
- LH0143: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0143]
- LH0144: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0144]
- LH0145: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0145]
- LH0146: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0146]
- LH0147: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0147]
- LH0148: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0148]
- LH0149: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0149]
- LH0150: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0150]
- LH0151: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0151]
- LH0152: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0152]
- LH0153: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0153]
- LH0154: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0154]
- LH0155: upstream base-branch drift swept in by the harvest diff base, not proof work [LH0155]
- LH0156: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0156]
- LH0157: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0157]
- LH0158: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0158]
- LH0159: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0159]
- LH0160: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0160]
- LH0162: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0162]
- LH0163: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0163]
- LH0164: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0164]
- LH0165: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0165]
- LH0166: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0166]
- LH0167: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0167]
- LH0168: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0168]
- LH0169: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0169]
- LH0170: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0170]
- LH0171: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0171]
- LH0172: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0172]
- LH0173: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0173]
- LH0174: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0174]
- LH0175: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0175]
- LH0176: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0176]
- LH0177: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0177]
- LH0178: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0178]
- LH0179: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0179]
- LH0180: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0180]
- LH0181: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0181]
- LH0182: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0182]
- LH0183: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0183]
- LH0184: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0184]
- LH0185: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0185]
- LH0186: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0186]
- LH0187: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0187]
- LH0188: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0188]
- LH0189: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0189]
- LH0190: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0190]
- LH0191: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0191]
- LH0192: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0192]
- LH0193: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0193]
- LH0194: CI/devnet harness infrastructure, not a spec obligation [LH0194]
- LH0195: CI/devnet harness infrastructure, not a spec obligation [LH0195]
- LH0196: CI/devnet harness infrastructure, not a spec obligation [LH0196]
- LH0197: CI/devnet harness infrastructure, not a spec obligation [LH0197]
- LH0198: build-manifest/dependency metadata [LH0198]
- LH0199: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0199]
- LH0200: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0200]
- LH0201: non-EIP-8025 change per the row notes (upstream or unrelated work) [LH0201]
- LH0202: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0202]
- LH0203: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0203]
- LH0204: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0204]
- LH0205: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0205]
- LH0206: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0206]
- LH0207: build-manifest/dependency metadata [LH0207]
- LH0208: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0208]
- LH0209: WIP scaffolding (disabled or skip-guarded path), not finalized design [LH0209]
- LH0210: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0210]
- LH0211: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0211]
- LH0212: build-manifest/dependency metadata [LH0212]
- LH0213: build-manifest/dependency metadata [LH0213]
- LH0214: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0214]
- LH0215: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0215]
- LH0216: CI/devnet harness infrastructure, not a spec obligation [LH0216]
- LH0217: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0217]
- LH0218: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0218]
- LH0219: build-manifest/dependency metadata [LH0219]
- LH0220: CI/devnet harness infrastructure, not a spec obligation [LH0220]
- LH0221: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0221]
- LH0222: CI/devnet harness infrastructure, not a spec obligation [LH0222]
- LH0223: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0223]
- LH0224: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0224]
- LH0225: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0225]
- LH0226: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0226]
- LH0227: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0227]
- LH0228: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0228]
- LH0229: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0229]
- LH0230: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0230]
- LH0231: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0231]
- LH0232: WIP scaffolding (disabled or skip-guarded path), not finalized design [LH0232]
- LH0233: build-manifest/dependency metadata [LH0233]
- LH0234: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0234]
- LH0235: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0235]
- LH0236: test or test-harness infrastructure supporting the proofs feature, no direct requirement mapping [LH0236]
- LH0237: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0237]
- LH0238: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0238]
- LH0239: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0239]
- LH0240: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0240]
- LH0241: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0241]
- LH0242: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0242]
- LH0243: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0243]
- LH0244: build-manifest/dependency metadata [LH0244]
- LH0245: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0245]
- LH0246: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0246]
- LH0247: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0247]
- LH0249: EIP-8025 implementation infrastructure/wiring not keyed to a single extracted requirement [LH0249]
