# Grandine Execution Proof Study

This project is an evidence-backed study of what it would take to add [EIP-8025: Optional Execution Proofs](https://eips.ethereum.org/EIPS/eip-8025) to Grandine.

Rather than jumping straight to a design, it starts with pinned specifications and evidence from an existing implementation. The aim is to separate what the protocol requires, what Lighthouse chose to do, and what would make sense for Grandine.

## What it produces

* A structured inventory of EIP and consensus-spec requirements
* A classification of the pinned Lighthouse implementation, organized by functional area
* Mappings from those areas to Grandine modules, types, handlers, and storage
* Documented implementation gaps, risks, scope choices, and unresolved questions

## Workflow

The project moves through a series of small phases:

1. A self-contained phase specification is written.
2. The specification is reviewed and authorized.
3. The declared work is executed, and the resulting artifacts are published.
4. A separate, read-only completion audit checks the published artifacts.

The project advances only after `PASS` or an explicitly accepted `REVIEW REQUIRED`. A `BLOCK` result must be resolved first.

See [`workflow/ROADMAP.md`](workflow/ROADMAP.md) for the phase map.

## Repository map

```text
.
├── CLAUDE.md              # Project-wide rules
├── workflow/              # Roadmap, procedures, briefs, and prompts
├── phases/                # Executable phase specifications
├── notes/
│   ├── refs.md            # Pinned references and decisions
│   ├── open-questions.md  # Human-assigned open questions
│   ├── raw/               # Append-only published evidence
│   ├── matrix/            # Structured analysis
│   └── draft/             # Proposal drafts
└── .work/                 # Temporary and recovery state
```

## Start here

1. Read [`CLAUDE.md`](CLAUDE.md) for the project-wide rules.
2. Read [`workflow/ROADMAP.md`](workflow/ROADMAP.md) for the phase sequence and handoffs.
3. Open the current specification under [`phases/`](phases/) before executing any work.

For the current state of the project, check the active phase specification, the relevant matrix or batch log, and the latest audit.
