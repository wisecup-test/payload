# Adopt One-Way Hashing for Sensitive Data Protection: Sensitive Data Including

These rules are ALWAYS ACTIVE for all components handling sensitive data including telemetry, user identifiers, payment information, and personally identifiable information (PII).

### Rules

- **R-HASH-001** MUST: All sensitive data including PII, authentication tokens, telemetry identifiers, and payment information MUST be transformed using one-way cryptographic hash functions before storage or transmission to external systems.

### Verify

```bash
# Check for usage of approved hashing functions
grep -r "oneWayHash\|createHash\|bcrypt\|argon2" --include="*.ts" --include="*.js" packages/ src/

# Check for hashing in beforeChange hooks
grep -r "beforeChange.*hash\|hash.*beforeChange" --include="*.ts" --include="*.js" packages/ src/

# Audit for cryptographic vulnerabilities
npm audit | grep -i "crypto\|hash" || echo "No cryptographic vulnerabilities found"

# Check for deprecated hash algorithms (MD5, SHA-1)
grep -r "md5\|sha1\|MD5\|SHA1" --include="*.ts" --include="*.js" packages/ src/ && echo "WARNING: Deprecated algorithms detected" || echo "No deprecated algorithms found"
```

**Accept when:**
- All sensitive data fields identified in policy scope (PII, authentication tokens, telemetry identifiers, payment information, session identifiers) are protected by cryptographic hashing before storage or transmission
- Hashing implementations use approved algorithms (SHA-256, SHA-512, bcrypt, Argon2) with no usage of deprecated algorithms (MD5, SHA-1)
- Password hashing includes proper salting with unique salts per user and appropriate work factors (bcrypt cost factor ≥ 12)
- Hashing operations are centralized in utility modules with security team oversight
- Data models and schemas document which fields are hashed to prevent confusion during debugging

<enforcement>
Claude Code MUST NOT skip or defer verification of sensitive data protection rules. All code handling PII, authentication tokens, payment information, or telemetry identifiers MUST be verified against these rules before approval.
</enforcement>