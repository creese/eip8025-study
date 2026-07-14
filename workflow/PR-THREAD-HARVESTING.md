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
gh api graphql -f query='query{repository(owner:"<owner>",name:"<repo>"){pullRequest(number:<n>){reviewThreads(first:100){nodes{isResolved,comments(first:50){nodes{author{login},body,path,createdAt}}}}}}}'
```
Fallback: manual browser export into `notes/raw/` before the session,
as decided in Phase 0. If neither the preferred commands nor the
recorded fallback is available, stop and report; do not continue with
partial PR-thread evidence.
**Rule:** resolved/unresolved state may be incomplete regardless of method. A `reviewer-contested` classification must cite the specific comment present in raw evidence — never an inferred thread state.
