# Git Workflow - Standard Operating Procedures

**Type:** Version Control & Collaboration
**Recommended Strategy:** Gitflow Workflow
**Source:** Industry best practices from GitHub, Next.js, VS Code

## Overview

A standardized Git workflow ensures:
- Clear code review process
- Organized version history
- Minimal merge conflicts
- Easy rollback capability
- Team transparency

## Branch Model: Gitflow Workflow

### Main Branches

**1. `main` (Production)**
- Always production-ready code
- Protected branch (require PR review)
- Tagged with semantic versioning
- Only merges from `release/` branches

**2. `develop` (Integration)**
- Integration branch for features
- May contain pre-release code
- Source for feature and bugfix branches
- May have automated deployments to staging

### Supporting Branches

**Feature Branches: `feature/*`**
- Branch from: `develop`
- Merge back into: `develop`
- Naming: `feature/description-of-feature`
- Examples: `feature/user-auth`, `feature/payment-integration`

**Bugfix Branches: `bugfix/*`**
- Branch from: `develop`
- Merge back into: `develop`
- Naming: `bugfix/description-of-bug`
- Examples: `bugfix/login-error`, `bugfix/memory-leak`

**Release Branches: `release/*`**
- Branch from: `develop`
- Merge back into: `main` and `develop`
- Naming: `release/v1.0.0`
- Purpose: Final testing and minor fixes before release
- Deleted after merge to `main`

**Hotfix Branches: `hotfix/*`**
- Branch from: `main`
- Merge back into: `main` and `develop`
- Naming: `hotfix/critical-issue`
- Purpose: Critical production fixes
- Deleted after merge to `main`

## Step-by-Step Workflows

### Creating a Feature

```bash
# 1. Ensure develop is up-to-date
git checkout develop
git pull origin develop

# 2. Create feature branch
git checkout -b feature/my-feature

# 3. Make changes and commit
git add .
git commit -m "feat: add new feature"

# 4. Keep up with develop changes
git fetch origin develop
git rebase origin/develop

# 5. Push to remote
git push origin feature/my-feature

# 6. Create Pull Request on GitHub
# (Include description, tests, link to issue)
```

### Reviewing & Merging a PR

```bash
# 1. Fetch the branch locally (for testing)
git fetch origin feature/my-feature
git checkout feature/my-feature

# 2. Test the feature
npm install
npm run test
npm run build

# 3. Approve or request changes in GitHub

# 4. Merge via GitHub (never force push)
# Enable: "Delete branch after merge"

# 5. Update local develop
git checkout develop
git pull origin develop
```

### Creating a Release

```bash
# 1. Create release branch from develop
git checkout develop
git pull origin develop
git checkout -b release/v1.1.0

# 2. Update version numbers
# - package.json version
# - Other version files
# - CHANGELOG.md

# 3. Commit changes
git add .
git commit -m "chore: bump version to 1.1.0"

# 4. Push release branch
git push origin release/v1.1.0

# 5. Create PR for final review
# (Link to release notes/changelog)

# 6. After approval, merge to main
git checkout main
git pull origin main
git merge --no-ff release/v1.1.0
git tag -a v1.1.0 -m "Release version 1.1.0"
git push origin main --tags

# 7. Merge back to develop
git checkout develop
git pull origin develop
git merge --no-ff release/v1.1.0
git push origin develop

# 8. Delete release branch
git branch -d release/v1.1.0
git push origin --delete release/v1.1.0
```

### Emergency Hotfix

```bash
# 1. Create hotfix from main
git checkout main
git pull origin main
git checkout -b hotfix/critical-bug

# 2. Fix the issue and test
# (Make minimal changes)

# 3. Update version (patch bump)
# - package.json version
# - CHANGELOG.md

# 4. Commit and push
git add .
git commit -m "fix: critical bug in production"
git push origin hotfix/critical-bug

# 5. Create PR for review (expedited)

# 6. Merge to main
git checkout main
git pull origin main
git merge --no-ff hotfix/critical-bug
git tag -a v1.0.1 -m "Hotfix: critical bug"
git push origin main --tags

# 7. Merge to develop
git checkout develop
git pull origin develop
git merge --no-ff hotfix/critical-bug
git push origin develop

# 8. Delete hotfix branch
git branch -d hotfix/critical-bug
git push origin --delete hotfix/critical-bug

# 9. Deploy immediately
npm run deploy:production
```

## Commit Conventions

### Message Format

```
<type>: <subject>

<body>

<footer>
```

### Types

- **feat**: New feature
- **fix**: Bug fix
- **docs**: Documentation changes
- **style**: Code style (formatting, missing semicolons)
- **refactor**: Code refactoring without feature changes
- **perf**: Performance improvements
- **test**: Adding or updating tests
- **chore**: Maintenance tasks (dependencies, config)
- **ci**: CI/CD configuration changes

### Examples

**Good Commits:**
```
feat: add user authentication with JWT

- Implement JWT token generation
- Add token validation middleware
- Store tokens securely in httpOnly cookies

Closes #123
```

```
fix: resolve memory leak in event listener

The event listener was not being properly unsubscribed
when component unmounted, causing memory buildup.

Fixes #456
```

```
refactor: simplify user validation logic

Extract validation rules to separate module for reusability.
No functional changes.
```

**Bad Commits:**
```
❌ Updated stuff
❌ Fixed bugs
❌ Changes
❌ wip
```

### Commit Best Practices

1. **Atomic Commits**
   - One logical change per commit
   - Commit shouldn't break tests
   - Easy to revert if needed

2. **Meaningful Messages**
   - First line (50 chars max): summary
   - Blank line
   - Body (72 chars per line): detailed explanation
   - Footer: references and keywords

3. **Frequency**
   - Commit often (multiple times per day)
   - Don't wait for feature completion
   - Helps with code review granularity
   - Makes history clearer

## Pull Request Process

### Before Creating PR

1. **Update branch**
   ```bash
   git fetch origin develop
   git rebase origin/develop
   ```

2. **Run tests locally**
   ```bash
   npm run test
   npm run lint
   npm run build
   ```

3. **Clean up commits**
   ```bash
   # Squash fixup commits if needed
   git rebase -i origin/develop
   ```

### PR Description Template

```markdown
## Description
Brief explanation of changes

## Related Issue
Closes #123

## Changes Made
- Change 1
- Change 2
- Change 3

## Testing
- [ ] Unit tests added/updated
- [ ] Manual testing completed
- [ ] No regressions observed

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-reviewed code
- [ ] Comments added for complex logic
- [ ] Documentation updated
- [ ] No new warnings generated

## Screenshots (if applicable)
[Add screenshots for UI changes]

## Performance Impact
[Note any performance implications]
```

### Code Review Standards

**Reviewers should check:**
- [ ] Code is readable and maintainable
- [ ] Tests are adequate
- [ ] No hardcoded values/secrets
- [ ] Error handling is appropriate
- [ ] Performance impact acceptable
- [ ] Security best practices followed
- [ ] Documentation is clear
- [ ] No unnecessary dependencies added

**Approval Requirements:**
- At least 1 approval required (minimum)
- 2 approvals for critical code
- All tests passing
- No merge conflicts
- No security violations

### Merging

```bash
# Option 1: Merge PR via GitHub (recommended)
# Go to GitHub PR page and click "Merge pull request"
# Select merge type: "Create a merge commit"

# Option 2: Merge locally
git checkout develop
git pull origin develop
git merge --no-ff feature/my-feature
git push origin develop

# After merge, delete branch
git branch -d feature/my-feature
git push origin --delete feature/my-feature
```

## Handling Conflicts

### Prevention

1. **Keep branches short-lived** (max 1-2 weeks)
2. **Rebase frequently** on target branch
3. **Communicate** with team about changes
4. **Review similar changes** before pushing

### Resolution

```bash
# 1. Update branch with latest changes
git fetch origin
git rebase origin/develop

# 2. Identify conflicts
git status

# 3. Edit conflicted files
# Look for markers:
# <<<<<<< HEAD
# [your changes]
# =======
# [incoming changes]
# >>>>>>> branch-name

# 4. Resolve conflicts manually
# (Keep what's needed from both versions)

# 5. Mark as resolved and continue
git add .
git rebase --continue

# 6. Force push only if rebasing
git push origin feature/my-feature --force-with-lease
```

## Best Practices

### Do's ✅

- ✅ Keep branches focused on single feature
- ✅ Write clear commit messages
- ✅ Test before pushing
- ✅ Rebase before creating PR
- ✅ Keep PRs reasonably sized (max 400-500 lines)
- ✅ Delete branches after merging
- ✅ Use descriptive branch names
- ✅ Link related issues in PRs
- ✅ Request reviews from appropriate people

### Don'ts ❌

- ❌ Push directly to main/develop
- ❌ Force push to shared branches
- ❌ Large monolithic commits
- ❌ Merge without tests passing
- ❌ Leave stale branches
- ❌ Inconsistent naming conventions
- ❌ Vague commit messages
- ❌ Rebase publicly shared branches
- ❌ Commit secrets or sensitive data

## Branch Protection Rules

### Configure on GitHub

1. **For `main` branch:**
   - Require pull request reviews (2+ reviewers)
   - Require status checks to pass
   - Require branches to be up to date
   - Require code review from code owners
   - Dismiss stale reviews when new commits pushed
   - Require administrator approval for dismissed reviews

2. **For `develop` branch:**
   - Require pull request reviews (1+ reviewer)
   - Require status checks to pass
   - Allow auto-merge (if status checks pass)
   - Delete head branches automatically

3. **Implementation:**
   ```bash
   # In .github/settings.yml or via GitHub UI
   protected_branches:
     - name: main
       enforce_admins: true
       require_linear_history: true
       required_status_checks:
         - build
         - test
         - lint
     - name: develop
       enforce_admins: false
       required_status_checks:
         - test
   ```

## Useful Commands

```bash
# View branch history
git log --oneline --graph --all

# See changes between branches
git diff develop feature/my-feature

# Cherry-pick specific commit
git cherry-pick <commit-hash>

# Create branch from commit
git checkout -b new-branch <commit-hash>

# View commits not in main
git log main..feature/my-feature

# Find which branch contains commit
git branch --contains <commit-hash>

# Search commit messages
git log --grep="search term" --oneline

# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1
```

## Related Resources

- [Gitflow Cheatsheet](https://danielkummer.github.io/git-flow-cheatsheet/)
- [GitHub Flow Guide](https://guides.github.com/introduction/flow/)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Git Documentation](https://git-scm.com/doc)

---

**Last Updated:** December 2024
**Version:** 1.0
**Status:** Active
