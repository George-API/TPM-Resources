# Git Best Practices & Workflows

**Focus**: Git workflow patterns, commit conventions, configuration, hooks, protocols - supplements [Git Command Reference](git.md).

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

**Conventional Commits**: `<type>(<scope>): <subject>`

**Types**: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`, `ci`, `build`

**Examples**: `feat(auth): add OAuth2`, `fix(api): resolve null pointer`, `docs(readme): update install`

**Guidelines**: Atomic commits, descriptive messages, present tense, 50 char subject, reference issues (`Closes #123`)

**Workflow**:
```bash
git add src/auth/login.py tests/test_auth.py
git diff --staged
git commit -m "feat(auth): add login validation"
```

---

## Branching Strategies

**Git Flow**: `main` → `develop` → `feature/*`, `release/*`, `hotfix/*`
```bash
git checkout develop && git pull origin develop
git checkout -b feature/user-auth
git rebase develop  # or merge
git checkout develop && git merge --no-ff feature/user-auth
```

**GitHub Flow**: `main` (always deployable) → `feature/*` (from main)
```bash
git checkout main && git pull origin main
git checkout -b feature/new-feature
git push -u origin feature/new-feature
# After PR merge: git checkout main && git pull && git branch -d feature/new-feature
```

**Trunk-Based**: `main` (single branch), short-lived feature branches (< 1 day)
```bash
git checkout -b feature/quick-fix
git add . && git commit -m "fix: resolve issue"
git push -u origin feature/quick-fix
# Merge immediately via PR
```

---

## Git Configuration

**Essential global config**:
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --global core.editor "code --wait"  # VS Code
git config --global core.autocrlf true  # Windows, input for Linux/Mac
git config --global push.default simple
git config --global push.autoSetupRemote true
```

**Useful aliases**:
```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.lg "log --oneline --graph --decorate -20"
git config --global alias.unstage "restore --staged"
```

**Repository-specific**: Omit `--global` for repo-only config

**.gitignore patterns**: `node_modules/`, `venv/`, `__pycache__/`, `.vscode/`, `.DS_Store`, `dist/`, `.env`, `*.log`

---

## Git Hooks

**Pre-commit** (`.git/hooks/pre-commit`):
```bash
#!/bin/sh
npm run lint && npm test || exit 1
```

**Commit-msg** (enforce format):
```bash
#!/bin/sh
pattern="^(feat|fix|docs|style|refactor|perf|test|chore|ci|build)(\(.+\))?: .{1,50}"
grep -qE "$pattern" "$1" || exit 1
```

**Pre-push** (run tests):
```bash
#!/bin/sh
npm run test:all || exit 1
```

**Sharing hooks**: Husky (Node.js), pre-commit (Python) - Configure in `package.json` or `.pre-commit-config.yaml`

---

## Git Protocols

**Transfer protocols**: HTTPS (`https://github.com/user/repo.git`), SSH (`git@github.com:user/repo.git`), Git (unencrypted, rarely used)

**HTTPS credential helper**:
```bash
git config --global credential.helper cache
git config --global credential.helper 'store --file ~/.git-credentials'
```

**SSH key setup**:
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
eval "$(ssh-agent -s)" && ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub  # Add to Git hosting service
```

**Remote management**:
```bash
git remote -v
git remote set-url origin https://github.com/user/repo.git
git remote add upstream https://github.com/original/repo.git
```

**Fetch vs Pull**: `git fetch origin` (download only), `git pull origin main` (fetch + merge), `git pull --rebase origin main` (fetch + rebase)

**Push behavior**:
```bash
git push  # current branch
git push -u origin feature/x  # set upstream
git push --force-with-lease origin feature/x  # safer force push
```

**Branch tracking**: `git branch --set-upstream-to=origin/main main`, `git branch -vv` (view tracking)

---

## Common Patterns

**Update feature branch**:
```bash
# Rebase (linear history)
git checkout feature/x && git fetch origin && git rebase origin/main

# Merge (preserves history)
git checkout feature/x && git fetch origin && git merge origin/main
```

**Squash commits**: `git rebase -i HEAD~3` (change `pick` to `squash`)

**Find bug**: `git bisect start`, `git bisect bad`, `git bisect good <sha>`, test and mark good/bad, `git bisect reset`

**Clean merged branches**: `git branch --merged main | grep -v "\*\|main\|develop" | xargs -n 1 git branch -d`

**Partial staging**: `git add -p <file>` (interactive staging)

**Stash**: `git stash push -m "WIP"`, `git stash list`, `git stash apply stash@{0}`, `git stash pop stash@{0}`

**Recover lost commits**: `git reflog`, `git checkout <sha>`, `git branch recovery-branch <sha>`

---

## Anti-Patterns

**Don't commit large files**: Use `git lfs install`, `git lfs track "*.psd"`

**Don't force push shared branches**: Use `git push --force-with-lease` (safer) instead of `--force`

**Don't commit secrets**: Use `git-secrets` or similar, remove with `git filter-branch` if already committed

**Don't rewrite public history**: Use `git revert <sha>` for shared branches instead of rebase

**Don't commit generated files**: Add to `.gitignore`: `dist/`, `build/`, `*.min.js`

**Don't use `git add .` blindly**: Review with `git status`, stage specific files: `git add src/ tests/`

**Don't commit directly to main**: Use feature branches: `git checkout -b feature/fix`

---

> **Note**: For DevOps platform integration (Azure DevOps, CI/CD pipelines), see [DevOps](concepts/software/devops.md). For Git command syntax, see [Git Command Reference](git.md).
