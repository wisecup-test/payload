# Enforce Integer Input Validation and Type Safety in Test Specifications: Integer Inputs Test

These rules are ALWAYS ACTIVE for all test specifications and utility functions that process numeric inputs, particularly integer values used in configuration, database operations, and payload transformations.

### Rules

- **R-INT-001** MUST: All integer inputs in test specifications MUST be validated for type correctness before processing.
- **R-INT-002** MUST: Use `Number.isInteger()` or `Number.isSafeInteger()` for robust integer validation instead of `typeof` checks.
- **R-INT-003** MUST: Utility functions in packages/payload/src/utilities that process integer values MUST reject invalid integer inputs with clear error messages.
- **R-INT-004** MUST: Test suites MUST include boundary condition tests for integer inputs (MIN_SAFE_INTEGER, MAX_SAFE_INTEGER, zero, negative values).
- **R-INT-005** MUST: Avoid loose equality (==) or implicit type coercion for integer comparisons in scope files; use strict equality (===).
- **R-INT-006** SHOULD: Document validation requirements in function JSDoc comments, specifying expected ranges and behavior for invalid inputs.
- **R-INT-007** SHOULD: Create shared validation utility modules that encapsulate common validation logic including type checking, range validation, and NaN detection.

### Verify

```bash
# Check for Number.isInteger or Number.isSafeInteger usage in test and utility files
grep -r 'Number\.isInteger\|Number\.isSafeInteger' test/ packages/payload/src/utilities/

# Find test spec files with parseInt/parseFloat but missing isNaN or Number.isInteger checks
grep -r '\.spec\.ts' test/ | xargs grep -l 'parseInt\|parseFloat' | xargs grep -L 'isNaN\|Number\.isInteger'

# Run integer validation and boundary tests
npm run test -- --grep 'integer.*validation|boundary.*test'

# Check for loose equality operators with numeric comparisons
grep -r '==\s*[0-9]\|[0-9]\s*==' test/ packages/payload/src/utilities/ --include='*.ts'
```

**Accept when:**
- All test specification files containing integer inputs include explicit validation using `Number.isInteger()` or equivalent
- Utility functions in transformColumnPreferences and similar modules reject invalid integer inputs with clear error messages
- Test suites include boundary condition tests for integer inputs (MIN_SAFE_INTEGER, MAX_SAFE_INTEGER, zero, negative values)
- No instances of loose equality (==) or implicit type coercion for integer comparisons in scope files
- All new or modified test specification files pass verification commands without detecting missing validation patterns

<enforcement>
Claude Code MUST NOT skip or defer verification. All integer input validation rules MUST be enforced during code review and CI pipeline execution. Violations block merge until validation is added or an approved exception is documented.
</enforcement>