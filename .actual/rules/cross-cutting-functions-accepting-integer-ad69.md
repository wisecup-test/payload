# Enforce Integer Input Validation and Type Safety in Test Specifications: Functions Accepting Integer

These rules are ALWAYS ACTIVE for all test specifications and utility functions that process numeric inputs, particularly integer values used in configuration, database operations, and payload transformations.

### Rules

- **R-INT-001** MUST: Functions accepting integer parameters MUST reject non-numeric types and NaN values with explicit error messages.

### Verify

```bash
# Check for Number.isInteger() or Number.isSafeInteger() usage in test and utility files
grep -r 'Number\.isInteger\|Number\.isSafeInteger' test/ packages/payload/src/utilities/

# Find test spec files with parseInt/parseFloat but missing validation
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
Claude Code MUST NOT skip or defer verification of integer input validation in test specifications and utility functions. All functions accepting integer parameters must include explicit type and NaN checks before processing.
</enforcement>