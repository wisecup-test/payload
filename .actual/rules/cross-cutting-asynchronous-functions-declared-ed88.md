# Adopt Promise-Based Async/Await Pattern for Asynchronous Operations: Asynchronous Functions Declared

These rules are ALWAYS ACTIVE for all asynchronous code development and govern the concurrency model used throughout the codebase.

### Rules

- **R-ASYNC-001** MUST: All asynchronous functions MUST be declared using the async keyword and return Promises.

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
Verification via ESLint rules (@typescript-eslint/no-floating-promises, @typescript-eslint/require-await), TypeScript strict mode, and code review is mandatory. CI pipeline must fail on violations. Exceptions require tech lead approval and documentation in technical debt register.
</enforcement>