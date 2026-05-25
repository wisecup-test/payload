# Standardize Service Boundary Configuration Through Typed Config Objects: Configuration Objects Defining

These rules are ALWAYS ACTIVE for all service modules, authentication services, queue workflows, UI components, relationship management services, and API endpoint definitions that define service boundaries.

### Rules

- **R-SBC-001** MUST: Configuration objects defining service boundaries MUST be co-located with their service implementation or in a dedicated config module within the service directory.

### Verify

```bash
# Count exported config objects in service-related files
grep -r "export.*config" --include="*.ts" --include="*.tsx" | grep -E "(auth|queue|relationship|component)" | wc -l

# Find config files with explicit type definitions
find . -name "config.ts" -o -name "*.config.ts" | xargs grep -l "interface.*Config\|type.*Config" | wc -l

# Run tests for config-based dependency injection
npm test -- --testPathPattern="(auth|queue|relationship)" --testNamePattern="config" 2>&1 | grep -E "(PASS|FAIL)"
```

**Accept when:**
- At least 80% of service modules contain explicit configuration objects with type definitions
- All new services and components include configuration objects that pass type checking and validation
- Test suites demonstrate configuration-based dependency injection for service boundaries
- Architecture documentation includes examples of configuration patterns from the codebase

<enforcement>
Claude Code MUST NOT skip or defer verification. Configuration objects must be validated at code review and CI pipeline stages before merge approval.
</enforcement>