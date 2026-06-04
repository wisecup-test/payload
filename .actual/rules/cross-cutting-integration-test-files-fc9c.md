# Adopt .spec.ts Extension for Test Files: Integration Test Files

These rules are ALWAYS ACTIVE for all TypeScript test files in the codebase, including unit tests, integration tests, and end-to-end tests across both packages/ and test/ directories.

### Rules

- **R-SPEC-001** MUST: Integration test files MUST use the naming pattern `*int.spec.ts`.
- **R-SPEC-002** MUST: All test files MUST use the `.spec.ts` extension for automated test discovery.
- **R-SPEC-003** SHOULD: Test files SHOULD follow semantic naming patterns: `name.spec.ts` for unit tests, `name/int.spec.ts` for integration tests, `name/e2e.spec.ts` for end-to-end tests.
- **R-SPEC-004** MUST: Test runner configurations (Jest, Mocha, etc.) MUST explicitly include `**/*.spec.ts` pattern in test match globs.

### Verify

```bash
# Verify no non-standard test file extensions exist
find . -type f \( -name '*.test.ts' -o -name '_test.ts' \) | grep -v node_modules | wc -l | grep -q '^0$'

# Count compliant .spec.ts files
find . -type f -name '*.spec.ts' | grep -v node_modules | wc -l

# Verify test runner configuration includes .spec.ts pattern
grep -r 'testMatch.*\.spec\.ts' jest.config.* || grep -r 'spec.*\.spec\.ts' package.json
```

**Accept when:**
- No test files with `.test.ts`, `_test.ts`, or other non-standard extensions exist in the codebase (excluding node_modules).
- All test files use `.spec.ts` extension with appropriate prefixes (`int.spec.ts`, `e2e.spec.ts`) for their test type.
- Test runner configuration explicitly includes `.spec.ts` pattern in test discovery settings.
- Integration test files specifically follow the `*int.spec.ts` naming pattern.

<enforcement>
Claude Code MUST NOT skip or defer verification. All test files must be checked for naming compliance before acceptance. CI/CD pipeline MUST fail if test files with non-standard extensions are detected.
</enforcement>