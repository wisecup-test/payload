# Standardize Environment-Based Configuration Management Pattern: Business Logic Test

These rules are ALWAYS ACTIVE for all configuration management implementations across runtime environments, test suites, and utility modules.

### Rules

- **R-CONFIG-001** MUST NOT: Business logic and test specifications MUST NOT contain hardcoded environment-specific values.

### Verify

```bash
# Check for direct process.env access in test files (should be < 5 instances outside config.ts)
grep -r "process\.env" --include="*.spec.ts" --include="*.test.ts" | grep -v "config\.ts" | wc -l | awk '{if ($1 > 5) exit 1}'

# Verify at least one dedicated configuration file exists
find . -name "config.ts" -o -name "settings.ts" | wc -l | awk '{if ($1 < 1) exit 1}'

# Check for untracked hardcoded environment values marked as TODO/FIXME
grep -r "hardcoded.*environment" --include="*.ts" | grep -i "TODO\|FIXME" | wc -l
```

**Accept when:**
- Test files reference configuration from dedicated config modules rather than directly accessing environment variables (fewer than 5 direct process.env references in test files)
- At least one dedicated configuration file (config.ts or settings.ts) exists in the codebase
- No untracked hardcoded environment-specific values exist outside of configuration modules (or are marked with TODO/FIXME for refactoring)

<enforcement>
Claude Code MUST NOT skip or defer verification of these rules. Violations must be flagged during code review and CI pipeline checks must fail if hardcoded environment values are detected outside configuration modules.
</enforcement>