# Adopting the Workflow in an Existing Repository

Use this when a repository should start following the workflow. Treat the
adoption itself as normal work: open an issue for each piece and land each one
as its own small PR.

## 1. Settle the decisions first

Ask the maintainer about anything the repo can't tell you:

- **Branch mapping.** The workflow expects `main` as trunk and `release` as the
  current production version. If the default branch has another name (say
  `production`), choose one of these:
  - keep it as trunk and document the mapping (least disruptive)
  - add `main` as trunk and keep the old branch as the release branch
  - rename it to `main` and add a `release` branch
- **Ticket IDs.** Usually the GitHub issue number (`#42`). Use an external
  tracker key (e.g. `ABC-42`) if the team has one.
- **Who reviews and merges.** See the solo-maintainer note below.
- **Language.** Repository docs and templates stay in the language the project
  already uses.

## 2. Moving trunk to a new branch

If you add `main` as trunk:

1. Create `main` from the current default branch and set it as the default
   (`gh repo edit <owner/name> --default-branch main`).
2. Retarget open PRs: `gh pr edit <n> --base main`.
3. Update CI so `push` and `pull_request` run on `main` (and still on the
   release branch).
4. Check how releases are published. Tag- or release-triggered publishing is
   unaffected. Branch-triggered publishing needs the new branch name.
5. Document the release flow, i.e. how `main` gets promoted to the release
   branch.

## 3. Files to add or update

- `.github/PULL_REQUEST_TEMPLATE.md`: the template from
  `../assets/pull_request_template.md`, keeping any project-specific checklist
  items the repo already had.
- `.github/ISSUE_TEMPLATE/`: bug report, feature request, and `config.yml`
  pointing security reports to `SECURITY.md`.
- `CONTRIBUTING.md`: issue first, branch names, commit size and Conventional
  Commits, PR title format with the ticket ID, review and merge rules, and the
  release flow.
- `README.md`: overview, tech stack, setup and run, environment variables
  (state "none" if there are none), deployment or release.
- `docs/adr/`: an ADR template, plus a first ADR recording the branching
  decision.

## 4. CI enforcement

Add a lightweight `pull_request` workflow that checks:

- the title matches `type(scope)!: description (#<ticket>)`
- the body links an issue (`Closes|Fixes|Resolves #<n>`)

Implementation notes:

- Pass the title and body through `env:` and read them as shell variables.
  Never interpolate `${{ github.event.pull_request.title }}` directly into a
  `run:` script, because that allows script injection.
- Prefer an inline script. If you use a marketplace action, pin it to a commit
  SHA.
- Exempt bot PRs (e.g. Dependabot) from the ticket rule.
- Give the job a stable name so it can be a required status check.

## 5. Ruleset on trunk

Protect trunk with a repository ruleset (Settings → Rules, or the API):

- require a pull request before merging
- require status checks (the CI jobs and the PR-conventions job)
- require conversation resolution
- block force pushes and deletion
- bypass: admins only, in "pull request" mode, for incident hotfixes

Example (`gh api -X POST repos/<owner>/<name>/rulesets --input ruleset.json`):

```json
{
  "name": "trunk",
  "target": "branch",
  "enforcement": "active",
  "conditions": { "ref_name": { "include": ["~DEFAULT_BRANCH"], "exclude": [] } },
  "bypass_actors": [
    { "actor_id": 5, "actor_type": "RepositoryRole", "bypass_mode": "pull_request" }
  ],
  "rules": [
    { "type": "deletion" },
    { "type": "non_fast_forward" },
    {
      "type": "pull_request",
      "parameters": {
        "required_approving_review_count": 1,
        "dismiss_stale_reviews_on_push": true,
        "require_code_owner_review": false,
        "require_last_push_approval": false,
        "required_review_thread_resolution": true
      }
    },
    {
      "type": "required_status_checks",
      "parameters": {
        "strict_required_status_checks_policy": false,
        "required_status_checks": [{ "context": "<CI job name>" }]
      }
    }
  ]
}
```

Add the ruleset only after the status checks it requires have run at least once
on a PR, so that their names exist.

## 6. Solo maintainers

GitHub doesn't let you approve your own PR. With a single maintainer, set
`required_approving_review_count` to `0` and keep the review step anyway. Post
the review as a PR comment (required vs nit), fix the required items, then
merge. The review is still recorded on the PR, and CI plus resolved
conversations remain enforced.

## 7. Merge method

Squash-merge with the PR title as the commit subject, so trunk history reads as
Conventional Commits with ticket IDs:

```bash
gh pr merge <n> --squash --delete-branch --subject "<PR title>"
```
