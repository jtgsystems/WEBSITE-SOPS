## Development Gates
- [ ] **Code Quality Gate**
  - Review code structure and architecture decisions
  - Validate adherence to coding standards and best practices
  - Assess code maintainability and documentation
  - Confirm version control and collaboration practices
  - Enforce automated linters/formatters (ESLint, Prettier/Tailwind lint) on every change; fail CI on style or unused code
  - Require unit/integration test coverage targets and mutation tests on critical paths; block merges on coverage drop
  - Enforce automated policy checks on AI/agent-generated diffs; block merges if missing tests or intent notes
  - Agents commit only to feature branches with bot identities; merges happen via automated quality gates (tests, lint, policy) and branch protection
  - Provide pre-commit hooks and local parity scripts the agent can run (lint, typecheck, tests) to reduce CI/agent drift
  - Require agent self-verification: rerun generated tests/specs locally and bail out with failure summary if red; no auto-retries beyond budget

- [ ] **Performance Optimization Gate**
  - Validate Core Web Vitals metrics (LCP, INP, CLS)
  - Track Interaction to Next Paint (INP) as the primary responsiveness metric (replaced FID in March 2024); keep INP <200ms in field data
  - Assess loading speed and optimization techniques
  - Review resource optimization (images, fonts, scripts)
  - Confirm caching strategies and CDN implementation
  - Evaluate mobile performance and battery impact
  - Add runtime Web Vitals beacons (LCP/INP/CLS/TTFB/FID) using `web-vitals` and ship to analytics for real-user monitoring

- [ ] **Security Review Gate**
  - Assess input validation and sanitization
  - Validate authentication and authorization mechanisms
  - Review data protection and privacy compliance
  - Confirm secure coding practices and vulnerability scanning
  - Evaluate third-party dependency security
