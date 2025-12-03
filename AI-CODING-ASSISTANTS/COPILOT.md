# GitHub Copilot - Standard Operating Procedures

**Source:** [github.com/features/copilot](https://github.com/features/copilot)
**Type:** AI Coding Agent (Native GitHub)
**Integration:** GitHub, VS Code, GitHub Actions

## Overview

GitHub Copilot is:
- Native GitHub AI coding assistant
- Coding agent with agentic workflows
- Runs in secure GitHub Actions environments
- Creates draft PRs with session logs
- Available across multiple editors (VS Code, Visual Studio, JetBrains, Neovim)

## Setup & Installation

### Prerequisites

- GitHub account with Copilot subscription
- Supported IDE or editor
- GitHub repository access
- GitHub Personal Access Token (PAT)

### Installation by IDE

**VS Code:**
1. Install "GitHub Copilot" extension from marketplace
2. Install "GitHub Copilot Chat" extension (optional)
3. Sign in with GitHub account
4. Authorize extension

**Visual Studio:**
1. Open Extensions > Manage Extensions
2. Search for "GitHub Copilot"
3. Install and restart Visual Studio
4. Sign in with GitHub

**JetBrains IDEs:**
1. Go to Settings > Plugins
2. Search for "GitHub Copilot"
3. Install and restart IDE
4. Sign in with GitHub

**Neovim:**
```bash
# Using vim-plug
Plug 'github/copilot.vim'

# Using packer.nvim
use 'github/copilot.vim'

# Setup authentication
:Copilot setup
:Copilot auth
```

## Using Copilot

### Code Completion

**Features:**
- Context-aware suggestions as you type
- Accept with Tab, Reject with Esc
- Cycle through suggestions (Alt + [ / Alt + ])
- Open suggestions panel (Alt + Shift + \)

**Best Practices:**
1. Write clear variable/function names
2. Add helpful comments above code
3. Provide context with docstrings
4. Review suggestions before accepting
5. Refine and iterate suggestions

### Copilot Chat

**Available in:**
- VS Code (with Copilot Chat extension)
- GitHub.com (web interface)
- GitHub Copilot CLI

**Usage:**
- `/explain` - Explain selected code
- `/tests` - Generate tests
- `/fix` - Fix bugs in code
- `/doc` - Generate documentation
- Ask custom questions about code

**Example Interactions:**
```
User: Explain this algorithm
Copilot: [Provides detailed explanation with examples]

User: Generate tests for this function
Copilot: [Creates comprehensive test suite]

User: How do I optimize this query?
Copilot: [Suggests optimization strategies with code]
```

## Copilot CLI

### Installation

```bash
# Requires GitHub Copilot subscription
gh extension install github/gh-copilot

# Authenticate
gh auth login
gh extension upgrade gh-copilot
```

### Commands

```bash
# Interactive code explanation
gh copilot explain "cd src && ls -la"

# Generate shell commands
gh copilot suggest "commit all changes with message"

# Get help
gh copilot explain --help
```

## Agentic Workflows

### GitHub Copilot Coding Agent

**Purpose:** Autonomous code generation and fixes in GitHub

**Capabilities:**
- Reviews repository context
- Analyzes issues and PRs
- Writes code changes
- Runs tests and debugging
- Creates draft pull requests
- Generates session logs

### Setting Up Agent Workflows

**Requirements:**
1. Enable Copilot in organization settings
2. GitHub Actions available
3. Sufficient GitHub Actions minutes

**Configuration:**

**Example 1: Automated Code Review**
```yaml
name: Copilot Code Review

on: [pull_request]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Run Copilot Review
        uses: github/copilot-code-review-action@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}

      - name: Post Review Comment
        uses: actions/github-script@v6
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '✅ Copilot review completed'
            })
```

**Example 2: Fix Issues Automatically**
```yaml
name: Copilot Auto-Fix

on:
  issues:
    types: [labeled]

jobs:
  fix:
    if: github.event.issue.labels.*.name contains 'copilot-fix'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Create Feature Branch
        run: |
          git checkout -b fix/issue-${{ github.event.issue.number }}

      - name: Run Copilot Agent
        uses: github/copilot-agent@v1
        with:
          issue-number: ${{ github.event.issue.number }}
          github-token: ${{ secrets.GITHUB_TOKEN }}

      - name: Commit Changes
        run: |
          git add .
          git commit -m "Fix: Issue #${{ github.event.issue.number }}"
          git push origin fix/issue-${{ github.event.issue.number }}

      - name: Create Pull Request
        uses: actions/github-script@v6
        with:
          script: |
            await github.rest.pulls.create({
              owner: context.repo.owner,
              repo: context.repo.repo,
              title: 'Fix: Issue #${{ github.event.issue.number }}',
              head: 'fix/issue-${{ github.event.issue.number }}',
              base: 'main',
              body: 'Fixes #${{ github.event.issue.number }}'
            })
```

## Custom Instructions

### Organization-Level Settings

**Configure for Entire Organization:**

1. Go to GitHub Settings > Copilot
2. Set default coding language
3. Add organization guidelines
4. Configure allowed domains

**Example Instructions:**
```
Our organization uses:
- TypeScript for type safety
- React for frontend development
- Node.js for backend services
- PostgreSQL for databases
- Jest for testing

Code should follow:
- ESLint configuration
- Prettier formatting
- 80% minimum test coverage
- Security best practices
```

### Repository-Level Instructions

**Create `.github/copilot-instructions.md`:**

```markdown
# Copilot Instructions for [Project Name]

## Technology Stack
- Framework: Next.js 14
- Language: TypeScript
- Testing: Jest + Cypress
- Database: PostgreSQL
- ORM: Prisma

## Code Standards
- Use TypeScript strict mode
- Follow ESLint rules
- Add JSDoc comments for public APIs
- Write tests for all business logic

## Project Structure
- `/src/app` - Next.js app directory
- `/src/components` - React components
- `/src/api` - API routes
- `/src/lib` - Utility functions
- `/tests` - Test suites

## Important Patterns
[Document key patterns used in project]

## Security Requirements
- Never hardcode secrets
- Use environment variables
- Validate user input
- Implement CORS properly
```

## Best Practices

### Effective Prompts

**For Code Generation:**
```
❌ Bad: "Create a function"
✅ Good: "Create a TypeScript function that validates email format using regex, handles edge cases, and returns a boolean"

❌ Bad: "Write tests"
✅ Good: "Write Jest unit tests for the getUserById function covering success case, not found, and database error scenarios"

❌ Bad: "Fix the bug"
✅ Good: "Fix the bug where login fails when password contains special characters. The error occurs in the validatePassword function. Include tests"
```

### Code Review with Copilot

**Review Checklist:**
- [ ] Code is readable and maintainable
- [ ] No security vulnerabilities
- [ ] Tests are comprehensive
- [ ] Performance is acceptable
- [ ] Follows project conventions
- [ ] Documentation is complete
- [ ] No hardcoded values/secrets
- [ ] Error handling is appropriate

### Security Best Practices

**Important:**
- Review all Copilot-generated code
- Never commit secrets or credentials
- Verify security implementations
- Keep dependencies updated
- Use GitHub Secret Scanning
- Implement Code Scanning alerts
- Review Actions permissions

## GitHub Actions Integration

### Complete CI/CD Example

```yaml
name: Copilot-Assisted CI/CD

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  setup:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install Dependencies
        run: npm install

  lint:
    needs: setup
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      - name: Run Linter
        run: npm run lint

  test:
    needs: setup
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      - name: Install Dependencies
        run: npm install
      - name: Run Tests
        run: npm test -- --coverage

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Dependency Check
        uses: dependabot/fetch-metadata@v1
      - name: GitHub Advanced Security
        uses: github/super-linter@v4

  copilot-review:
    needs: [lint, test, security]
    if: github.event_name == 'pull_request'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Copilot Code Review
        uses: github/copilot-code-review-action@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}

  build:
    needs: [lint, test, security]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build

  deploy:
    needs: build
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Deploy to Production
        run: |
          npm install
          npm run build
          npm run deploy
```

## Troubleshooting

### Common Issues

**Issue:** Copilot suggestions not appearing
- **Solution:** Check that extension is enabled
- **Action:** Restart IDE and authenticate again

**Issue:** Slow code completion
- **Solution:** Large files can impact performance
- **Action:** Check internet connection, restart IDE

**Issue:** Agent failing to create PR
- **Solution:** Verify GitHub token permissions
- **Action:** Check repository branch protection rules

**Issue:** Copilot suggesting outdated patterns
- **Solution:** Update custom instructions
- **Action:** Add current project patterns to instructions

## Monitoring & Metrics

### Track Copilot Usage

1. **GitHub Settings > Copilot**
   - View usage statistics
   - Monitor acceptance rates
   - Track productivity metrics

2. **PR Analysis**
   - Measure code quality improvements
   - Track bug reduction
   - Monitor deployment success

3. **Team Metrics**
   - Acceptance rate per developer
   - Time saved on coding
   - Code review efficiency

## Multi-Editor Support

### Universal Features

Across all supported editors:
- Code completion
- Copilot Chat (where supported)
- Slash commands in chat
- Custom instructions

### Editor-Specific Features

| Feature | VS Code | Visual Studio | JetBrains | Neovim |
|---------|---------|---------------|-----------|--------|
| Code Completion | ✅ | ✅ | ✅ | ✅ |
| Copilot Chat | ✅ | ✅ | ✅ | ⚠️ |
| Custom Instructions | ✅ | ✅ | ✅ | ✅ |
| Code Review Action | ✅ | ✅ | ⚠️ | ❌ |

## Quick Reference

| Task | Command |
|------|---------|
| Accept suggestion | Tab |
| Reject suggestion | Esc |
| Next suggestion | Alt + ] |
| Previous suggestion | Alt + [ |
| Open chat | Ctrl+Shift+I |
| Explain code | /explain |
| Generate tests | /tests |
| Fix bug | /fix |
| Ask question | Type normally |

## Related Resources

- [GitHub Copilot Features](https://github.com/features/copilot)
- [VS Code Extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)
- [Copilot Chat Extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat)
- [Copilot Documentation](https://docs.github.com/en/copilot)
- [Coding Agent 101](https://github.blog/ai-and-ml/github-copilot/github-copilot-coding-agent-101-getting-started-with-agentic-workflows-on-github/)

---

**Last Updated:** December 2024
**Version:** 1.0
**Status:** Active
