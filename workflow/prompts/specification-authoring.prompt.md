# Prompt template — phase-specification authoring

Fill every `<PLACEHOLDER>` before use. Durable behavior — authoring
procedure, prohibited actions, git-diff reporting, commit prohibition
— is governed by CLAUDE.md and workflow/SPEC-AUTHORING.md and is
deliberately not restated here.

---

Follow CLAUDE.md and workflow/SPEC-AUTHORING.md.

Audit result: `<PASS or REVIEW REQUIRED>`

I have reviewed the audit result for `<COMPLETED_PHASE>` and authorize
specification authoring.

If the audit result is `BLOCK`, or this authorization statement is
incomplete, stop and report.

Next phase: `<NEXT_PHASE>`
Authoring mode: `<create new specification or revise existing specification>`
Target file: `phases/<NEXT_PHASE_SPEC_FILE>`
Design brief: `workflow/phase-briefs/<NEXT_PHASE_BRIEF_FILE>`
Reusable workflow inputs: `<WORKFLOW_INPUT_FILES, or "none">`
Decision input: applicable decisions recorded in `notes/refs.md`
Structural references only (form, not scope):
`<STRUCTURAL_REFERENCE_PHASE_FILES, or "none">`

Deliverable: the target file as a self-contained executable
specification, followed by the report required by
workflow/SPEC-AUTHORING.md.

