# Secret Management - Standard Operating Procedures

**Type:** Security Best Practices
**Scope:** All projects and environments
**Source:** Industry best practices from GitHub, OWASP, Next.js

## Overview

Proper secret management prevents:
- Credential leaks
- Unauthorized access
- Data breaches
- Supply chain compromise
- Compliance violations

## Core Principles

1. **Never commit secrets** - Use `.gitignore` and `.env.local`
2. **Separate per environment** - Different secrets for dev, staging, prod
3. **Rotate regularly** - Especially after incidents
4. **Principle of least privilege** - Grant minimum necessary access
5. **Audit and monitor** - Track secret access and usage
6. **Encrypt in transit and at rest** - Use HTTPS, TLS, encryption

## Secret Types

### Categories

1. **API Keys** - Third-party service authentication
2. **Database Credentials** - Connection strings, passwords
3. **Encryption Keys** - JWT secrets, encryption keys
4. **Access Tokens** - OAuth tokens, bearer tokens
5. **Certificates** - SSL/TLS certificates, signing keys
6. **Passwords** - User accounts, admin passwords
7. **Private Keys** - SSH keys, signing keys

## Local Development

### .env Files

**Template (.env.example):**
```bash
# Never commit actual values!
# This file shows structure only

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
GOOGLE_API_KEY=your_google_key

# Feature Flags
ENABLE_ANALYTICS=true
DEBUG_MODE=false
```

### Local Setup

```bash
# 1. Copy template
cp .env.example .env.local

# 2. Fill in actual values (local only)
# Edit .env.local with your local credentials

# 3. Verify it's in .gitignore
cat .gitignore | grep ".env"
# Should see: .env.local

# 4. Never commit
git status
# Should NOT see .env.local in changes

# 5. Share credentials securely
# Use team password manager or 1Password
# Never share via Slack, email, or chat
```

### .gitignore Checklist

```bash
# Environment variables
.env
.env.local
.env.*.local
.env.production.local

# Secrets and credentials
.DS_Store
*.pem
*.key
*.crt
*.jks
secrets/
.ssh/

# IDE and OS files
.vscode/settings.json
.idea/
*.swp
*.swo
Thumbs.db

# Dependencies
node_modules/
*.lock

# Build outputs
dist/
build/
.next/
```

## Production Secrets

### GitHub Secrets

**Configuration:**
1. Go to Repository Settings
2. Select Secrets and variables → Actions
3. Click "New repository secret"
4. Add secret name and value
5. Use in workflows with `${{ secrets.SECRET_NAME }}`

**Example Workflow:**
```yaml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Deploy to Production
        env:
          DATABASE_URL: ${{ secrets.PROD_DATABASE_URL }}
          API_KEY: ${{ secrets.PROD_API_KEY }}
          JWT_SECRET: ${{ secrets.PROD_JWT_SECRET }}
        run: |
          npm run build
          npm run deploy

      - name: Notify Slack
        if: failure()
        uses: slackapi/slack-github-action@v1
        with:
          webhook-url: ${{ secrets.SLACK_WEBHOOK }}
```

### Environment-Specific Secrets

**Development:**
```bash
# Minimal or test credentials
API_KEY=dev_test_key_12345
DATABASE_URL=postgresql://dev:dev@localhost:5432/devdb
JWT_SECRET=dev_secret_short
```

**Staging:**
```bash
# Real-like but separate from production
API_KEY=staging_key_abcde
DATABASE_URL=postgresql://stage_user:stage_pass@staging.example.com/stagedb
JWT_SECRET=staging_long_secret_key_xyz
```

**Production:**
```bash
# Strong, unique, regularly rotated
API_KEY=prod_key_strong_long_random_string
DATABASE_URL=postgresql://prod_user:prod_pass@prod.example.com/proddb
JWT_SECRET=prod_secret_very_long_random_string_for_jwt
```

## Handling Secret Leaks

### If Secret Accidentally Committed

**Immediate Actions (within 1 hour):**

1. **Stop using the compromised secret**
   ```bash
   # Rotate immediately in production
   # Delete all copies
   ```

2. **Identify scope**
   - When was it committed?
   - How many people have access?
   - Has it been pushed?
   - Is it in a public repository?

3. **Remove from git history**
   ```bash
   # Option 1: Use git-filter-branch (simple)
   git filter-branch --tree-filter 'rm -f path/to/secrets.env' -- --all

   # Option 2: Use BFG Repo-Cleaner (recommended for large repos)
   bfg --delete-files secrets.env
   bfg --replace-text passwords.txt

   # After cleaning, force push
   git push origin main --force-with-lease
   ```

4. **Force-push to all branches**
   ```bash
   git push origin main --force-with-lease
   git push origin develop --force-with-lease
   # Notify team to re-clone
   ```

5. **Rotate the secret**
   - Generate new credentials
   - Update all systems using it
   - Update GitHub Secrets
   - Update production environment variables
   - Update team password manager

6. **Post-incident review**
   - Document what happened
   - Identify prevention measures
   - Update team training
   - Review .gitignore coverage

### Prevention

**Pre-commit Hook:**
```bash
#!/bin/bash
# .git/hooks/pre-commit

# Check for secrets in staged files
if git diff --cached | grep -E '(password|secret|api.?key|token).*=' ; then
  echo "❌ ERROR: Potential secrets found in commit!"
  echo "Please remove secrets and use .env.local"
  exit 1
fi

exit 0
```

**Enable GitHub Secret Scanning:**
1. Settings > Security & Analysis
2. Enable "Secret scanning"
3. Enable "Push protection"
4. Configure custom patterns if needed

## Secret Rotation

### Schedule

- **API Keys:** Every 3 months or after incidents
- **Database Passwords:** Every 6 months
- **JWT Secrets:** Every 1 year or after breaches
- **SSH Keys:** Every 1 year
- **SSL Certificates:** Automated before expiration

### Rotation Process

```bash
# 1. Generate new secret/key
openssl rand -base64 32

# 2. Add new secret to systems (keep old for transition)
# Update in GitHub Secrets, environment, systems

# 3. Deploy with new secret active
npm run build && npm run deploy

# 4. Monitor for errors (watch logs)
npm run logs | grep -i error

# 5. Update dependent systems
# Update services that use this secret

# 6. Remove old secret after transition period
# Typically 1-7 days depending on service

# 7. Document rotation
echo "API Key rotated: $(date)" >> CHANGELOG.md
```

## Third-Party Services

### Secure Configuration

**Stripe:**
```javascript
// src/lib/stripe.ts
import Stripe from 'stripe';

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!, {
  apiVersion: '2023-10-16',
});

export default stripe;
```

**GitHub:**
```javascript
// API calls use GITHUB_TOKEN
const response = await fetch('https://api.github.com/user', {
  headers: {
    'Authorization': `token ${process.env.GITHUB_TOKEN}`,
    'Accept': 'application/vnd.github.v3+json',
  },
});
```

**Database:**
```javascript
// src/lib/db.ts
import { Pool } from 'pg';

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  max: 20,
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
});

export default pool;
```

## Security Checklist

### Before Committing

- [ ] No `.env.local` file in git
- [ ] All secrets removed from code
- [ ] No hardcoded API keys/passwords
- [ ] No database connection strings with credentials
- [ ] No SSH private keys
- [ ] No API tokens or bearer tokens
- [ ] `.gitignore` covers all secret files
- [ ] Run `git diff --cached` to double-check

### Before Deploying

- [ ] GitHub Secrets configured for all environments
- [ ] Environment variables set on hosting platform
- [ ] Database credentials updated
- [ ] API keys rotated if necessary
- [ ] SSL certificates valid
- [ ] Secrets encrypted at rest and in transit
- [ ] Audit logs enabled
- [ ] Team notified of any changes

### Before Going Public

- [ ] GitHub Secret Scanning enabled
- [ ] Push protection enabled
- [ ] No secrets in README or docs
- [ ] No example secrets in .env.example
- [ ] License allows open source
- [ ] Security policy documented
- [ ] Team trained on secret handling
- [ ] Incident response plan in place

## Tools & Resources

### Tools

- **1Password** - Team password manager
- **Vault** - Secret management server
- **git-secrets** - Prevents accidental commits
- **TruffleHog** - Searches for secrets in git history
- **GitHub Secret Scanning** - Automatic secret detection
- **SOPS** - Secrets Operations for encrypted files

### Commands

```bash
# Check git history for secrets
git log -p | grep -i "password\|secret\|key"

# Search for common patterns
grep -r "api_key\|API_KEY\|password" src/ --exclude-dir=node_modules

# Generate secure random string
openssl rand -base64 32

# Encrypt file with openssl
openssl enc -aes-256-cbc -in secrets.txt -out secrets.txt.enc

# Decrypt file
openssl enc -d -aes-256-cbc -in secrets.txt.enc -out secrets.txt
```

## Team Guidelines

### Do's ✅

- ✅ Use `.env.local` for local development
- ✅ Use GitHub Secrets for CI/CD
- ✅ Use team password manager for shared secrets
- ✅ Rotate secrets regularly
- ✅ Document which secrets are used where
- ✅ Monitor secret access and usage
- ✅ Train team on secret handling
- ✅ Review and audit secrets quarterly

### Don'ts ❌

- ❌ Commit `.env` files to git
- ❌ Share secrets via email, Slack, or chat
- ❌ Hardcode secrets in source code
- ❌ Use same secrets for dev and prod
- ❌ Leave default credentials unchanged
- ❌ Forget to update `.gitignore`
- ❌ Neglect secret rotation
- ❌ Ignore secret scanning alerts

## Related Resources

- [GitHub Secret Scanning](https://docs.github.com/en/code-security/secret-scanning)
- [OWASP Secrets Management](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html)
- [HashiCorp Vault](https://www.vaultproject.io/)
- [git-secrets Tool](https://github.com/awslabs/git-secrets)
- [TruffleHog](https://github.com/trufflesecurity/trufflehog)

---

**Last Updated:** December 2024
**Version:** 1.0
**Status:** Active
