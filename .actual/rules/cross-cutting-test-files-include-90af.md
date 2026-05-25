# Adopt .spec.ts Extension for Test Files: Test Files Include

These rules are ALWAYS ACTIVE for all TypeScript test files in the codebase, including unit tests, integration tests, and end-to-end tests across both packages/ and test/ directories.

### Rules

- **R-TEST-001** MUST: All TypeScript test files use the .spec.ts extension.
- **R-TEST-002** MAY: Test files MAY include additional descriptive segments in the filename before the type indicator (e.g., forgot-password-localized/int.spec.ts).
- **R-TEST-003** SHOULD: Integration tests use the int.spec.ts pattern to provide clear semantic meaning about test scope.
- **R-TEST-004** SHOULD: End-to-end tests use the e2e.spec.ts pattern to distinguish execution context from unit and integration tests.

### Verify

```bash
# Verify no non-standard test file extensions exist
find . -type f \( -name '*.test.ts' -o -name '_test.ts' \) | grep -v node_modules | wc -l | grep -q '^0$'

# Count .spec.ts files to confirm adoption
find . -type f -name '*.spec.ts' | grep -v node_modules | wc -l

# Verify test runner configuration includes .spec.ts pattern
grep -r 'testMatch.*\.spec\.ts' jest.config.* || grep -r 'spec.*\.spec\.ts' package.json
```

**Accept when:**
- No test files with .test.ts, _test.ts, or other non-standard extensions exist in the codebase (excluding node_modules)
- All test files use .spec.ts extension with appropriate prefixes (int.spec.ts, e2e.spec.ts) for their test type
- Test runner configuration explicitly includes .spec.ts pattern in test discovery settings

<enforcement>
Claude Code MUST NOT skip or defer verification. All test files must conform to the .spec.ts naming convention before code is approved for merge.
</enforcement>