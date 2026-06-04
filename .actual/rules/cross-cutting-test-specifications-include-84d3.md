# Enforce Integer Input Validation and Type Safety in Test Specifications: Test Specifications Include

These rules are ALWAYS ACTIVE for all test specifications and utility functions that process numeric inputs, particularly integer values used in configuration, database operations, and payload transformations.

### Rules

- **R-INT-001** SHOULD: Test specifications SHOULD include explicit test cases for integer boundary conditions, negative values, and zero.

### Verify

```bash
# Check for Number.isInteger() and Number.isSafeInteger() usage in test and utility files
grep -r 'Number\.isInteger\|Number\.isSafeInteger' test/ packages/payload/src/utilities/

# Find test spec files with parseInt/parseFloat but missing isNaN or Number.isInteger checks
grep -r '\.spec\.ts' test/ | xargs grep -l 'parseInt\|parseFloat' | xargs grep -L 'isNaN\|Number\.isInteger'

# Run test suite filtering for integer validation and boundary tests
npm run test -- --grep 'integer.*validation|boundary.*test'
```

**Accept when:**
- All test specification files containing integer inputs include explicit validation using Number.isInteger() or equivalent
- Utility functions in transformColumnPreferences and similar modules reject invalid integer inputs with clear error messages
- Test suites include boundary condition tests for integer inputs (MIN_SAFE_INTEGER, MAX_SAFE_INTEGER, zero, negative values)
- No instances of loose equality (==) or implicit type coercion for integer comparisons in scope files

<enforcement>
Claude Code MUST NOT skip or defer verification. Automated linting rules, code review checklists, and CI pipeline checks are mandatory. Violations block merge until validation is added or an approved exception is documented.
</enforcement>