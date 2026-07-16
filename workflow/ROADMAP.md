# EIP-8025 Grandine Proposal Study — Roadmap

This file defines phase order, primary outputs, handoffs, and human
checkpoints.

## Authority

The document-authority model is defined in `CLAUDE.md`, under
“Document authority.”

This file contains phase navigation only. Future-phase design briefs
are stored under `workflow/phase-briefs/`. The phase-completion-audit and specification-authoring procedures are
stored in `workflow/PHASE-AUDIT.md` and `workflow/SPEC-AUTHORING.md`.

## Phase map

| Phase | Purpose | Primary output | Next step |
|---|---|---|---|
| 0 | Verify the environment and pin all required refs | `notes/refs.md` | Phase 1a-1 |
| 1a-1 | Harvest EIP PR evidence | `notes/raw/eip-manifest.tsv`, `notes/raw/eip-files/`, `notes/raw/eip-threads.md` | Phase 1a-2 |
| 1a-2 | Harvest the consensus-spec snapshot | `notes/raw/spec-manifest.txt`, `notes/raw/spec-files/`, `notes/raw/spec-todos.txt` | Phase 1a-3 |
| 1a-3 | Harvest complete pre-PR and post-PR EIP text | `notes/raw/eip-8025-pre-pr.md`, `notes/raw/eip-8025-post-pr.md` | Phase 1b |
| 1b | Build the normative requirement inventory | `notes/matrix/requirements.tsv` | Phase 2a |
| 2a | Harvest Lighthouse PR evidence | `notes/raw/lh-*` | Phase 2b |
| 2b | Classify Lighthouse files and map them to requirements | `notes/matrix/lh-files.tsv` | Phase 2bq |
| 2bq | Distill P2b session-report leads into a durable non-evidence lead register | `notes/leads/p2b-lead-register.md` | Phase 2c |
| 2c | Synthesize behavior clusters and reconcile orphan evidence | `notes/02-clusters.md` | Phase 3a |
| 3a | Harvest Grandine layout and search evidence | `notes/raw/gr-*` | Phase 3b |
| 3b | Map Grandine implementation surfaces or cited absences | `notes/matrix/gr-surfaces.tsv` | Phase 4 |
| 4 | Compare requirements, Lighthouse evidence, and Grandine surfaces | `notes/matrix/gap.tsv` | Phase 5 |
| 5 | Harvest ecosystem risks, then synthesize the risk register | `notes/raw/risks-*`, `notes/05-risks.md` | Phase 6 |
| 6 | Classify implementation scope | `notes/matrix/scope.tsv` | Phase 6z |
| 6z | Checkpoint G: make the human go/no-go and maintainer-question decisions | Decision in `notes/refs.md` | Phase 7, narrower scope, or stop |
| 7 | Critique and verify the human-written proposal | `notes/draft/proposal.md` | Final submission review |
