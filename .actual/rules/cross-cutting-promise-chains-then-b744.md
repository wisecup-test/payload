# Adopt Promise-Based Async/Await Pattern for Asynchronous Operations: Promise Chains Then

These rules are ALWAYS ACTIVE for all asynchronous code development across utilities, tests, and application logic.

### Rules

- **R-ASYNC-001** SHOULD: Promise chains (.then/.catch) SHOULD be refactored to async/await syntax for improved readability.

### Verify

```bash
# Check for callback-based async patterns in non-legacy code
grep -r "function.*callback" --include="*.ts" --include="*.tsx" | grep -v "node_modules" | grep -v "legacy"

# Run ESLint with async pattern rules
eslint . --ext .ts,.tsx --rule '@typescript-eslint/no-floating-promises: error' --rule '@typescript-eslint/require-await: warn'

# Count remaining .then chains
grep -r "\.then(" --include="*.ts" --include="*.tsx" | grep -v "node_modules" | wc -l
```

**Accept when:**
- No new callback-based async patterns are introduced in non-legacy code
- ESLint checks pass with no-floating-promises and require-await rules enabled
- All async functions are properly typed with Promise return types
- Test suites properly await all async operations without warnings

<enforcement>
Clause Code MUST NOT skip or defer verification. ESLint rules in CI pipeline (@typescript-eslint/no-floating-promises, @typescript-eslint/require-await) are mandatory. TypeScript compiler strict mode checks are mandatory. Code review checklist must verify async/await patterns. Violations block merge; exceptions require tech lead approval and documentation in technical debt register.
</enforcement>