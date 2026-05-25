# Standardize Environment-Based Configuration Management Pattern: Configuration Files Located

These rules are ALWAYS ACTIVE for all test configuration files (*.spec.ts, *.test.ts), dedicated configuration modules (config.ts, settings.ts), utility modules requiring environment-aware behavior, and authentication, routing, and database connection configuration across runtime environments.

### Rules

- **R-CONFIG-001** SHOULD: Configuration files SHOULD be co-located with the modules they configure (e.g., test/auth/config.ts for authentication tests).
- **R-CONFIG-002** SHOULD: Use environment variables as the primary source for environment-specific values, with sensible defaults for development.
- **R-CONFIG-003** SHOULD: Export configuration objects or functions from config modules rather than individual constants to maintain cohesion.
- **R-CONFIG-004** SHOULD: Document all configuration options including their purpose, valid values, and default behavior in code comments or README files.
- **R-CONFIG-005** MAY: Consider using configuration validation libraries (e.g., zod, joi) to enforce configuration schema at runtime.

### Verify

```bash
# Check for direct process.env access in test files outside config modules
grep -r "process\.env" --include="*.spec.ts" --include="*.test.ts" | grep -v "config\.ts" | wc -l | awk '{if ($1 > 5) exit 1}'

# Verify at least one dedicated configuration file exists
find . -name "config.ts" -o -name "settings.ts" | wc -l | awk '{if ($1 < 1) exit 1}'

# Check for untracked hardcoded environment-specific values
grep -r "hardcoded.*environment" --include="*.ts" | grep -i "TODO\|FIXME" | wc -l
```

**Accept when:**
- Test files reference configuration from dedicated config modules rather than directly accessing environment variables (fewer than 5 direct process.env references in test files)
- At least one dedicated configuration file (config.ts or settings.ts) exists in the codebase
- No untracked hardcoded environment-specific values exist outside of configuration modules (or are marked with TODO/FIXME for refactoring)

<enforcement>
Claude Code MUST NOT skip or defer verification. Violations detected by CI pipeline checks for hardcoded environment values result in pipeline failure. Code review requires changes before approval if configuration pattern is violated.
</enforcement>