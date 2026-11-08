---
name: remote-orchestrator
description: Run the local Claude Code session as an orchestrator while a nested worker session on a remote SSH host (cloud VM, dev box, homelab) does the actual processing - cloning, installing, coding, testing, committing and opening PRs. The orchestrator plans, briefs, reviews and merges. Use when the user wants work to "run on <host>", "via ssh", "in my remote/VM/OCI/server environment", or "in a nested session", or asks this session to coordinate, review or merge rather than code.
---

# Remote Orchestrator

There are two roles:

- **Orchestrator** (the local session) settles decisions with the user, writes
  briefs, reviews the worker's PRs, merges, and changes repository settings. It
  doesn't edit project code locally. The local checkout may be stale, and the
  point of this setup is that processing happens on the remote machine.
- **Worker** (a nested session on the SSH host) does all processing in the
  remote checkout, then reports back with links and test results.

The remote box carries the heavy work (dependency installs, builds, test
suites)
and usually matches the CI or production OS. The local session stays light and
keeps the conversation with the user.

Pair this skill with `pr-review-workflow`. The worker follows it as an author,
and the orchestrator follows it as reviewer and merger.

## 1. Preflight the host

From the orchestrator, check the host before starting a worker. A worker that
finds a missing tool halfway through wastes a whole turn.

```bash
ssh -o BatchMode=yes -o ConnectTimeout=10 <host> '
  hostname
  ls -d <project-dir> && git -C <project-dir> status -sb
  command -v git gh <runtime-tools>
  gh auth status'
```

- **Unreachable or prompting for a password:** stop and ask the user. Don't
  work around their SSH config.
- **Project folder missing:** clone it, e.g.
  `ssh <host> 'gh repo clone <owner/name> <project-dir>'`, then check out the
  base branch.
- **`gh` not authenticated or tools missing:** ask the user to fix it on the
  host. Never copy credentials over.

## 2. Settle open decisions before briefing

The worker only sees its brief. Every open question in the brief becomes either
a guess or a stalled session. Ask the user first about anything the repo can't
answer: branch model, scope, who merges, language, naming. Put the answers in
the brief, quoting the user's own words where precision matters.

## 3. Start the worker

**Claude desktop app (Code tab):** start a new session on the saved SSH
connection with the project folder as its working directory. From an agent,
call `start_session` with `target: "ssh"`, `host: "<saved connection name>"`,
`cwd: "<project-dir>"`, a fresh context, and wake-on-done, so the orchestrator
resumes when the worker finishes its turn.

**Claude Code CLI:** run the worker inside tmux on the host so it survives
disconnects:

```bash
scp brief.md <host>:<project-dir>/.brief.md
ssh -t <host> 'tmux new -As worker "cd <project-dir> && claude"'
```

Paste the brief as the first message, or run it headless with
`claude -p "$(cat .brief.md)"`. Keep `.brief.md` out of git.

## 4. Write a self-contained brief

Include:

- **Goal and definition of done.** Name the concrete outputs, e.g. "one issue
  and one PR per item, all CI green".
- **Where:** the host, the project folder, the base branch, and the repo
  (`owner/name`).
- **Decisions already made** and the constraints that follow from them.
- **Workflow:** follow `pr-review-workflow`, and run the repo's own local
  quality gates before every commit.
- **Boundaries:** what the worker must not do (merge, change repository
  settings, push to trunk or release branches, force-push, touch unrelated
  files).
- **Conflict plan:** when it opens several PRs at once, each touches different
  files, or they're stacked.
- **Report format:** issue and PR URLs, what each changes, gate results, and
  open questions. Then end the turn so the orchestrator can review.

## 5. Review, merge, iterate

When the worker reports:

1. Treat the report as a claim, not proof. Read each PR's diff
   (`gh pr diff <n>`) and CI status (`gh pr checks <n>`) yourself.
2. Review against the `pr-review-workflow` checklist. Post the review on the PR
   (`gh pr review <n> --comment -b "..."`), with each point labeled
   `required:` or `nit:`. GitHub doesn't allow approving or requesting changes
   on your own PR, so use a comment when both sessions use the same account.
3. Send required changes back to the **same** worker session, which already has
   the context, rather than starting a new one.
4. Merge only when the merge gate holds:
   `gh pr merge <n> --squash --delete-branch --subject "<PR title>"`.
5. Apply repository settings (rulesets, default branch, merge options) from the
   orchestrator, and only after the checks they reference exist.

## Safety

- Never put secrets in a brief. The worker uses the host's own credentials.
- Keep host names, IPs and private paths out of anything you publish.
- Use one writer per set of files. With several workers, give each its own
  files or its own worktree.
- Repository settings changes, deletions and public posts need the user's
  explicit go-ahead, the same as in a local session.
