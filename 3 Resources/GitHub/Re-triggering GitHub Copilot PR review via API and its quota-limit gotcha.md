---
title: "Re-triggering GitHub Copilot PR review via API and its quota-limit gotcha"
created: 2026-06-22
type: howto
status: seedling
source: "fb-info-project session 2026-06-22"
tags: [github, copilot, code-review, gh-cli, gotcha]
---

# Re-triggering GitHub Copilot PR review via API and its quota-limit gotcha

To re-trigger a GitHub Copilot code review on a PR from the CLI, POST to the REST requested_reviewers endpoint with login **`Copilot`** (not `copilot-pull-request-reviewer`, which returns 422 "not a collaborator"; and the GraphQL `requestReviews` mutation rejects the bot node id with "Could not resolve to User node"):

```
gh api --method POST repos/{owner}/{repo}/pulls/{n}/requested_reviewers -f "reviewers[]=Copilot"
```

Quirk: the 2xx response echoes an **empty** `requested_reviewers` array even when it worked — verify by polling `pulls/{n}/reviews` for a new review by `copilot-pull-request-reviewer[bot]` on the latest commit.

**Quota gotcha:** when the requesting user has exhausted their Copilot quota, the bot still posts a review with `state=COMMENTED` and **no inline comments**, whose body is: *"Copilot was unable to review this pull request because the user who requested the review has reached their quota limit."* So "zero new review comments" can mean *rate-limited*, not *clean* — always read the review body before concluding the code passed.
