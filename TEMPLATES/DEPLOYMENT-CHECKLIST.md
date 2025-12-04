# Deployment Checklist Template

**Version:** 1.0
**Last Updated:** December 2025
**Use:** Copy this template for each deployment

---

## Deployment Information

| Field | Value |
|-------|-------|
| **Deployment Date** | |
| **Version/Release** | |
| **Environment** | [ ] Staging [ ] Production |
| **Deployment Lead** | |
| **Approver** | |

---

## Pre-Deployment Checklist

### Code Readiness

- [ ] All PRs merged and approved
- [ ] Code freeze in effect
- [ ] Version number updated
- [ ] CHANGELOG updated
- [ ] Feature flags configured

### Testing Complete

- [ ] Unit tests passing (coverage > 80%)
- [ ] Integration tests passing
- [ ] E2E tests passing
- [ ] Performance tests completed
- [ ] Security scan completed (no critical/high issues)
- [ ] Accessibility audit passed
- [ ] Cross-browser testing completed
- [ ] Mobile responsiveness verified

### Documentation

- [ ] API documentation updated
- [ ] User documentation updated
- [ ] Release notes prepared
- [ ] Runbook/playbook updated
- [ ] Known issues documented

### Infrastructure

- [ ] Environment variables configured
- [ ] Secrets rotated if needed
- [ ] Database migrations ready
- [ ] CDN cache purge planned
- [ ] DNS changes prepared (if any)
- [ ] SSL certificates valid (> 30 days)
- [ ] Scaling configured appropriately

### Stakeholder Communication

- [ ] Team notified of deployment window
- [ ] Support team briefed on changes
- [ ] Customer communication prepared (if needed)
- [ ] Status page update drafted

---

## Deployment Procedure

### Step 1: Final Verification

```bash
# Verify build
npm run build
npm run test

# Check environment
echo $NODE_ENV
echo $DATABASE_URL | head -c 20

# Verify git status
git log -1 --oneline
git status
```

- [ ] Build successful
- [ ] Environment verified
- [ ] Correct commit/tag

### Step 2: Database Migration

```bash
# Backup database first
pg_dump -h $DB_HOST -U $DB_USER $DB_NAME > backup_$(date +%Y%m%d).sql

# Run migrations
npm run migrate

# Verify migrations
npm run migrate:status
```

- [ ] Database backed up
- [ ] Migrations executed successfully
- [ ] Migration status verified

### Step 3: Deploy Application

```bash
# Deploy to platform
vercel deploy --prod
# OR
npm run deploy:production
```

- [ ] Deployment initiated
- [ ] Deployment completed without errors
- [ ] Deployment URL accessible

### Step 4: Smoke Tests

```bash
# Run smoke tests
npm run test:smoke

# Manual verification
curl -I https://your-app.com/health
curl -I https://your-app.com/api/status
```

- [ ] Health endpoint responding
- [ ] API endpoints responding
- [ ] Static assets loading
- [ ] Authentication working
- [ ] Critical user flows verified

### Step 5: Monitoring Verification

- [ ] Error rates normal
- [ ] Response times acceptable
- [ ] No memory/CPU anomalies
- [ ] Log aggregation working
- [ ] Alerts configured

---

## Post-Deployment Checklist

### Immediate (0-1 hour)

- [ ] Monitor error rates
- [ ] Check performance metrics
- [ ] Verify all services healthy
- [ ] Review initial user feedback
- [ ] Confirm analytics tracking

### Short-term (1-24 hours)

- [ ] Extended monitoring period
- [ ] Address any issues discovered
- [ ] Collect user feedback
- [ ] Update status page
- [ ] Send deployment notification

### Follow-up (1-7 days)

- [ ] Conduct deployment retrospective
- [ ] Document lessons learned
- [ ] Update procedures if needed
- [ ] Close deployment ticket
- [ ] Archive deployment artifacts

---

## Rollback Procedure

### Trigger Conditions

Initiate rollback if any of the following occur:
- [ ] Error rate > 5% (baseline: <1%)
- [ ] Response time > 3s (baseline: <500ms)
- [ ] Critical functionality broken
- [ ] Data integrity issues
- [ ] Security vulnerability discovered

### Rollback Steps

```bash
# 1. Notify team
# Post in #incidents channel

# 2. Rollback deployment
vercel rollback
# OR
git revert HEAD && git push origin main

# 3. Rollback database (if needed)
psql -h $DB_HOST -U $DB_USER $DB_NAME < backup_YYYYMMDD.sql

# 4. Verify rollback
curl -I https://your-app.com/health

# 5. Investigate root cause
# Check logs, metrics, and recent changes
```

### Rollback Checklist

- [ ] Team notified of rollback
- [ ] Previous version deployed
- [ ] Database rolled back (if applicable)
- [ ] Services verified healthy
- [ ] Incident documented
- [ ] Root cause analysis scheduled

---

## Deployment Metrics

### Record These Metrics

| Metric | Target | Actual |
|--------|--------|--------|
| Deployment duration | < 15 min | |
| Downtime | 0 min | |
| Error rate (post-deploy) | < 1% | |
| Rollback required | No | |
| Incidents created | 0 | |

---

## Contacts

### Escalation Path

| Role | Name | Contact |
|------|------|---------|
| Deployment Lead | | |
| On-call Engineer | | |
| Engineering Manager | | |
| VP Engineering | | |

### External Contacts

| Service | Contact |
|---------|---------|
| Hosting Provider | |
| CDN Support | |
| Database Support | |

---

## Sign-off

### Pre-Deployment Approval

| Role | Name | Signature | Date |
|------|------|-----------|------|
| QA Lead | | | |
| Tech Lead | | | |
| Product Owner | | | |

### Post-Deployment Confirmation

| Item | Confirmed By | Time |
|------|-------------|------|
| Deployment Complete | | |
| Smoke Tests Passed | | |
| Monitoring Verified | | |

---

## Notes & Issues

### Deployment Notes
```
[Record any notable events, decisions, or deviations from the standard procedure]


```

### Issues Encountered
```
[Document any issues that occurred and how they were resolved]


```

### Lessons Learned
```
[What went well? What could be improved?]


```

---

**Template Version:** 1.0
**Created:** December 2025
**Review Cycle:** After each major deployment
