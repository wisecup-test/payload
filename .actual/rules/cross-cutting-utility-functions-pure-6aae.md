# Adopt Utility Function Pattern for Public API Contracts: Utility Functions Pure

These rules are ALWAYS ACTIVE for all utility functions exported from packages/payload/src/utilities/, packages/ui/src/elements/ utility modules, and shared utility functions used in test suites that demonstrate public API patterns.

### Rules

- **R-UTIL-001** SHOULD: Utility functions SHOULD be pure functions without side effects when possible, especially for transformation and validation operations.

### Verify

```bash
# Count exported utility functions
grep -r 'export.*function' packages/*/src/utilities/ | wc -l

# Find utility files without exports
find packages/*/src/utilities -name '*.ts' -exec grep -L 'export' {} \;

# Run utility tests with coverage thresholds
npm test -- --testPathPattern=utilities --coverage --coverageThreshold='{"global":{"branches":80,"functions":80,"lines":80}}'

# Verify strict TypeScript compliance
tsc --noEmit --strict packages/payload/src/utilities/*.ts packages/ui/src/elements/*/utilities/*.ts
```

**Accept when:**
- All utility functions in packages/*/src/utilities/ have explicit TypeScript type definitions and pass strict type checking
- Unit test coverage for utility functions exceeds 80% for branches, functions, and lines
- All exported utility functions are documented in API reference with usage examples
- No utility function duplication exists across packages for the same logical operation

<enforcement>
Claude Code MUST NOT skip or defer verification. All utility functions must pass strict TypeScript type checking and meet 80%+ test coverage thresholds before acceptance.
</enforcement>