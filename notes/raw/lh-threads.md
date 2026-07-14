## `gh pr view 39 --repo eth-act/lighthouse --json title,state,body,baseRefName,headRefName,headRefOid,baseRefOid,isDraft,mergedAt,closed`
```
{"baseRefName":"unstable","baseRefOid":"dfb259171a65cacd6db57b8874af8f543cabcb7a","body":"## Description\n\nAdds optional EIP-8025 execution proof support on top of the updated unstable branch.\n\nThis wires proof types, proof-engine client integration, proof status tracking, execution proof gossip/RPC, HTTP pool submission, and proof catch-up sync. Proof validity remains optional for fork choice: payload validity is not flipped to valid unless normal execution validation succeeds, with proof-backed promotion guarded behind non-default quorum configuration.\n\n## Additional Info\n\nBase branch eth-act/unstable was updated to upstream/unstable at dfb259171 before opening this draft.\n\nValidation run locally:\n\n- cargo fmt --all\n- env -u RUSTC_WRAPPER cargo test -p beacon_chain latest_status_with_valid_proofs_ignores_empty_and_unconfigured_statuses\n- env -u RUSTC_WRAPPER cargo test -p network servable_missing_proofs_starts_at_finalized_slot --lib\n- env -u RUSTC_WRAPPER cargo check -p network\n- In a temporary old optional-proofs worktree, test::test_proof_engine_sync passed with the equivalent requester-side finalized-window clamp applied, then the temporary patch was reverted\n\nDraft because the migrated simulator proof-engine test crate is not yet present on this branch and the proof-sync design still needs review.","closed":false,"headRefName":"optional-proofs-unstable","headRefOid":"0dd6c3b8cf3b1eece82a0a7ee87282a222d93bf5","isDraft":true,"mergedAt":null,"state":"OPEN","title":"Add optional EIP-8025 execution proofs"}
```

## `gh api repos/eth-act/lighthouse/issues/39/comments --paginate`
```
[]
```

## `gh api repos/eth-act/lighthouse/pulls/39/comments --paginate`
```
[]
```

## `gh api repos/eth-act/lighthouse/pulls/39/reviews --paginate`
```
[]
```

## `gh api graphql -f query='query{repository(owner:"eth-act",name:"lighthouse"){pullRequest(number:39){reviewThreads(first:100){pageInfo{hasNextPage endCursor},nodes{isResolved,comments(first:50){pageInfo{hasNextPage endCursor},nodes{author{login},body,path,createdAt}}}}}}}'`
```
{"data":{"repository":{"pullRequest":{"reviewThreads":{"pageInfo":{"hasNextPage":false,"endCursor":null},"nodes":[]}}}}}
```

