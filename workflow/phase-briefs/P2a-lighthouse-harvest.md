# Phase 2a design brief — Lighthouse harvest

**Status:** Authoritative drafting source until
`phases/P2a-lighthouse-harvest.md` exists as an executable phase
specification under the rules in `CLAUDE.md`.

Applicable workflow inputs:
- `workflow/DIFF-MANIFESTS.md`
- `workflow/PR-THREAD-HARVESTING.md`

1. Gate: `git fetch`; compare head SHA to pin; on mismatch, stop, log delta in `notes/refs.md`, await your re-pin decision.
2. Run the following harvest operations exactly. The phase
   specification may replace placeholders with variables, but must not
   omit or substitute an operation:
   - `git diff --name-status -M <base>..<head>` → build
     `notes/raw/lh-manifest.tsv` per
     `workflow/DIFF-MANIFESTS.md` (reconciliation source of truth)
   - `git diff --stat <base>..<head>` (human-readable summary only)
   - `git log --oneline <base>..<head>`
   - per-manifest-row `git diff <base>..<head> -- <path> > notes/raw/lh-files/<index>.diff`
   - `git diff <base>..<head> -- Cargo.toml Cargo.lock '**/Cargo.toml' > notes/raw/lh-deps.diff`
   - PR metadata/threads per `workflow/PR-THREAD-HARVESTING.md`
3. Record merge-base drift vs `sigp/lighthouse@unstable` once in
   `notes/refs.md`.

## Retained v3.2 completion goal
`notes/raw/lh-manifest.tsv` exists; per-file diff count = manifest row count; `notes/raw/lh-deps.diff` exists; threads harvested
