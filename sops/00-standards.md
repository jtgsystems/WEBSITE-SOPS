# 🔍 Quality Control Gates for Website Design

## 2025 Additional Standards (Applies to All Gates)
- [ ] **Governance & Compliance Gate**
  - Validate data classification (public/internal/confidential/PII/PHI/PCI) and lawful bases (GDPR/CCPA/UK GDPR/PCI DSS/COPPA where applicable)
  - Confirm Records of Processing Activities (RoPA) and Data Protection Impact Assessments are completed for new/changed data flows
  - Ensure contracts & DPAs exist for every third-party processor and marketing/analytics script; document data egress locations
  - Verify legal content (Terms, Privacy, Cookies, Accessibility, Imprint) is current with jurisdictional requirements and surfaced in the footer
  - Require cookie consent/management platform with region-aware defaults and granular opt-ins; audit firing rules

- [ ] **Privacy by Design Gate**
  - Minimize collection of personal data; specify retention/deletion rules and run test deletions (Right to Erasure/Access exports)
  - Provide opt-out controls for analytics, personalization, and AI features; honor GPC/Do-Not-Track where required
  - Mask/obfuscate PII in logs, traces, and analytics events; prohibit storing secrets or tokens client-side
  - Validate secure data transport (HTTPS/HSTS), at-rest encryption choices, and key rotation schedules

- [ ] **Secure Development Lifecycle Gate**
  - Perform lightweight threat modeling (STRIDE/LINDDUN) for new features; record mitigations
  - Generate SBOM and run SCA (dependency) plus SAST and DAST scans; gate merges on critical/high vulns
  - Enforce secret scanning in CI and verify `.env` handling; no secrets in repo, commits, or frontend bundles
  - Require security headers baseline (CSP, HSTS, XFO, XSS-Protection, Referrer-Policy, Permissions-Policy, COOP/COEP/CORP)
  - Plan annual penetration testing or after major architectural changes

- [ ] **Reliability & Observability Gate**
  - Define SLIs/SLOs (availability, latency, error rate) and error budgets; add runbooks and on-call rotation
  - Instrument logging, metrics, and tracing (OpenTelemetry preferred); ensure PII-safe log policies
  - Configure uptime/synthetic checks for key journeys; add client-side RUM for Core Web Vitals and INP
  - Verify incident response flow (paging, status page updates, postmortems) and escalation ladder

- [ ] **Performance & Sustainability Gate**
  - Set performance budgets: LCP < 2.5s, INP < 200ms, CLS < 0.1, TTFB < 800ms, JS per page < 200KB gz; track in CI
  - Use priority hints/preload/prefetch, HTTP/3/QUIC, adaptive loading, and image/CDN optimization (AVIF/WEBP, responsive srcset)
  - Measure energy/CO₂ impact where possible; avoid unnecessary animations on low-power devices

- [ ] **Accessibility 2025 Gate**
  - Align with WCAG 2.2 AA (focus indicators, dragging alternatives, target size 24x24px, error prevention)
  - Support reduced motion/transparency/media preferences; ensure accessible authentication without cognitive tests
  - Provide captions, transcripts, and audio descriptions; verify accessible multimedia players
  - Validate mobile and touch accessibility (gestures, hit areas, orientation changes)

- [ ] **Internationalization & Localization Gate**
  - Implement i18n framework: locale detection, `hreflang`, language switcher, RTL layouts, and pluralization rules
  - Localize dates, numbers, currencies, time zones, and addresses; avoid hard-coded strings
  - Maintain translation workflow (TM/glossary, review loop); ensure fallbacks and untranslated-string monitoring

- [ ] **SEO, Discovery & Content Quality Gate**
  - Maintain XML sitemap, robots directives, canonical/alternate tags; validate crawl budget and 404/410 behaviors
  - Add structured data (schema.org) for key templates; validate with Rich Results tests
  - Standardize titles, meta descriptions, Open Graph/Twitter tags; monitor broken links and redirect maps
  - Demonstrate E-E-A-T: clear authorship, dates, sources, and fact-checking for high-stakes content

- [ ] **Payments & Commerce Gate (if applicable)**
  - Enforce PSD2/SCA, PCI DSS scope controls, and secure payment tokenization; forbid card data persistence
  - Validate tax/shipping calculations, address verification, and fraud/risk checks (3DS, velocity rules)
  - Provide compliant receipts/invoices and refund/chargeback flows

- [ ] **Release & Change Management Gate**
  - Use feature flags, canary/blue-green deploys, and safe rollbacks; document migration/back-out steps
  - Require QA sign-off with regression packs for critical paths; gate releases on green CI (lint, tests, build, security scans)
  - Keep deployment audit logs and approvals; tag releases and changelogs for traceability

- [ ] **AI/ML Use Gate (if applicable)**
  - Disclose model use, limitations, and data sources; provide user control for AI-generated outputs
  - Evaluate bias, safety, and prompt-injection defenses; log prompts/responses with PII redaction
  - Separate training/serving data; ensure consent for any user data used in fine-tuning

- [ ] **Agent-Assisted Delivery Gate (AutoGPT/AutoGen/Devin)**
  - Keep agents in isolated sandboxes (separate devbox/VM) with least-privilege network/file access; no production creds
  - Require plan → risk-eval → auto-execute → auto-report loop; approvals enforced by policy engine, not humans
  - Enforce runtime guardrails (e.g., policy/AgentSpec) for risky actions: file writes, network calls, shell, deployments; block on policy violations and log interventions
  - Track and enforce API/runtime budgets; fail fast when budget/time thresholds hit; surface remaining budget to operators
  - Maintain SBOM + dependency pinning and apply security advisories for agent frameworks; forbid legacy/`classic` AutoGPT code path
  - Version and test prompts/agent configs; measure stability/variance of auto-generated prompts before rollout
  - Log every agent action (prompt, tool call, file diff) to immutable storage with PII/key redaction
  - Provide explicit allowlist/denylist for commands and domains; disable self-updates and package installs unless pre-approved
  - Freeze production environments: agents may only touch feature branches or preview deployments; merges/deploys gated by automated policy checks

- [ ] **Backup, DR & Business Continuity Gate**
  - Define RPO/RTO per system; schedule tested restores for databases, assets, and critical configs
  - Validate multi-region/zone redundancy where needed; document dependency failovers
  - Run chaos or game-day exercises at least annually

- [ ] **Data & Asset Lifecycle Gate**
  - Track media licensing/attribution; verify expiration and usage rights
  - Define retention/deletion schedules for content, analytics events, and backups; test purge jobs

- [ ] **Analytics & Experimentation Gate**
  - Standardize event taxonomy (name, schema, PII classification); validate firing and deduplication
  - Ensure A/B tests have guardrails (SRM checks, exposure caps, kill switches) and documented hypotheses
  - Respect consent and geo rules before firing analytics/marketing pixels; provide server-side tagging where possible
