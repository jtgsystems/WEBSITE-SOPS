# Project Setup Template - Standard Operating Procedures

**Type:** Template & Checklist
**Scope:** New project initialization
**Source:** Best practices from Next.js, GitHub, industry standards

## Project Initialization Checklist

### Phase 1: Repository Setup

**GitHub Repository Creation:**
- [ ] Create repository on GitHub
- [ ] Set repository description
- [ ] Set repository topics/tags
- [ ] Choose appropriate license (MIT/Apache 2.0 for open source)
- [ ] Enable Issues
- [ ] Enable Discussions
- [ ] Enable GitHub Pages (if needed)
- [ ] Add main branch protection rules
- [ ] Add develop branch protection rules

**Branch Protection Configuration:**
```
Main Branch:
  - Require pull request reviews: 2
  - Require status checks to pass: Yes
  - Require branches to be up to date: Yes
  - Require code review from owners: Yes
  - Automatically delete head branches: Yes

Develop Branch:
  - Require pull request reviews: 1
  - Require status checks to pass: Yes
  - Allow auto-merge: Yes
  - Automatically delete head branches: Yes
```

**Initial Files:**
- [ ] Create `.gitignore`
- [ ] Create `README.md`
- [ ] Create `LICENSE` file
- [ ] Create `.env.example`
- [ ] Create `CONTRIBUTING.md`
- [ ] Create `.github/CODE_OF_CONDUCT.md`

### Phase 2: Project Structure

**Create Directory Structure:**
```bash
mkdir -p {src/{app,components,lib,types,styles},tests/{unit,integration},public,docs,.github/workflows}
```

**Initialize Node.js Project:**
```bash
# Initialize package.json
npm init -y

# Or for specific setup
npm create next-app@latest
# or
npm create vite@latest
# or
npx create-react-app
```

**Directory Confirmation:**
```
project/
├── .github/
│   ├── workflows/
│   │   ├── ci.yml
│   │   ├── test.yml
│   │   └── deploy.yml
│   └── CODE_OF_CONDUCT.md
├── public/
├── src/
│   ├── app/
│   ├── components/
│   ├── lib/
│   ├── types/
│   └── styles/
├── tests/
│   ├── unit/
│   └── integration/
├── docs/
├── .gitignore
├── .env.example
├── README.md
├── LICENSE
├── CONTRIBUTING.md
├── package.json
├── package-lock.json
└── tsconfig.json
```

### Phase 3: Dependencies & Configuration

**Install Core Dependencies:**
```bash
# TypeScript support
npm install --save-dev typescript @types/node @types/react

# Linting & Formatting
npm install --save-dev eslint prettier eslint-config-next

# Testing
npm install --save-dev jest @testing-library/react @testing-library/jest-dom

# Git hooks
npm install --save-dev husky lint-staged
npx husky install

# Build tools (if not using framework defaults)
npm install --save-dev webpack webpack-cli
```

**TypeScript Configuration (tsconfig.json):**
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "jsx": "react-jsx",
    "module": "ESNext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "allowImportingTsExtensions": true,
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src", "tests"],
  "exclude": ["node_modules"]
}
```

**ESLint Configuration (.eslintrc.json):**
```json
{
  "extends": ["next/core-web-vitals", "prettier"],
  "rules": {
    "@typescript-eslint/no-unused-vars": "warn",
    "@typescript-eslint/no-explicit-any": "error",
    "react/no-unescaped-entities": "warn",
    "prefer-const": "error",
    "no-var": "error"
  }
}
```

**Prettier Configuration (.prettierrc):**
```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "arrowParens": "always"
}
```

### Phase 4: Environment Configuration

**Create .env.example:**
```bash
# API Configuration
API_URL=http://localhost:3001
API_KEY=your_api_key_here

# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/dbname

# Authentication
JWT_SECRET=your_jwt_secret_key
NEXTAUTH_SECRET=your_nextauth_secret
NEXTAUTH_URL=http://localhost:3000

# Third-party Services
STRIPE_SECRET_KEY=sk_test_...
GITHUB_CLIENT_ID=your_github_id
GITHUB_CLIENT_SECRET=your_github_secret

# Feature Flags
ENABLE_ANALYTICS=true
DEBUG_MODE=false
```

**Create .env.local (local development only):**
```bash
# Copy from .env.example and fill in actual values
cp .env.example .env.local

# Edit .env.local with your local configuration
# This file should NEVER be committed to git
```

### Phase 5: Git Hooks & Pre-commit

**Initialize Husky:**
```bash
npx husky install
npx husky add .husky/pre-commit "npm run lint-staged"
npx husky add .husky/commit-msg "npx --no -- commitlint --edit"
```

**Configure lint-staged (package.json):**
```json
{
  "lint-staged": {
    "*.{ts,tsx}": ["eslint --fix", "prettier --write"],
    "*.{json,css,md}": ["prettier --write"]
  }
}
```

### Phase 6: GitHub Actions CI/CD

**Create .github/workflows/test.yml:**
```yaml
name: Tests

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run linter
        run: npm run lint

      - name: Run tests
        run: npm test

      - name: Build project
        run: npm run build

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        if: always()
```

### Phase 7: Scripts & Commands

**Update package.json scripts:**
```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "eslint src --ext .ts,.tsx",
    "lint:fix": "eslint src --ext .ts,.tsx --fix",
    "format": "prettier --write \"src/**/*.{ts,tsx,json,md}\"",
    "type-check": "tsc --noEmit",
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "deploy": "vercel --prod",
    "clean": "rm -rf .next dist build node_modules"
  }
}
```

### Phase 8: Documentation

**README.md Structure:**
```markdown
# Project Name

Brief description of the project.

## Features

- Feature 1
- Feature 2
- Feature 3

## Quick Start

### Prerequisites
- Node.js 18+
- npm or yarn

### Installation
\`\`\`bash
git clone <repo-url>
cd <project>
npm install
cp .env.example .env.local
npm run dev
\`\`\`

### Configuration
Edit `.env.local` with your credentials.

## Development

\`\`\`bash
# Start dev server
npm run dev

# Run tests
npm test

# Build for production
npm run build

# Run linter
npm run lint
\`\`\`

## Project Structure

- `/src` - Source code
- `/tests` - Test files
- `/public` - Static assets
- `/docs` - Documentation

## API Documentation

[Add API docs or link to them]

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md)

## License

[Your License]

## Support

For questions, open an [issue](../../issues) or see [discussions](../../discussions).
```

**CONTRIBUTING.md:**
```markdown
# Contributing Guide

Thank you for your interest in contributing!

## Getting Started

1. Fork the repository
2. Clone your fork: \`git clone <your-fork-url>\`
3. Create a branch: \`git checkout -b feature/your-feature\`
4. Make your changes
5. Commit: \`git commit -m "feat: description"\`
6. Push: \`git push origin feature/your-feature\`
7. Open a Pull Request

## Guidelines

- Follow the code style (ESLint, Prettier)
- Write tests for new features
- Update documentation
- Use conventional commits
- Link related issues

## Code of Conduct

Be respectful and inclusive. See [CODE_OF_CONDUCT.md](./.github/CODE_OF_CONDUCT.md)
```

### Phase 9: Team Setup

**Invite Team Members:**
- [ ] Add collaborators with appropriate permissions
- [ ] Configure team access levels
- [ ] Setup GitHub organization (if needed)
- [ ] Create team discussions channel
- [ ] Share documentation

**Team Communication:**
- [ ] Share `.env.example` securely
- [ ] Provide local setup instructions
- [ ] Schedule onboarding session
- [ ] Document common workflows

### Phase 10: Initial Commits

**Create Initial Commit Structure:**
```bash
# Initialize and commit structure
git add .
git commit -m "chore: initial project setup

- Create project structure
- Configure TypeScript, ESLint, Prettier
- Setup GitHub Actions workflows
- Add documentation templates"

# Create develop branch
git checkout -b develop
git push origin develop

# Push main
git push origin main
```

## Verification Checklist

**Before Starting Development:**

- [ ] Repository is created and accessible
- [ ] Branch protection rules configured
- [ ] `.gitignore` is comprehensive
- [ ] `.env.example` is properly set up
- [ ] TypeScript configured correctly
- [ ] ESLint and Prettier configured
- [ ] Test framework installed
- [ ] Git hooks working (pre-commit)
- [ ] GitHub Actions workflows running
- [ ] README is clear and complete
- [ ] Team members have access
- [ ] `.env.local` is NOT committed
- [ ] Documentation is up-to-date

## Quick Reference

| Task | Command |
|------|---------|
| Install dependencies | `npm install` |
| Start dev server | `npm run dev` |
| Run tests | `npm test` |
| Lint code | `npm run lint` |
| Format code | `npm run format` |
| Build for production | `npm run build` |
| Create feature branch | `git checkout -b feature/name` |
| Commit changes | `git commit -m "type: message"` |

## Related Resources

- [GitHub Quickstart](https://docs.github.com/en/get-started/quickstart)
- [Next.js Setup Guide](https://nextjs.org/docs/getting-started)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [Git Workflow Guide](./GIT-WORKFLOW.md)

---

**Last Updated:** December 2024
**Version:** 1.0
**Status:** Active
