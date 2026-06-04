# Adopt Utility Function Pattern for Public API Contracts: Utility Functions Used

These rules are ALWAYS ACTIVE for all utility functions exported from packages/payload/src/utilities/, packages/ui/src/elements/ utility modules, and shared utility functions used in test suites that demonstrate public API patterns.

### Rules

- **R-UTIL-001** SHOULD: Utility functions used across multiple packages SHOULD be placed in shared package locations to avoid duplication.

### Verify

```bash
# Count exported utility functions
grep -r 'export.*function' packages/*/src/utilities/ | wc -l

# Find utility files without exports
find packages/*/src/utilities -name '*.ts' -exec grep -L 'export' {} \;

# Check test coverage for utility functions
npm test -- --testPathPattern=utilities --coverage --coverageThreshold='{"global":{"branches":80,"functions":80,"lines":80}}'

# Verify TypeScript strict mode compliance
tsc --noEmit --strict packages/payload/src/utilities/*.ts packages/ui/src/elements/*/utilities/*.ts
```

**Accept when:**
- All utility functions in packages/*/src/utilities/ have explicit TypeScript type definitions and pass strict type checking
- Unit test coverage for utility functions exceeds 80% for branches, functions, and lines
- All exported utility functions are documented in API reference with usage examples
- No utility function duplication exists across packages for the same logical operation

<enforcement>
Claude Code MUST NOT skip or defer verification. Automated CI pipeline checks for TypeScript strict mode compliance on utility modules are mandatory. Code coverage gates requiring 80%+ coverage for utility function tests must pass. Pull request reviews using utility function standards checklist are required. Static analysis tools (ESLint) enforcing naming conventions and export patterns must succeed. Violations result in CI build failure, blocked pull requests, and automated comments identifying non-compliant utilities.
</enforcement>