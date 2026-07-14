# PR thread harvesting

This procedure is reusable drafting material for phase specifications
that harvest pull-request evidence. Applicable requirements must be
incorporated into each self-contained phase specification.

Preferred (REST, all three endpoints, paginated):
```
gh pr view <n> --repo <owner/repo> --json title,state,body,baseRefName,headRefName,headRefOid
gh api repos/<owner/repo>/issues/<n>/comments --paginate
gh api repos/<owner/repo>/pulls/<n>/comments --paginate
gh api repos/<owner/repo>/pulls/<n>/reviews --paginate
```
For thread resolution state, prefer GraphQL:
```
gh api graphql -f query='query{repository(owner:"<owner>",name:"<repo>"){pullRequest(number:<n>){reviewThreads(first:100){pageInfo{hasNextPage endCursor},nodes{isResolved,comments(first:50){pageInfo{hasNextPage endCursor},nodes{author{login},body,path,createdAt}}}}}}}'
```
Pagination must be complete before thread evidence is published: REST
harvesting uses `--paginate`; for GraphQL, if any `hasNextPage` is
true in the saved output, stop and report before publication — do not
improvise pagination and do not treat a truncated thread set as
complete.

Live harvesting and manual fallback are mutually exclusive modes; a
session runs in exactly one, declared by its phase specification. The
fallback mode is usable only when a previously recorded decision names
the exact raw-artifact path(s) of a manual export completed before the
session, and the phase specification declares those paths as input. A
session must never invent, guess, or create a fallback path during
execution. If the live route is unavailable and no such recorded
fallback exists, stop and report; do not continue with partial
PR-thread evidence.
**Rule:** resolved/unresolved state may be incomplete regardless of method. A `reviewer-contested` classification must cite the specific comment present in raw evidence — never an inferred thread state.
