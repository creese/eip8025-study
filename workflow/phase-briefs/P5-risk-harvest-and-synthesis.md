# Phase 5 design brief — risk harvest and synthesis

**Status:** Authoritative drafting source until
`phases/P5-risk-harvest-and-synthesis.md` exists as an executable phase
specification under the rules in `CLAUDE.md`.

**`notes/05-risks.md`**: `risk_id | description | likelihood (H/M/L) | impact (H/M/L) | evidence ([raw:<path>] or matrix row) | mitigation or 'accept' | proposal section it affects`.

## Retained v3.2 drafting source
**P5 — Risk harvest then synthesis (Claude Code, then Chat):**
> (Harvest, Claude Code) You may use network, GitHub, and web searches for this session, but every finding must be saved to a raw file before any synthesis: consensus-specs test-vector status for EIP-8025 → `notes/raw/risks-spec-tests.txt`; Grandine issue-tracker search for EIP-8025/execution-proof work → `notes/raw/risks-grandine-issues.txt`; Grandine PR search → `notes/raw/risks-grandine-prs.txt`; L1-zkEVM breakout-call notes/status → `notes/raw/risks-breakout-notes.txt`; dependency analysis of `notes/raw/lh-deps.diff` (new crates, versions, licenses) → `notes/raw/risks-prover-deps.txt`. Save search queries alongside results. No conclusions in this session.
> (Synthesis, Chat) Loaded: the five `notes/raw/risks-*` files plus matrices. Fill `notes/05-risks.md` per schema; every evidence cell is `[raw:<path>]` or a matrix row.

## Retained v3.2 completion goal
All five `notes/raw/risks-*` files exist (with saved queries) before synthesis; risk table covers at minimum spec-status/stagnancy, fork drift, scaffolding, test-vector absence, prover dependencies, duplicate-effort check; every evidence cell cites `[raw:<path>]` or a matrix row
