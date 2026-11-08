---
name: pr-review-workflow
description: Team SOP for shipping code through GitHub - issue first, trunk-based branches, small Conventional Commits, structured pull requests with a ticket ID in the title, and a code review and merge gate. Use it whenever you are about to commit, create a branch, open or update a pull request, write a PR description, review a PR, respond to review comments, or merge. Also use it to set up contribution rules in a repository (PR and issue templates, CONTRIBUTING, branch rulesets, PR title checks), even if the user only says "push this", "ship it", "open a PR", or "review PR 12".
---

# PR & Code Review Workflow

This is a small-batch, trunk-based way to get changes into a GitHub repository.
Each change starts as an issue and lives on a short-lived branch. It lands as
small Conventional Commits and merges through a reviewed pull request once CI
passes. The goal is a history anyone can follow: why each change exists, a
review someone can finish in one sitting, and a revert that cleanly undoes one
thing.

Check the repository's own rules before applying these (CONTRIBUTING.md, the PR
template, CI, existing branch names). Where the repo has a rule, it wins. Where
it says nothing, follow this workflow. If the user wants this workflow adopted
in a repo that doesn't use it yet, read `references/adopting.md`.

## Authoring a change

1. **Start with an issue.** Find or open an issue that states the problem and
   the approach you plan to take. For anything non-trivial, get the approach
   confirmed on the issue before coding. This avoids duplicate work and
   solutions that don't fit. The issue number is the ticket ID used in the PR
   title.
2. **Branch from trunk.** Trunk is `main`, or whichever branch the repo uses as
   trunk. Name the branch after the kind of change: `feature/<short-description>`,
   `fix/<bug-description>`, `chore/<maintenance>`. Never commit directly to
   trunk. The only exception is an emergency or incident hotfix, and even then a
   fast PR is better.
3. **Commit in small steps.** A commit is one logical change, about 5–100
   lines, that you can describe in one sentence. Commit every 15–60 minutes of
   active coding, or whenever a function, a fix, or a logical step is done. If
   you pause work, you should be able to commit first. Keep a refactor separate
   from a feature, and a fix separate from reformatting. Write messages in
   Conventional Commits format: `type(scope): description`. Details are in
   `references/repository-rules.md`.
4. **Test before every commit.** All tests pass, new behavior has new tests,
   lint is clean, and you've checked the change locally. Reviewers should spend
   their time on design and correctness, not on failures CI would have caught.
5. **Open the PR into trunk.**
   - Title: a Conventional Commit plus the ticket ID, e.g.
     `feat(auth): add JWT refresh token (#42)`.
   - Body: follow `assets/pull_request_template.md` (Summary, Problem, Solution,
     Test Evidence, Deployment Prerequisite / Breaking Changes, Notes), and link
     the issue with `Closes #42`.
   - Scope: one problem per PR, ideally under about 500 changed lines. Split
     larger work into stacked PRs.

   Details are in `references/pull-requests.md`.
6. **Respond to every review comment.** For each one, fix it, ask a question, or
   explain why you disagree. Don't apply a fix you don't understand. Push the
   follow-ups as new commits, then re-request review and point out what changed.

## Reviewing a change

The full checklist is in `references/code-review.md`. In short: check
correctness (including edge cases), readability, fit with the architecture,
meaningful tests, and updated docs. Mark each comment as **required** or
**nit** (optional), explain why, and ask a question instead of guessing when
something is unclear. Don't block a PR on trivial issues. The goal is better
code, not criticism of the author.

## Merging

Merge only when all of these are true:

- All CI checks pass.
- Every review discussion is resolved.
- The required reviewers have approved. For a solo maintainer, the review has
  been done and recorded on the PR.
- The commit messages, or the squash title, follow Conventional Commits.

If CI fails, update the PR before merging. Never merge a red PR.

## Never commit

`.env` files, API keys or other credentials, or logs.

## Working as an agent

- Don't push to trunk or force-push a shared branch.
- Don't merge a PR unless the user explicitly gave you the merge role.
- For non-UI changes, test evidence is the commands you ran and their results.
  For UI changes, include screenshots or a short recording that covers the
  normal flow and a few edge cases.
- When you open several PRs at once, make sure they touch different files, or
  stack them, so that merging one doesn't conflict with the others.

## Reference files

- `references/repository-rules.md`: commit frequency and size, Conventional
  Commits, branching, documentation and security standards.
- `references/pull-requests.md`: PR size, stacked PRs, required contents, and
  the description template.
- `references/code-review.md`: reviewer goals and checklist, comment
  etiquette, responding to feedback, and the merge gate.
- `references/adopting.md`: how to bring this workflow into an existing repo
  (mapping branches, templates, CI checks, rulesets, solo-maintainer setup).
- `assets/pull_request_template.md`: a drop-in
  `.github/PULL_REQUEST_TEMPLATE.md`.
