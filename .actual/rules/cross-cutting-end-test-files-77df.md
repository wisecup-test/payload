# Adopt .spec.ts Extension for Test Files: End Test Files

These rules are ALWAYS ACTIVE for all TypeScript test files in the codebase, including unit tests, integration tests, and end-to-end tests across both packages/ and test/ directories.

### Rules

- **R-SPEC-001** MUST: End-to-end test files MUST use the naming pattern `*e2e.spec.ts`.
- **R-SPEC-002** MUST: All test files MUST use the `.spec.ts` extension (unit: `name.spec.ts`, integration: `name/int.spec.ts`, e2e: `name/e2e.spec.ts`).
- **R-SPEC-003** MUST: No test files with `.test.ts`, `_test.ts`, or other non-standard extensions are permitted in the codebase (excluding node_modules and vendored third-party code).
- **R-SPEC-004** SHOULD: Test runner configurations (Jest, Mocha, etc.) SHOULD explicitly include `**/*.spec.ts` pattern in test match globs.
- **R-SPEC-005** SHOULD: IDE templates and snippets SHOULD be provided for generating new test files with correct naming conventions.
- **R-SPEC-006** SHOULD: ESLint or custom linting rules SHOULD warn when test files do not follow the `.spec.ts` convention.
- **R-SPEC-007** SHOULD: Pre-commit hooks SHOULD validate test file naming conventions before allowing commits.

### Verify

```bash
# Verify no non-standard test file extensions exist
find . -type f \( -name '*.test.ts' -o -name '_test.ts' \) | grep -v node_modules | wc -l | grep -q '^0$'

# Count all .spec.ts files
find . -type f -name '*.spec.ts' | grep -v node_modules | wc -l

# Verify test runner configuration includes .spec.ts pattern
grep -r 'testMatch.*\.spec\.ts' jest.config.* || grep -r 'spec.*\.spec\.ts' package.json
```

**Accept when:**
- No test files with `.test.ts`, `_test.ts`, or other non-standard extensions exist in the codebase (excluding node_modules and vendored code).
- All test files use `.spec.ts` extension with appropriate prefixes (`int.spec.ts`, `e2e.spec.ts`) for their test type.
- Test runner configuration explicitly includes `.spec.ts` pattern in test discovery settings.
- End-to-end test files specifically follow the `*e2e.spec.ts` naming pattern.

<enforcement>
Claude Code MUST NOT skip or defer verification. All test files must comply with the `.spec.ts` extension standard before code is approved. CI/CD pipeline MUST fail if test files with non-standard extensions are detected. Pull requests with non-compliant test files MUST receive automated comments requesting correction.
</enforcement>