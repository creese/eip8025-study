# Phase completion audits

Before advancing past a phase, run a dedicated audit session for the
completed phase.

Declared inputs: the completed phase's executable specification, the
published outputs named by its `Done when` criteria, and any source,
evidence, manifest, log, or control artifact that the specification
requires to evaluate those criteria. Apparent relevance alone does not
authorize additional inputs.

If the completed phase has no executable phase specification under
`CLAUDE.md`, report `BLOCK`. A design brief is not sufficient evidence
that the phase was validly executed.

Verify every criterion in the `Done when` section against the declared
inputs. Read-only inspection commands over the declared inputs (e.g.
grep, wc, sha256sum, field-count checks) are permitted. The audit
session must not modify, create, or delete any study artifact, and
must not create or revise the next phase specification.

Where a criterion records a session-time fact that cannot be
re-established from declared durable artifacts, report it as `NOT
RE-VERIFIABLE` and identify the surviving state that was checked. If
neither the fact nor a relevant surviving state can be checked, treat
the criterion as unmet and report `BLOCK`.

Do not describe such a criterion as independently verified. The audit
report must clearly distinguish:

- independently verified criteria;
- criteria supported only by surviving state;
- unmet criteria.

The user may explicitly accept criteria supported only by surviving
state and authorize advancement, but they are not independently
verified.

Report exactly one verdict:

- `PASS`: every `Done when` criterion was independently verified.
- `REVIEW REQUIRED`: no criterion was found unmet, but one or more
  criteria are `NOT RE-VERIFIABLE`.
- `BLOCK`: the phase has no executable phase specification, a required 
  declared input is absent, authoritative inputs conflict, or one or 
  more criteria are unmet.
  
For each criterion, report its result and the declared artifacts or
checks used to reach that result. 

If any criterion is unmet, report `BLOCK`, identify the specific unmet
criterion, and stop. The audit result is session output; create no
files.

Specification authoring may begin only after the audit reports `PASS`,
or after the user explicitly accepts a `REVIEW REQUIRED` result and
authorizes advancement.
