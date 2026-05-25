# Adopt Promise-Based Async/Await Pattern for Asynchronous Operations: Promise Chains Then

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ALWAYS ACTIVE for all asynchronous code development and governs the concurrency model used throughout the codebase.

## Context

- The codebase requires a consistent approach to handling asynchronous operations across utilities, tests, and application logic
- Modern JavaScript/TypeScript provides multiple concurrency models (callbacks, promises, async/await, observables) requiring standardization
- Pattern detected across 5 files with 89.84% confidence indicates widespread adoption of promise-based async/await pattern
- Test files and utility functions demonstrate consistent use of async/await for timing operations, API calls, and test orchestration
- The facet 'paradigm.concurrency_model' indicates this is a fundamental architectural choice affecting code readability, error handling, and maintainability

## Problem Statement

Without a standardized concurrency model, asynchronous code becomes inconsistent, difficult to maintain, and prone to errors. Mixed patterns (callbacks, promises, async/await) create cognitive overhead, complicate error handling, and reduce code readability. The codebase needs a unified approach to asynchronous operations that provides clear error handling, sequential reasoning, and composability.

## Decision

1. SHOULD: Promise chains (.then/.catch) SHOULD be refactored to async/await syntax for improved readability

## Policy Block

- SHOULD Promise chains (.then/.catch) SHOULD be refactored to async/await syntax for improved readability

In scope:
- All new TypeScript/JavaScript code implementing asynchronous operations
- Utility functions handling timing, delays, and async coordination
- Test suites requiring async setup, teardown, or assertions
- API client code and HTTP request handlers
- Database operations and data access layers

Out of scope:
- Third-party library internals (use as-is)
- Legacy callback-based APIs that cannot be wrapped
- Event emitter patterns where callbacks are idiomatic
- Stream processing where reactive patterns are more appropriate

Exceptions:
- EXC-001: Interfacing with legacy Node.js APIs that only support callbacks (e.g., fs.readFile)
- EXC-002: Performance-critical hot paths where Promise overhead is measurably significant

## Rationale

- Pattern detected in 5 files with 89.84% confidence indicates this is already the de facto standard in the codebase
- Async/await provides superior readability by allowing asynchronous code to be written in a synchronous style, reducing cognitive load
- Error handling with try/catch is more intuitive and consistent with synchronous error handling patterns
- Modern TypeScript tooling provides excellent type inference and IDE support for async/await patterns

## Consequences

Positive:
- Improved code readability with sequential, synchronous-looking async code
- Consistent error handling patterns across the entire codebase
- Better debugging experience with clearer stack traces
- Enhanced maintainability through standardized async patterns
- Reduced cognitive overhead when switching between different parts of the codebase

Negative:
- Requires all developers to understand Promise semantics and async/await behavior
- May introduce subtle bugs if developers don't properly await async operations
- Potential for unhandled promise rejections if error handling is incomplete
- Legacy callback-based code requires refactoring or wrapping

## Alternatives

- Continue using mixed callback and Promise patterns without standardization (rejected)
  Rejected because: Creates inconsistent codebase with high cognitive overhead, difficult error handling, and poor maintainability
  When valid: Never - standardization is critical for team productivity
- Adopt reactive programming with RxJS Observables for all async operations (rejected)
  Rejected because: Adds significant complexity and learning curve; overkill for most use cases in this codebase; async/await is sufficient
  When valid: Consider for specific modules with complex event streams or real-time data processing requirements
- Use raw Promises with .then/.catch chains instead of async/await (rejected)
  Rejected because: Less readable than async/await, creates callback-like nesting, harder to debug, and async/await is the modern standard
  When valid: Only when async/await is not available (pre-ES2017 environments)

## Risks

- Developers may forget to await async operations, causing race conditions and subtle bugs
  Mitigation: Enable TypeScript strict mode and ESLint rules (no-floating-promises, require-await) to catch missing awaits at compile/lint time
  Owner: Engineering team + CI/CD pipeline
- Unhandled promise rejections may crash the application or cause silent failures
  Mitigation: Implement global unhandled rejection handlers, enforce try/catch in async functions, and use linting rules to detect missing error handling
  Owner: Platform team
- Refactoring legacy callback code to async/await may introduce regressions
  Mitigation: Refactor incrementally with comprehensive test coverage, use util.promisify for Node.js callbacks, and conduct thorough code review
  Owner: Engineering team

## Implementation Notes

- Use TypeScript's Promise<T> type annotations to document async function return types explicitly
- Create centralized utility functions for common patterns (wait, retry, timeout) to promote reuse and consistency
- Configure ESLint with @typescript-eslint/no-floating-promises and @typescript-eslint/require-await rules
- For Node.js callback APIs, use util.promisify to convert to Promise-based interfaces
- Document async function behavior in JSDoc comments, especially regarding error conditions and return values
- In test files, ensure all async test functions are properly awaited or return promises to test runners

## Continuation Context


Verify commands:
- grep -r "function.*callback" --include="*.ts" --include="*.tsx" | grep -v "node_modules" | grep -v "legacy"
- eslint . --ext .ts,.tsx --rule '@typescript-eslint/no-floating-promises: error' --rule '@typescript-eslint/require-await: warn'
- grep -r "\.then(" --include="*.ts" --include="*.tsx" | grep -v "node_modules" | wc -l

Accept when:
- No new callback-based async patterns are introduced in non-legacy code
- ESLint checks pass with no-floating-promises and require-await rules enabled
- All async functions are properly typed with Promise return types
- Test suites properly await all async operations without warnings

## Enforcement

- Verified by: ESLint rules in CI pipeline (@typescript-eslint/no-floating-promises, @typescript-eslint/require-await)
- Verified by: TypeScript compiler strict mode checks
- Verified by: Code review checklist requiring async/await pattern verification
- Verified by: Automated grep-based checks in pre-commit hooks
- Violation handling: CI pipeline fails on ESLint violations related to async patterns
- Violation handling: Code review blocks merge if callback patterns are introduced without justification
- Violation handling: Automated comments on PRs highlighting .then chains that should be refactored
- Violation handling: Monthly technical debt review to identify and prioritize legacy callback refactoring
- Exception process: Developer documents exception rationale in code comments and PR description
- Exception process: Tech lead or architect reviews and approves exception with justification
- Exception process: Exception is logged in technical debt register with remediation plan
- Exception process: Exception code is marked with TODO or LEGACY comments for future refactoring