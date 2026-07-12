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

### Consensus-specs ref targeted

- Source: `testing/ef_tests/Makefile` on the PR head branch,
  `CONSENSUS_SPECS_TEST_VERSION ?= v1.7.0-alpha.8`
- `git diff` of this file between PR base (`dfb259171a...`) and PR head
  shows no changes — this pin is inherited from the base branch, not
  introduced/modified by PR #39.
- No EIP-8025-specific consensus-specs branch/commit/PR reference was
  found in code comments or vendored paths (checked doc-comments in
  `beacon_node/beacon_chain/src/eip8025/*` and
  `beacon_node/execution_layer/src/eip8025/*`, plus repo-wide grep for
  `consensus-specs/(pull|commit|blob)` links). Treating the general
  ef-tests version pin above as the targeted spec ref; determinable,
  so no PROVISIONAL-SPEC-BASELINE / open question triggered.

## Pin: ethereum/EIPs PR #11604

- URL: https://github.com/ethereum/EIPs/pull/11604
- Title: "Update EIP-8025: Move to Draft"
- State: OPEN, mergedAt: null, closed: false
- head: `feat/eip8025` @ `d5653bc4d9b86997e069567dcd1eb8766b0c8a55`
- base: `master`

## Pin: Grandine

- Repo: https://github.com/grandinetech/grandine
- Branch: `develop`
- HEAD SHA at check time: `6ae713fca1fe6620ef7e45b864c3e136a767b1c1`

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
