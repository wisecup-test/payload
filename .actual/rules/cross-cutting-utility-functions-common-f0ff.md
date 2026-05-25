# Adopt Promise-Based Async/Await Pattern for Asynchronous Operations: Utility Functions Common

These rules are ALWAYS ACTIVE for all asynchronous code development and govern the concurrency model used throughout the codebase.

### Rules

- **R-ASYNC-001** SHOULD: Utility functions for common async patterns (wait, retry, timeout) SHOULD be centralized and reused.
- **R-ASYNC-002** MUST: All new TypeScript/JavaScript code implementing asynchronous operations MUST use async/await pattern, not callbacks or raw .then/.catch chains.
- **R-ASYNC-003** MUST: All async functions MUST be explicitly typed with Promise<T> return type annotations.
- **R-ASYNC-004** MUST: ESLint rules @typescript-eslint/no-floating-promises and @typescript-eslint/require-await MUST be enabled and passing.
- **R-ASYNC-005** MUST: All async operations in test files MUST be properly awaited or return promises to test runners.
- **R-ASYNC-006** SHOULD: For Node.js callback APIs, util.promisify SHOULD be used to convert to Promise-based interfaces.
- **R-ASYNC-007** SHOULD: Async function behavior SHOULD be documented in JSDoc comments, especially regarding error conditions and return values.

### Verify

```bash
# Check for callback-based async patterns in non-legacy code
grep -r "function.*callback" --include="*.ts" --include="*.tsx" | grep -v "node_modules" | grep -v "legacy"

# Run ESLint with async/await enforcement rules
eslint . --ext .ts,.tsx --rule '@typescript-eslint/no-floating-promises: error' --rule '@typescript-eslint/require-await: warn'

# Count remaining .then() chains that should be refactored
grep -r "\.then(" --include="*.ts" --include="*.tsx" | grep -v "node_modules" | wc -l
```

**Accept when:**
- No new callback-based async patterns are introduced in non-legacy code
- ESLint checks pass with no-floating-promises and require-await rules enabled
- All async functions are properly typed with Promise return types
- Test suites properly await all async operations without warnings
- No floating promises detected in the codebase

<enforcement>
Clause Code MUST NOT skip or defer verification. ESLint violations block CI pipeline. Code review must verify async/await pattern compliance before merge.
</enforcement>