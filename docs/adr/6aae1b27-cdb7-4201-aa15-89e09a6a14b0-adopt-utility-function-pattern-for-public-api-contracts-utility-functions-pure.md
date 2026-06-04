# Adopt Utility Function Pattern for Public API Contracts: Utility Functions Pure

Status: proposed
Date: 2024-01-09
Deciders: Detection Pipeline (automated)

## Context

- The codebase contains 64 files exhibiting a consistent pattern of utility functions that form part of the public API contract, with 88.46% confidence
- Utility functions like validateTimezones, validateMimeType, toKebabCase, wait, and timestamp provide reusable validation and transformation capabilities across the system
- These utilities are distributed across packages (payload, ui) and test suites, indicating they serve as foundational building blocks for both internal and external API consumers
- The pattern emerged from the need to standardize common operations like validation, formatting, and timing across different modules and workflows
- Evidence shows these utilities are used in critical paths including authentication, queues/workflows, relationships, and bulk operations

## Problem Statement

Public APIs require consistent, well-tested utility functions for common operations like validation, transformation, and timing. Without a standardized approach to designing and exposing these utilities, API consumers face inconsistent interfaces, duplicated logic, and unpredictable behavior across different modules. The challenge is to establish clear contracts for utility functions that balance reusability, type safety, and maintainability while serving both internal and external API consumers.

## Decision

1. SHOULD: Utility functions SHOULD be pure functions without side effects when possible, especially for transformation and validation operations

## Policy Block

- SHOULD Utility functions SHOULD be pure functions without side effects when possible, especially for transformation and validation operations

In scope:
- All utility functions exported from packages/payload/src/utilities/
- All utility functions exported from packages/ui/src/elements/ utility modules
- Shared utility functions used in test suites that demonstrate public API patterns
- Validation, transformation, timing, and formatting utilities exposed to external consumers
- Helper functions that form part of documented public API contracts

Out of scope:
- Internal helper functions not exported from package entry points
- Private implementation details within class methods
- Test-only utilities not intended for production use
- Deprecated utility functions marked for removal
- Third-party library utilities re-exported without modification

Exceptions:
- EXC-001: Legacy utility functions that predate this ADR and are widely used in production
- EXC-002: Performance-critical utilities that require side effects or impure operations

## Rationale

- Pattern detection identified 64 files with 88.46% confidence exhibiting consistent utility function patterns, indicating this is an established architectural practice
- Evidence from critical modules (authentication, queues, relationships, bulk operations) demonstrates these utilities are foundational to system functionality
- Standardizing utility function contracts reduces cognitive load for API consumers and ensures consistent behavior across the platform
- The distribution across packages (payload, ui) and test suites shows these utilities serve as reusable building blocks that benefit from formal architectural guidance

## Consequences

Positive:
- Improved API consistency and predictability for both internal and external consumers
- Reduced code duplication through centralized, well-tested utility functions
- Better type safety and IDE support through explicit TypeScript definitions
- Easier onboarding for new developers with clear utility function patterns
- Enhanced testability through pure, single-responsibility utility functions

Negative:
- Additional overhead in creating and maintaining utility function documentation and tests
- Potential over-abstraction if utilities become too generic or complex
- Risk of breaking changes when modifying widely-used utility functions
- Increased package coupling if utilities are not properly organized by domain

## Alternatives

- Inline utility logic directly in consuming modules without shared utilities (rejected)
  Rejected because: Leads to code duplication, inconsistent implementations, and higher maintenance burden across 64+ files
  When valid: Only for truly unique, one-off operations that will never be reused
- Use third-party utility libraries (lodash, ramda) exclusively without custom utilities (rejected)
  Rejected because: Third-party libraries lack domain-specific validation and transformation logic needed for this system (e.g., validateTimezones, buildVersionCompoundIndexes)
  When valid: For generic operations like array manipulation or object cloning where third-party libraries provide sufficient functionality
- Implement utilities as class-based services with dependency injection (rejected)
  Rejected because: Adds unnecessary complexity for stateless operations; functional utilities are simpler, more testable, and easier to tree-shake
  When valid: When utilities require stateful configuration or complex lifecycle management

## Risks

- Breaking changes to widely-used utility functions could impact many consumers across 64+ files
  Mitigation: Implement semantic versioning, deprecation warnings, and maintain backward compatibility for at least one major version
  Owner: API Platform Team
- Utility function proliferation could lead to namespace pollution and discovery challenges
  Mitigation: Establish clear naming conventions, organize by functional domain, and maintain comprehensive API documentation with examples
  Owner: Engineering Team
- Performance overhead from abstraction layers in utility functions
  Mitigation: Profile critical paths, optimize hot-path utilities, and document performance characteristics for performance-sensitive operations
  Owner: Performance Engineering Team

## Implementation Notes

- Start by auditing existing utility functions in packages/payload/src/utilities/ and packages/ui/src/elements/ to identify patterns and consolidation opportunities
- Create a utility function template with TypeScript types, JSDoc documentation, unit tests, and usage examples
- Establish naming conventions: use verb-noun patterns for actions (validateTimezone, toKebabCase) and noun patterns for data transformations (timestamp)
- Organize utilities into domain-specific modules (validation/, transformation/, timing/, formatting/) to improve discoverability
- Add ESLint rules to enforce that exported utility functions have type definitions and test coverage
- Document all public utilities in API reference documentation with code examples and common use cases

## Continuation Context


Verify commands:
- grep -r 'export.*function' packages/*/src/utilities/ | wc -l
- find packages/*/src/utilities -name '*.ts' -exec grep -L 'export' {} \;
- npm test -- --testPathPattern=utilities --coverage --coverageThreshold='{"global":{"branches":80,"functions":80,"lines":80}}'
- tsc --noEmit --strict packages/payload/src/utilities/*.ts packages/ui/src/elements/*/utilities/*.ts

Accept when:
- All utility functions in packages/*/src/utilities/ have explicit TypeScript type definitions and pass strict type checking
- Unit test coverage for utility functions exceeds 80% for branches, functions, and lines
- All exported utility functions are documented in API reference with usage examples
- No utility function duplication exists across packages for the same logical operation

## Enforcement

- Verified by: Automated CI pipeline checks for TypeScript strict mode compliance on utility modules
- Verified by: Code coverage gates requiring 80%+ coverage for utility function tests
- Verified by: Pull request reviews using checklist for utility function standards
- Verified by: Static analysis tools (ESLint) enforcing naming conventions and export patterns
- Violation handling: CI build fails if utility functions lack type definitions or fail strict type checking
- Violation handling: Pull requests blocked if new utilities do not meet test coverage thresholds
- Violation handling: Automated comments on PRs identifying utility functions without documentation
- Violation handling: Quarterly audits to identify and remediate non-compliant utility functions
- Exception process: Submit exception request to architecture review board with justification and impact analysis
- Exception process: Document exception in ADR exceptions log with approval date and reviewer names
- Exception process: Add inline code comments referencing exception ID and rationale
- Exception process: Schedule follow-up review within 6 months to reassess exception necessity