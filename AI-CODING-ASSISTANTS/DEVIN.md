# Devin AI - Standard Operating Procedures

**Source:** [docs.devin.ai](https://docs.devin.ai)
**Type:** AI Software Engineer
**Integration:** GitHub, VSCode IDE

## Overview

Devin is an autonomous AI software engineer capable of:
- Writing and testing code
- Executing code with terminal access
- Fixing bugs and running PRs
- Parallel task execution
- Code modernization

## Setup & Authentication

### Initial Configuration

1. **Access Devin Dashboard**
   - Navigate to [docs.devin.ai](https://docs.devin.ai)
   - Sign in with GitHub account
   - Authorize GitHub integration

2. **Repository Connection**
   - Grant Devin access to repositories
   - Configure SSH keys for git operations
   - Setup GitHub personal access token (PAT)

3. **IDE Setup**
   - Devin uses embedded VSCode interface
   - Interactive browser for testing and verification
   - Integrated terminal for command execution

## Workflows

### Code Development Workflow

```
1. Define Task/Issue
   ├── Create clear GitHub issue
   ├── Include acceptance criteria
   └── Add relevant context/files

2. Activate Devin Agent
   ├── Assign task to Devin
   ├── Provide additional context if needed
   └── Monitor execution

3. Code Implementation
   ├── Devin analyzes repository
   ├── Writes code changes
   ├── Runs tests and debugging
   └── Creates pull request

4. Review & Merge
   ├── Human review of PR
   ├── Request changes if needed
   ├── Approve and merge
   └── Deploy to production
```

### Knowledge Onboarding

**Purpose:** Help Devin understand your codebase and project structure

**Steps:**
1. Create or update `AGENTS.md` file in repository root
2. Document:
   - Project overview and goals
   - Technology stack and architecture
   - Coding conventions and patterns
   - Common workflows and procedures
   - Useful commands and shortcuts

**Example Structure:**
```markdown
# Project Knowledge for AI Agents

## Architecture
- Frontend: React/Next.js
- Backend: Node.js/Express
- Database: PostgreSQL
- Deployment: AWS/Docker

## Key Directories
- `/src/components` - React components
- `/src/api` - API endpoints
- `/src/utils` - Utility functions
- `/tests` - Test suites

## Conventions
- Use TypeScript for type safety
- Follow ESLint configuration
- Write tests for all features
- Use semantic commit messages

## Useful Commands
- `npm run dev` - Start development server
- `npm run test` - Run test suite
- `npm run build` - Build for production
```

### PR Review Process

**Devin's Capabilities:**
- Analyze code for bugs
- Run tests and report failures
- Suggest improvements
- Check for security issues
- Review pull requests automatically

**Configuration:**
```yaml
# .github/workflows/devin-review.yml
name: Devin AI Review
on: [pull_request]
jobs:
  devin-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run Devin Review
        run: |
          # Devin analyzes PR changes
          # Posts review comments
          # Flags potential issues
```

### VPN & Secure Connections

**Setup for Private Networks:**

1. **VPN Configuration**
   - Add VPN credentials to Devin settings
   - Specify network requirements
   - Configure access tokens

2. **Network Access**
   - Private databases accessible via VPN
   - Internal API endpoints
   - Secure artifact repositories

3. **Security Considerations**
   - Rotate credentials regularly
   - Use service accounts for Devin access
   - Monitor Devin's network activity
   - Implement audit logging

## Best Practices

### Code Review Preparation

Before assigning tasks to Devin:

1. **Clear Specifications**
   - Break down complex features
   - Provide example implementations if available
   - List edge cases to handle
   - Specify performance requirements

2. **Context Documentation**
   - Maintain updated AGENTS.md
   - Document recent architectural changes
   - Provide links to related issues/PRs
   - Include relevant test examples

3. **Quality Assurance**
   - Review Devin's code thoroughly
   - Check for security vulnerabilities
   - Verify test coverage
   - Validate performance implications

### Task Assignment Best Practices

**Effective Task:**
```
Title: Implement user authentication with JWT

Description:
- Add JWT token generation on login
- Implement token refresh mechanism
- Add auth middleware for protected routes
- Write tests for all auth flows

Acceptance Criteria:
- [ ] Login endpoint returns JWT token
- [ ] Token stored securely in httpOnly cookie
- [ ] Protected routes require valid token
- [ ] Token refresh works correctly
- [ ] 90% test coverage for auth module
- [ ] All tests pass
```

**Ineffective Task:**
```
Title: Fix authentication

Description:
Authentication is broken.
```

## Integration with GitHub

### Pull Request Workflow

1. **PR Creation**
   - Devin creates PRs automatically
   - Includes descriptive commit messages
   - References relevant issues

2. **PR Components**
   - Clear title and description
   - Linked issue
   - Automatic test execution
   - Code review comments

3. **Merge Requirements**
   - All tests passing
   - Human code review approved
   - No merge conflicts
   - Meets coding standards

### GitHub Actions Integration

**Example Workflow:**
```yaml
name: Devin-Assisted Development
on: [push, pull_request]

jobs:
  devin-tasks:
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
        run: npm test

      - name: Run Linting
        run: npm run lint

      - name: Build Project
        run: npm run build

      - name: Deploy if Main Branch
        if: github.ref == 'refs/heads/main'
        run: npm run deploy
```

## Troubleshooting

### Common Issues

**Issue:** Devin unable to find files
- **Solution:** Ensure AGENTS.md is up-to-date with file locations
- **Check:** Repository structure hasn't changed significantly

**Issue:** Tests failing after code generation
- **Solution:** Devin may not understand test setup
- **Action:** Add test examples to AGENTS.md

**Issue:** VPN connection timeout
- **Solution:** Verify VPN credentials are valid
- **Check:** Network connectivity and firewall rules

**Issue:** PR stuck in review
- **Solution:** Provide specific feedback on changes
- **Action:** Create new issue with clearer requirements

## Security Considerations

### Access Control

- **GitHub Permissions:** Grant minimal necessary access
- **Repository Secrets:** Use GitHub Secrets for sensitive data
- **SSH Keys:** Rotate regularly and use limited scope
- **API Tokens:** Create dedicated service account tokens

### Code Security

- **Secret Scanning:** Enable GitHub Secret Scanning
- **Dependency Scanning:** Monitor for vulnerabilities
- **Code Analysis:** Use GitHub Advanced Security
- **Audit Logs:** Review Devin's actions regularly

## Performance Monitoring

### Track Devin's Productivity

1. **Metrics to Monitor**
   - Tasks completed vs. assigned
   - Code quality scores
   - Test pass rates
   - PR review time
   - Deployment success rate

2. **Optimization**
   - Refine task descriptions based on results
   - Update AGENTS.md with lessons learned
   - Document successful patterns
   - Improve onboarding documentation

## Quick Reference

| Task | Command/Action |
|------|----------------|
| Create issue | GitHub issue with clear spec |
| Assign to Devin | @ mention in issue |
| Monitor progress | Watch PR and Devin UI |
| Provide feedback | Comment on PR/issue |
| Deploy | Merge PR to main branch |

## Related Resources

- [Devin Official Docs](https://docs.devin.ai)
- [GitHub Integration Guide](https://docs.devin.ai/integrations/gh)
- [Quickstart Guide](https://docs.devin.ai/get-started/quickstart)
- [DeepWiki Auto-Documentation](https://huggingface.co/blog/lynn-mikami/deepwiki)

---

**Last Updated:** December 2024
**Version:** 1.0
**Status:** Active
