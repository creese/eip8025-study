# Provisional cluster taxonomy

**Provisional cluster taxonomy (fixed before 2b, used by 2b and 2c):**
C1 SSZ/types · C2 gossip validation · C3 Req/Resp · C4 proof engine ·
C5 EL integration · C6 block import/forkchoice · C7 config/CLI ·
C8 validator/prover service · C9 sync · C10 tests/CI · C-unassigned.
2c may split/merge but must append a remap table
(`old_id → new_id, date`) to `notes/02-clusters.md`; row citations are
interpreted through the remap.

**Shared mistake to avoid (2b and 2c):** treating WIP scaffolding
(devnet hacks, TODOs, reverted code) as Lighthouse's intended design.
