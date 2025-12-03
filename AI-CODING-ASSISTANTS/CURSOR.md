# Cursor - Standard Operating Procedures

**Source:** [cursor.com/docs](https://cursor.com/docs)
**Type:** AI-Powered Code Editor
**Integration:** GitHub, CLI, GitHub Actions

## Overview

Cursor is an AI code editor with:
- Intelligent code completion with codebase context
- GitHub agent integration for automated workflows
- CLI support for CI/CD pipelines
- Multi-file editing capabilities
- Custom rules via `.cursorrules` files

## Installation & Setup

### Initial Setup

1. **Download and Install**
   - Visit [cursor.com](https://cursor.com)
   - Download for your OS (Windows, macOS, Linux)
   - Run installer and authorize

2. **Authentication**
   - Sign in with GitHub account
   - Grant repository access permissions
   - Create Cursor API key for CLI usage

3. **Repository Configuration**
   - Create `.cursorrules` file in project root
   - Configure project-specific AI behavior
   - Define coding conventions and patterns

### Project Configuration (.cursorrules)

**File Location:** `<project-root>/.cursorrules`

**Purpose:** Guide Cursor's AI to understand project conventions and context

**Example Structure:**
```
# Project Setup and Architecture
[Describe project structure, tech stack, key directories]

# Key Technologies
- Frontend: React/Next.js with TypeScript
- Backend: Node.js/Express
- Database: PostgreSQL
- Testing: Jest, Cypress
- Package Manager: npm
- Deployment: Docker, Kubernetes

# Code Style Guidelines
- Use TypeScript for type safety
- Follow ESLint rules in .eslintrc.json
- Use Prettier for formatting
- Naming: camelCase for variables, PascalCase for components

# Common Patterns
[Document frequent code patterns used in project]

# Commands
npm run dev      - Start development server
npm run test     - Run tests
npm run build    - Build for production
npm run lint     - Check code style

# Important Files
/src/config.ts   - Configuration settings
/src/types.ts    - Type definitions
/src/utils.ts    - Utility functions
```

**Community Collection:** [awesome-cursorrules](https://github.com/PatrickJS/awesome-cursorrules) (35.8k stars)
- 100+ ready-to-use .cursorrules files
- Framework-specific configurations
- Testing framework setups
- Language-specific guidelines

## GitHub Integration

### Agent Workflows

**Purpose:** Automate development tasks using GitHub integration

**Workflow Triggers:**
1. **PR Comments** - `@cursor [prompt]`
2. **Issues** - Create issue with clear specifications
3. **GitHub Actions** - Scheduled or event-triggered tasks

### Setting Up GitHub Agent

**Steps:**

1. **Create GitHub App Integration**
   - Connect Cursor to GitHub organization
   - Grant necessary repository permissions
   - Configure webhook endpoints

2. **Enable Agent Mode**
   - In Cursor settings: Enable "GitHub Agent"
   - Configure auto-response rules
   - Set up notification preferences

3. **Create Agent Workflows**

**Example: Automated Code Review**
```
Trigger: Pull Request created
Action: @cursor review this PR for security vulnerabilities

Cursor will:
1. Analyze PR changes
2. Check for security issues
3. Review code quality
4. Post review comments
5. Request changes if needed
```

**Example: Issue Resolution**
```
Trigger: Issue labeled "bug"
Action: @cursor fix this bug and create a PR

Cursor will:
1. Read issue description
2. Search relevant code
3. Identify root cause
4. Create fix
5. Write tests
6. Create PR with description
```

### GitHub Actions Integration

**Using Cursor in CI/CD:**

```yaml
name: Cursor-Assisted CI/CD

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  cursor-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Run Cursor CLI Review
        env:
          CURSOR_API_KEY: ${{ secrets.CURSOR_API_KEY }}
        run: |
          cursor review --mode=strict --file=changed-files

      - name: Comment PR with Review Results
        uses: actions/github-script@v6
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: 'Review completed by Cursor AI'
            })
```

## CLI Usage

### Cursor Command Line Interface

**Installation:**
```bash
npm install -g @cursor/cli
# or
curl -sSL https://cursor.com/cli/install.sh | bash
```

**Basic Commands:**

```bash
# Review code changes
cursor review --path=./src

# Generate tests
cursor test --file=./src/app.ts

# Create documentation
cursor docs --generate

# Check code quality
cursor lint --strict

# Create PR description
cursor pr-describe --branch=feature/new-feature
```

**Advanced Usage:**

```bash
# Use in GitHub Actions
cursor review --github --pr=${{ github.event.pull_request.number }}

# Generate with specific rules
cursor generate --rules=.cursorrules --file=./src/new-feature.ts

# Batch processing
cursor batch --input=file-list.txt --action=review
```

## Workflow Patterns

### Development Workflow

```
1. Create Feature Branch
   git checkout -b feature/new-feature

2. Edit Code in Cursor
   - Write code with AI assistance
   - Use Ctrl+K for inline edits
   - Use Ctrl+L for multifile edits

3. Test Implementation
   npm run test
   npm run lint

4. Push and Create PR
   git push origin feature/new-feature
   gh pr create --fill

5. AI Review
   @cursor please review this for security and performance

6. Address Feedback
   - Implement suggestions
   - Push changes
   - Cursor auto-reviews updates

7. Merge and Deploy
   - Approve PR
   - Merge to main
   - Deploy to production
```

### Code Review Process

**For Reviewers:**

1. **Automated Review**
   - Cursor analyzes changes automatically
   - Posts preliminary review comments
   - Flags potential issues

2. **Manual Review**
   - Review Cursor's findings
   - Add contextual feedback
   - Request specific changes if needed

3. **Iterative Discussion**
   - Author responds to comments
   - Cursor can refine based on feedback
   - Continue until approved

**Best Practices:**
- Keep PRs focused and manageable
- Provide clear context in PR description
- Link related issues
- Update .cursorrules if patterns change

## Custom Rules Management

### Creating Project-Specific Rules

**Template:**
```
# [Project Name] Development Rules

## Architecture
[Explain project architecture]

## Directory Structure
[Document key directories]

## Technology Stack
[List technologies used]

## Coding Standards
[Define code style, naming conventions, patterns]

## Testing Requirements
[Specify test framework, coverage expectations]

## Performance Guidelines
[Document performance expectations]

## Security Practices
[Define security requirements]

## Common Tasks
[Document frequent development tasks]

## Useful Commands
[List important CLI commands]
```

### Updating Rules

**When to Update:**
- After major architecture changes
- When introducing new patterns
- After team discussions on standards
- When onboarding new team members

**Process:**
1. Modify .cursorrules file
2. Commit and push changes
3. Test with `cursor validate-rules`
4. Document changes in git message

## Integration with Slack and Linear

### Slack Integration

**Setup:**
1. Connect Cursor to Slack workspace
2. Install Cursor bot
3. Configure notification channels
4. Enable PR notifications

**Usage:**
```
@cursor-bot summarize #project-channel
@cursor-bot generate test for file.ts
@cursor-bot explain this code snippet
```

### Linear Integration

**Setup:**
1. Link Linear workspace to Cursor
2. Authenticate with Linear API key
3. Configure issue synchronization

**Workflow:**
- Create Linear issue with specs
- Link to GitHub issue
- Cursor reads both for context
- Updates Linear when PR created
- Marks complete when merged

## Best Practices

### Effective Rule Creation

**DO:**
- Keep rules concise and focused
- Include real code examples
- Update regularly with team feedback
- Document architecture clearly
- Provide helpful command references

**DON'T:**
- Create overly complex rules
- Include outdated information
- Make assumptions about project
- Ignore team conventions
- Leave rules undocumented

### PR Best Practices

1. **Clear Descriptions**
   - Explain what changed and why
   - Link related issues
   - Include testing notes
   - Describe deployment impact

2. **Manageable Size**
   - Keep PRs focused
   - Max 400-500 lines of code
   - One feature per PR when possible
   - Revert unrelated changes

3. **Quality Gates**
   - All tests passing
   - Code coverage maintained
   - Security scan clear
   - Performance acceptable

### Security Practices

- Never commit secrets to .cursorrules
- Use environment variables for sensitive data
- Rotate API keys regularly
- Review Cursor's generated code for security
- Implement secret scanning in CI/CD

## Troubleshooting

### Common Issues

**Issue:** Cursor misunderstands project structure
- **Solution:** Update .cursorrules with detailed architecture info
- **Check:** Ensure all key directories documented

**Issue:** AI generates code that doesn't match style
- **Solution:** Add specific code examples to .cursorrules
- **Action:** Test with `cursor validate-rules`

**Issue:** GitHub agent not responding
- **Solution:** Check GitHub permissions and webhook configuration
- **Action:** Re-authenticate and verify integration

**Issue:** PR review incomplete or inaccurate
- **Solution:** Provide more context in issue/PR description
- **Action:** Update .cursorrules with relevant patterns

## Performance Tips

### Optimizing Cursor Usage

1. **Keep Rules Fresh**
   - Review .cursorrules monthly
   - Update with new patterns
   - Remove outdated information

2. **Batch Operations**
   - Group similar reviews
   - Batch documentation generation
   - Combine CI/CD checks

3. **Feedback Loop**
   - Track Cursor's accuracy
   - Provide corrections in rules
   - Measure productivity gains
   - Adjust configuration based on results

## Quick Reference

| Task | Method |
|------|--------|
| Inline edit | Ctrl+K |
| Multi-file edit | Ctrl+L |
| Ask Cursor | Ctrl+I |
| Review PR | `cursor review` |
| Generate tests | `cursor test` |
| Check rules | `cursor validate-rules` |
| Create PR description | `cursor pr-describe` |

## Related Resources

- [Cursor Official Docs](https://cursor.com/docs)
- [Cursor GitHub Integration](https://cursor.com/docs/integrations/github)
- [Cursor CLI Documentation](https://cursor.com/docs/cli)
- [awesome-cursorrules](https://github.com/PatrickJS/awesome-cursorrules)
- [Cursor Pricing & Features](https://cursor.com/pricing)

---

**Last Updated:** December 2024
**Version:** 1.0
**Status:** Active
