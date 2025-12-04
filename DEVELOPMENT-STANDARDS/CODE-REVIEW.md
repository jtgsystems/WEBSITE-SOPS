# Code Review Process - Standard Operating Procedure

**Version:** 1.0
**Last Updated:** December 2024
**Applies To:** All Development Teams

---

## Overview

Code review is a critical quality gate in our development process. This SOP defines the standards, responsibilities, and procedures for conducting effective code reviews that improve code quality, share knowledge, and maintain consistency across projects.

---

## Review Requirements

### Mandatory Reviews

| Branch Target | Minimum Approvals | Required Reviewers |
|---------------|-------------------|-------------------|
| `main` | 2 | Senior Dev + Team Lead |
| `develop` | 1 | Any team member |
| `release/*` | 2 | QA + Senior Dev |
| `hotfix/*` | 1 | Senior Dev (expedited) |

### Review Scope

All code changes must be reviewed including:
- New features and functionality
- Bug fixes
- Refactoring
- Configuration changes
- Documentation updates
- Dependency updates

---

## Reviewer Responsibilities

### Before Reviewing

```markdown
Preparation checklist:
[ ] Understand the ticket/issue being addressed
[ ] Review the PR description and linked documentation
[ ] Check if tests are included
[ ] Verify CI/CD pipeline status
[ ] Allocate adequate time (not rushed)
```

### During Review

```markdown
Review focus areas:
1. CORRECTNESS - Does it work as intended?
2. SECURITY - Are there vulnerabilities?
3. PERFORMANCE - Will it scale?
4. MAINTAINABILITY - Is it readable and documented?
5. TESTING - Is it adequately tested?
6. STANDARDS - Does it follow our conventions?
```

### After Review

```markdown
Post-review actions:
[ ] Provide clear, actionable feedback
[ ] Approve, request changes, or comment
[ ] Follow up on requested changes
[ ] Verify fixes before final approval
```

---

## Review Checklist

### Code Quality

```markdown
[ ] Code is readable and self-documenting
[ ] Functions/methods have single responsibility
[ ] No duplicate code (DRY principle)
[ ] Appropriate error handling
[ ] No hardcoded values (use constants/config)
[ ] Meaningful variable and function names
[ ] Comments explain "why" not "what"
```

### Security

```markdown
[ ] No sensitive data in code (secrets, keys, passwords)
[ ] Input validation on all user data
[ ] SQL injection prevention (parameterized queries)
[ ] XSS prevention (output encoding)
[ ] Authentication/authorization checks
[ ] Secure API endpoints
[ ] No vulnerable dependencies
```

### Performance

```markdown
[ ] Efficient algorithms and data structures
[ ] No N+1 query problems
[ ] Appropriate caching strategies
[ ] Lazy loading where applicable
[ ] No memory leaks
[ ] Optimized database queries
[ ] Minimal bundle size impact
```

### Testing

```markdown
[ ] Unit tests for new functionality
[ ] Integration tests for API changes
[ ] Edge cases covered
[ ] Negative test cases included
[ ] Test coverage maintained or improved
[ ] Tests are readable and maintainable
[ ] No flaky tests introduced
```

### Documentation

```markdown
[ ] README updated if needed
[ ] API documentation current
[ ] Inline comments for complex logic
[ ] CHANGELOG updated
[ ] Migration steps documented
[ ] Breaking changes noted
```

---

## Giving Feedback

### Feedback Guidelines

```markdown
DO:
- Be specific and actionable
- Explain the "why" behind suggestions
- Offer alternative solutions
- Acknowledge good work
- Use questions to prompt thinking
- Focus on the code, not the person

DON'T:
- Use harsh or condescending language
- Make demands without explanation
- Nitpick on style (use linters)
- Block PRs for minor issues
- Ignore the context/constraints
```

### Comment Categories

Use prefixes to clarify intent:

```markdown
[BLOCKER] - Must fix before merge
[SUGGESTION] - Consider this improvement
[QUESTION] - Need clarification
[NIT] - Minor style preference
[PRAISE] - Highlight good code
```

### Example Comments

```markdown
Good:
"[BLOCKER] This SQL query is vulnerable to injection.
Consider using parameterized queries:
`db.query('SELECT * FROM users WHERE id = $1', [userId])`"

Bad:
"This is wrong, fix it."

Good:
"[SUGGESTION] This function is doing multiple things.
Would it be cleaner to extract the validation logic
into a separate `validateInput()` function?"

Bad:
"Refactor this."
```

---

## Receiving Feedback

### Response Guidelines

```markdown
DO:
- Thank reviewers for their time
- Ask for clarification if needed
- Explain your reasoning when disagreeing
- Make requested changes promptly
- Mark conversations as resolved

DON'T:
- Take feedback personally
- Ignore comments without response
- Push back on every suggestion
- Make unrelated changes in response
```

### Handling Disagreements

```markdown
1. Acknowledge the reviewer's perspective
2. Explain your reasoning clearly
3. Provide evidence (benchmarks, docs, examples)
4. If unresolved, escalate to team lead
5. Document the decision for future reference
```

---

## PR Best Practices

### Writing Good PRs

```markdown
Title format:
[TYPE] Brief description (#issue-number)

Examples:
[FEAT] Add user authentication flow (#123)
[FIX] Resolve memory leak in image processing (#456)
[REFACTOR] Simplify payment service logic (#789)
```

### PR Description Template

```markdown
## Summary
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Changes Made
- Change 1
- Change 2

## Testing Done
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] Manual testing completed

## Screenshots (if applicable)

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Documentation updated
- [ ] No new warnings
```

### PR Size Guidelines

```markdown
Ideal: < 200 lines changed
Acceptable: 200-400 lines
Large: 400+ lines (consider splitting)

Tips for smaller PRs:
- One feature per PR
- Separate refactoring from feature work
- Stack dependent PRs
- Use feature flags for partial features
```

---

## Review Turnaround

### SLA Targets

| Priority | Initial Response | Complete Review |
|----------|-----------------|-----------------|
| Critical (hotfix) | 1 hour | 4 hours |
| High | 4 hours | 1 business day |
| Normal | 1 business day | 2 business days |
| Low | 2 business days | 3 business days |

### Expediting Reviews

```markdown
For urgent reviews:
1. Mark PR as urgent in title: [URGENT]
2. Post in #code-review Slack channel
3. Tag specific reviewers
4. Provide context for urgency
```

---

## Tools & Automation

### Required Checks

```yaml
# .github/workflows/pr-checks.yml
name: PR Checks

on: [pull_request]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm run lint

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm test

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm audit
```

### Branch Protection

```yaml
# Branch protection settings
protection_rules:
  main:
    required_reviews: 2
    dismiss_stale_reviews: true
    require_code_owner_review: true
    require_status_checks:
      - lint
      - test
      - security
    require_linear_history: true
```

### CODEOWNERS

```bash
# .github/CODEOWNERS
# Default owners
* @team-leads

# Frontend
/src/components/ @frontend-team
/src/styles/ @frontend-team

# Backend
/api/ @backend-team
/services/ @backend-team

# Security-sensitive
/auth/ @security-team @team-leads
/.env.example @security-team
```

---

## Metrics & Improvement

### Key Metrics

```markdown
Track these metrics monthly:
- Average time to first review
- Average time to merge
- Review iterations per PR
- PR rejection rate
- Review comment volume
- Reviewer load distribution
```

### Continuous Improvement

```markdown
Quarterly review process:
1. Analyze review metrics
2. Gather team feedback
3. Identify bottlenecks
4. Update guidelines
5. Share learnings
```

---

## Resources

- [Google Engineering Practices](https://google.github.io/eng-practices/review/)
- [Conventional Comments](https://conventionalcomments.org/)
- [How to Do Code Reviews Like a Human](https://mtlynch.io/human-code-reviews-1/)

---

**Document Version:** 1.0
**Maintained by:** Engineering Team
**Review Cycle:** Quarterly
