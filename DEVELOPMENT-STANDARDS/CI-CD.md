# CI/CD Pipeline - Standard Operating Procedure

**Version:** 1.0
**Last Updated:** December 2024
**Platform:** GitHub Actions (Primary)

---

## Overview

This SOP defines standards for Continuous Integration and Continuous Deployment pipelines. Proper CI/CD ensures consistent builds, automated testing, and reliable deployments.

---

## Pipeline Architecture

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Commit    │───▶│    Build    │───▶│    Test     │───▶│   Deploy    │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
                          │                  │                  │
                          ▼                  ▼                  ▼
                   ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
                   │    Lint     │    │   Security  │    │   Monitor   │
                   │   Format    │    │    Scan     │    │   Notify    │
                   └─────────────┘    └─────────────┘    └─────────────┘
```

---

## GitHub Actions Workflows

### Main CI Workflow

```yaml
# .github/workflows/ci.yml
name: CI Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

env:
  NODE_VERSION: '20'
  PNPM_VERSION: '8'

jobs:
  # ============================================
  # QUALITY CHECKS
  # ============================================
  lint:
    name: Lint & Format
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}

      - name: Install pnpm
        uses: pnpm/action-setup@v2
        with:
          version: ${{ env.PNPM_VERSION }}

      - name: Get pnpm store
        id: pnpm-cache
        run: echo "STORE_PATH=$(pnpm store path)" >> $GITHUB_OUTPUT

      - name: Cache dependencies
        uses: actions/cache@v4
        with:
          path: ${{ steps.pnpm-cache.outputs.STORE_PATH }}
          key: ${{ runner.os }}-pnpm-${{ hashFiles('**/pnpm-lock.yaml') }}
          restore-keys: ${{ runner.os }}-pnpm-

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Run ESLint
        run: pnpm lint

      - name: Check formatting
        run: pnpm format:check

      - name: Type check
        run: pnpm type-check

  # ============================================
  # SECURITY SCANNING
  # ============================================
  security:
    name: Security Scan
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          scan-ref: '.'
          format: 'sarif'
          output: 'trivy-results.sarif'

      - name: Upload Trivy scan results
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: 'trivy-results.sarif'

      - name: Check for secrets
        uses: trufflesecurity/trufflehog@main
        with:
          path: ./
          base: ${{ github.event.pull_request.base.sha }}
          head: ${{ github.event.pull_request.head.sha }}

  # ============================================
  # TESTING
  # ============================================
  test:
    name: Unit & Integration Tests
    runs-on: ubuntu-latest
    needs: lint
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_USER: test
          POSTGRES_PASSWORD: test
          POSTGRES_DB: test
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

      redis:
        image: redis:7
        ports:
          - 6379:6379

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}

      - uses: pnpm/action-setup@v2
        with:
          version: ${{ env.PNPM_VERSION }}

      - run: pnpm install --frozen-lockfile

      - name: Run tests
        run: pnpm test:ci
        env:
          DATABASE_URL: postgresql://test:test@localhost:5432/test
          REDIS_URL: redis://localhost:6379

      - name: Upload coverage
        uses: codecov/codecov-action@v4
        with:
          token: ${{ secrets.CODECOV_TOKEN }}
          files: ./coverage/lcov.info

  # ============================================
  # BUILD
  # ============================================
  build:
    name: Build
    runs-on: ubuntu-latest
    needs: [lint, test]
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}

      - uses: pnpm/action-setup@v2
        with:
          version: ${{ env.PNPM_VERSION }}

      - run: pnpm install --frozen-lockfile

      - name: Build application
        run: pnpm build
        env:
          NODE_ENV: production

      - name: Upload build artifacts
        uses: actions/upload-artifact@v4
        with:
          name: build-output
          path: |
            .next/
            dist/
          retention-days: 7

  # ============================================
  # E2E TESTS
  # ============================================
  e2e:
    name: E2E Tests
    runs-on: ubuntu-latest
    needs: build
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}

      - uses: pnpm/action-setup@v2
        with:
          version: ${{ env.PNPM_VERSION }}

      - run: pnpm install --frozen-lockfile

      - name: Install Playwright
        run: pnpm exec playwright install --with-deps

      - name: Download build
        uses: actions/download-artifact@v4
        with:
          name: build-output

      - name: Run E2E tests
        run: pnpm test:e2e

      - name: Upload test results
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: playwright-report
          path: playwright-report/
```

### Deployment Workflow

```yaml
# .github/workflows/deploy.yml
name: Deploy

on:
  push:
    branches: [main]
  workflow_dispatch:
    inputs:
      environment:
        description: 'Deployment environment'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production

jobs:
  deploy-staging:
    name: Deploy to Staging
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment:
      name: staging
      url: https://staging.example.com
    steps:
      - uses: actions/checkout@v4

      - name: Deploy to Vercel (Staging)
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          scope: ${{ secrets.VERCEL_ORG_ID }}

  deploy-production:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: deploy-staging
    if: github.event_name == 'workflow_dispatch' && github.event.inputs.environment == 'production'
    environment:
      name: production
      url: https://example.com
    steps:
      - uses: actions/checkout@v4

      - name: Deploy to Vercel (Production)
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          vercel-args: '--prod'
          scope: ${{ secrets.VERCEL_ORG_ID }}

      - name: Notify Slack
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          channel: '#deployments'
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```

---

## Environment Configuration

### GitHub Secrets Management

```yaml
# Required secrets per environment
secrets:
  # Shared
  - CODECOV_TOKEN
  - SLACK_WEBHOOK

  # Staging
  - STAGING_DATABASE_URL
  - STAGING_API_KEY

  # Production
  - PROD_DATABASE_URL
  - PROD_API_KEY

  # Deployment
  - VERCEL_TOKEN
  - VERCEL_ORG_ID
  - VERCEL_PROJECT_ID
```

### Environment Protection Rules

```yaml
# GitHub Environment Settings
environments:
  staging:
    protection_rules:
      - required_reviewers: 0
      - wait_timer: 0
    deployment_branches:
      - main
      - develop

  production:
    protection_rules:
      - required_reviewers: 1
      - wait_timer: 5  # 5-minute delay
    deployment_branches:
      - main
```

---

## Pipeline Stages

### 1. Build Stage

```yaml
build:
  steps:
    - checkout
    - setup-node
    - cache-dependencies
    - install-dependencies
    - compile-typescript
    - bundle-assets
    - generate-artifacts
```

### 2. Test Stage

```yaml
test:
  parallel:
    - unit-tests
    - integration-tests
    - security-scan
    - dependency-audit
  sequential:
    - e2e-tests (after build)
```

### 3. Deploy Stage

```yaml
deploy:
  staging:
    trigger: push to main
    approval: none
    rollback: automatic

  production:
    trigger: manual or tag
    approval: required
    rollback: manual
```

---

## Caching Strategies

### Dependency Caching

```yaml
- name: Cache node modules
  uses: actions/cache@v4
  with:
    path: |
      ~/.pnpm-store
      node_modules
    key: ${{ runner.os }}-pnpm-${{ hashFiles('**/pnpm-lock.yaml') }}
    restore-keys: |
      ${{ runner.os }}-pnpm-
```

### Build Caching

```yaml
- name: Cache Next.js build
  uses: actions/cache@v4
  with:
    path: |
      .next/cache
    key: ${{ runner.os }}-nextjs-${{ hashFiles('**/pnpm-lock.yaml') }}-${{ hashFiles('**/*.ts', '**/*.tsx') }}
    restore-keys: |
      ${{ runner.os }}-nextjs-${{ hashFiles('**/pnpm-lock.yaml') }}-
```

---

## Notifications

### Slack Integration

```yaml
- name: Notify on failure
  if: failure()
  uses: 8398a7/action-slack@v3
  with:
    status: ${{ job.status }}
    fields: repo,message,commit,author,action,workflow
    channel: '#ci-alerts'
  env:
    SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```

### GitHub Status Checks

```yaml
# Required status checks for protected branches
required_status_checks:
  strict: true
  contexts:
    - lint
    - test
    - build
    - security
```

---

## Rollback Procedures

### Automated Rollback

```yaml
- name: Health check
  run: |
    for i in {1..5}; do
      if curl -sf ${{ env.DEPLOY_URL }}/health; then
        exit 0
      fi
      sleep 10
    done
    exit 1

- name: Rollback on failure
  if: failure()
  run: |
    vercel rollback --token=${{ secrets.VERCEL_TOKEN }}
```

### Manual Rollback

```bash
# Rollback to previous deployment
vercel rollback

# Rollback to specific deployment
vercel rollback <deployment-url>

# Git-based rollback
git revert HEAD
git push origin main
```

---

## Monitoring & Metrics

### Pipeline Metrics

```yaml
# Track these metrics
metrics:
  - build_duration
  - test_pass_rate
  - deployment_frequency
  - lead_time_for_changes
  - mean_time_to_recovery
  - change_failure_rate
```

### Alerting Thresholds

```yaml
alerts:
  - name: slow_build
    condition: build_duration > 10m
    channel: '#ci-alerts'

  - name: high_failure_rate
    condition: test_pass_rate < 95%
    channel: '#ci-alerts'

  - name: deployment_failed
    condition: deployment_status == 'failed'
    channel: '#deployments'
```

---

## Best Practices

### Pipeline Optimization

```markdown
DO:
- Cache dependencies and build artifacts
- Run independent jobs in parallel
- Fail fast on critical errors
- Use matrix builds for cross-platform testing
- Keep workflows DRY with reusable actions

DON'T:
- Install dependencies in every job
- Run E2E tests on every commit
- Store secrets in workflow files
- Skip tests to speed up builds
- Ignore flaky tests
```

### Security

```markdown
Security requirements:
- Use GitHub Secrets for sensitive data
- Limit workflow permissions (least privilege)
- Pin action versions to SHA
- Scan for vulnerabilities in dependencies
- Review third-party actions before use
```

---

## Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Vercel Deployment](https://vercel.com/docs/deployments)
- [DORA Metrics](https://cloud.google.com/blog/products/devops-sre/dora-2022-accelerate-state-of-devops-report-now-out)

---

**Document Version:** 1.0
**Maintained by:** DevOps Team
**Review Cycle:** Quarterly
