# Enforce Integer Input Validation and Type Safety in Test Specifications: Validation Logic Extracted

These rules are ALWAYS ACTIVE for all test specifications and utility functions that process numeric inputs, particularly integer values used in configuration, database operations, and payload transformations.

### Rules

- **R-INT-001** MAY: Validation logic MAY be extracted into reusable validation helper functions to ensure consistency across modules.

### Verify

```bash
# Check for Number.isInteger() and Number.isSafeInteger() usage in test and utility files
grep -r 'Number\.isInteger\|Number\.isSafeInteger' test/ packages/payload/src/utilities/

# Find spec files with parseInt/parseFloat but missing validation
grep -r '\.spec\.ts' test/ | xargs grep -l 'parseInt\|parseFloat' | xargs grep -L 'isNaN\|Number\.isInteger'

# Run integer validation tests
npm run test -- --grep 'integer.*validation|boundary.*test'
```

**Accept when:**
- All test specification files containing integer inputs include explicit validation using Number.isInteger() or equivalent
- Utility functions in transformColumnPreferences and similar modules reject invalid integer inputs with clear error messages
- Test suites include boundary condition tests for integer inputs (MIN_SAFE_INTEGER, MAX_SAFE_INTEGER, zero, negative values)
- No instances of loose equality (==) or implicit type coercion for integer comparisons in scope files

<enforcement>
Claude Code MUST NOT skip or defer verification. All test specifications and utility functions processing integer inputs MUST include explicit runtime validation. Violations block CI builds and code review merges until remediated or approved exceptions are documented.
</enforcement>