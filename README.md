# pr-review-workflow

Claude Code skills that follow a small-batch, trunk-based way of shipping code
on GitHub, plus a pattern for running the work on a remote machine while your
local session coordinates.

| Skill | What it does |
| --- | --- |
| `pr-review-workflow` | Issue first, `feature/`/`fix/`/`chore/` branches, small Conventional Commits, PR titles with a ticket ID, a structured PR description, a reviewer checklist (required vs nit), and a merge gate. Also includes a guide to adopting the workflow in an existing repo (templates, CI title check, trunk ruleset, solo-maintainer setup). |
| `remote-orchestrator` | Use the local session as an orchestrator and a nested Claude Code session on an SSH host as the worker. The worker clones, builds, tests and opens PRs. The orchestrator settles decisions, writes the brief, reviews and merges. |

## Install

As a Claude Code plugin:

```text
/plugin marketplace add al-dioooo/pr-review-workflow
/plugin install pr-review-workflow@pr-review-workflow
```

Or copy (or symlink) the skill folders into your personal skills directory:

```bash
git clone https://github.com/al-dioooo/pr-review-workflow.git
cp -R pr-review-workflow/plugins/pr-review-workflow/skills/* ~/.claude/skills/
```

## The workflow in one screen

1. **Issue first.** Describe the problem and the approach, and confirm it with
   the maintainers. The issue number is the ticket ID.
2. **Branch from trunk:** `feature/<desc>`, `fix/<desc>`, `chore/<desc>`. No
   direct commits to `main` except incident hotfixes.
3. **Small commits:** one logical change of about 5–100 lines, in Conventional
   Commits form (`type(scope): description`). Tests and lint pass before every
   commit.
4. **Small PRs:** one problem each, ideally under 500 lines, stacked when
   larger. The title looks like `feat(auth): add JWT refresh token (#42)`. The
   body has Summary, Problem, Solution, Test Evidence, Deployment Prerequisite /
   Breaking Changes, and Notes.
5. **Review:** correctness, readability, architecture, tests, docs. Each
   comment is labeled `required:` or `nit:`, with the reasoning.
6. **Merge** when CI is green, every discussion is resolved, and the required
   approvals are in. Squash with the PR title as the commit subject.

Never commit `.env` files, credentials, or logs.

The skill files in [`plugins/pr-review-workflow/skills`](plugins/pr-review-workflow/skills)
have the full rules and the reasoning behind them.

## License

[Apache-2.0](LICENSE)
