First, preflight: confirm `git` works, `gh auth status` succeeds,
network is available, and a test `git fetch`/clone succeeds for
eth-act/lighthouse, ethereum/EIPs, and the Grandine repo. Record
results in the preflight block of `notes/refs.md`; if `gh` or
network fails, stop and I'll choose the manual-export fallback.

Then pin — do not search for or guess any repo/branch:
- `eth-act/lighthouse` PR #39
- `ethereum/EIPs` PR #11604
- Grandine at https://github.com/grandinetech/grandine, branch
  `develop` (record HEAD SHA)
- the consensus-specs ref the Lighthouse PR targets

To identify the spec ref you may read the PR description, feature-
flag names, and vendored spec paths — read only enough to identify
the ref, and do not summarize implementation behavior. If
indeterminable, pin the latest EIP-8025-relevant snapshot as
PROVISIONAL-SPEC-BASELINE and log an open question via
`open-questions.md` (next monotonic ID).

Fill `refs.md` per schema, including LH merge-base vs
`sigp/lighthouse@unstable` and the current status of
eips.ethereum.org/EIPS/eip-8025. Metadata only; no content summaries.
