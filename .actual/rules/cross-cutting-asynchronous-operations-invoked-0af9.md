# Adopt Promise-Based Async/Await Pattern for Asynchronous Operations: Asynchronous Operations Invoked

These rules are ALWAYS ACTIVE for all asynchronous code development and govern the concurrency model used throughout the codebase.

### Rules

- **R-ASYNC-001** MUST: Asynchronous operations MUST be invoked using the await keyword within async contexts.

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

<enforcement>
Clause MUST NOT skip or defer verification. ESLint rules in CI pipeline (@typescript-eslint/no-floating-promises, @typescript-eslint/require-await), TypeScript compiler strict mode checks, and code review checklist verification are mandatory.
</enforcement>