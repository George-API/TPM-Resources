# Git Quick Reference (Tight) — 1-liners + Comments

Quick reference for common Git commands with inline comments.

## Table of Contents

- [Status & Inspection](#status--inspection)
- [Staging & Committing](#staging--committing)
- [Branching](#branching)
- [Remotes](#remotes)
- [Merging & Rebasing](#merging--rebasing)
- [Stashing](#stashing)
- [Undoing Changes](#undoing-changes)
- [Tags](#tags)
- [Cleanup](#cleanup)
- [Configuration](#configuration)
- [Quick Flows](#quick-flows)

---

## Status & Inspection
- `git status` - what changed (unstaged/staged/untracked)
- `git log --oneline --graph --decorate -n 20` - compact history
- `git show <sha>` - inspect commit
- `git diff` - unstaged diff
- `git diff --staged` - staged diff
- `git blame -L 1,80 <file>` - who changed these lines

## Staging & Committing
- `git add <file>` - stage a file
- `git add .` - stage current dir changes
- `git add -A` - stage ALL (incl deletes/renames)
- `git restore --staged <file>` - unstage (keep file edits)
- `git commit -m "msg"` - commit staged changes
- `git commit --amend -m "msg"` - amend last commit (rewrite last commit)

## Pushing & Pulling
- `git push` - push current branch to upstream
- `git push -u origin <branch>` - first push + set upstream tracking
- `git pull` - fetch + merge
- `git pull --rebase` - fetch + rebase (linear history)
- `git fetch --prune` - update remote refs + prune deleted

## Branching
- `git branch` - list local branches
- `git branch -a` - list local + remotes
- `git switch <branch>` - switch branches (modern)
- `git switch -c feature/x` - create + switch
- `git branch -d feature/x` - delete local branch (merged only)
- `git branch -D feature/x` - force delete local branch
- `git branch -m old new` - rename branch

## Remotes
- `git remote -v` - show remotes
- `git remote add origin <url>` - add remote

## Merging & Rebasing
- `git merge <branch>` - merge branch into current
- `git rebase <branch>` - rebase current onto branch
- `git rebase --continue` - continue after conflict fix
- `git rebase --abort` - cancel rebase
- `git merge --abort` - cancel merge conflict state

## Stashing
- `git stash push -m "wip"` - stash tracked changes
- `git stash push -u -m "wip"` - stash incl untracked
- `git stash list` - list stashes
- `git stash pop` - apply + drop latest stash
- `git stash apply stash@{0}` - apply specific stash (keep it)
- `git stash drop stash@{0}` - delete specific stash

## Undoing Changes
- `git revert <sha>` - new commit that undoes <sha> (safe on shared)
- `git reset --soft HEAD~1` - undo commit; keep staged
- `git reset --mixed HEAD~1` - undo commit; keep unstaged (default)
- `git reset --hard HEAD~1` - undo commit; discard changes (DANGEROUS)
- `git reflog` - recover lost commits/HEAD moves

## Tags
- `git tag` - list tags
- `git tag v1.0.0` - create lightweight tag
- `git tag -a v1.0.0 -m "release"` - annotated tag
- `git push origin v1.0.0` - push one tag
- `git push origin --tags` - push all tags

## Cleanup
- `git clean -n` - preview removing untracked
- `git clean -fd` - delete untracked files/dirs (DANGEROUS)

## Configuration
- `git config --global user.name "Name"` - set name
- `git config --global user.email "you@x.com"` - set email
- `git config --global --list` - show global config

## Quick Flows
- `git switch -c feature/x && git add -A && git commit -m "feat: x" && git push -u origin feature/x` - Start feature branch
- `git fetch origin && git rebase origin/main` - Update feature via rebase
- `git push --force-with-lease` - Push rebased branch (safer force)
