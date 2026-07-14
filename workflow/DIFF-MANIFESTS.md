# Diff manifests

**Diff manifests** (`notes/raw/lh-manifest.tsv`, `notes/raw/eip-manifest.tsv`): `index | path | status (A/M/D/Rxx) | old_path_if_renamed | diff_file`. Built from `git diff --name-status -M`; the reconciliation source for `Done when` criteria.

Each phase specification that builds a diff manifest must define a
deterministic construction procedure that:

* records paths exactly as emitted by `git diff --name-status -M`;
* handles each supported status (A, M, D, Rxx) with an explicit rule;
* stops and reports on any malformed line or unsupported status
  rather than improvising a row;
* validates, before publication, that every data row has exactly the
  five schema fields and that the data-row count equals the source
  name-status line count.
