# Master Index - Website SOP Catalog

## AI Coding Assistants

### Devin AI
- **Source:** [docs.devin.ai](https://docs.devin.ai)
- **Document:** [DEVIN.md](./AI-CODING-ASSISTANTS/DEVIN.md)
- **Key Focus:** Autonomous code development, testing, PR reviews
- **Integration:** GitHub integration, VSCode IDE

### Cursor
- **Source:** [cursor.com/docs](https://cursor.com/docs)
- **Document:** [CURSOR.md](./AI-CODING-ASSISTANTS/CURSOR.md)
- **Key Focus:** GitHub agent workflows, CI/CD integration
- **Community:** [awesome-cursorrules](https://github.com/PatrickJS/awesome-cursorrules) (35.8k stars)

### GitHub Copilot
- **Source:** [github.com/features/copilot](https://github.com/features/copilot)
- **Document:** [COPILOT.md](./AI-CODING-ASSISTANTS/COPILOT.md)
- **Key Focus:** Coding agent, GitHub Actions integration
- **Features:** Custom instructions, multi-editor support

### Sourcegraph Cody
- **Source:** [docs.sourcegraph.com/cody](https://docs.sourcegraph.com/cody)
- **Document:** [CODY.md](./AI-CODING-ASSISTANTS/SOURCEGRAPH-CODY.md)
- **Key Focus:** Context-aware code completion, semantic search
- **Architecture:** Search-first approach

## Web Frameworks & Builders

### Next.js (Vercel)
- **Source:** [github.com/vercel/next.js](https://github.com/vercel/next.js)
- **Document:** [NEXTJS.md](./WEB-FRAMEWORKS/NEXTJS.md)
- **Repository:** 136k stars
- **Key Focus:** React framework, SSR, deployment
- **Tech Stack:** TypeScript (28.3%), JavaScript (58%), Rust (12.4%)

### Webflow
- **Source:** [github.com/webflow](https://github.com/webflow)
- **Document:** [WEBFLOW.md](./WEB-FRAMEWORKS/WEBFLOW.md)
- **Key Repos:** js-webflow-api (331⭐), mcp-server (89⭐)
- **Integration:** GitHub Actions, CI/CD, APIs

### Wix
- **Source:** [github.com/wix](https://github.com/wix)
- **Document:** [WIX.md](./WEB-FRAMEWORKS/WIX.md)
- **Key Projects:** react-native-navigation (13k⭐), Detox (11k⭐)
- **Integration:** Git integration, Wix CLI

### Squarespace
- **Source:** [developers.squarespace.com](https://developers.squarespace.com)
- **Document:** [SQUARESPACE.md](./WEB-FRAMEWORKS/SQUARESPACE.md)
- **Note:** Public repos archived as of Nov 2024
- **Access:** Developer Mode for template repositories

### Framer
- **Source:** [github.com/framer](https://github.com/framer)
- **Document:** [FRAMER.md](./WEB-FRAMEWORKS/FRAMER.md)
- **Key:** Plugin development, GitHub Actions workflows

## Development Standards

### Git Workflow & Branching
- **Document:** [GIT-WORKFLOW.md](./DEVELOPMENT-STANDARDS/GIT-WORKFLOW.md)
- **Strategy:** Gitflow (main, develop, feature/*)
- **Best Practices:** Branch naming, merge requests, pull requests

### Code Review Process
- **Document:** [CODE-REVIEW.md](./DEVELOPMENT-STANDARDS/CODE-REVIEW.md)
- **Standard:** Peer review before merge
- **Tools:** GitHub pull requests, Slack integration
- **Checklist:** Test coverage, security, documentation

### Testing Standards
- **Document:** [TESTING.md](./DEVELOPMENT-STANDARDS/TESTING.md)
- **Frameworks:** Jest, Cypress, Playwright, Vitest
- **Coverage:** Unit, integration, E2E tests
- **CI/CD:** Automated test execution

### CI/CD Pipeline
- **Document:** [CI-CD.md](./DEVELOPMENT-STANDARDS/CI-CD.md)
- **Platform:** GitHub Actions
- **Stages:** Build, test, deploy, release
- **Triggers:** Branch pushes, pull requests, releases

### Branching Strategy
- **Document:** [BRANCHING-STRATEGY.md](./DEVELOPMENT-STANDARDS/BRANCHING-STRATEGY.md)
- **Models:** Gitflow, Trunk-based development
- **Naming Conventions:** feature/, bugfix/, release/
- **Lifecycle:** Create, develop, review, merge, delete

## VS Code & Extensions

### Extension Development
- **Document:** [EXTENSION-DEVELOPMENT.md](./VS-CODE/EXTENSION-DEVELOPMENT.md)
- **Source:** [microsoft/vscode](https://github.com/microsoft/vscode) (179k⭐)
- **Stack:** TypeScript (95.3%)

### Contribution Guidelines
- **Document:** [CONTRIBUTION-GUIDE.md](./VS-CODE/CONTRIBUTION-GUIDE.md)
- **Requirements:** CLA, tests, Coding Guidelines
- **Support:** Stack Overflow, GitHub Issues, Discussions
- **Resources:** Help wanted, Good first issue labels

## Security Best Practices

### Secret Management
- **Document:** [SECRET-MANAGEMENT.md](./SECURITY/SECRET-MANAGEMENT.md)
- **Key Points:**
  - Never commit `.env` files
  - Use environment variables for credentials
  - Implement `.gitignore` properly
  - Destroy commits if secrets leaked
  - Use GitHub Secrets for CI/CD

### Data Protection
- **Document:** [DATA-PROTECTION.md](./SECURITY/DATA-PROTECTION.md)
- **Standards:**
  - GDPR compliance for EU users
  - Data encryption in transit and at rest
  - Access control and authentication
  - Audit logging

## Templates

### Project Setup
- **Document:** [PROJECT-SETUP.md](./TEMPLATES/PROJECT-SETUP.md)
- **Contents:**
  - Repository initialization
  - Development environment setup
  - Dependencies and package managers
  - Configuration files

### Documentation Template
- **Document:** [DOCUMENTATION-TEMPLATE.md](./TEMPLATES/DOCUMENTATION-TEMPLATE.md)
- **Sections:**
  - Overview and features
  - Installation and setup
  - Usage examples
  - API documentation
  - Contributing guidelines

### Deployment Checklist
- **Document:** [DEPLOYMENT-CHECKLIST.md](./TEMPLATES/DEPLOYMENT-CHECKLIST.md)
- **Pre-deployment:** Testing, security, performance
- **Deployment:** Procedure, rollback plan
- **Post-deployment:** Monitoring, verification

## External Resources

### Primary Sources
1. **[Devin AI Documentation](https://docs.devin.ai)** - Autonomous AI development
2. **[Cursor Documentation](https://cursor.com/docs)** - AI-powered code editor
3. **[GitHub Copilot](https://github.com/features/copilot)** - GitHub native AI coding
4. **[Next.js Contributing Guide](https://github.com/vercel/next.js/blob/canary/contributing.md)** - React framework standards
5. **[VS Code Contributing Guide](https://github.com/microsoft/vscode/wiki/How-to-Contribute)** - Open source standards

### Community Collections
1. **[awesome-cursorrules](https://github.com/PatrickJS/awesome-cursorrules)** - 100+ Cursor configurations
2. **[SOPLibrary](https://github.com/peytontolbert/SOPLibrary)** - Comprehensive software engineering SOPs
3. **[GDI SOPs](https://github.com/GenomicDataInfrastructure/standard-operating-procedures)** - Enterprise governance framework
4. **[CDU GitHub SOP](https://cdu-data-science-team.github.io/team-blog/posts/2022-03-21-github-sop/)** - Practical Git workflows

### Website Builder Resources
1. **[Webflow Developer Docs](https://developers.webflow.com)** - No-code platform API
2. **[Wix Developer Docs](https://dev.wix.com/docs)** - Platform development
3. **[Squarespace Developers](https://developers.squarespace.com)** - Template development
4. **[Framer Plugins](https://github.com/framer/plugins)** - Plugin development

## Quick Reference

### For New Projects
1. Follow [PROJECT-SETUP.md](./TEMPLATES/PROJECT-SETUP.md)
2. Implement [GIT-WORKFLOW.md](./DEVELOPMENT-STANDARDS/GIT-WORKFLOW.md)
3. Review [BRANCHING-STRATEGY.md](./DEVELOPMENT-STANDARDS/BRANCHING-STRATEGY.md)
4. Setup [CI-CD.md](./DEVELOPMENT-STANDARDS/CI-CD.md)

### For Code Review
1. Reference [CODE-REVIEW.md](./DEVELOPMENT-STANDARDS/CODE-REVIEW.md)
2. Check [TESTING.md](./DEVELOPMENT-STANDARDS/TESTING.md)
3. Verify [SECRET-MANAGEMENT.md](./SECURITY/SECRET-MANAGEMENT.md)

### For Team Onboarding
1. Start with [README.md](./README.md)
2. Review [PROJECT-SETUP.md](./TEMPLATES/PROJECT-SETUP.md)
3. Study [GIT-WORKFLOW.md](./DEVELOPMENT-STANDARDS/GIT-WORKFLOW.md)
4. Learn [CODE-REVIEW.md](./DEVELOPMENT-STANDARDS/CODE-REVIEW.md)

---

**Total Resources:** 25+ SOPs from industry leaders
**Last Updated:** December 2024
**Version:** 1.0
