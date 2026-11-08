# Code Review

## Review process

1. Assign a reviewer
2. Feedback
3. Discussion
4. Revision
5. Repeat until approved
6. Merge

Each round improves the change through collaboration.

## Goals

Reviewers focus on:

- correctness
- maintainability
- performance
- security
- consistency with the architecture

The goal is **to improve the code, not to criticize the author**.

## What to check

**Correctness**
- Does the code solve the problem?
- Are edge cases handled?

**Readability**
- Is the code easy to understand?
- Are any functions too large or too complex?

**Architecture**
- Does the change fit the system design?
- Does it add unnecessary coupling?

**Testing**
- Are there tests, and do they test something meaningful?

**Documentation**
- Are comments and docs updated where behavior changed?

## How to give feedback

- Be respectful and constructive.
- Explain the reasoning behind each comment.
- Make clear which comments are which:
  - **Required changes** must be addressed before merge.
  - **Optional suggestions (nit)** are up to the author.
- If something is unclear, ask a question instead of assuming.
- Don't block on trivial issues.

A simple convention is to start each comment with `required:` or `nit:`.

## Responding to review feedback (authors)

- Address every review comment.
- Ask for clarification when you need it.
- Explain your reasoning when you disagree.
- Don't apply fixes you don't understand.

After updating:

- re-request review
- point out the important changes

Keep review discussions collaborative.

## CI and merge requirements

Before merging, make sure:

- all CI checks pass
- all discussions are resolved
- the required reviewers have approved
- commit messages follow the guidelines

If CI fails, update the PR before merging.

## Summary

**Authors**

- Communicate early
- Keep PRs small
- Write clear descriptions
- Follow the commit conventions
- Test thoroughly

**Reviewers**

- Focus on improvement
- Give clear, actionable feedback
- Don't block on trivial issues
- Encourage maintainable code

## Further reading

- <https://www.swarmia.com/blog/a-complete-guide-to-code-reviews>
- <https://google.github.io/eng-practices/review/reviewer/looking-for.html>
