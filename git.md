#!/usr/bin/env bash
# ==========================================
# Git Quick Ref (Tight) — 1-liners + comments
# ==========================================

git status                                     # what changed (unstaged/staged/untracked)
git add <file>                                 # stage a file
git add .                                      # stage current dir changes
git add -A                                     # stage ALL (incl deletes/renames)
git restore --staged <file>                    # unstage (keep file edits)
git commit -m "msg"                            # commit staged changes
git commit --amend -m "msg"                    # amend last commit (rewrite last commit)
git push                                       # push current branch to upstream
git push -u origin <branch>                    # first push + set upstream tracking
git pull                                       # fetch + merge
git pull --rebase                              # fetch + rebase (linear history)
git fetch --prune                              # update remote refs + prune deleted
git log --oneline --graph --decorate -n 20     # compact history
git show <sha>                                 # inspect commit
git diff                                       # unstaged diff
git diff --staged                              # staged diff
git blame -L 1,80 <file>                       # who changed these lines

git branch                                     # list local branches
git branch -a                                  # list local + remotes
git switch <branch>                            # switch branches (modern)
git switch -c feature/x                        # create + switch
git branch -d feature/x                        # delete local branch (merged only)
git branch -D feature/x                        # force delete local branch
git branch -m old new                          # rename branch

git remote -v                                  # show remotes
git remote add origin <url>                    # add remote

git merge <branch>                             # merge branch into current
git rebase <branch>                            # rebase current onto branch
git rebase --continue                          # continue after conflict fix
git rebase --abort                             # cancel rebase
git merge --abort                              # cancel merge conflict state

git stash push -m "wip"                        # stash tracked changes
git stash push -u -m "wip"                     # stash incl untracked
git stash list                                 # list stashes
git stash pop                                  # apply + drop latest stash
git stash apply stash@{0}                      # apply specific stash (keep it)
git stash drop stash@{0}                       # delete specific stash

git revert <sha>                               # new commit that undoes <sha> (safe on shared)
git reset --soft HEAD~1                        # undo commit; keep staged
git reset --mixed HEAD~1                       # undo commit; keep unstaged (default)
git reset --hard HEAD~1                        # undo commit; discard changes (DANGEROUS)
git reflog                                     # recover lost commits/HEAD moves

git tag                                        # list tags
git tag v1.0.0                                 # create lightweight tag
git tag -a v1.0.0 -m "release"                 # annotated tag
git push origin v1.0.0                         # push one tag
git push origin --tags                         # push all tags

git clean -n                                   # preview removing untracked
git clean -fd                                  # delete untracked files/dirs (DANGEROUS)

git config --global user.name "Name"           # set name
git config --global user.email "you@x.com"     # set email
git config --global --list                     # show global config

# --- Quick flows (still 1-liners) ---
git switch -c feature/x && git add -A && git commit -m "feat: x" && git push -u origin feature/x   # start feature
git fetch origin && git rebase origin/main                                                          # update feature via rebase
git push --force-with-lease                                                                        # push rebased branch (safer force)
