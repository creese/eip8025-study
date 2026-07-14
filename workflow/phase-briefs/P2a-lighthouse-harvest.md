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
   omit or substitute an operation. Destinations below name final
   published paths; staging, set validation, and no-clobber
   publication follow `CLAUDE.md` artifact governance:
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
4. PR-thread mode: live harvesting only. No manual-fallback export was
   recorded for PR #39; if the live route is unavailable, stop and
   report per `workflow/PR-THREAD-HARVESTING.md`.
5. Publication coordination: publish the validated raw harvest set
   first; append the merge-base drift record to `notes/refs.md` only
   after that publication succeeds, guarded so the record is written
   exactly once. Resume after interruption completes only the missing
   step — raw set, then drift record — never a duplicate of either. If
   only part of the raw artifact set exists at the final published
   paths, treat it as an interrupted partial publication. Resume only
   through the recovery procedure defined by the executable
   specification; do not assume that publishing the missing files
   alone is safe.

## Required published artifacts
- `notes/raw/lh-name-status.txt` — verbatim `--name-status` output
  (manifest source)
- `notes/raw/lh-manifest.tsv`
- `notes/raw/lh-diffstat.txt`
- `notes/raw/lh-log.txt`
- `notes/raw/lh-files/<index>.diff`, one per manifest row
- `notes/raw/lh-deps.diff`
- `notes/raw/lh-threads.md`
- `notes/raw/lh-harvest-receipt.txt` — durable mechanical validation
  receipt: the gate and set-validation commands with their verbatim
  outputs and exit codes (row counts, per-file diff count, byte
  counts, and checksums of the other staged artifacts), sufficient for
  a later read-only audit
- merge-base drift record appended to `notes/refs.md` (exactly once)

## Retained v3.2 completion goal
`notes/raw/lh-manifest.tsv` exists; per-file diff count = manifest row count; `notes/raw/lh-deps.diff` exists; threads harvested

## Additional completion requirements
All required published artifacts above exist;
`notes/raw/lh-harvest-receipt.txt` records the set-validation results;
the drift record exists in `notes/refs.md` exactly once.
