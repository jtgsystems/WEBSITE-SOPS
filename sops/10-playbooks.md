# Playbooks (Agent & Human-Autonomy Safe Defaults)

Use these turnkey sequences when spinning up autonomous runs for web projects. Each playbook is short, machine-executable, and policy-friendly.

## P0: Bugfix or Small UI Tweak
1) Pull feature branch scaffold (`git switch -c fix/<slug>`).
2) Run linters + typecheck locally: `pnpm lint && pnpm typecheck`.
3) Add/adjust unit test covering the regression; run `pnpm test --runInBand`.
4) If UI: run visual snapshot test for touched component/page.
5) Run Lighthouse CI on affected route (mobile + desktop).
6) Commit with bot identity `bot/fix: <summary>`; open PR with short changelog and test commands.

## P1: New Section/Page Build (Marketing/LP)
1) Generate content draft (headings, body, CTA) from brief; check brand tone guidelines.
2) Create page scaffold under `app/.../page.tsx`; keep components in `components/`.
3) Apply design tokens; ensure 44px targets, focus-visible styles, skip links present.
4) Add schema.org for page type (Article/Product/Service/Breadcrumb/FAQ as needed).
5) Optimize assets: WebP/AVIF, responsive `srcset`, lazy/background priority tags.
6) Validate: lint, typecheck, unit + integration for forms/CTA, visual diff.
7) Run Lighthouse CI (Perf ≥90, A11y ≥95, BP ≥95, SEO ≥95) on route.
8) Update sitemap, robots if needed; ensure canonical/hreflang set; add Open Graph/Twitter.
9) Commit `bot/feat: add <page>`; PR with before/after screenshots + Lighthouse JSON.

## P2: Component Refactor / Design System Upgrade
1) Snapshot current stories/visuals; lock baselines in Chromatic/Playwright trace.
2) Refactor component in isolation; keep API-compatible props or add codemod.
3) Add/extend unit + visual tests; ensure accessibility props intact (aria-*, keyboard traps, focus ring).
4) Run bundle-size check for component chunk (limit regressions < +5KB gz).
5) Run tree-shaking check (unused exports flagged).
6) Storybook accessibility scan + interaction tests; update MDX docs.
7) Lighthouse CI on pages using the component; verify Core Web Vitals budgets unchanged.
8) Commit `bot/refactor: <component>`; PR with migration notes and test matrix.

## P3: Performance Hardening Sprint (Page/Flow)
1) Baseline: WebPageTest/LH (mobile, cable); record LCP element & INP events.
2) Reduce JS: defer non-critical, code-split route, remove unused 3P tags; cap JS <200KB gz.
3) Optimize images/fonts: preload hero, use `font-display: swap`, convert hero to AVIF/WebP.
4) Add priority hints (`fetchpriority`, `rel=preload/prefetch`) for hero image, critical CSS, API.
5) Implement RUM beacons (LCP/INP/CLS/TTFB); alert p75 budgets.
6) Re-run LH/WPT; compare vs baseline; ensure budgets met.
7) Commit `bot/perf: harden <page>`; PR with before/after charts and main lighthouse report.

## P4: AI/Agent Feature Release
1) Define spec: inputs/outputs, safety rules, denied tools, API/time budget, log redaction.
2) Add guardrails: prompt templates, allow/deny lists, policy checks, sandbox FS/network.
3) Implement handler + tests (goldens for prompts/responses, injection tests, rate limits).
4) Run automated prompt-attack suite; log defenses.
5) Add UI affordances: disclosure, user control, retry/stop, feedback capture.
6) Add monitoring: prompt/response logs (redacted), latency, token spend, error rate.
7) Final Lighthouse + a11y on surfaces using the feature.
8) Commit `bot/feat: ai <feature>`; PR with safety report and test evidence.

## P5: Incident Patch / Hotfix
1) Freeze deploys except hotfix; create `hotfix/<slug>`.
2) Reproduce in staging; add failing test first.
3) Minimal fix; run targeted tests + linters + typecheck.
4) Smoke in staging; Lighthouse quick run if UI.
5) Tag hotfix release; backport to main; open retro issue.

## P6: Content Freeze & Launch Finalization
1) Lock content; rerun schema/OG/canonical/hreflang validators.
2) Run final Lighthouse/PSI on top pages; ensure budgets met.
3) Verify security headers A-grade; rerun CSP check for inline/script allowances.
4) Validate analytics/consent firing with third-party cookies blocked.
5) Enable uptime and synthetic flows; set launch banner if needed.
6) Prepare rollback plan and owner on-call list.

## P7: Post-Launch 48h
1) Monitor RUM p75 LCP/INP/CLS; alert on regressions.
2) Track errors (Sentry), logs, and support tickets; triage hourly.
3) Validate analytics integrity; compare to pre-launch baselines.
4) Run quick LH sample; ensure vitals steady.
5) File follow-ups for any regressions; schedule patch window.
