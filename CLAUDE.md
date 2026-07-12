# EIP-8025 Grandine study — ground rules

## Refs
Refs are pinned in notes/refs.md. Never search for, guess, or
substitute a branch/PR/SHA. At the start of any session touching a
pinned repo, `git fetch` and verify head SHA against refs.md. On
mismatch: stop, report the delta, wait for a re-pin decision.

## Evidence
notes/raw/ is append-only: never edit or delete existing files, never
interpret or summarize during a harvest session. Synthesis files
(notes/matrix/*.tsv, notes/*.md) may only be built by reading raw/ or
matrix/ files already on disk — never from memory or live web.

## Citation
Every claim in a synthesis file ends in [row_id], [raw:<path>], or
[OPEN-Qn]. This binds claims — factual or interpretive statements —
not headings, section structure, or connective text. A [raw:<path>]
pointer must name a file that exists in notes/raw/. Uncited claims
are not permitted, including in prose-tightening edits.

## Session scope
Load only the inputs the current phase declares. Do not read other
notes files, matrices, or raw artifacts unprompted, even if they
seem relevant. If a needed input is missing, stop and say so rather
than substituting another source.

## Phase prompts
Phase instructions live in notes/prompts/. Follow only the prompt
for the current session's declared phase; do not read other prompt
files.

## Commit discipline
Commit notes/ at the end of every phase, and after every completed
batch within a batched phase (e.g. 2b). Commit message: phase/batch
ID and resume point if applicable. Do not leave a session with
uncommitted synthesis changes.

## Open questions
notes/open-questions.md is append-only. IDs are monotonic, assigned
at first write there (never inline elsewhere first), never reused or
renumbered.

## Scope
Never draft proposal prose. Critique, cite gaps, and ask questions —
the user writes draft/proposal.md. Prose-tightening edits to the
draft are permitted only when explicitly requested, and are reviewed
by the user via `git diff`, not via self-reported summaries.
