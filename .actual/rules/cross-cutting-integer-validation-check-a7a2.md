# Enforce Integer Input Validation and Type Safety in Test Specifications: Integer Validation Check

These rules are ALWAYS ACTIVE for all test specifications and utility functions that process numeric inputs, particularly integer values used in configuration, database operations, and payload transformations.

### Rules

- **R-INT-001** MUST: Integer validation MUST check for boundary conditions including minimum and maximum safe integer values.

### Verify

```bash
# Check for Number.isInteger() and Number.isSafeInteger() usage in scope files
grep -r 'Number\.isInteger\|Number\.isSafeInteger' test/ packages/payload/src/utilities/

# Find test spec files with parseInt/parseFloat but missing isNaN or Number.isInteger checks
grep -r '\.spec\.ts' test/ | xargs grep -l 'parseInt\|parseFloat' | xargs grep -L 'isNaN\|Number\.isInteger'

# Run integer validation and boundary condition tests
npm run test -- --grep 'integer.*validation|boundary.*test'
```

**Accept when:**
- All test specification files containing integer inputs include explicit validation using Number.isInteger() or equivalent
- Utility functions in transformColumnPreferences and similar modules reject invalid integer inputs with clear error messages
- Test suites include boundary condition tests for integer inputs (MIN_SAFE_INTEGER, MAX_SAFE_INTEGER, zero, negative values)
- No instances of loose equality (==) or implicit type coercion for integer comparisons in scope files

<enforcement>
Claude Code MUST NOT skip or defer verification. All integer inputs in test specifications and utility functions must be validated against boundary conditions before use.
</enforcement>