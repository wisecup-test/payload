# Adopt Utility Function Pattern for Public API Contracts: Common Utility Patterns

These rules are ALWAYS ACTIVE for all utility functions exported from packages/payload/src/utilities/, packages/ui/src/elements/ utility modules, and shared utility functions used in test suites that demonstrate public API patterns.

### Rules

- **R-UTIL-001** SHOULD: Common utility patterns (validation, transformation, timing, formatting) SHOULD be organized into separate modules by functional domain.
- **R-UTIL-002** MUST: All utility functions exported from package entry points MUST have explicit TypeScript type definitions.
- **R-UTIL-003** MUST: All exported utility functions MUST pass strict TypeScript type checking (tsc --strict).
- **R-UTIL-004** MUST: Unit test coverage for utility functions MUST exceed 80% for branches, functions, and lines.
- **R-UTIL-005** MUST: All exported utility functions MUST be documented in API reference with usage examples.
- **R-UTIL-006** SHOULD: Utility function naming SHOULD follow verb-noun patterns for actions (validateTimezone, toKebabCase) and noun patterns for data transformations (timestamp).
- **R-UTIL-007** SHOULD: Utilities SHOULD be organized into domain-specific modules (validation/, transformation/, timing/, formatting/) to improve discoverability.
- **R-UTIL-008** MUST: No utility function duplication MUST exist across packages for the same logical operation.

### Verify

```bash
# Count exported utility functions
grep -r 'export.*function' packages/*/src/utilities/ | wc -l

# Find utility files without exports
find packages/*/src/utilities -name '*.ts' -exec grep -L 'export' {} \;

# Run utility tests with coverage thresholds
npm test -- --testPathPattern=utilities --coverage --coverageThreshold='{"global":{"branches":80,"functions":80,"lines":80}}'

# Verify strict TypeScript compilation
tsc --noEmit --strict packages/payload/src/utilities/*.ts packages/ui/src/elements/*/utilities/*.ts
```

**Accept when:**
- All utility functions in packages/*/src/utilities/ have explicit TypeScript type definitions and pass strict type checking
- Unit test coverage for utility functions exceeds 80% for branches, functions, and lines
- All exported utility functions are documented in API reference with usage examples
- No utility function duplication exists across packages for the same logical operation

<enforcement>
Claude Code MUST NOT skip or defer verification. CI build MUST fail if utility functions lack type definitions or fail strict type checking. Pull requests MUST be blocked if new utilities do not meet test coverage thresholds. Automated comments MUST be added to PRs identifying utility functions without documentation. Quarterly audits MUST be performed to identify and remediate non-compliant utility functions.
</enforcement>