# Adopt Utility Function Pattern for Public API Contracts: Public Utility Functions

These rules are ALWAYS ACTIVE for all utility functions exported from dedicated utility modules across packages/payload/src/utilities/, packages/ui/src/elements/ utility modules, and shared utility functions used in test suites that demonstrate public API patterns.

### Rules

- **R-UTIL-001** MUST: All public utility functions MUST be exported from dedicated utility modules with clear, single-responsibility purposes (e.g., validateTimezones, validateMimeType, toKebabCase).
- **R-UTIL-002** MUST: All exported utility functions have explicit TypeScript type definitions and pass strict type checking.
- **R-UTIL-003** MUST: Unit test coverage for utility functions must exceed 80% for branches, functions, and lines.
- **R-UTIL-004** MUST: All exported utility functions are documented in API reference with usage examples.
- **R-UTIL-005** SHOULD: Use verb-noun patterns for actions (validateTimezone, toKebabCase) and noun patterns for data transformations (timestamp).
- **R-UTIL-006** SHOULD: Organize utilities into domain-specific modules (validation/, transformation/, timing/, formatting/) to improve discoverability.
- **R-UTIL-007** SHOULD: Implement semantic versioning and maintain backward compatibility for at least one major version when modifying widely-used utility functions.

### Verify

```bash
# Count exported functions in utility modules
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
- Utility functions follow naming conventions (verb-noun for actions, noun for transformations)
- Utilities are organized into domain-specific modules by functional area

<enforcement>
Claude Code MUST NOT skip or defer verification. All utility functions must pass TypeScript strict mode and achieve 80%+ test coverage before acceptance. CI pipeline checks are mandatory.
</enforcement>