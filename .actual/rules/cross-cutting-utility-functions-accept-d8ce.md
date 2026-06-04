# Adopt Utility Function Pattern for Public API Contracts: Utility Functions Accept

These rules are ALWAYS ACTIVE for all utility functions exported from packages/payload/src/utilities/, packages/ui/src/elements/ utility modules, and shared utility functions used in test suites that demonstrate public API patterns.

### Rules

- **R-UTIL-001** MAY: Utility functions MAY accept optional configuration objects to support extensibility without breaking existing API contracts.

### Verify

```bash
# Count exported utility functions
grep -r 'export.*function' packages/*/src/utilities/ | wc -l

# Find utility files without exports
find packages/*/src/utilities -name '*.ts' -exec grep -L 'export' {} \;

# Verify test coverage meets 80% threshold
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
Claude Code MUST NOT skip or defer verification. All utility functions must pass TypeScript strict mode and meet coverage thresholds before acceptance.
</enforcement>