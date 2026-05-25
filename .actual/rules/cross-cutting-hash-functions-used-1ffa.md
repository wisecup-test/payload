# Adopt One-Way Hashing for Sensitive Data Protection: Hash Functions Used

These rules are ALWAYS ACTIVE for all components handling sensitive data including telemetry, user identifiers, payment information, and personally identifiable information (PII).

### Rules

- **R-HASH-001** MUST: Hash functions used MUST be cryptographically secure (e.g., SHA-256, SHA-512, bcrypt, Argon2) and MUST NOT use deprecated algorithms like MD5 or SHA-1.

### Verify

```bash
# Check for approved cryptographic hash function usage
grep -r "oneWayHash\|createHash\|bcrypt\|argon2" --include="*.ts" --include="*.js" packages/ src/

# Check for hashing in data transformation layers
grep -r "beforeChange.*hash\|hash.*beforeChange" --include="*.ts" --include="*.js" packages/ src/

# Verify no cryptographic vulnerabilities in dependencies
npm audit | grep -i "crypto\|hash" || echo "No cryptographic vulnerabilities found"

# Check for deprecated hash algorithms
grep -r "md5\|MD5\|sha1\|SHA1" --include="*.ts" --include="*.js" packages/ src/ || echo "No deprecated algorithms detected"
```

**Accept when:**
- All sensitive data fields identified in policy scope are protected by cryptographic hashing before storage or transmission
- Hashing implementations use approved algorithms (SHA-256, SHA-512, bcrypt, Argon2) with no usage of deprecated algorithms (MD5, SHA-1)
- Password hashing includes proper salting with unique salts per user and appropriate work factors (bcrypt cost factor ≥ 12)
- No plaintext sensitive data patterns detected in telemetry, user identifiers, payment information, or PII fields

<enforcement>
Claude Code MUST NOT skip or defer verification of cryptographic hash function compliance. All sensitive data handling MUST be reviewed against R-HASH-001 requirements before approval.
</enforcement>