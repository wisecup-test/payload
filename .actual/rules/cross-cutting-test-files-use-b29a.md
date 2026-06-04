# Adopt .spec.ts Extension for Test Files: Test Files Use

These rules are ALWAYS ACTIVE for all TypeScript test files in the codebase, including unit tests, integration tests, and end-to-end tests across both packages/ and test/ directories.

### Rules

- **R-TEST-001** MUST: All test files MUST use the .spec.ts extension for TypeScript test files.
- **R-TEST-002** MUST: Test files in scope include unit tests, integration tests, and end-to-end tests in both packages/ and test/ directories.
- **R-TEST-003** MUST: New test files created during development MUST follow the .spec.ts naming convention.
- **R-TEST-004** MUST: Refactored or renamed test files MUST be renamed to use the .spec.ts extension.
- **R-TEST-005** SHOULD: Use semantic prefixes for test scope clarity (e.g., int.spec.ts for integration tests, e2e.spec.ts for end-to-end tests).

### Verify

```bash
# Verify no test files use non-standard extensions
find . -type f \( -name '*.test.ts' -o -name '_test.ts' \) | grep -v node_modules | wc -l | grep -q '^0$'

# Count compliant .spec.ts files
find . -type f -name '*.spec.ts' | grep -v node_modules | wc -l

# Verify test runner configuration includes .spec.ts pattern
grep -r 'testMatch.*\.spec\.ts' jest.config.* || grep -r 'spec.*\.spec\.ts' package.json
```

**Accept when:**
- No test files with .test.ts, _test.ts, or other non-standard extensions exist in the codebase (excluding node_modules)
- All test files use .spec.ts extension with appropriate prefixes (int.spec.ts, e2e.spec.ts) for their test type
- Test runner configuration explicitly includes .spec.ts pattern in test discovery settings

<enforcement>
Claude Code MUST NOT skip or defer verification. All test files must comply with the .spec.ts naming convention before code is approved for merge.
</enforcement>