# Git Best Practices & Workflows

**Focus**: Git best practices, workflow patterns, configuration, hooks, and protocols - supplements [Git Command Reference](git.md).

**Purpose**: Use this for Git workflow patterns, commit conventions, configuration, and protocols. For DevOps platform integration, see [DevOps](concepts/software/devops.md).

## Table of Contents

- [Commit Best Practices](#commit-best-practices)
- [Branching Strategies](#branching-strategies)
- [Git Configuration](#git-configuration)
- [Git Hooks](#git-hooks)
- [Git Protocols](#git-protocols)
- [Common Patterns](#common-patterns)
- [Anti-Patterns](#anti-patterns)

---

## Commit Best Practices

### Commit Message Conventions

**Conventional Commits**: `<type>(<scope>): <subject>`

**Types**: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`, `ci`, `build`

**Examples**:
```
feat(auth): add OAuth2 authentication
fix(api): resolve null pointer in user endpoint
docs(readme): update installation instructions
refactor(utils): simplify date parsing logic
```

### Commit Guidelines

- **Atomic commits**: One logical change per commit
- **Descriptive messages**: Explain what and why, not how
- **Present tense**: "Add feature" not "Added feature"
- **50 char subject**: Keep first line concise
- **Body for context**: Use body for complex changes
- **Reference issues**: `Closes #123` or `Fixes #456`

### Commit Workflow

```bash
# Stage specific files, review, then commit
git add src/auth/login.py tests/test_auth.py
git diff --staged
git commit -m "feat(auth): add login validation"
# For complex changes: git commit  # Opens editor
```

---

## Branching Strategies

### Git Flow

**Structure**: `main` (production) → `develop` (integration) → `feature/*`, `release/*`, `hotfix/*`

```bash
# Start feature
git checkout develop && git pull origin develop
git checkout -b feature/user-authentication

# Keep updated
git checkout develop && git pull origin develop
git checkout feature/user-authentication
git rebase develop  # or merge develop

# Finish feature
git checkout develop
git merge --no-ff feature/user-authentication
git push origin develop
git branch -d feature/user-authentication
```

### GitHub Flow

**Structure**: `main` (always deployable) → `feature/*` (from main)

```bash
# Start feature
git checkout main && git pull origin main
git checkout -b feature/new-feature

# Work and push
git add . && git commit -m "feat: add new feature"
git push -u origin feature/new-feature

# After PR merge: cleanup
git checkout main && git pull origin main
git branch -d feature/new-feature
```

### Trunk-Based Development

**Structure**: `main` (single branch, frequent commits), short-lived feature branches (< 1 day)

```bash
# Quick feature branch
git checkout -b feature/quick-fix
git add . && git commit -m "fix: resolve issue"
git push -u origin feature/quick-fix
# Merge immediately via PR, delete after merge
```

---

## Git Configuration

### Essential Global Config

```bash
# Identity
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Default branch, editor, line endings
git config --global init.defaultBranch main
git config --global core.editor "code --wait"  # VS Code
git config --global core.autocrlf true  # Windows
git config --global core.autocrlf input  # Linux/Mac

# Push/pull behavior
git config --global push.default simple
git config --global push.autoSetupRemote true
git config --global pull.rebase false  # merge (default)
```

### Useful Aliases

```bash
# Shortcuts
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.sw switch
git config --global alias.lg "log --oneline --graph --decorate -20"
git config --global alias.unstage "restore --staged"
git config --global alias.amend "commit --amend --no-edit"
```

### Repository-Specific Config

```bash
# Set for current repo only (omit --global)
git config user.name "Project Name"
git config core.autocrlf false

# View config
git config --list  # all
git config --local --list  # repo-specific
git config --global --list  # global
```

### Ignore Patterns (.gitignore)

**Common patterns**:
```
# Dependencies: node_modules/, venv/, __pycache__/, *.pyc
# IDE: .vscode/, .idea/, *.swp
# OS: .DS_Store, Thumbs.db
# Build: dist/, build/, *.egg-info/
# Environment: .env, .env.local
# Logs: *.log, logs/
```

**Best practices**: Use global `.gitignore` for IDE/OS files, repo-specific for project files, keep patterns specific.

---

## Git Hooks

### Pre-Commit Hook

**`.git/hooks/pre-commit`** (make executable):
```bash
#!/bin/sh
npm run lint && npm test || exit 1
```

### Commit-Msg Hook

**`.git/hooks/commit-msg`** (enforce commit message format):
```bash
#!/bin/sh
commit_msg=$(cat "$1")
pattern="^(feat|fix|docs|style|refactor|perf|test|chore|ci|build)(\(.+\))?: .{1,50}"
echo "$commit_msg" | grep -qE "$pattern" || exit 1
```

### Pre-Push Hook

**`.git/hooks/pre-push`** (run tests before push):
```bash
#!/bin/sh
npm run test:all || (echo "Tests must pass before pushing" && exit 1)
```

### Sharing Hooks

**Husky (Node.js)**:
```json
// package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "npm run lint",
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
  }
}
```

**pre-commit (Python)**:
```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
```

---

## Git Protocols

### Transfer Protocols

**HTTPS**: `https://github.com/user/repo.git`
- Works through firewalls, requires authentication
- Good for most use cases

**SSH**: `git@github.com:user/repo.git`
- Key-based authentication, faster for frequent operations
- Requires SSH key setup

**Git**: `git://github.com/user/repo.git`
- Unencrypted, read-only, rarely used

### Authentication Methods

**HTTPS with Credential Helper**:
```bash
# Cache credentials
git config --global credential.helper cache
git config --global credential.helper 'store --file ~/.git-credentials'

# Use personal access token (GitHub/GitLab)
# Store in credential helper or environment variable
```

**SSH Key Setup**:
```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your_email@example.com"

# Add to SSH agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Add public key to Git hosting service
cat ~/.ssh/id_ed25519.pub
```

### Remote URL Management

```bash
# View remotes
git remote -v

# Change remote URL
git remote set-url origin https://github.com/user/repo.git
git remote set-url origin git@github.com:user/repo.git

# Add multiple remotes
git remote add upstream https://github.com/original/repo.git
git remote add fork https://github.com/yourname/repo.git
```

### Fetch vs Pull

**Fetch**: Download changes without merging
```bash
git fetch origin  # Fetch all branches
git fetch origin main  # Fetch specific branch
git fetch --prune  # Remove deleted remote branches
```

**Pull**: Fetch + merge
```bash
git pull origin main  # Fetch and merge
git pull --rebase origin main  # Fetch and rebase
```

### Push Behavior

```bash
# Push current branch
git push

# Push with upstream tracking
git push -u origin feature/x

# Push all branches
git push --all origin

# Push tags
git push origin v1.0.0
git push --tags

# Force push (use with caution)
git push --force-with-lease origin feature/x  # Safer
git push --force origin feature/x  # Dangerous
```

### Branch Tracking

```bash
# Set upstream branch
git branch --set-upstream-to=origin/main main

# View tracking info
git branch -vv

# Push and set upstream
git push -u origin feature/x
```

---

## Common Patterns

### Keeping Feature Branch Updated

```bash
# Option 1: Rebase (linear history)
git checkout feature/x && git fetch origin
git rebase origin/main

# Option 2: Merge (preserves history)
git checkout feature/x && git fetch origin
git merge origin/main
```

### Squashing Commits

```bash
# Interactive rebase to squash last 3 commits
git rebase -i HEAD~3
# In editor, change 'pick' to 'squash' for commits to combine
```

### Finding When Bug Was Introduced

```bash
# Binary search with git bisect
git bisect start
git bisect bad  # current commit is bad
git bisect good <sha>  # known good commit
# Git checks out middle commit, test it: git bisect good/bad
# Repeat until found, then: git bisect reset
```

### Cleaning Up Merged Branches

```bash
# Delete merged branches
git branch --merged main | grep -v "\*\|main\|develop" | xargs -n 1 git branch -d
git remote prune origin
```

### Partial Staging

```bash
# Stage specific lines/hunks
git add -p <file>  # interactive staging
git add -p  # stage parts of all files
```

### Stashing Work in Progress

```bash
# Stash with message
git stash push -m "WIP: working on feature"
git stash push -u -m "WIP: including new files"  # incl untracked

# List, apply, drop
git stash list
git stash apply stash@{0}  # keep stash
git stash pop stash@{0}  # apply and drop
```

### Recovering Lost Commits

```bash
# View reflog (history of HEAD movements)
git reflog

# Recover lost commit
git reflog
git checkout <sha>  # from reflog
git branch recovery-branch <sha>
```

---

## Anti-Patterns

### Don't Commit Large Files

```bash
# Use Git LFS for large files
git lfs install
git lfs track "*.psd"
git add .gitattributes
```

### Don't Force Push to Shared Branches

```bash
# Bad: git push --force origin main
# Good: Use force-with-lease (safer)
git push --force-with-lease origin feature/x
```

### Don't Commit Secrets

```bash
# Remove committed secrets
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch .env" \
  --prune-empty --tag-name-filter cat -- --all

# Use git-secrets or similar
git secrets --install
git secrets --register-aws
```

### Don't Rewrite Public History

```bash
# Bad: git rebase -i HEAD~5  # on shared branch
# Good: Use revert for shared branches
git revert <sha>
```

### Don't Commit Generated Files

```bash
# Add to .gitignore: dist/, build/, *.min.js
```

### Don't Use `git add .` Blindly

```bash
# Bad: git add .
# Good: Review and stage specific files
git status
git add src/ tests/
```

### Don't Commit Directly to Main

```bash
# Bad: Working directly on main
git checkout main
git add . && git commit -m "fix"

# Good: Use feature branches
git checkout -b feature/fix
git add . && git commit -m "fix"
git push -u origin feature/fix
```

---

> **Note**: For DevOps platform integration (Azure DevOps, CI/CD pipelines), see [DevOps](concepts/software/devops.md). For Git command syntax, see [Git Command Reference](git.md).
