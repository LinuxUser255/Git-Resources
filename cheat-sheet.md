# Ultimate Git Cheat Sheet – 2025 Edition

<br>

**For everyone: student → junior → senior → wizard**


```markdown
# Ultimate Git Cheat Sheet – 2025 Edition
For everyone: student → junior → senior → wizard
Perfect for daily or any serious repo

## Repository Info (MyRepo example)
Main repo: git@gitlab.com:UserAccount/myrepo.git
Primary branches: main (production), dev (next release), rebrand-myrepo-fleet (long-running rebrand)

## Essential Git Commands – When, Why & Real-World Examples

### Status & Information – “Where am I and what’s changed?”

| Command                                    | When you use it                                                                                 | Example scenario (CryptoAnalysisBot)                                      | Pro tip for seniors/experts                                      |
|------------------------------------------------|--------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|------------------------------------------------------------------|
| `git status`                                   | Literally every single time you touch the repo                                                   | “Did I forget to add that new horizon config?”                                   | Use `git status -sb` for ultra-compact view                      |
| `git branch -vv`                               | You’re unsure if your branch is tracking the right remote or is ahead/behind                    | “Why is my portfolio bugfix not on origin?”                                      | Shows [ahead 3, behind 1] at a glance                            |
| `git branch -a`                                | You need to see every local + remote branch in one list                                           | “Is someone else already working on aladdin-v2?”                                 | Pipe to grep: `git branch -a | grep aladdin`                     |
| `git --no-pager log --oneline -10`             | Quick look at recent history without less                                                        | “What was the last time I touched the VaR calculator?”                            | Great in scripts / CI                                            |
| `git log --oneline --graph main..feature`      | Visualise what your feature branch added compared to main                                        | See exactly what the rebrand branch changed                                      | Use alias `glg`                                                  |
| `git branch --contains <hash>`                 | Find which branches still have a specific commit (e.g., a hotfix)                                | “Which branches still have that old HOLD-signal bug?”                             | Lifesaver before deleting branches                               |

### Fetching & Pulling – “Bring the world up to date”

| Command                              | When to use                                                                                 | Typical situation                                                                 | Safer alternative for experts                     |
|--------------------------------------|---------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------|---------------------------------------------------|
| `git fetch --all --prune`            | Every morning / before any merge / after someone says “I pushed”                           | Clean up-to-date view of all remote branches and delete gone ones                   | Run this instead of plain `git pull`              |
| `git pull --rebase origin main`      | You want a perfectly linear history (most teams prefer this)                                | Updating your feature branch before opening an MR                                 | Prevents unnecessary merge bubbles                        |
| `git pull` (merge style)             | Only when the team explicitly allows merge commits                                           | Quick personal experiments                                                        | —                                                 |

### Pushing Changes – “Send your work to the server”

| Command                                          | When it’s safe and correct to use                                                                 | Real example                                                                        |
|--------------------------------------------------|---------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
| `git push -u origin my-branch`                   | First push of a new branch → sets upstream tracking                                                | `git push -u origin feature/aladdin-dashboard`                                       |
| `git push --force-with-lease`                    | You rebased or amended commits and need to update remote (safer than --force)                     | After interactive rebase cleaning up 15 messy commits                               |
| `git push origin --delete old-garbage-branch`    | Remote cleanup after the branch was merged and deleted locally                                    | Housekeeping after the rebrand is finished                                           |

### Branching – “Isolate work without breaking main”

| Command                                          | Typical use case                                                                                   | Example (your bot)                                                   |
|--------------------------------------------------|----------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| `git checkout -b fix/portfolio-persistence`      | Starting a hotfix                                                                                  | Portfolio holdings vanish on restart → immediate branch               |
| `git branch backup-$(date +%Y%m%d-%H%M)`         | Right before a dangerous rebase or merge                                                           | `git branch backup-20251206-1430` before merging rebrand branch     |
| `git branch -m old-name new-name`                | Renaming a branch locally (and usually push with `-u`)                                             | `git branch -m rebrand-myrepo-fleet → fleet-integration`       |
| `git branch -D nasty-branch`                     | Force-delete a local branch you no longer need                                                     | Clean up after experimentation                                       |

### Merging vs Rebasing – “How to integrate work”

| Strategy                     | When to pick it                                                                                 | Example command                                                            |
|------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------|
| Merge (--no-ff)              | You want history to show when a feature was completed (most company policies)                    | `git merge --no-ff feature/aladdin-v2`                                         |
| Rebase                       | You want a clean, linear history and are still working on the branch                             | `git rebase main` → then force-with-lease push                                 |
| Interactive rebase (-i)      | Clean up, squash, reorder, edit, or split commits before merging/PR                             | `git rebase -i HEAD~8` → squash 8 tiny “fix typo” commits into one meaningful commit |

### Git Grep – “Find anything, anywhere, instantly”

| Command                                   | Real-world win                                                                                 |
|-------------------------------------------|------------------------------------------------------------------------------------------------|
| `git grep -n "OLD_NAME`                  | Find every leftover string during a rebrand                                                    |
| `git grep "TODO" -- "*.py"`               | Quick audit before a release                                                                   |
| `git grep -C3 "KrakenAPI"`                | See surrounding context when debugging authentication                                            |

### Diff & Comparison – “What actually changed?”

| Command                                          | Perfect moment                                                                                     |
|--------------------------------------------------|---------------------------------------------------------------------------------------------------|
| `git diff`                                       | See unstaged changes right now                                                                     |
| `git diff --staged`                              | Review exactly what will go into the next commit                                                  |
| `git diff main...feature-branch` (three dots)    | Show only what the feature branch adds on top of main (ignores main’s new commits)                |
| `git diff --name-status release/v1.2..main`      | Quick list of files touched since last release                                                     |

### Tagging – “Mark releases forever”

| Command                                          | When you tag                                                                      |
|--------------------------------------------------|------------------------------------------------------------------------------------|
| `git tag -a v2.3.0 -m "Live trading ready"`      | Before deploying to production                                                      |
| `git push origin v2.3.0`                        | Make the tag visible to the team and CI/CD                                          |

### Cleanup & Maintenance – “Keep the repo healthy”

| Command                                          | When to run it                                                                      |
|--------------------------------------------------|-------------------------------------------------------------------------------------|
| `git gc --prune=now --aggressive`                | Repo feels slow or >500 MB                                                          |
| `git branch --merged main | grep -v "\*\|main" | xargs -r git branch -d` | Weekly house-cleaning of merged branches                                      |

### Emergency & Recovery Commands – “Save your bacon”

| Command                                          | Situation                                                                          |
|--------------------------------------------------|------------------------------------------------------------------------------------|
| `git reflog` → `git checkout -b rescued-branch <hash>` | “I deleted my branch!!” → recover it in 30 seconds                          |
| `git reset --hard origin/main`                   | Nuke local mess and match remote exactly (use only on personal clones)              |
| `git stash` / `git stash pop`                    | Quick context switch without committing nothing                                        |
| `git cherry-pick abc1234`                        | Bring one golden commit from another branch                                          |
| `git revert abc1234`                             | Undo a commit that’s already published (creates reversing commit)                   |

### One-Liners You’ll Use Every Day

```bash
# How many commits ahead/behind am I?
git status -sb

# Show me the last tag and how far I am from it
git describe --tags --abbrev=0

# Who’s been touching the portfolio code lately?
git shortlog -sn --since="2 weeks ago" -- src/portfolio/

# Find the commit that removed a file
git log --diff-filter=D --summary | grep delete

# Instantly open the repo in browser (GitLab/GitHub)
git remote get-url origin | sed ...   # or just use `glab repo view` / `gh repo view`
```

### Recommended Aliases (add to ~/.bashrc or ~/.zshrc)

```bash
alias gs='git status -sb'
alias gl='git --no-pager log --oneline -10'
alias gla='git log --oneline --all --graph'
alias gd='git diff'
alias gdc='git diff --cached'
alias gp='git push'
alias gpf='git push --force-with-lease'
alias gco='git checkout'
alias gb='git branch'
alias gba='git branch -a'
alias gm='git merge'
alias gr='git rebase'
alias gri='git rebase -i'
alias gclean='git branch --merged main | grep -v "\*\|main\|dev" | xargs -r git branch -d'
```

### Final Wisdom from a 20+ Year Veteran
- `fetch --prune` + `pull --rebase` = happy linear history
- `--force-with-lease` is the only force push you should ever use
- Interactive rebase is the single most powerful history-cleanup tool in existence
- When in doubt → `git reflog` will save you 99 % of the time

You now have a cheat sheet that grows with you from first-year CS student to the grizzled maintainer of a 40 k-line security tooling repo.

```

Copy-paste this entire thing into your personal wiki/Notion/Obsidian — it’s the one Git sheet you’ll actually keep forever.
```
