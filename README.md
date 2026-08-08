<p align="center"><a href="https://loop.sv-academy.org"><img src="https://raw.githubusercontent.com/sva-admin/claude-skills/main/assets/sv-academy-512.png" width="96" alt="Silicon Valley Academy"/></a></p>

# git-pushing

Version control without learning git: say "save my work" and Claude sweeps for secrets, writes the commit message, pushes to your backup, and tells you in plain English how to get back to any version.

Learn it free, step by step: [Save your work like a pro](https://loop.sv-academy.org/articles/save-your-work-like-a-pro)

## Try it

Paste this into Claude Code:

```
Install the skill from https://github.com/sva-admin/git-pushing, then save my work: commit everything with a clear message and push it to GitHub.
```

## What it does

- **Sets up version control if you have none.** Initializes the repository, writes a `.gitignore` matched to your stack before the first commit, and creates the remote backup once you have said where it should live.
- **Checks for secrets before anything is staged.** Environment files, keys, tokens, connection strings, and anything else that should never reach a public repository get excluded, and you are told what was excluded and why.
- **Writes the commit message for you.** Conventional Commits, written from the actual diff, so your history reads like a record instead of a list of "update files".
- **Branches when branching is the safe move.** Experiments go on their own branch so your working version stays untouched.
- **Reports back in words you can act on.** What got saved, where it lives, whether it is really backed up, and how to return to yesterday's version.

## What it will not do

Force push. Rewrite history that is already on the remote. Commit a flagged secret because you said it was fine. Discard your changes without asking twice. Every destructive move stops and explains what cannot be recovered before it runs.

Say "go back to the version that worked" and it finds that version, shows you its message and date, and gets you there without a single git command on your side.

## Credit

Inspired by git-pushing by sickn33 ([antigravity-awesome-skills](https://github.com/sickn33/antigravity-awesome-skills)). This is an independent SV Academy implementation: the instructions here were written from scratch, and no text or structure was copied from the original.

The SV Academy text in this repository is MIT licensed. See [LICENSE](LICENSE).

## More from Silicon Valley Academy

- All of our published skills: [github.com/sva-admin/claude-skills](https://github.com/sva-admin/claude-skills)
- Courses and free lessons: [sv-academy.org](https://sv-academy.org)
