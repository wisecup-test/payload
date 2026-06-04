# Adopt .spec.ts Extension for Test Files: Unit Test Files

These rules are ALWAYS ACTIVE for all TypeScript test files in the codebase, including unit tests, integration tests, and end-to-end tests across both packages/ and test/ directories.

### Rules

- **R-SPEC-001** SHOULD: Unit test files SHOULD use the naming pattern matching the source file (e.g., traverseFields.spec.ts for traverseFields.ts).
- **R-SPEC-002** SHOULD: Integration test files SHOULD use the naming pattern int.spec.ts (e.g., traverseFields.int.spec.ts).
- **R-SPEC-003** SHOULD: End-to-end test files SHOULD use the naming pattern e2e.spec.ts (e.g., traverseFields.e2e.spec.ts).
- **R-SPEC-004** MUST: Test runner configurations (Jest, Mocha, etc.) MUST explicitly include **/*.spec.ts pattern in test match globs.

### Verify

```bash
# Check for non-standard test file extensions
find . -type f -name '*.test.ts' | grep -v node_modules | wc -l | grep -q '^0$'

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
Claude Code MUST NOT skip or defer verification. All test files created or modified MUST comply with the .spec.ts naming convention before merge approval.
</enforcement>