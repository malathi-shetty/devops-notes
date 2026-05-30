# Git Commands Reference

# Note: Updated from  CLI for Day 22 task

## Setup & Config

### git config
- Sets user configuration like name and email
- Example:
  - git config --global user.name "Your Name"
  - git config --global user.email "your@email.com"

### git config --list
- Displays all Git configuration values
- Example:
  git config --global --list

---

## Basic Workflow

### git init
- Initializes a new Git repository
- Example:
  git init

### git add
- Adds files to the staging area
- Example:
  git add git-commands.md
  git add .

### git commit
- Saves staged changes as a snapshot with a message
- Example:
  git commit -m "Initial commit"

---

## Viewing Changes

### git status
- Shows the current state of the working directory
- Lists which files are modified, staged, or untracked
- Example:
  git status

### git log
- Shows full commit history (hash, author, date, message)
- Example:
  git log

### git diff
- Shows changes between working directory and staging area
- Example:
  git diff

### git log --oneline
- Shows compact commit history (one line per commit)
- Example:
  git log --oneline

---

## Undo / Restore

### git restore
- Discards changes in the working directory
- Example:
  git restore git-commands.md
  
---

# Note: Updated from GitHub UI for Day 23 task

## Branching Commands

### `git branch`
- Lists all local branches
- Example:
  git branch

### `git branch -a`
- Lists all branches (local + remote)
- Example:
  git branch -a

### `git branch -r`
- Lists only remote branches
- Example:
  git branch -r

### `git branch --show-current`
- Shows only the current branch name
- Example:
  git branch --show-current

### `git branch <branch-name>`
- Creates a new branch
- Example:
  git branch feature-1

---

### git switch
- Switches to a branch (modern way)
- Example:
  git switch feature-1

### git checkout
- Switches branches (older method)
- Example:
  git checkout feature-1

---

### git switch -c
- Creates and switches to a new branch
- Example:
  git switch -c feature-2

### git checkout -b
- Creates and switches to a new branch (older method)
- Example:
  git checkout -b feature-3

---

### git branch -d
- Deletes a branch safely
- Example:
  git branch -d feature-2

### git branch -D
- Force deletes a branch
- Example:
  git branch -D feature-2

---

## Switching & Navigation

### git switch master
- Switches back to main/master branch
- Example:
  git switch master

### git switch -
- Switches to the previous branch
- Example:
  git switch -

### git branch
- Shows current branch
- Example:
  git branch

---

## View History (Branch Verification)

### git log --oneline
- Shows compact commit history (one line per commit)
- Example:
  git log --oneline

### `git log --oneline --decorate --graph`
- Shows commit graph with branch pointers
- Example:
  git log --oneline --decorate --graph

### `git log <branch1>..<branch2>`
- Shows commits present in second branch but not in first
- Example:
  git log master..feature-1

---

## Remote Repository Commands

### git remote add origin
- Adds a remote repository
- Example:
  git remote add origin git@github.com:username/repo.git

### git remote -v
- Displays remote repository URLs
- Example:
  git remote -v

### `git push origin <branch-name>`
- Pushes a branch to GitHub
- Example:
  git push origin feature-1

### `git push -u origin <branch-name>`
- Pushes branch and sets upstream tracking
- Example:
  git push -u origin feature-1

---

## Pull & Fetch

### `git pull origin <branch-name>`
- Fetches and merges changes from remote
- Example:
  git pull origin master

### git fetch origin
- Downloads changes without merging
- Example:
  git fetch origin

### git fetch --all
- Fetches updates from all remotes
- Example:
  git fetch --all

---

## Upstream (Fork Workflow)

### git remote add upstream
- Adds original repository as upstream
- Example:
  git remote add upstream git@github.com:original/repo.git

### git fetch upstream
- Fetches changes from upstream
- Example:
  git fetch upstream

### git merge upstream/master
- Merges upstream changes into current branch
- Example:
  git merge upstream/master

---

## Practice Commands (Day 23)

### echo
- Creates or updates a file
- Example:
  echo "feature work" >> file.txt

### git add .
- Stages all changes
- Example:
  git add .

### git commit -m
- Commits changes with a message
- Example:
  git commit -m "Added feature work"

### git log master..feature-1
- Shows commits in feature branch but not in master
- Example:
  git log master..feature-1

---

## Important Notes

- Branches only differ when new commits are made  
- If all branches point to the same commit → no real branching has happened
- git pull = git fetch + git merge

---

## Tip

- git switch is the modern alternative to git checkout  
- git checkout is older and does multiple things (switch + restore files)  



---

Note: Updated from CLI for Day 24 task


# Git Commands – Day 24 (Advanced Git)

---

##  Merge

### Explanation
Merge combines changes from one branch into the current branch. It preserves history.

### Command
git merge <branch-name>

### Example
git switch main  
git merge feature-login

### Fast-forward vs Merge commit
- Fast-forward: no new commit, pointer moves forward  
- Merge commit: new commit created when branches diverge  

---

##  Rebase

### Explanation
Rebase moves commits onto another branch to create linear history.

### Command
git rebase <branch-name>

### Example
git switch feature-dashboard  
git rebase main

### Continue / Abort
git rebase --continue  
git rebase --abort  

---

##  Squash Merge

### Explanation
Combines multiple commits into one single commit.

### Command
git merge --squash <branch-name>  
git commit -m "Squash merged feature"

---

##  Stash

### Explanation
Temporarily saves uncommitted work.

### Commands
git stash  
git stash -u  

### List stash
git stash list  

### Apply stash
git stash apply stash@{n}  

### Pop stash
git stash pop  

### Drop stash
git stash drop stash@{n}  

---

##  Cherry Pick

### Explanation
Applies a specific commit from another branch.

### Command
git cherry-pick <commit-hash>

### Example
git switch main  
git cherry-pick abc1234  

### Continue / Abort
git cherry-pick --continue  
git cherry-pick --abort  

---

##  Logs

git log  
git log --oneline --graph --all  
git log --oneline --graph --decorate --all  

---

##  Conflicts

git status  
# fix file manually  
git add .  
git commit -m "Resolve conflict"

---

##  Quick Reference

Create branch → git checkout -b <name>  
Switch branch → git switch <name>  
Merge → git merge <branch>  
Rebase → git rebase <branch>  
Stash → git stash  
Cherry-pick → git cherry-pick <hash>  

---

# Git Commands – Day 25 (Reset & Revert)

## Reset
**Explanation**

Resets the current branch to a previous commit. Can modify commit history depending on the mode.

`git reset --soft`
- Moves HEAD to previous commit
- Keeps changes staged

**Command**
```bash
git reset --soft HEAD~1
```
---

**git reset --mixed (default)**
- Moves HEAD to previous commit
- Unstages changes (keeps them in working directory)

---

**Command**
```bash
git reset --mixed HEAD~1
```

---

**`git reset --hard`**
- Moves HEAD to previous commit
- Deletes changes from staging and working directory
- Destructive operation

**Command**
```bash
git reset --hard HEAD~1
```
---

**Reset to specific commit**
```bash
git reset --hard <commit-hash>
```
---

**Important Note**
- git reset rewrites history
- Avoid using on pushed/shared branches
- Safe for local cleanup only

---

### Revert
**Explanation**

Creates a new commit that undoes changes from a previous commit without removing history.

**Command**
```bash
git revert <commit-hash>
```
---

**Example**
```bash
git revert abc1234
```
---

**Behavior**
- Does NOT delete commit
- Adds a new commit like:
`"Revert 'Commit message'"`

---

**Conflict Handling**
```bash
git status
# fix conflicts manually
git add .
git commit
```
---

**Important Note**
- Safe for shared branches
- Does NOT rewrite history

---

**Reset vs Revert (Quick Reference)**
|                          | git reset     | git revert          |
| ------------------------ | ------------- | ------------------- |
| History                  | Rewritten     | Preserved           |
| Commit removed           | Yes           | No                  |
| Safe for shared branches | No            | Yes                 |
| Use case                 | Local cleanup | Undo in shared repo |


---

**`git reflog`**
- Shows all recent Git actions (even after reset)
```bash
git reflog
```
**Restore lost commit**
```bash
git reset --hard <commit-id>
```
#### Notes
- reset = rewrite history
- revert = safe undo
- --hard is destructive
- Always avoid reset after push (unless force push is intended)

--------------------

# Note: Updated for Day 26 task

> These commands are part of my DevOps learning journey and can be used for automating GitHub workflows using CLI.

---

## GitHub CLI (gh)

### Explanation
GitHub CLI allows you to manage repositories, issues, pull requests, and workflows directly from the terminal.

---

##  Authentication
```bash
gh auth login
gh auth status
gh auth refresh -h github.com -s delete_repo
```
##  Check Status
```bash
gh auth status
```

**Notes**
- Supports browser login and PAT
- Required before using gh commands

 **Repository Management**
- **Create Repo:** `gh repo create`
- **Clone Repo:** `gh repo clone owner/repo`
- **View Repo:** `gh repo view owner/repo`
- **Open in Browser:** `gh repo view owner/repo --web`
- **List Repos:** `gh repo list`
- **Delete Repo:** `gh repo delete owner/repo`

 **Issues**
- **Create Issue:** `gh issue create --repo owner/repo`
- **List Issues:** `gh issue list --repo owner/repo`
- **View Issue:** `gh issue view <issue-number> --repo owner/repo`
- **Comment on Issue:** `gh issue comment <issue-number> --body "message" --repo owner/repo`
- **Close Issue:** `gh issue close <issue-number> --comment "resolved" --repo owner/repo`

 **Pull Requests**
- **Create PR:** `gh pr create`
- **List PRs:** `gh pr list`
- **View PR:** `gh pr view <pr-number>`
- **View PR Details (JSON):** `gh pr view <pr-number> --json state,reviewRequests,statusCheckRollup`
- **Merge PR:** 
  - `gh pr merge <pr-number> --merge`
  - `gh pr merge <pr-number> --squash`
  - `gh pr merge <pr-number> --rebase`

 **PR Review**
- **Checkout PR:** `gh pr checkout <pr-number>`
- **Approve PR:** `gh pr review <pr-number> --approve`
- **Comment on PR:** `gh pr review <pr-number> --comment --body "Looks good"`
- **Request Changes:** `gh pr review <pr-number> --request-changes`

**GitHub Actions**
- **List Workflow Runs:** `gh run list --repo owner/repo`
- **View Workflow Run:** `gh run view <run-id> --repo owner/repo` 
- **Watch Workflow:** `gh run watch `<run-id>` —repo owner / repo` 

**Notes:**
- gh reduces dependency on GitHub UI
- Useful for automation and CI/CD pipelines
- Supports JSON output for scripting
  
##  Useful Tricks

- gh repo view --web
- gh pr create --fill
- gh issue list --state closed --repo owner/repo
