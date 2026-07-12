# EIP-8025 study — pinned refs

Schema: preflight block, then one pinned-ref block per source, then a
discrepancy/notes block. Metadata only — no implementation/content
summaries. All checks run 2026-07-12 (~2026-07-12T05:56Z).

## Preflight

| Check | Result |
|---|---|
| `git --version` | OK — git version 2.55.0 |
| `gh auth status` | OK — logged in to github.com as `creese`, token active |
| Network reachability (`curl -sI https://github.com`) | OK — HTTP/2 200 |
| Test fetch `eth-act/lighthouse` (`git ls-remote`) | OK |
| Test fetch `ethereum/EIPs` (`git ls-remote`) | OK |
| Test fetch `grandinetech/grandine` (`git ls-remote`) | OK |

No fallback needed; proceeding with pin phase.

## Pin: eth-act/lighthouse PR #39

- URL: https://github.com/eth-act/lighthouse/pull/39
- Title: "Add optional EIP-8025 execution proofs"
- State: OPEN, isDraft: true, mergedAt: null
- head: `optional-proofs-unstable` @ `0dd6c3b8cf3b1eece82a0a7ee87282a222d93bf5`
- base: `unstable` (in eth-act/lighthouse) @ `dfb259171a65cacd6db57b8874af8f543cabcb7a`
- headRepositoryOwner: eth-act

### LH merge-base vs sigp/lighthouse@unstable

- `sigp/lighthouse` `unstable` HEAD at check time: `7d2b64341bcabaed85332fa59e7be28d3740e88a`
  (moving branch; SHA valid as of the 2026-07-12 check only)
- `git merge-base` of PR head (`0dd6c3b8c...`) vs `sigp/lighthouse@unstable`:
  `494b00a3491e2c5e281f6972aa00694b17f16722`
- PR's stated base commit `dfb259171a65...` is confirmed an ancestor of
  `sigp/lighthouse@unstable` (i.e. eth-act/unstable's base point is on
  sigp history), but the merge-base with the PR head lands later, at
  `494b00a3...`, indicating the PR branch also carries upstream commits
  past the stated base point.
- `git merge-base` of PR base (`dfb259171a65...`) vs PR head
  (`0dd6c3b8c...`), i.e. merge-base vs PR base branch:
  `dfb259171a65cacd6db57b8874af8f543cabcb7a` (equal to the stated base
  itself — the PR head is a straight descendant of eth-act/unstable's
  base commit, with no divergence on that pairing).
- PR head appears to carry upstream commits past the stated base, so
  the Phase 2a diff base must be chosen and logged carefully. Candidate
  baselines recorded for that decision: the merge-base vs PR base
  branch (`dfb259171a65...`, i.e. the stated base commit), and the
  merge-base vs sigp/lighthouse@unstable (`494b00a3...`). Decision
  deferred to the Phase 2a decision log.
- Decision input for Phase 2a: a diff from the stated PR base
  (`dfb259171a65...`) to the PR head may include upstream commits
  between `dfb259171a65...` and `494b00a3...`; the harvest diff base
  must be chosen explicitly and logged before collecting file manifests.

### PR #39 body scan

- Consensus-specs reference (branch/commit/PR link) in PR body: none found.
- Feature-flag names checked as metadata clues from
  `beacon_node/src/cli.rs` on the PR head branch:
  `execution-proof-quorum`, `proof-engine-endpoint`. No
  consensus-specs ref found from these names; none found verbatim in
  the PR body.

### Consensus-specs ref targeted — PROVISIONAL-SPEC-BASELINE (provisional: yes)

- Source: `testing/ef_tests/Makefile` on the PR head branch,
  `CONSENSUS_SPECS_TEST_VERSION ?= v1.7.0-alpha.8`
- `git diff` of this file between PR base (`dfb259171a...`) and PR head
  shows no changes — this pin is inherited from the base branch, not
  introduced/modified by PR #39.
- No EIP-8025-specific consensus-specs branch/commit/PR reference was
  found in code comments, vendored paths, or the PR #39 body (checked
  doc-comments in `beacon_node/beacon_chain/src/eip8025/*` and
  `beacon_node/execution_layer/src/eip8025/*`, plus repo-wide grep for
  `consensus-specs/(pull|commit|blob)` links; see PR #39 body scan
  above).
- Conclusion: the `v1.7.0-alpha.8` ef-tests pin is inherited and
  unchanged by PR #39, and no EIP-8025-specific consensus-specs ref
  was found anywhere searched. Marking **PROVISIONAL-SPEC-BASELINE**
  (provisional: yes) — the inherited ef-tests pin is kept as
  supporting metadata only, not as a confirmed EIP-8025 spec target.
  See [OPEN-Q1].
- Future claims depending on this provisional baseline must be tagged `PROV-SPEC` until [OPEN-Q1] is resolved.

## Pin: ethereum/EIPs PR #11604

- URL: https://github.com/ethereum/EIPs/pull/11604
- Title: "Update EIP-8025: Move to Draft"
- State: OPEN, mergedAt: null, closed: false
- head: `feat/eip8025` @ `d5653bc4d9b86997e069567dcd1eb8766b0c8a55`
- base: `master` @ baseRefOid `d2fb2b2e6104e7484f552bf142c500a9d2a6ef4e`
- isDraft: false
- EIP PR merge-base (`git merge-base d2fb2b2e... d5653bc4...`): `2215c17cde2c7ee0bb5068f2beb573c4776e92ac`
- Decision input for Phase 1a: the EIPs `master` base branch has
  advanced past the PR branch fork point; the harvest diff base must be
  chosen explicitly and logged before collecting EIP PR diffs.

## Pin: Grandine

- Repo: https://github.com/grandinetech/grandine
- Branch: `develop`
- HEAD SHA at check time: `6ae713fca1fe6620ef7e45b864c3e136a767b1c1`

## consensus-specs tag v1.7.0-alpha.8

- Repo: https://github.com/ethereum/consensus-specs
- Tag: `refs/tags/v1.7.0-alpha.8`
- Dereferenced commit SHA: `932c6d691e0d5ed4a003c8bfb9c1c6731ce01924`
  (lightweight tag — `git ls-remote` returned only the one ref, no
  separate `^{}` peeled entry, so the tag SHA is itself the commit SHA)

## eips.ethereum.org/EIPS/eip-8025 — live site status

- Fetched 2026-07-12 via WebFetch.
- Status shown: **Stagnant** (Standards Track: Core)
- Title shown: "EIP-8025: Optional Execution Proofs"
- Date Created shown: September 17, 2025
- No separate "last updated" field displayed.

## Discrepancy note

- EIPs PR #11604 (open, unmerged) proposes moving status
  Stagnant → Draft. The live eips.ethereum.org page still shows
  Stagnant, consistent with the PR not yet being merged. No action
  taken; flagging as expected given PR state, not a ref-mismatch.
