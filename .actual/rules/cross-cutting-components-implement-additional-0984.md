# Adopt One-Way Hashing for Sensitive Data Protection: Components Implement Additional

These rules are ALWAYS ACTIVE for all components handling sensitive data including telemetry, user identifiers, payment information, and personally identifiable information (PII).

### Rules

- **R-HASH-001** MAY: Components MAY implement additional data protection measures such as truncation, masking, or tokenization in combination with hashing.

### Verify

```bash
# Check for usage of approved hashing functions
grep -r "oneWayHash\|createHash\|bcrypt\|argon2" --include="*.ts" --include="*.js" packages/ src/

# Check for hashing in beforeChange hooks
grep -r "beforeChange.*hash\|hash.*beforeChange" --include="*.ts" --include="*.js" packages/ src/

# Audit for cryptographic vulnerabilities
npm audit | grep -i "crypto\|hash" || echo "No cryptographic vulnerabilities found"

# Check for deprecated hash algorithms (MD5, SHA-1)
grep -r "md5\|sha1\|MD5\|SHA1" --include="*.ts" --include="*.js" packages/ src/ | grep -v node_modules || echo "No deprecated algorithms detected"
```

**Accept when:**
- All sensitive data fields identified in policy scope are protected by cryptographic hashing before storage or transmission
- Hashing implementations use approved algorithms (SHA-256, SHA-512, bcrypt, Argon2) with no usage of deprecated algorithms (MD5, SHA-1)
- Password hashing includes proper salting with unique salts per user and appropriate work factors (bcrypt cost factor ≥ 12)
- No plaintext sensitive data patterns detected in telemetry collection, e-commerce cart operations, or relationship data handling

<enforcement>
Claude Code MUST NOT skip or defer verification of hashing implementations for sensitive data. All violations involving PII or payment data require immediate remediation within 7 days.
</enforcement>