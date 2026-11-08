# Repository Rules

## 1. Commit frequency and granularity

### Core principle

A commit should be:

- **Small and focused**: about 5–100 changed lines
- **One logical change**
- **Easy to review and easy to revert**

### How often to commit

Ideally, commit every **15–60 minutes** of active coding, or whenever:

- a function is finished
- a bug is fixed
- a logical step is done

**Rule of thumb:** if you pause work, you should be able to commit first.

### Commit size

A good commit:

- has a single concern
- can be described in one sentence
- doesn't mix a refactor with a feature, or a fix with reformatting

Good:

```text
feat: add login API integration
fix: handle empty email validation
refactor: extract auth service
```

Bad:

```text
update code
fix bugs
final fix for real this time
```

### Avoid

- Large commits (1000+ lines, many files, many concerns)
- "WIP" commits on a shared branch
- Commits from panicked coding that were never cleaned up
- Large amounts of work with no intermediate commits

## 2. Commit messages

Use [Conventional Commits](https://www.conventionalcommits.org/):

```text
type(scope): description
```

Common types:

- `feat`: new feature
- `fix`: bug fix
- `refactor`: code restructuring with no behavior change
- `docs`: documentation
- `chore`: maintenance
- `test`: tests

The scope is optional:

```text
feat(auth): add JWT refresh token
```

Mark breaking changes with `!`:

```text
feat(api)!: change response format
```

More examples:

```text
feat: add payment gateway integration
fix: handle null response on login API
refactor: simplify auth middleware
```

### Before every commit

- All tests pass
- New features come with new tests
- No lint errors
- The change has been checked locally

Testing first means reviewers can focus on **design and correctness instead of
basic failures**.

## 3. Branching strategy

Use [trunk-based development](https://trunkbaseddevelopment.com/).

### Long-lived branches

- `main`: production-ready, can be released at any time (the trunk)
- `release`: the version currently in production

There are no `develop`, `staging`, or similar branches.

### Branch names

Name the branch after the kind of change:

```text
feature/<short-description>
fix/<bug-description>
chore/<maintenance>
```

Examples:

```text
feature/payment-integration
fix/login-validation-error
```

### Rules

- No direct commits to `main`, except an emergency or incident that needs a
  hotfix.
- Every change goes through a branch and a pull request.

## 4. Documentation standard

### Every README includes

- Project overview
- Tech stack
- Setup and how to run
- Environment variables
- Deployment (optional)

### Additional docs

- Architecture diagram
- API spec
- Decision log (ADRs, Architecture Decision Records)

## 5. Security

Never commit:

- `.env` files
- API keys or other credentials
- Logs
