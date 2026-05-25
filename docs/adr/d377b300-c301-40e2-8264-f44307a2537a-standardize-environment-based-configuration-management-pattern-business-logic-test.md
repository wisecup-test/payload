# Standardize Environment-Based Configuration Management Pattern: Business Logic Test

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ALWAYS ACTIVE for all configuration management implementations across runtime environments, test suites, and utility modules.

## Context

- The codebase exhibits a consistent pattern of environment-based configuration management across multiple test suites and utility modules, with 5 files demonstrating this approach
- Configuration sources are centralized in dedicated config files (test/auth/config.ts, test/base-path/config.ts) and utility modules (timestamp.ts), indicating a deliberate architectural choice for managing runtime behavior
- Test specifications (int.spec.ts, e2e.spec.ts) consume these configuration sources, suggesting a need for consistent environment setup across different testing contexts
- The pattern appears in both authentication, base-path routing, and utility timestamp handling contexts, indicating cross-cutting concern management
- The 87.50% confidence and significance scores across 5 files suggest this is an established, intentional pattern rather than ad-hoc configuration handling

## Problem Statement

Applications require a consistent, maintainable approach to managing configuration across different runtime environments (development, testing, production) and functional domains (authentication, routing, utilities). Without a standardized pattern, configuration becomes scattered, difficult to test, and prone to environment-specific bugs. The challenge is to establish a unified configuration management strategy that supports testability, environment isolation, and cross-cutting concerns while maintaining code clarity and reducing duplication.

## Decision

1. MUST_NOT: Business logic and test specifications MUST NOT contain hardcoded environment-specific values

## Policy Block

- MUST_NOT Business logic and test specifications MUST NOT contain hardcoded environment-specific values

In scope:
- All test configuration files (*.spec.ts, *.test.ts)
- Dedicated configuration modules (config.ts, settings.ts)
- Utility modules requiring environment-aware behavior
- Authentication and authorization configuration
- Routing and base-path configuration
- Database connection and runtime environment settings

Out of scope:
- Build-time constants that never change across environments
- Type definitions and interfaces without runtime values
- Pure algorithmic code without environmental dependencies
- Third-party library configurations managed by external tools

Exceptions:
- EXC-001: Temporary debugging or development experiments require inline configuration overrides
- EXC-002: Legacy code modules scheduled for refactoring within current sprint

## Rationale

- The pattern detection identified 5 files with 87.50% confidence demonstrating consistent environment-based configuration management, indicating this is an established architectural practice
- Centralizing configuration in dedicated modules improves testability by allowing test suites to inject different configurations without modifying business logic
- The presence of this pattern across authentication, routing, and utility contexts demonstrates its effectiveness as a cross-cutting concern solution
- Environment-based configuration management reduces deployment risks by ensuring consistent behavior across development, testing, and production environments while allowing necessary environment-specific customization

## Consequences

Positive:
- Improved testability through configuration injection and environment isolation
- Reduced code duplication by centralizing environment-specific values
- Enhanced maintainability with clear separation between configuration and business logic
- Easier debugging and troubleshooting with explicit configuration sources
- Better support for multiple deployment environments without code changes

Negative:
- Additional indirection layer may increase initial complexity for simple applications
- Requires discipline to maintain configuration centralization as codebase grows
- Potential for configuration drift if not properly versioned and documented
- May require additional tooling for configuration validation and type safety

## Alternatives

- Hardcode configuration values directly in business logic and test files (rejected)
  Rejected because: Creates tight coupling between environment-specific values and business logic, making testing difficult and increasing deployment risk. The detected pattern explicitly avoids this approach.
  When valid: Only acceptable for true constants that never vary across environments (e.g., mathematical constants, protocol specifications)
- Use a single global configuration object for all modules (rejected)
  Rejected because: Creates tight coupling between unrelated modules and makes it difficult to understand configuration dependencies. The detected pattern favors co-located configuration files.
  When valid: May be appropriate for very small applications with minimal configuration needs
- Use dependency injection framework for all configuration management (deferred)
  Rejected because: While providing additional benefits, this adds framework dependency and complexity. The current pattern achieves configuration management goals with simpler module-based approach.
  When valid: Consider for larger applications requiring sophisticated dependency management and lifecycle control

## Risks

- Configuration files may become scattered across the codebase without clear organization conventions
  Mitigation: Establish naming conventions (config.ts, settings.ts) and directory structure guidelines. Document configuration file locations in project README.
  Owner: Engineering team
- Missing or invalid configuration values may cause runtime failures that are difficult to diagnose
  Mitigation: Implement configuration validation at application startup. Use TypeScript types to enforce configuration schema. Provide clear error messages for missing required values.
  Owner: Engineering team
- Developers may bypass configuration pattern for convenience, leading to pattern erosion
  Mitigation: Implement automated checks in CI pipeline to detect hardcoded environment-specific values. Include configuration pattern in code review checklist.
  Owner: Engineering team and DevOps

## Implementation Notes

- Create dedicated config.ts files co-located with the modules they configure (e.g., test/auth/config.ts for authentication tests)
- Use environment variables as the primary source for environment-specific values, with sensible defaults for development
- Export configuration objects or functions from config modules rather than individual constants to maintain cohesion
- Document all configuration options including their purpose, valid values, and default behavior in code comments or README files
- Consider using configuration validation libraries (e.g., zod, joi) to enforce configuration schema at runtime

## Continuation Context


Verify commands:
- grep -r "process\.env" --include="*.spec.ts" --include="*.test.ts" | grep -v "config\.ts" | wc -l | awk '{if ($1 > 5) exit 1}'
- find . -name "config.ts" -o -name "settings.ts" | wc -l | awk '{if ($1 < 1) exit 1}'
- grep -r "hardcoded.*environment" --include="*.ts" | grep -i "TODO\|FIXME" | wc -l

Accept when:
- Test files reference configuration from dedicated config modules rather than directly accessing environment variables (fewer than 5 direct process.env references in test files)
- At least one dedicated configuration file (config.ts or settings.ts) exists in the codebase
- No untracked hardcoded environment-specific values exist outside of configuration modules (or are marked with TODO/FIXME for refactoring)

## Enforcement

- Verified by: Automated CI pipeline checks for hardcoded environment values using grep patterns
- Verified by: Code review checklist includes verification of configuration pattern compliance
- Verified by: Static analysis tools configured to flag direct environment variable access outside config modules
- Violation handling: CI pipeline fails if hardcoded environment values are detected outside configuration modules
- Violation handling: Code review requires changes before approval if configuration pattern is violated
- Violation handling: Technical debt tickets are created for legacy code violations with prioritized remediation plan
- Exception process: Developer documents exception rationale in code comments with ticket reference
- Exception process: Tech lead reviews and approves exception with documented timeline for remediation
- Exception process: Exception is tracked in technical debt backlog with regular review in sprint planning