## Operational & Governance Gates
- [ ] **Privacy, Legal & Compliance Gate**
  - Validate regional privacy laws (GDPR, CCPA, HIPAA, etc.) and data residency needs
  - Confirm consent management, cookie policies, and data retention schedules
  - Review terms of use, privacy policy, and accessibility statements with legal stakeholders
  - Assess third-party contracts, DPAs, and vendor risk documentation
  - Honor Global Privacy Control/universal opt-out signals (e.g., Colorado recognizes GPC as UOOM) and document how signals are enforced
  - Track AI transparency/labeling duties ahead of EU AI Act Article 50 enforcement (effective August 2, 2026) for synthetic media
  - Evaluate incident response, breach notification plans, and compliance audit evidence

- [ ] **DevOps & Observability Gate**
  - Confirm infrastructure-as-code definitions, environment parity, and rollback strategies
  - Validate CI/CD pipelines, quality gates, and artifact provenance
  - Review logging, metrics, tracing, and synthetic monitoring coverage
  - Assess alert routing, on-call playbooks, and disaster-recovery drills
  - Evaluate capacity planning, autoscaling thresholds, and cost optimization
  - Enable web-layer rate limiting/WAF and basic brute-force protections (e.g., Fail2Ban) plus file-integrity monitoring (e.g., AIDE); aggregate security logs centrally
  - Monitor autonomous/agent jobs separately with clear ownership, SLAs, and automated kill-switches; audit all actions

- [ ] **Content Governance & Localization Gate**
  - Confirm editorial workflows, approval matrices, and CMS permissions
  - Validate copy editing, tone-of-voice, and brand narrative consistency
  - Review structured data markup, schema updates, and content personalization rules
  - Assess localization quality, cultural reviews, and fallback language handling
  - Evaluate content lifecycle plans (retirement, archival, refresh cadence)

- [ ] **Support & Feedback Readiness Gate**
  - Prepare help center articles, FAQs, and escalation scripts
  - Train customer support teams and confirm issue triage SLAs
  - Set up in-product feedback widgets, surveys, and qualitative listening posts
  - Review CRM/marketing automation integrations for post-launch nurturing
  - Establish closed-loop processes for prioritizing insights into the roadmap
