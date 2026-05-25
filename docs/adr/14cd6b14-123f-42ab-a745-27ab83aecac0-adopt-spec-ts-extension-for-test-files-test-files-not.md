# Adopt .spec.ts Extension for Test Files: Test Files Not

Status: proposed
Date: 2024-01-09
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ACTIVE for all test file creation and naming decisions in the codebase.

## Context

- The codebase contains multiple test files across authentication, relationships, and utility modules that consistently use the .spec.ts extension pattern
- Test files are distributed across both integration tests (int.spec.ts) and end-to-end tests (e2e.spec.ts), indicating a need for standardized naming conventions
- The pattern appears in 8 files with 91.46% confidence, suggesting this is an established convention rather than an ad-hoc choice
- TypeScript-based testing frameworks like Jest and Mocha commonly support .spec.ts as a standard test file identifier
- Consistent test file naming enables automated test discovery, IDE integration, and clear separation between production and test code

## Problem Statement

Without a standardized test file naming convention, teams may use inconsistent patterns (.test.ts, .spec.ts, _test.ts, etc.) leading to fragmented test discovery configurations, confusion about which files contain tests, and difficulty maintaining consistent tooling across the codebase. A clear standard is needed to ensure all test files are easily identifiable and automatically discoverable by test runners.

## Decision

1. MUST_NOT: Test files MUST NOT use alternative extensions such as .test.ts, _test.ts, or -test.ts

## Policy Block

- MUST_NOT Test files MUST NOT use alternative extensions such as .test.ts, _test.ts, or -test.ts

In scope:
- All TypeScript test files in the codebase
- Unit tests, integration tests, and end-to-end tests
- Test files in both packages/ and test/ directories
- New test files created during development
- Refactored or renamed test files

Out of scope:
- JavaScript test files (.spec.js may follow similar conventions but are not covered by this ADR)
- Test configuration files (jest.config.js, etc.)
- Test utility files that don't contain actual test cases
- Mock data files or fixtures
- Non-test TypeScript files

Exceptions:
- EX-001: Legacy test files in third-party dependencies or vendored code that cannot be renamed
- EX-002: Framework-specific test files that require different naming conventions (e.g., Playwright test files)

## Rationale

- The .spec.ts extension is widely recognized in the TypeScript/JavaScript ecosystem and supported by major testing frameworks including Jest, Mocha, and Jasmine
- Pattern detection shows 91.46% confidence across 8 files, indicating this is already the de facto standard in the codebase
- Consistent naming enables glob patterns like **/*.spec.ts for test discovery, simplifying CI/CD configuration and IDE test runner integration
- The int.spec.ts and e2e.spec.ts patterns provide clear semantic meaning about test scope and execution context, helping developers understand test boundaries

## Consequences

Positive:
- Automated test discovery becomes reliable and predictable across all testing tools
- Developers can immediately identify test files in directory listings and search results
- IDE test runners can automatically detect and run tests without manual configuration
- Code coverage tools can accurately map test files to source files
- Onboarding new developers is simplified with clear, consistent naming conventions

Negative:
- Existing test files using different conventions will need to be renamed, potentially breaking git history and requiring updates to documentation
- Teams familiar with .test.ts convention from other projects may need to adjust their habits
- CI/CD pipelines and test configurations may need updates to reflect the standardized pattern
- Some tooling configurations may need to be updated to recognize the .spec.ts pattern if they were configured for alternatives

## Alternatives

- Use .test.ts extension for all test files (rejected)
  Rejected because: The codebase already has established .spec.ts usage with 91.46% confidence across 8 files, and changing would require significant refactoring without clear benefits
  When valid: Could be considered for greenfield projects with no existing test files
- Allow both .spec.ts and .test.ts extensions interchangeably (rejected)
  Rejected because: Mixed conventions would defeat the purpose of standardization and create confusion about which pattern to use for new tests
  When valid: Never recommended; consistency is more valuable than flexibility in this context
- Use __tests__ directory with no special file extension (rejected)
  Rejected because: The current codebase uses both co-located tests and test/ directories, and the .spec.ts extension provides clear identification regardless of location
  When valid: Could be used in conjunction with .spec.ts for organizing test suites, but should not replace the extension convention

## Risks

- Renaming existing test files could break CI/CD pipelines, git history, and external references
  Mitigation: Implement gradual migration with git mv to preserve history, update all pipeline configurations before renaming, and communicate changes to all teams
  Owner: Engineering team with DevOps support
- Developers may forget the convention and create test files with non-standard extensions
  Mitigation: Implement linting rules or pre-commit hooks to enforce naming conventions, add documentation to contribution guidelines, and provide IDE templates for test file creation
  Owner: Engineering team
- Third-party tools or frameworks may not recognize .spec.ts pattern by default
  Mitigation: Document configuration requirements for all testing tools, maintain a testing setup guide, and validate tool compatibility during evaluation
  Owner: QA and DevOps teams

## Implementation Notes

- Update test runner configurations (Jest, Mocha, etc.) to explicitly include **/*.spec.ts pattern in test match globs
- Create IDE templates or snippets for generating new test files with correct naming conventions
- Add ESLint or custom linting rules to warn when test files don't follow the .spec.ts convention
- Document the naming patterns (unit: name.spec.ts, integration: name/int.spec.ts, e2e: name/e2e.spec.ts) in the project's testing guidelines
- Consider adding a pre-commit hook that validates test file naming conventions before allowing commits

## Continuation Context


Verify commands:
- find . -type f -name '*.test.ts' | grep -v node_modules | wc -l | grep -q '^0$'
- find . -type f -name '*.spec.ts' | grep -v node_modules | wc -l
- grep -r 'testMatch.*\.spec\.ts' jest.config.* || grep -r 'spec.*\.spec\.ts' package.json

Accept when:
- No test files with .test.ts, _test.ts, or other non-standard extensions exist in the codebase (excluding node_modules)
- All test files use .spec.ts extension with appropriate prefixes (int.spec.ts, e2e.spec.ts) for their test type
- Test runner configuration explicitly includes .spec.ts pattern in test discovery settings

## Enforcement

- Verified by: Automated linting in CI/CD pipeline checking for non-compliant test file names
- Verified by: Code review checklist includes verification of test file naming conventions
- Verified by: Pre-commit hooks validate test file extensions before allowing commits
- Verified by: Periodic audits using grep/find commands to identify non-compliant files
- Violation handling: CI/CD pipeline fails if test files with non-standard extensions are detected
- Violation handling: Pull requests with non-compliant test files receive automated comments requesting correction
- Violation handling: Existing violations are tracked in technical debt backlog with priority for remediation
- Violation handling: New violations must be corrected before merge approval
- Exception process: Developer submits exception request to tech lead with justification (e.g., framework requirement, third-party constraint)
- Exception process: Tech lead or architecture team reviews the request and approves/denies within 2 business days
- Exception process: Approved exceptions are documented in the test directory README with expiration date or review period
- Exception process: All exceptions are reviewed quarterly to determine if they can be resolved or if the policy needs adjustment