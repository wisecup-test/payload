# Adopt One-Way Hashing for Sensitive Data Protection: One Way Hash

These rules are ALWAYS ACTIVE for all components handling sensitive data including telemetry, user identifiers, payment information, and personally identifiable information (PII).

### Rules

- **R-HASH-001** SHOULD: One-way hash utilities SHOULD be centralized in shared utility modules to ensure consistent implementation across the codebase.

### Verify

```bash
# Check for one-way hashing utilities and cryptographic usage
grep -r "oneWayHash\|createHash\|bcrypt\|argon2" --include="*.ts" --include="*.js" packages/ src/

# Check for hashing in beforeChange hooks
grep -r "beforeChange.*hash\|hash.*beforeChange" --include="*.ts" --include="*.js" packages/ src/

# Audit for cryptographic vulnerabilities
npm audit | grep -i "crypto\|hash" || echo "No cryptographic vulnerabilities found"

# Check for deprecated hash algorithms
grep -r "MD5\|SHA-1\|md5\|sha1" --include="*.ts" --include="*.js" packages/ src/ || echo "No deprecated algorithms detected"
```

**Accept when:**
- All sensitive data fields identified in policy scope are protected by cryptographic hashing before storage or transmission
- Hashing implementations use approved algorithms (SHA-256, SHA-512, bcrypt, Argon2) with no usage of deprecated algorithms (MD5, SHA-1)
- Password hashing includes proper salting with unique salts per user and appropriate work factors (bcrypt cost factor ≥ 12)
- Hashing utilities are centralized in shared modules with consistent implementation across the codebase
- No plaintext sensitive data patterns detected in telemetry, authentication, PII, or payment information handling

<enforcement>
Claude Code MUST NOT skip or defer verification of one-way hashing rules. All sensitive data handling MUST be reviewed for appropriate cryptographic protection before acceptance.
</enforcement>