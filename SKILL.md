---
name: git-pushing
description: Save work to version control and push it, in plain English, for people who do not know git. Sets up the repo if needed, sweeps for secrets before staging, writes a Conventional Commits message, pushes with upstream tracking, and reports back with a restore path. Trigger on "save my work", "commit this", "commit and push", "push this up", "push to GitHub", "back this up", "set up version control", "put this on a branch", "go back to the version that worked", or when a working change is finished and nothing has been committed yet.
---

# Git Pushing

Give someone real version control without teaching them git. They say "save my work"; you handle the repository, the secret sweep, the commit, the branch, and the push, then explain what happened in words a non-developer can act on.

## Ground rules

1. **Narrate before you act.** Every git command gets one plain sentence first: what it does, and what changes on disk or on the remote. No silent runs.
2. **Nothing destructive without an explicit yes.** Discarding changes, resetting, rewriting history, and force pushing are all stop-and-ask, every time, even if the user asked for the outcome.
3. **Secrets never get committed.** The sweep in step 3 is not optional and not skippable on the user's say-so. A committed secret is public the moment it is pushed, and removing it later means rewriting history.
4. **Speak outcomes, not git.** "Your work is saved and backed up on GitHub" beats "pushed 3 commits to origin/main". Keep raw git output for when they ask for it.
5. **One repo, one question.** Confirm the project root and the remote once at the start, then stop asking.

## Workflow

### 1. Read the room

Before touching anything, find out where you are: is this a git repository, what branch is checked out, is there a remote, what is uncommitted, and is anything already staged. Also check whether the branch has an upstream, because that decides the push command later.

Report the situation in one or two sentences. If the working tree is clean and nothing is unpushed, say so and stop. There is nothing to save.

### 2. Set up the repository if there is not one

If this is not a repository yet:

- Initialize it, and name the first branch `main`.
- Write a `.gitignore` before the first commit, matched to the stack you actually see in the folder: dependency folders, build output, local environment files, editor and OS junk. Do this first so the first commit is already clean.
- Ask where the backup should live before creating anything remote. Creating a repository under the wrong account is annoying to undo.
- If they want it on GitHub and `gh` is authenticated, create it and set the remote. Ask public or private explicitly, and default to private when they do not answer.

If the repository exists but has no remote, offer to add one. Local-only commits are still version control, they are just not a backup. Say that out loud so the user knows what protection they actually have.

### 3. Sweep for secrets

Look at what is about to be staged, not at the whole repository, and check for:

- Environment files and key material: `.env` and variants, `.pem`, `.key`, `.p12`, `id_rsa`, service account JSON, `credentials`, `secrets.*`.
- High-entropy strings and known key shapes inside changed files: API keys, tokens, connection strings with passwords in them, private keys, session cookies.
- Large or generated files that do not belong in history: dependency folders, build artifacts, database dumps, media the project does not need.

Anything you find gets added to `.gitignore` and left out of the commit, and you tell the user which file you excluded and why. If a secret is already tracked from an earlier commit, say so plainly: it is in history, ignoring it now does not remove it, the credential should be rotated, and cleaning history is a separate job to schedule.

### 4. Choose the branch

If the change is small, self-contained, and the user works alone, committing on the current branch is fine.

Move to a new branch first when any of these are true: the user called it an experiment or a "try", the change is large enough that they may want to abandon it, the repository has a pull request workflow, or `main` is protected. Name it after the work, not the date: `feat/loop-checkout`, `fix/login-redirect`.

Say what you did: "I put this on its own branch, so your working version is untouched until you decide to keep this."

### 5. Stage deliberately

Stage the files that belong to the change the user described. Do not sweep the whole tree by reflex. Untracked files in particular get a look before they are added, because that is how junk and secrets enter a repository.

If the working tree holds two unrelated changes, offer two commits. Do not silently bundle them.

### 6. Write the commit message

Conventional Commits, subject line under 72 characters, imperative mood, no trailing period:

```
<type>(<optional scope>): <what changed>

<optional body: why it changed, what it affects>
```

| Type | Use it for |
| --- | --- |
| `feat` | new capability the user can see |
| `fix` | corrected behavior |
| `docs` | documentation only |
| `refactor` | restructuring with no behavior change |
| `test` | tests only |
| `chore` | dependencies, config, tooling, housekeeping |
| `perf` | speed or resource improvement |

Write the subject from the diff, not from the user's request. "fix(auth): stop redirect loop on expired session" is worth reading in a year; "update files" is not. Add a body when the reason is not obvious from the subject. If the user supplied their own message, use it as given and only adjust the prefix if it is missing.

### 7. Push

Push to the remote. On a branch with no upstream, set upstream on the first push so later pushes need no arguments.

If the push is rejected because the remote moved ahead, stop. Explain that someone else's work is on the remote and yours is not on top of it yet, then offer to pull and integrate. Never resolve a rejected push with force.

### 8. Report back in plain English

Close with:

- What was saved, in one sentence about the work, not the file count.
- Where it lives now: branch name, and whether it is backed up on the remote or only on this machine.
- Anything you deliberately left out, and why.
- One line on how to get back: "If today's changes turn out wrong, ask me to take you back to the version from before this save."

## Undo playbook

Read the request, pick the smallest reversible move, describe it, then wait for a yes.

| They say | What they mean | Safe move |
| --- | --- | --- |
| "undo that commit, keep my work" | wrong message or too much bundled in | soft reset of the last commit, files stay as they are |
| "throw away today's changes" | uncommitted work is wrong | confirm loudly, then restore the tracked files; this cannot be undone |
| "go back to the version that worked" | a past commit was good | find it, show its message and date, then check it out or revert forward to it |
| "that commit is already pushed and it is wrong" | published history | revert with a new commit, never rewrite what others may have pulled |
| "I committed a password" | leaked credential | rotate the credential first, then plan the history cleanup |

Anything that rewrites published history, drops commits, or deletes a branch is a stop-and-ask, and it gets a one-line warning about what cannot be recovered.

## Never

- Force push, in any form, without an explicit and specific instruction from the user for that exact branch.
- Commit a file the sweep flagged, even if the user says it is fine.
- Amend or rebase commits that already exist on the remote.
- Commit as anyone other than the configured author, or invent an author identity.
- Delete branches, stashes, or worktrees as cleanup nobody asked for.
- Run `git add -A` on a repository you have not looked at.

## Done looks like

- The commit exists, with a message that explains the change to someone who was not there.
- Nothing secret, generated, or oversized went in.
- The branch is pushed and tracking its upstream, or the user knows the work is local-only and why.
- The user can say, in their own words, what got saved and how to get it back.
