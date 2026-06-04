# Adopt One-Way Hashing for Sensitive Data Protection: Hashing Implementations Include

These rules are ALWAYS ACTIVE for all components handling sensitive data including telemetry, user identifiers, payment information, and personally identifiable information (PII).

### Rules

- **R-HASH-001** MUST: Hashing implementations MUST include appropriate salt values for password hashing and SHOULD use unique salts per data item to prevent rainbow table attacks.

### Verify

```bash
# Check for hashing implementations across the codebase
grep -r "oneWayHash\|createHash\|bcrypt\|argon2" --include="*.ts" --include="*.js" packages/ src/

# Check for hashing in beforeChange hooks
grep -r "beforeChange.*hash\|hash.*beforeChange" --include="*.ts" --include="*.js" packages/ src/

# Verify no cryptographic vulnerabilities
npm audit | grep -i "crypto\|hash" || echo "No cryptographic vulnerabilities found"
```

**Accept when:**
- All sensitive data fields identified in policy scope are protected by cryptographic hashing before storage or transmission
- Hashing implementations use approved algorithms (SHA-256, SHA-512, bcrypt, Argon2) with no usage of deprecated algorithms (MD5, SHA-1)
- Password hashing includes proper salting with unique salts per user and appropriate work factors (bcrypt cost factor ≥ 12)
- Hashing operations are applied in data transformation layers such as beforeChange hooks, service boundaries, or data access objects before persistence

<enforcement>
Claude Code MUST NOT skip or defer verification. All sensitive data handling must be reviewed for appropriate cryptographic protection before approval.
</enforcement>