# Pull Requests

Follow `repository-rules.md` before you open a PR.

## Purpose

Pull requests and code review make sure every change meets the project's
quality bar and fits the codebase's design. The review process aims to:

- keep code **high quality and readable**
- catch **bugs and edge cases**
- keep **coding style consistent**
- **share knowledge and collaborate** across the team

## Contribution workflow

The workflow follows a **branch and pull request model**:

1. **Create or find an issue.** Discuss the problem or feature with the
   maintainers before implementing it, and make sure it fits the project's
   direction.
2. **Create a branch.** Work on a dedicated feature branch.
3. **Implement the solution.** Follow the coding standards and add the tests
   and documentation the change needs.
4. **Test the changes.** Run unit tests, integration tests and linters, and
   confirm the feature works as expected.
5. **Open a pull request.** Give it a clear description and context.
6. **Review.** Reviewers give feedback and the author updates the PR.
7. **Approve and merge.** Once it's approved and CI passes, the PR is merged.

## Communicate early

Before you start building:

- comment on the related issue
- confirm the implementation approach with the maintainers
- avoid duplicate work and solutions that don't fit

## Keep pull requests small

Small PRs are easier to review and safer to merge:

- solve **one problem per PR**
- don't mix unrelated changes
- prefer several small PRs to one big one
- aim for a **manageable size**, often under 500 changed lines

This gives you faster reviews, easier rollbacks and lower merge risk.

For work that's too large for one PR, use **stacked pull requests**: a chain of
small PRs where each builds on the one before it.

- <https://graphite.com/blog/stacked-prs>
- <https://tinyideasxyz.medium.com/daisy-chaining-pull-requests-0d5a41a87b23>
- <https://medium.com/inside-bukalapak/smaller-faster-code-review-with-stacked-merge-request-1dc130705bc1>

## What a PR contains

### Required

- The ticket ID in the PR title, e.g. `fix(auth): handle expired token (#42)`
- A description of the change
- A linked issue (`Closes #42`)
- Tests and documentation where they apply
- Any environment variable or production config changes needed before
  deployment
- Passing CI checks

### Recommended

- Screenshots for UI changes
- A performance comparison, if relevant
- Migration instructions for breaking changes

## The PR description

Every PR explains:

- **Problem / motivation**
- **The solution**
- **How it was tested**
- **Impact or breaking changes**
- **Anything reviewers should focus on**

Template (also available as `../assets/pull_request_template.md`):

```markdown
## Summary
Brief description of the change.

## Problem
What issue or limitation this PR addresses.

## Solution
How the change solves the problem.

## Test Evidence
Add a screenshot or a short screen recording that covers the normal flow and
some edge cases.

## Deployment Prerequisite / Breaking Changes
Mention env changes, DB migrations, deleted functions, etc.

## Notes
Additional context for reviewers.
```

A clear description lets reviewers understand the change without digging
through the whole codebase. For CLI, library or docs changes, the test evidence
is the commands you ran and what they printed.
