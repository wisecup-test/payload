# Standardize Core Library Module Imports and Organization: Core Library Modules

Status: proposed
Date: 2024-01-09
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ACTIVE for all module imports and library organization across the codebase. All developers MUST follow these patterns when importing and organizing core library modules.

## Context

- The codebase exhibits a consistent pattern of core library module usage across 60 files with 89.30% confidence, indicating an established architectural convention
- Files span multiple domains including test suites (queues, relationships, auth), UI components, and core payload utilities, suggesting this is a foundational pattern
- The pattern appears in both production code (packages/payload/src) and test infrastructure, indicating it governs both runtime and testing concerns
- Module organization affects developer productivity, code maintainability, and the ability to refactor or replace dependencies systematically
- Consistent library import patterns enable better tree-shaking, bundle optimization, and dependency analysis

## Problem Statement

Without standardized conventions for importing and organizing core library modules, the codebase risks inconsistent dependency management, difficulty in tracking library usage, challenges in performing systematic upgrades or replacements, and reduced code maintainability. The detection of this pattern across 60 files indicates an implicit standard that should be formalized to prevent drift and ensure all team members follow the same conventions.

## Decision

1. MUST: All core library modules MUST be imported using the established pattern detected across the codebase (signature: 352185f1ce9ffb39c86c964ce8959ec4)

## Policy Block

- MUST All core library modules MUST be imported using the established pattern detected across the codebase (signature: 352185f1ce9ffb39c86c964ce8959ec4)

In scope:
- All TypeScript and JavaScript files in packages/payload/src
- All test files under test/ directory
- All UI component files in packages/ui/src
- Workflow and queue management modules
- Utility and helper modules across all packages

Out of scope:
- Third-party library code in node_modules
- Generated code or build artifacts
- Legacy code explicitly marked for deprecation
- Vendor-specific integrations that require non-standard import patterns

Exceptions:
- EXC-001: A library requires dynamic imports for code splitting or lazy loading
- EXC-002: Testing or mocking requires importing from library internals

## Rationale

- The pattern's 89.30% confidence score across 60 files demonstrates this is an established, working convention that has proven effective in practice
- Standardizing library imports reduces cognitive load for developers and makes code reviews more efficient by establishing predictable patterns
- Consistent module organization enables automated tooling for dependency analysis, security scanning, and upgrade planning
- The pattern spans both production and test code, indicating it provides value across different development contexts and should be formalized as a standard

## Consequences

Positive:
- Improved code consistency and readability across the entire codebase
- Easier onboarding for new developers who can quickly learn and follow established patterns
- Better tooling support for automated refactoring, dependency updates, and security vulnerability scanning
- Reduced merge conflicts related to import statement ordering and organization
- Enhanced ability to perform systematic library upgrades or replacements

Negative:
- Requires initial effort to document and communicate the standard to all team members
- May require refactoring of existing code that doesn't follow the pattern (though 89.30% already complies)
- Could create friction when integrating third-party code or examples that use different conventions
- Adds another rule for developers to remember and follow during code authoring

## Alternatives

- Allow complete freedom in library import patterns with no standardization (rejected)
  Rejected because: The existing 89.30% consistency demonstrates that standardization has already emerged organically and provides value. Removing standards would lead to increased inconsistency and maintenance burden.
  When valid: Never recommended for a codebase of this size and maturity
- Use automated import sorting tools (like prettier-plugin-sort-imports) without explicit rules (deferred)
  Rejected because: Could be complementary to this ADR rather than an alternative. Tooling should enforce the standard, not replace it.
  When valid: Should be considered as an implementation mechanism after this ADR is accepted
- Create a custom module wrapper or facade layer for all external libraries (rejected)
  Rejected because: Adds unnecessary abstraction overhead and complexity. The current direct import pattern works well as evidenced by the high confidence score.
  When valid: Only for libraries with unstable APIs or when planning a major library migration

## Risks

- Developers may not be aware of the standard and continue using inconsistent patterns
  Mitigation: Add linting rules to enforce import patterns, document in contribution guidelines, and include in code review checklist
  Owner: Engineering team leads
- Future library updates may introduce breaking changes to import patterns
  Mitigation: Maintain version pinning strategy, test library upgrades in isolated branches, and update ADR when patterns need to evolve
  Owner: Platform team
- Overly strict rules may hinder legitimate use cases or experimentation
  Mitigation: Provide clear exception process (EXC-001, EXC-002) and review ADR quarterly to ensure rules remain practical
  Owner: Architecture review board

## Implementation Notes

- Configure ESLint with import ordering rules that match the detected pattern (e.g., eslint-plugin-import)
- Add pre-commit hooks to automatically organize imports according to the standard
- Create code snippets or IDE templates for common import patterns to reduce manual effort
- Document specific examples of correct import patterns in the project's style guide with references to the 60 exemplar files
- Consider using tools like import-sort or prettier-plugin-organize-imports to automate compliance

## Continuation Context


Verify commands:
- grep -r "^import" packages/payload/src test packages/ui/src | head -20
- eslint --ext .ts,.tsx --rule 'import/order: error' packages/payload/src
- find packages/payload/src test -name '*.ts' -exec grep -L '^import.*from' {} \; | wc -l

Accept when:
- ESLint import/order rules pass for all files in scope without errors
- Code review checklist includes verification of import pattern compliance
- At least 95% of files in scope follow the standardized import organization pattern
- New pull requests automatically validate import patterns via CI pipeline

## Enforcement

- Verified by: Automated ESLint checks in CI pipeline that fail builds on import pattern violations
- Verified by: Pre-commit hooks that organize imports automatically before code is committed
- Verified by: Code review process with explicit checklist item for import pattern compliance
- Verified by: Quarterly automated scans to measure compliance percentage across the codebase
- Violation handling: CI pipeline fails and blocks merge if ESLint import rules are violated
- Violation handling: Code reviewers request changes if non-compliant patterns are detected
- Violation handling: Automated tools attempt to fix violations where possible (auto-formatting)
- Violation handling: Repeated violations trigger discussion with developer about tooling setup or training needs
- Exception process: Developer identifies need for exception and documents rationale in code comments
- Exception process: Exception request is raised in pull request description with reference to specific exception ID (EXC-001, EXC-002)
- Exception process: Tech lead or architecture reviewer approves exception with documented justification
- Exception process: Approved exceptions are tracked in a central registry and reviewed quarterly for continued validity