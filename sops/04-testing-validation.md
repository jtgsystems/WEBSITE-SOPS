## Testing & Validation Gates
- [ ] **Functional Testing Gate**
  - Validate all features work as specified
  - Assess cross-browser compatibility (Chrome, Firefox, Safari, Edge)
  - Test key journeys with third-party cookies blocked (Safari/Firefox defaults and Chrome Tracking Protection) to avoid breakage while Chrome’s cookie roadmap evolves
  - Confirm responsive behavior across all devices
  - Review form validation and error handling
  - Evaluate integration with external services/APIs
  - For agent-generated changes, auto-run reproducible test scripts from agent logs in clean ephemeral environments
  - Run static analysis and lint in CI (ESLint, TypeScript strict, stylelint/Tailwind lint); block on any warning-level issues for shipping branches
  - Require Lighthouse/PSI or WebPageTest checks for key templates; fail gate if Core Web Vitals budgets regress

- [ ] **Accessibility Compliance Gate**
  - Validate WCAG 2.1 AA compliance (and WCAG 2.2 additions: focus not obscured, target size 24x24px, dragging alternatives)
  - Assess screen reader compatibility
  - Confirm keyboard navigation support
  - Verify visible focus indicators on all interactive elements (focus-visible rings) and presence of skip-to-content/skip-to-footer links
  - Check mobile/touch targets meet 44x44px minimum for nav/footer links and critical controls
  - Review color contrast and visual accessibility
  - Evaluate focus management and ARIA implementation

- [ ] **User Acceptance Testing Gate**
  - Conduct comprehensive user testing scenarios
  - Validate business requirements fulfillment
  - Assess user satisfaction and usability metrics
  - Review stakeholder feedback and sign-off
  - Confirm training materials and documentation completeness
