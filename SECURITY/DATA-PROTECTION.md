# Data Protection - Standard Operating Procedure

**Version:** 1.0
**Last Updated:** December 2025
**Compliance:** GDPR, CCPA, SOC 2

---

## Overview

This SOP defines standards for protecting user data, ensuring privacy compliance, and implementing security best practices across all web applications and services.

---

## Data Classification

### Classification Levels

| Level | Description | Examples | Handling |
|-------|-------------|----------|----------|
| **Public** | Non-sensitive, publicly available | Marketing content, public docs | Standard controls |
| **Internal** | Business use only | Internal processes, metrics | Access control required |
| **Confidential** | Sensitive business data | Financial data, contracts | Encryption + access logs |
| **Restricted** | Highly sensitive PII/secrets | Passwords, SSN, health data | Encryption + strict access |

### Data Inventory

```markdown
Required documentation for each data type:
- Data category and classification
- Collection purpose and legal basis
- Storage location and retention period
- Access controls and encryption status
- Third-party sharing and transfers
```

---

## Privacy Compliance

### GDPR Requirements (EU Users)

```markdown
Core Principles:
1. Lawfulness, fairness, transparency
2. Purpose limitation
3. Data minimization
4. Accuracy
5. Storage limitation
6. Integrity and confidentiality
7. Accountability

User Rights:
- Right to access
- Right to rectification
- Right to erasure ("right to be forgotten")
- Right to restrict processing
- Right to data portability
- Right to object
- Rights related to automated decision-making
```

### CCPA Requirements (California Users)

```markdown
Consumer Rights:
- Right to know what data is collected
- Right to delete personal information
- Right to opt-out of data sales
- Right to non-discrimination

Business Obligations:
- Privacy policy disclosure
- "Do Not Sell My Personal Information" link
- Respond to requests within 45 days
- Verify consumer identity for requests
```

### Cookie Consent

```typescript
// Cookie consent implementation
interface CookieConsent {
  necessary: boolean;      // Always true
  analytics: boolean;      // User choice
  marketing: boolean;      // User choice
  preferences: boolean;    // User choice
}

// Cookie banner requirements
const cookieBanner = {
  showOnFirstVisit: true,
  allowReject: true,
  granularControl: true,
  savePreferences: true,
  linkToPolicy: '/privacy-policy'
};
```

---

## Data Encryption

### Encryption at Rest

```yaml
# Database encryption
database:
  encryption: AES-256
  key_management: AWS KMS / HashiCorp Vault
  automatic_rotation: 90 days

# File storage encryption
storage:
  provider: S3 / GCS
  encryption: SSE-KMS
  bucket_policy: deny-unencrypted-uploads

# Backup encryption
backups:
  encryption: AES-256-GCM
  key_storage: separate-from-data
  verification: checksum-validated
```

### Encryption in Transit

```yaml
# TLS configuration
tls:
  minimum_version: TLS 1.3
  fallback: TLS 1.2
  cipher_suites:
    - TLS_AES_256_GCM_SHA384
    - TLS_CHACHA20_POLY1305_SHA256
  hsts:
    enabled: true
    max_age: 31536000
    include_subdomains: true
    preload: true

# Certificate management
certificates:
  provider: Let's Encrypt / AWS ACM
  auto_renewal: true
  monitoring: expiry-alerts-30-days
```

### Application-Level Encryption

```typescript
// Encrypt sensitive fields before storage
import { createCipheriv, createDecipheriv, randomBytes } from 'crypto';

class FieldEncryption {
  private algorithm = 'aes-256-gcm';

  encrypt(plaintext: string, key: Buffer): EncryptedData {
    const iv = randomBytes(16);
    const cipher = createCipheriv(this.algorithm, key, iv);

    let encrypted = cipher.update(plaintext, 'utf8', 'hex');
    encrypted += cipher.final('hex');

    return {
      iv: iv.toString('hex'),
      data: encrypted,
      tag: cipher.getAuthTag().toString('hex')
    };
  }

  decrypt(encrypted: EncryptedData, key: Buffer): string {
    const decipher = createDecipheriv(
      this.algorithm,
      key,
      Buffer.from(encrypted.iv, 'hex')
    );
    decipher.setAuthTag(Buffer.from(encrypted.tag, 'hex'));

    let decrypted = decipher.update(encrypted.data, 'hex', 'utf8');
    decrypted += decipher.final('utf8');

    return decrypted;
  }
}
```

---

## Access Control

### Role-Based Access Control (RBAC)

```typescript
// Role definitions
const roles = {
  admin: {
    permissions: ['read', 'write', 'delete', 'manage_users'],
    dataAccess: ['public', 'internal', 'confidential', 'restricted']
  },
  manager: {
    permissions: ['read', 'write', 'delete'],
    dataAccess: ['public', 'internal', 'confidential']
  },
  developer: {
    permissions: ['read', 'write'],
    dataAccess: ['public', 'internal']
  },
  viewer: {
    permissions: ['read'],
    dataAccess: ['public']
  }
};

// Access control middleware
async function checkAccess(user, resource, action) {
  const role = await getUserRole(user.id);
  const resourceClassification = await getResourceClassification(resource);

  if (!roles[role].permissions.includes(action)) {
    throw new ForbiddenError('Insufficient permissions');
  }

  if (!roles[role].dataAccess.includes(resourceClassification)) {
    throw new ForbiddenError('Access to this data classification denied');
  }

  await logAccess(user, resource, action);
  return true;
}
```

### Principle of Least Privilege

```markdown
Implementation requirements:
- Default deny all access
- Grant minimum necessary permissions
- Time-limited access for elevated privileges
- Regular access reviews (quarterly)
- Immediate revocation on role change/termination
- Separate production and development access
```

---

## Data Retention

### Retention Policies

| Data Type | Retention Period | Deletion Method |
|-----------|------------------|-----------------|
| User accounts | Account lifetime + 30 days | Soft delete → Hard delete |
| Transaction logs | 7 years | Archive → Secure delete |
| Access logs | 2 years | Automated purge |
| Analytics data | 26 months | Aggregation → Delete raw |
| Session data | 24 hours | Automatic expiry |
| Backups | 90 days | Secure overwrite |

### Automated Retention

```typescript
// Data retention job
async function enforceRetention() {
  const policies = await getRetentionPolicies();

  for (const policy of policies) {
    const expiredRecords = await findExpiredRecords(
      policy.table,
      policy.retentionDays
    );

    if (policy.archiveFirst) {
      await archiveRecords(expiredRecords, policy.archiveLocation);
    }

    await secureDelete(policy.table, expiredRecords);
    await logDeletion(policy.table, expiredRecords.length);
  }
}

// Run daily
schedule.every('day').at('02:00').do(enforceRetention);
```

---

## Audit Logging

### Required Audit Events

```typescript
// Events that must be logged
const auditEvents = [
  'user.login',
  'user.logout',
  'user.password_change',
  'user.mfa_change',
  'data.access',
  'data.export',
  'data.delete',
  'admin.user_create',
  'admin.user_delete',
  'admin.role_change',
  'admin.settings_change',
  'security.failed_login',
  'security.suspicious_activity'
];

// Audit log structure
interface AuditLog {
  id: string;
  timestamp: Date;
  event: string;
  actor: {
    id: string;
    email: string;
    ip: string;
    userAgent: string;
  };
  resource: {
    type: string;
    id: string;
  };
  action: string;
  outcome: 'success' | 'failure';
  metadata: Record<string, any>;
}
```

### Log Protection

```yaml
# Audit log security
audit_logs:
  storage: separate-secure-location
  encryption: AES-256
  immutability: write-once-read-many
  retention: 7 years
  access: security-team-only
  integrity: cryptographic-hashing
  backup: replicated-offsite
```

---

## Incident Response

### Data Breach Procedure

```markdown
1. IDENTIFICATION (0-4 hours)
   - Detect and confirm breach
   - Assess scope and impact
   - Activate incident response team

2. CONTAINMENT (4-24 hours)
   - Isolate affected systems
   - Preserve evidence
   - Block unauthorized access

3. NOTIFICATION (24-72 hours)
   - Notify supervisory authority (GDPR: 72 hours)
   - Notify affected users (if high risk)
   - Engage legal counsel

4. INVESTIGATION (1-2 weeks)
   - Determine root cause
   - Identify all affected data
   - Document timeline

5. REMEDIATION (2-4 weeks)
   - Fix vulnerabilities
   - Implement additional controls
   - Update procedures

6. REVIEW (1 month after)
   - Post-incident review
   - Update incident response plan
   - Training and awareness
```

### Breach Notification Template

```markdown
Subject: Important Security Notice - [Company Name]

Dear [User],

We are writing to inform you of a security incident that may have
affected your personal information.

**What Happened:**
[Brief description of the incident]

**Information Involved:**
[List of data types potentially affected]

**What We Are Doing:**
[Steps taken to address the incident]

**What You Can Do:**
[Recommended actions for the user]

**Contact Information:**
[Support contact details]

We sincerely apologize for any inconvenience this may cause.

[Company Name] Security Team
```

---

## Third-Party Data Sharing

### Vendor Assessment

```markdown
Required due diligence:
[ ] Security certifications (SOC 2, ISO 27001)
[ ] Data processing agreement (DPA) signed
[ ] Subprocessor disclosure
[ ] Breach notification procedures
[ ] Data location and transfer mechanisms
[ ] Right to audit
[ ] Termination and data return procedures
```

### Data Processing Agreement (DPA) Requirements

```markdown
Required clauses:
- Purpose limitation
- Data security measures
- Subprocessor restrictions
- Audit rights
- Breach notification (24-48 hours)
- Data deletion on termination
- Cross-border transfer safeguards
- Liability and indemnification
```

---

## Security Controls

### Technical Controls

```markdown
[ ] Web Application Firewall (WAF)
[ ] DDoS protection
[ ] Intrusion detection/prevention
[ ] Vulnerability scanning (weekly)
[ ] Penetration testing (annual)
[ ] Security headers configured
[ ] Rate limiting implemented
[ ] Input validation/sanitization
[ ] Parameterized queries (SQL injection prevention)
[ ] Output encoding (XSS prevention)
[ ] CSRF protection
```

### Security Headers

```typescript
// Required security headers
const securityHeaders = {
  'Strict-Transport-Security': 'max-age=31536000; includeSubDomains; preload',
  'X-Content-Type-Options': 'nosniff',
  'X-Frame-Options': 'DENY',
  'X-XSS-Protection': '1; mode=block',
  'Referrer-Policy': 'strict-origin-when-cross-origin',
  'Content-Security-Policy': "default-src 'self'; script-src 'self'",
  'Permissions-Policy': 'camera=(), microphone=(), geolocation=()'
};
```

---

## Training & Awareness

### Required Training

```markdown
All employees:
- Security awareness (annual)
- Phishing recognition
- Data handling procedures
- Incident reporting

Developers:
- Secure coding practices
- OWASP Top 10
- Security testing

Administrators:
- Access management
- Audit log review
- Incident response
```

---

## Resources

- [GDPR Official Text](https://gdpr.eu/)
- [CCPA Official Text](https://oag.ca.gov/privacy/ccpa)
- [OWASP Security Guidelines](https://owasp.org/www-project-web-security-testing-guide/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)

---

**Document Version:** 1.0
**Maintained by:** Security Team
**Review Cycle:** Quarterly
**Next Review:** March 2026
