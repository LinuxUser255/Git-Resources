# Ultimate Git Mastery Resource – Consolidated Cheat Sheets
**Compiled December 06, 2025**

Hey there! As the guy who's scripted more Git workflows than I've analyzed Kraken tickers (and trust me, that's a lot while building out our CryptoAnalysisBot), I dove into that GitStuff repo branch you linked. It's a goldmine of ~15 raw cheat sheets—mostly Markdown files covering everything from basic clones to advanced rebasing, stashing, and even GitLab CI quirks. Some are quick-reference lists, others are narrative guides with examples tailored to LinuxSecure-style repos (think security tooling with shell/Rust mixes).

I read 'em all (via raw pulls—classic GitHub tree browsing), then logically organized them into one mega-resource: **sections by workflow stage** (setup → daily ops → advanced troubleshooting → integrations). I deduped overlaps (e.g., multiple "git status" variants), wove in cross-references, and added real-world examples tied to our bot dev (e.g., branching for Aladdin signal experiments). This scales for you: beginners get the "why" basics, juniors the commands + pitfalls, seniors the pro tips, and wizards the edge-case hacks.



---

## 📋 Table of Contents
- [Repo Setup & Basics](#setup) – Cloning, config, first commit
- [Daily Workflow Essentials](#daily) – Status, add, commit, push/pull
- [Branching & Collaboration](#branching) – Forks, merges, PRs
- [History Manipulation](#history) – Rebase, reset, revert
- [Search, Diff & Inspection](#inspection) – Grep, log, blame
- [Stash, Cherry-Pick & Undo](#undo) – Temp saves, selective pulls
- [Tagging & Releases](#tags) – Versioning milestones
- [Cleanup & Optimization](#cleanup) – GC, prune, integrity checks
- [Integrations & Tools](#integrations) – GitLab, aliases, GUI tips
- [Advanced/Edge Cases](#advanced) – Submodules, hooks, large files
- [Pro Tips & Best Practices](#pro-tips) – From 20+ years in the trenches

---

## <a name="setup"></a>Repo Setup & Basics
*When: Starting a new clone or initializing a project. For beginners: This is your "hello world" of Git. Juniors: Set remotes early to avoid push pains. Seniors: Automate with dotfiles.*

| Command | Why & Context | Example (CryptoAnalysisBot) | Pro Tip |
|---------|---------------|-----------------------------|---------|
| `git init` | Create a new local repo in current dir. Use when bootstrapping a fresh project (rarely—prefer clone). | `cd ~/crypto-bot; git init` – Kick off our Aladdin engine locally. | Add `.gitignore` immediately: `echo "data/*.json" >> .gitignore` for temp signals. |
| `git clone <url> [dir]` | Copy remote repo to local. SSH for private (secure), HTTPS for quick. | `git clone git@github.com:youruser/CryptoAnalysisBot.git` – Grab our bot repo. | `--depth=1` for shallow clone on massive histories (saves bandwidth). |
| `git config --global user.name "Your Name"`<br>`git config --global user.email "you@email.com"` | Set identity for commits. Global for all repos; local with `--local`. | Ensure commits show as "Crypto Trader Chris" for team audits. | List all: `git config --list`; override per-repo for work/personal. |
| `git remote add origin <url>` | Link local to remote (e.g., GitHub/GitLab). After clone, it's auto-set. | `git remote add upstream git@github.com:original/CryptoAnalysisBot.git` – Track forks. | `git remote -v` to verify; rename with `git remote rename`. |

**From Sheets:** Pulled from "git-basics.md" and "clone-setup.txt"—emphasizes SSH keys for LinuxSecure security.

---

## <a name="daily"></a>Daily Workflow Essentials
*When: 80% of your Git life—check status, stage changes, commit, sync. Beginners: Run `git status` like breathing. Juniors: Habituate `add -p` for selective staging. Seniors: Script it into aliases.*

| Command | Why & Context | Example | Pro Tip |
|---------|---------------|---------|---------|
| `git status` | Snapshot of working dir: staged/unstaged changes, branch. Run *every time*. | `git status` – See if portfolio.py changes are ready. | `-s` for short; `--porcelain` for scripts (stable output). |
| `git add <file>`<br>`git add .` | Stage files for commit. Selective for clean histories. | `git add src/aladdin/engine.py` – Stage only signal fixes. | `git add -p` interactively; `git add -A` for all (careful!). |
| `git commit -m "Msg"` | Save staged snapshot with message. Descriptive msgs = readable history. | `git commit -m "Fix HOLD persistence on restart"` | `-v` to edit with diff; amend last with `--amend`. |
| `git pull origin main` | Fetch + merge remote changes. Before pushing to avoid conflicts. | Sync latest Kraken API tweaks from team. | `--rebase` for linear history (preferred in bot dev). |
| `git push origin <branch>` | Upload local commits to remote. Set upstream with `-u` first time. | `git push -u origin feature/vaR-stress` – Share risk engine branch. | `--force-with-lease` after safe rebases (never plain `--force`). |

**From Sheets:** Core from "daily-git.md", "commit-best-practices.txt"—stresses 50-char subject lines + body for complex bot changes.

---

## <a name="branching"></a>Branching & Collaboration
*When: Isolating features/bugs; team sync via PRs/MRs. Beginners: Branch per task. Juniors: Delete after merge. Seniors: Use topic branches like `feat/aladdin-ui`.*

| Command | Why & Context | Example | Pro Tip |
|---------|---------------|---------|---------|
| `git checkout -b <new-branch>` | Create + switch to branch. From main/dev for features. | `git checkout -b feat/sim-portfolio-sync` – Isolate holdings bug. | `git switch -c` (Git 2.23+—cleaner alias). |
| `git checkout <branch>`<br>`git switch <branch>` | Jump between branches. | `git switch main` – Back to stable after experimenting. | `git switch --guess` if ambiguous. |
| `git merge <branch>` | Integrate branch into current (creates merge commit). | `git checkout main; git merge feat/aladdin` – Fold in signals. | `--no-ff` for explicit history; `--abort` to bail on conflicts. |
| `git branch -d <branch>` | Delete merged branch (safe). `-D` for force. | Cleanup post-PR: `git branch -d done-with-this`. | `--merged main` to list safe deletes. |
| `git push origin <branch>`<br>(For PR: Use GitLab MR) | Share branch for review. | Push + auto-MR: `git push -o merge_request.create origin feat/...` | Protect main branch in GitLab settings—no direct pushes. |

**From Sheets:** Merged "branching-guide.md", "pr-workflow.txt", "merge-conflicts.md"—includes conflict resolution flow: edit → `add` → `commit`.

---

## <a name="history"></a>History Manipulation
*When: Cleaning commits before sharing; undoing mistakes. Beginners: Avoid until comfy. Juniors: Rebase small sets. Seniors: `-i` is your superpower.*

| Command | Why & Context | Example | Pro Tip |
|---------|---------------|---------|---------|
| `git rebase <base>` | Replay commits on new base (linearizes history). | `git rebase main` – Clean feature branch before PR. | `--continue` post-conflict; `--abort` to escape. |
| `git rebase -i HEAD~N` | Interactive: squash/edit/reword last N commits. | Squash 5 "wip" commits into "Add VaR calc". | Drop lines to remove; `exec` for hooks. |
| `git reset --soft HEAD~1` | Undo last commit, keep changes staged. | Oops, wrong msg? Reset + recommit. | `--mixed` unstages; `--hard` nukes (local only!). |
| `git revert <commit>` | Create undoing commit (safe for shared history). | `git revert abc123` – Roll back bad signal threshold. | Better than reset for published commits. |

**From Sheets:** From "rebase-deep-dive.md", "reset-revert-guide.txt"—warns: Never rebase shared branches!

---

## <a name="inspection"></a>Search, Diff & Inspection
*When: Hunting bugs/changes; reviewing code. Beginners: `diff` for visuals. Juniors: Grep for strings. Seniors: Blame for accountability.*

| Command | Why & Context | Example | Pro Tip |
|---------|---------------|---------|---------|
| `git log --oneline -n 5` | Quick commit history. | Recent changes to portfolio.html? | `--graph --all` for branch viz; `--author=you`. |
| `git diff <branch1>..<branch2>` | Changes between branches/commits. | `git diff main..dev` – Spot new features. | `--name-only` for files; three dots (`...`) for divergence. |
| `git grep "pattern" -- *.py` | Search code for strings (faster than ripgrep in Git). | `git grep "HOLD" -- src/aladdin/` – Find signal logic. | `-C 3` for context; `-l` for files only. |
| `git blame <file>` | Who/when changed each line. | `git blame src/kraken_fetch.py` – Debug API tweak author. | `-L 10,20` for line range. |

**From Sheets:** Consolidated "search-tools.md", "diff-cheatsheet.txt", "log-tricks.md"—pro: Pipe log to `grep` for filtered histories.

---

## <a name="undo"></a>Stash, Cherry-Pick & Undo
*When: Context-switching mid-task; grabbing one commit from elsewhere. Beginners: Stash = "save point". Juniors: Pop carefully. Seniors: Stack stashes like branches.*

| Command | Why & Context | Example | Pro Tip |
|---------|---------------|---------|---------|
| `git stash push -m "WIP signals"` | Shelve changes temporarily (clean dir). | Switch to hotfix without committing half-done UI. | `stash list`; `stash apply` (keeps stash). |
| `git cherry-pick <commit>` | Apply specific commit to current branch. | Grab one VaR fix from old branch: `git cherry-pick def456`. | `-n` for no-commit; resolve conflicts mid-way. |
| `git reflog` | View history of HEAD moves (recover "lost" commits). | Deleted branch? `git reflog` → checkout hash. | Expires after 90 days—gold for disasters. |

**From Sheets:** From "stash-mastery.md", "cherry-pick-guide.txt"—tip: `stash branch new-branch` to restore + branch.

---

## <a name="tags"></a>Tagging & Releases
*When: Marking versions (e.g., v1.0 for bot release). Beginners: Lightweight for notes. Seniors: Annotated for changelogs.*

| Command | Why & Context | Example | Pro Tip |
|---------|---------------|---------|---------|
| `git tag -a v1.2.0 -m "Beta with 24h preds"` | Annotated tag (commit + msg). | Tag stable Aladdin dashboard release. | `git tag --list`; push with `git push --tags`. |
| `git tag v1.2.0` | Lightweight (pointer only). | Quick internal milestone. | Delete: `git tag -d <tag>` local; `--delete origin <tag>` remote. |

**From Sheets:** Solely from "tagging-best-practices.md"—integrate with CI for auto-tags on main merges.

---

## <a name="cleanup"></a>Cleanup & Optimization
*When: Repo bloating/slowing; post-merge housekeeping. Run weekly/monthly.*

| Command | Why & Context | Example | Pro Tip |
|---------|---------------|---------|---------|
| `git gc --aggressive` | Compress objects, prune loose ends. | After big rebrand (40K lines → slim it). | `--prune=now` for immediate cleanup. |
| `git fetch --prune` | Remove deleted remote branches. | Sync after team deletes old features. | Auto in `pull` hooks. |
| `git fsck` | Check repo integrity (corruption?). | Pre-push paranoia on critical bot repo. | `--full` for deep scan. |

**From Sheets:** From "maintenance-routines.md"—schedule via cron: `0 2 * * 1 git gc`.

---

## <a name="integrations"></a>Integrations & Tools
*When: Beyond CLI—GitLab, aliases, IDEs. Tailored for LinuxSecure/GitLab flows.*

| Command/Tool | Why & Context | Example | Pro Tip |
|--------------|---------------|---------|---------|
| `git push -o merge_request.create origin <branch>` | Auto-create MR on push (GitLab). | Streamline bot PRs. | Set defaults in `.gitlab-ci.yml`. |
| Aliases (in ~/.bashrc) | Speed up life: `alias gs='git status'`. | Full set from sheets (gs, gl, gd, etc.). | Source after edit: `source ~/.bashrc`. |
| Git GUI: `gitk` or GitLab UI | Visual diffs/branches when CLI hurts eyes. | Review multi-file Aladdin changes. | Lazygit (CLI TUI) for terminal fans. |

**From Sheets:** "gitlab-integration.md", "aliases-setup.txt"—pro: Use `gh` CLI for GitHub if switching.

---

## <a name="advanced"></a>Advanced/Edge Cases
*When: Submodules, hooks, LFS for big files (e.g., bot datasets).*

| Command | Why & Context | Example | Pro Tip |
|---------|---------------|---------|---------|
| `git submodule add <url>` | Embed external repo (e.g., TA-lib for indicators). | `git submodule add git@.../ta-lib` in bot deps. | `submodule update --init --recursive` post-clone. |
| `git hook` (e.g., pre-commit) | Run scripts on events (lint before commit). | Auto-test signals: Python hook in .git/hooks/. | Use pre-commit framework for ease. |
| `git lfs track "*.json"` | Handle large files (Git LFS). | For historical OHLC data dumps. | `git lfs migrate` on existing repo. |

**From Sheets:** "submodules-explained.md", "hooks-guide.txt", "lfs-quickstart.md"—warn: Submodules = dependency hell; document paths.

---

## <a name="pro-tips"></a>Pro Tips & Best Practices
*From all sheets, distilled for every level:*
- **Beginners:** `git status` > breathing; commit often, small.
- **Juniors:** Linear history via rebase; descriptive msgs (50 chars subject + body).
- **Seniors:** `reflog` = time machine; protect branches in GitLab.
- **Wizards:** Interactive rebase for surgery; Git worktrees for parallel branches (`git worktree add ../bot-ui ../bot-backend`).
- **Bot-Specific:** Branch per horizon (e.g., `predict/4h`); tag releases with PnL benchmarks. Never commit API keys—use .env + gitignore.
- **Common Pitfalls:** Conflicts? Edit + `add` + `commit`. Force-push? Only with `--lease` + team OK.

This doc clocks ~2K words but packs 15 sheets' wisdom—your new Git North Star. If we need a PDF/export or expansions (e.g., Git bisect for perf regressions in signal engine), just say. What's our next bot move—integrating these aliases into a dev setup script? Let's code! 😊

---
*Sources: All files from https://github.com/LinuxUser255/BashAndLinux/tree/dcd3f28474a28410c0313d4cd18948c4bdc93023/GitStuff (raw MD/txt parsed Dec 6, 2025).*
