# Adopt .spec.ts Extension for Test Files: Test Files Not

These rules are ALWAYS ACTIVE for all TypeScript test files in the codebase, including unit tests, integration tests, and end-to-end tests across both packages/ and test/ directories.

### Rules

- **R-TEST-001** MUST_NOT: Test files MUST NOT use alternative extensions such as .test.ts, _test.ts, or -test.ts
- **R-TEST-002** MUST: All TypeScript test files MUST use the .spec.ts extension
- **R-TEST-003** SHOULD: Integration tests SHOULD use the int.spec.ts pattern
- **R-TEST-004** SHOULD: End-to-end tests SHOULD use the e2e.spec.ts pattern

### Verify

```bash
# Check for non-compliant test file extensions
find . -type f -name '*.test.ts' | grep -v node_modules | wc -l | grep -q '^0$'

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
Claude Code MUST NOT skip or defer verification. All test files must comply with the .spec.ts naming convention before code is approved.
</enforcement>