---
layout: post
title: "Git Workflow Guide: A Hands-On Journey Through Branch Management, Line Endings, and Synchronization"
date: 2025-12-09
categories: [git, version-control, tutorial]
tags: [git, github, workflow, debugging, line-endings, merge, branch]
author: Your Name
description: "A comprehensive guide to Git workflows including branch management, fixing line ending issues, merging strategies, and synchronization - learned through real-world problem-solving."
toc: true
print: true
---

# Git Workflow Guide: A Hands-On Journey

This guide documents a complete Git workflow learned through hands-on experience, covering branch creation, commits, handling line ending issues, merging, and synchronization. Each section includes detailed explanations, real-world scenarios, and practical solutions.

---

## Table of Contents

1. [Understanding Git Basics](#understanding-git-basics)
2. [Setting Up Your Workspace](#setting-up-your-workspace)
3. [Branch Management](#branch-management)
4. [Making and Committing Changes](#making-and-committing-changes)
5. [The Line Ending Problem](#the-line-ending-problem)
6. [Fixing Line Endings](#fixing-line-endings)
7. [Syncing with Remote Branches](#syncing-with-remote-branches)
8. [Complete Command Reference](#complete-command-reference)
9. [Common Scenarios and Solutions](#common-scenarios-and-solutions)
10. [Best Practices](#best-practices)

---

## Understanding Git Basics

### What is Git?

Git is a distributed version control system that tracks changes in your code over time. Think of it as a sophisticated "save" system that allows you to:

- Save snapshots of your work (commits)
- Create parallel versions of your code (branches)
- Collaborate with others
- Revert to previous versions if needed

### Key Concepts

#### Repository (Repo)
A folder that Git is tracking. Contains your code and Git's tracking information (in the `.git` folder).

#### Commit
A snapshot of your code at a specific point in time. Like a saved game checkpoint.

#### Branch
A parallel version of your code. Allows you to work on features without affecting the main code.

#### Remote
A version of your repository hosted on a server (like GitHub). Called `origin` by default.

#### Working Directory
The actual files you see and edit on your computer.

#### Staging Area (Index/Cache)
A temporary holding area for changes before they're committed. Like a loading dock before shipping.

---

## Setting Up Your Workspace

### Step 1: Check Your Current Status

```bash
git status
```

**Purpose:** Shows where you are and what's changed.

**Output Explanation:**
```
On branch main
Your branch is up to date with 'origin/main'.
nothing to commit, working tree clean
```

- **Branch:** Which branch you're currently on
- **Up to date:** Your local matches the remote
- **Working tree clean:** No uncommitted changes

**When to use:**
- Start of any work session
- Before making changes
- To check if you have uncommitted work

---

## Branch Management

### Creating a Feature Branch

Branches allow you to work on new features without affecting the main codebase.

#### Step 1: Switch to Main Branch

```bash
git checkout main
```

**What it does:** Moves you to the main branch.

**Analogy:** Like switching to the main highway before taking a different exit.

#### Step 2: Get Latest Changes

```bash
git pull origin main
```

**What it does:** Downloads and merges the latest changes from the remote main branch.

**Breaking it down:**
- `git pull` = fetch + merge in one command
- `origin` = the remote server (GitHub)
- `main` = the branch to pull from

**Why this matters:** Ensures you start with the most up-to-date code.

#### Step 3: Create and Switch to New Branch

```bash
git checkout -b feature/your-feature-name
```

**What it does:** Creates a new branch and switches to it.

**Flags explained:**
- `-b` = create a new branch

**Naming conventions:**
- `feature/feature-name` for new features
- `bugfix/bug-name` for bug fixes
- `hotfix/issue-name` for urgent fixes
- Use your username prefix: `username/feature-name`

**Visual representation:**
```
Before:
main ----A----B----C (you are here)

After:
main ----A----B----C
                    \
                     feature/new-feature (you are here)
```

---

## Making and Committing Changes

### The Standard Workflow

#### Step 1: Make Your Changes
Edit your files as needed.

#### Step 2: Check What Changed

```bash
git status
```

Shows which files were modified.

```bash
git diff
```

Shows the actual line-by-line changes.

#### Step 3: Stage Your Changes

```bash
git add filename.txt
```

**For multiple files:**
```bash
git add file1.txt file2.txt file3.txt
```

**For all changed files:**
```bash
git add .
```

**What staging means:** Telling Git "these are the changes I want to save in the next commit."

**Analogy:** Putting items in your shopping cart before checkout.

#### Step 4: Commit Your Changes

```bash
git commit -m "Add feature: description of what you did"
```

**Flags explained:**
- `-m` = message (commit description)

**Good commit message practices:**
- Use present tense: "Add feature" not "Added feature"
- Be specific: "Fix login button alignment" not "Fix stuff"
- Keep first line under 50 characters
- Add details in subsequent lines if needed

#### Step 5: Push to Remote

```bash
git push origin feature/your-feature-name
```

**What it does:** Uploads your branch to GitHub.

**First time pushing a new branch:**
```bash
git push -u origin feature/your-feature-name
```

The `-u` flag sets up tracking so future pushes can just use `git push`.

---

## The Line Ending Problem

### Understanding Line Endings

When you press Enter in a text file, different operating systems save it differently:

| System | Line Ending | Characters | Notation |
|--------|-------------|------------|----------|
| Windows | CRLF | Carriage Return + Line Feed | `\r\n` |
| Unix/Linux/Mac | LF | Line Feed only | `\n` |

### The Problem We Encountered

**Scenario:** After editing files on Windows, every line appeared as "changed" in Git, even though only a few lines were actually modified.

**Symptoms:**
```bash
git diff
```

Showed output like:
```diff
- Line 1 text here^M
+ Line 1 text here

- Line 2 text here^M
+ Line 2 text here
```

The `^M` symbol represents the extra Carriage Return character (CR).

**Root cause:**
- Original files had LF endings (Unix-style)
- Editor saved files with CRLF endings (Windows-style)
- Git saw every line as changed due to the invisible ending character change

**Impact:**
- Diff showed 1000+ lines changed instead of the actual 10-20 lines
- Impossible to review what actually changed
- Messy git history

### Why This Happens

Git has a setting called `core.autocrlf` that controls line ending behavior:

```bash
git config --get core.autocrlf
```

**Possible values:**
- `true` (Windows default): Convert LF to CRLF on checkout, CRLF to LF on commit
- `input` (Mac/Linux): Convert CRLF to LF on commit, no conversion on checkout
- `false`: No conversion at all

---

## Fixing Line Endings

### The Solution: Renormalizing Files

When you have files with mixed line endings, here's the step-by-step process to fix them:

#### Step 1: Undo the Last Commit (Keep Changes)

```bash
git reset --soft HEAD~1
```

**What it does:**
- Removes the last commit from history
- Keeps all your file changes
- Changes remain staged

**Breaking it down:**
- `git reset` = move the branch pointer
- `--soft` = keep changes in staging area
- `HEAD~1` = one commit before current (HEAD)

**Analogy:** Like pressing "undo" on your last save, but keeping all your work open.

**Visual:**
```
Before:
A -- B -- C (HEAD, bad line endings)

After:
A -- B (HEAD)
Your files still have all the changes from C, just uncommitted
```

#### Step 2: Clear Git's Staging Area

```bash
git rm --cached -r .
```

**What it does:**
- Removes all files from Git's staging area
- Files remain on your computer unchanged
- Git temporarily "forgets" about them

**Flags explained:**
- `--cached` = only affect staging area, not working directory
- `-r` = recursive (all files and subdirectories)
- `.` = current directory

**Analogy:** Emptying your shopping cart without returning items to the shelf.

#### Step 3: Restore Working Directory State

```bash
git reset HEAD
```

**What it does:**
- Refreshes Git's view of your files
- Files now appear as "modified" (not staged)
- Prepares for the next step

**Why needed:** After clearing the cache, we need to tell Git to look at the files again.

#### Step 4: Add Files with Normalized Line Endings

```bash
git add --renormalize filename1.txt filename2.txt
```

**This is the magic step!**

**What `--renormalize` does:**
1. Reads each file
2. Converts line endings according to `.gitattributes` or `core.autocrlf` settings
3. Typically converts CRLF → LF
4. Stages the "cleaned" version

**Result:**
```
Before renormalize:
Line 1\r\n    ← Git sees as different
Line 2\r\n    ← from these

After renormalize:
Line 1\n      ← Git sees as same
Line 2\n      ← as these
```

**Why this works:** Git now only sees the actual content changes, not the line ending changes.

#### Step 5: Create a Fresh Commit

```bash
git commit -m "Fix line endings: Convert CRLF to LF"
```

**Result:** Clean commit showing only actual changes.

**Before fix:**
```
254 files changed, 5000+ insertions, 5000+ deletions
```

**After fix:**
```
10 files changed, 117 insertions, 7 deletions
```

### Complete Line Ending Fix Script

Here's the complete sequence:

```bash
# 1. Undo the bad commit
git reset --soft HEAD~1

# 2. Clear staging area
git rm --cached -r .

# 3. Refresh Git's view
git reset HEAD

# 4. Re-add files with normalized line endings
git add --renormalize .

# 5. Create clean commit
git commit -m "Fix line endings"
```

---

## Syncing with Remote Branches

### Scenario: Branch is Behind Main

Your feature branch may fall behind the main branch as others merge their work.

#### Step 1: Fetch Latest Information

```bash
git fetch origin main
```

**What it does:** Downloads information about the main branch without changing your files.

**fetch vs pull:**
- `git fetch` = download info only
- `git pull` = download + merge

**Analogy:** Checking for app updates without installing them.

#### Step 2: Check for Differences

```bash
git log --oneline your-branch..origin/main
```

**What it does:** Shows commits in main that aren't in your branch.

**Reading the output:**
```
abc1234 Fix bug in login
def5678 Add new feature
```

These are commits you don't have yet.

**Syntax explanation:**
- `branch1..branch2` = "what's in branch2 but not in branch1"

#### Step 3: Merge Main into Your Branch

```bash
git merge origin/main
```

**What it does:**
- Combines main's changes with your branch
- Creates a merge commit
- Updates your files

**What happens:**
1. Git finds common ancestor of both branches
2. Applies changes from both branches
3. Creates a new commit linking them

**If no conflicts:**
```
Merge made by the 'ort' strategy.
 file1.txt | 4 ++++
 1 file changed, 4 insertions(+)
```

**Visual:**
```
Before:
Your branch:  A -- B -- C
Main:         A -- B -- D

After:
Your branch:  A -- B -- C -- M (merge commit)
                      \     /
Main:                 D ---/
```

#### Step 4: Resolve Conflicts (If Any)

**If conflicts occur:**
```
CONFLICT (content): Merge conflict in filename.txt
Automatic merge failed; fix conflicts and then commit the result.
```

**What to do:**

1. **Check which files have conflicts:**
```bash
git status
```

2. **Choose which version to keep:**

**Option A - Keep your version:**
```bash
git checkout --ours filename.txt
```

**Option B - Keep their version:**
```bash
git checkout --theirs filename.txt
```

**Option C - Manually edit the file:**

Open the file and look for conflict markers:
```
<<<<<<< HEAD (your changes)
Your code here
=======
Their code here
>>>>>>> origin/main
```

Edit to keep what you want, remove the markers, and save.

3. **Stage the resolved files:**
```bash
git add filename.txt
```

4. **Complete the merge:**
```bash
git commit -m "Merge main into feature branch"
```

#### Step 5: Push Updated Branch

```bash
git push origin your-branch-name
```

**If push is rejected:**

**Error:**
```
error: failed to push some refs
hint: Updates were rejected because the remote contains work that you do not have locally
```

**Solution:**
```bash
git pull origin your-branch-name
# Resolve any conflicts
git push origin your-branch-name
```

---

## Complete Command Reference

### Basic Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `git status` | Show current state | `git status` |
| `git log` | Show commit history | `git log --oneline -5` |
| `git diff` | Show changes | `git diff` |
| `git branch` | List/create branches | `git branch -a` |

### Branch Operations

| Command | Purpose | Example |
|---------|---------|---------|
| `git checkout <branch>` | Switch branches | `git checkout main` |
| `git checkout -b <branch>` | Create and switch | `git checkout -b feature/new` |
| `git branch -d <branch>` | Delete branch | `git branch -d old-feature` |
| `git branch -D <branch>` | Force delete | `git branch -D old-feature` |

### Committing Changes

| Command | Purpose | Example |
|---------|---------|---------|
| `git add <file>` | Stage file | `git add index.html` |
| `git add .` | Stage all changes | `git add .` |
| `git commit -m "message"` | Create commit | `git commit -m "Fix bug"` |
| `git commit --amend` | Modify last commit | `git commit --amend -m "New message"` |

### Remote Operations

| Command | Purpose | Example |
|---------|---------|---------|
| `git fetch origin` | Download info | `git fetch origin main` |
| `git pull origin <branch>` | Fetch and merge | `git pull origin main` |
| `git push origin <branch>` | Upload branch | `git push origin feature/new` |
| `git push -u origin <branch>` | Push and set upstream | `git push -u origin feature/new` |

### Advanced Operations

| Command | Purpose | Example |
|---------|---------|---------|
| `git reset --soft HEAD~1` | Undo commit, keep changes | `git reset --soft HEAD~1` |
| `git reset --hard HEAD~1` | Undo commit, discard changes | `git reset --hard HEAD~1` |
| `git reset HEAD` | Unstage files | `git reset HEAD file.txt` |
| `git rm --cached <file>` | Remove from staging | `git rm --cached file.txt` |
| `git add --renormalize` | Fix line endings | `git add --renormalize .` |
| `git merge <branch>` | Merge branch | `git merge origin/main` |
| `git merge --abort` | Cancel merge | `git merge --abort` |
| `git checkout --ours <file>` | Keep your version | `git checkout --ours file.txt` |
| `git checkout --theirs <file>` | Keep their version | `git checkout --theirs file.txt` |

### Viewing Information

| Command | Purpose | Example |
|---------|---------|---------|
| `git log --oneline` | Compact history | `git log --oneline -10` |
| `git log --graph` | Visual history | `git log --graph --oneline` |
| `git log A..B` | Commits in B not in A | `git log main..feature` |
| `git diff <branch1> <branch2>` | Compare branches | `git diff main feature` |
| `git show <commit>` | Show commit details | `git show abc1234` |

---

## Common Scenarios and Solutions

### Scenario 1: Made Changes on Wrong Branch

**Problem:** You made commits on `main` instead of a feature branch.

**Solution:**
```bash
# Create a new branch with your current changes
git branch feature/my-feature

# Switch to main
git checkout main

# Reset main to match remote (removes your commits from main)
git reset --hard origin/main

# Switch to your feature branch (your changes are there)
git checkout feature/my-feature
```

### Scenario 2: Need to Undo Last Commit

**Keep the changes (want to edit before re-committing):**
```bash
git reset --soft HEAD~1
# Make changes
git add .
git commit -m "Better commit message"
```

**Discard the changes completely:**
```bash
git reset --hard HEAD~1
```

### Scenario 3: Merge Conflicts

**Step-by-step resolution:**

1. **See which files conflict:**
```bash
git status
```

2. **For each conflicted file, choose a resolution strategy:**

**Keep your version:**
```bash
git checkout --ours filename.txt
git add filename.txt
```

**Keep their version:**
```bash
git checkout --theirs filename.txt
git add filename.txt
```

**Manual merge:** Edit the file, remove conflict markers, save.

3. **Complete the merge:**
```bash
git commit -m "Resolve merge conflicts"
```

### Scenario 4: Accidentally Committed Sensitive Data

**Remove from last commit:**
```bash
# Remove file
git rm --cached sensitive-file.txt

# Amend the commit
git commit --amend

# Add file to .gitignore
echo "sensitive-file.txt" >> .gitignore
git add .gitignore
git commit -m "Add sensitive file to .gitignore"
```

### Scenario 5: Want to Sync Feature Branch with Main

**Recommended approach (merge):**
```bash
# Get latest main
git fetch origin main

# Merge main into your branch
git merge origin/main

# Resolve any conflicts
# Then push
git push origin your-branch
```

**Alternative approach (rebase):**
```bash
# Get latest main
git fetch origin main

# Rebase your branch on top of main
git rebase origin/main

# Resolve any conflicts during rebase
# Then force push (use with caution)
git push --force origin your-branch
```

---

## Best Practices

### Commit Messages

**Good practices:**
- Use present tense: "Add feature" not "Added feature"
- Start with a verb: Add, Fix, Update, Remove, Refactor
- Be specific and descriptive
- Keep first line under 50 characters
- Add details in body if needed

**Examples:**

✅ Good:
```
Add user authentication system

- Implement login/logout functionality
- Add password hashing with bcrypt
- Create user session management
```

❌ Bad:
```
fixed stuff
```

### Branching Strategy

**Branch naming conventions:**
```
feature/short-description    # New features
bugfix/issue-description     # Bug fixes
hotfix/critical-issue        # Urgent fixes
release/version-number       # Release preparation
```

**Workflow:**
1. Always create branches from up-to-date `main`
2. Keep branches focused (one feature/fix per branch)
3. Regularly sync with main to avoid large conflicts
4. Delete branches after merging

### Before Committing

**Checklist:**
- [ ] Run tests (if applicable)
- [ ] Review your changes: `git diff`
- [ ] Stage only related changes
- [ ] Write a clear commit message
- [ ] Ensure no sensitive data is included

### Working with Teams

**Communication:**
- Pull before starting work
- Push regularly (at least end of day)
- Communicate about large changes
- Review others' code in pull requests

**Avoiding conflicts:**
- Pull frequently
- Keep changes small and focused
- Coordinate on file ownership
- Use feature flags for incomplete features

---

## Troubleshooting

### Problem: "Permission denied" when pushing

**Cause:** SSH key or authentication issue.

**Solution:**
```bash
# Check your remote URL
git remote -v

# If HTTPS, you might need to update credentials
# If SSH, check your SSH key
ssh -T git@github.com
```

### Problem: "Divergent branches"

**Error:**
```
Your branch and 'origin/main' have diverged
```

**Solution:**
```bash
# Option 1: Merge
git pull origin main

# Option 2: Rebase
git pull --rebase origin main
```

### Problem: Detached HEAD

**What it means:** You're not on a branch.

**Solution:**
```bash
# Create a branch at current position
git checkout -b recovery-branch

# Or return to a branch
git checkout main
```

### Problem: Lost commits

**Solution:** Git keeps commits for ~30 days in reflog.

```bash
# View reflog
git reflog

# Recover a commit
git checkout <commit-hash>
git checkout -b recovery-branch
```

---

## Quick Reference Cheat Sheet

### Daily Workflow

```bash
# Start of day
git checkout main
git pull origin main
git checkout -b feature/new-work

# During work
git add .
git commit -m "Descriptive message"
git push origin feature/new-work

# End of day
git push origin feature/new-work
```

### Emergency Commands

```bash
# Undo last commit (keep changes)
git reset --soft HEAD~1

# Discard all local changes
git reset --hard HEAD

# Abort a merge
git merge --abort

# View what would be pushed
git diff origin/branch-name
```

### Information Commands

```bash
# Where am I?
git status

# What changed?
git diff

# Recent commits
git log --oneline -5

# All branches
git branch -a
```

---

## Conclusion

This guide covered essential Git workflows including:

- ✅ Branch creation and management
- ✅ Committing changes effectively
- ✅ Fixing line ending issues
- ✅ Merging and conflict resolution
- ✅ Syncing with remote branches
- ✅ Troubleshooting common problems

Remember:
- **Git status** is your friend - use it often
- **Small, focused commits** are better than large ones
- **Pull before push** to avoid conflicts
- **Read error messages** - Git usually tells you what went wrong

Practice these workflows, and they'll become second nature. Don't be afraid to experiment in a test repository!

---

## Additional Resources

- [Official Git Documentation](https://git-scm.com/doc)
- [Pro Git Book](https://git-scm.com/book/en/v2) (Free online)
- [Git Visual Reference](http://marklodato.github.io/visual-git-guide/index-en.html)
- [Learn Git Branching](https://learngitbranching.js.org/) (Interactive tutorial)
- [Oh Shit, Git!?!](https://ohshitgit.com/) (Common problems and solutions)

---

*Last updated: December 9, 2025*

*This guide was created through hands-on experience and real-world problem-solving. All examples are based on actual workflows and issues encountered during development.*
