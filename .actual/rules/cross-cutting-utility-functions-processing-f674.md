# Enforce Integer Input Validation and Type Safety in Test Specifications: Utility Functions Processing

These rules are ALWAYS ACTIVE for all test specifications and utility functions that process numeric inputs, particularly integer values used in configuration, database operations, and payload transformations.

### Rules

- **R-INT-001** SHOULD: Utility functions processing integers SHOULD use TypeScript strict type checking and avoid implicit type coercion.

### Verify

```bash
# Check for Number.isInteger() and Number.isSafeInteger() usage in test and utility files
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
Claude Code MUST NOT skip or defer verification. Violations in new or modified files within scope MUST be caught during code review and CI pipeline checks. Security team MUST be notified of violations in critical paths (database operations, configuration parsing).
</enforcement>