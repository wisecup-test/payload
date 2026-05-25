# Adopt Event-Driven Integration Pattern with Process Isolation for Schema Generation and Script Execution: Generated Schemas Cached

These rules are ALWAYS ACTIVE for all integration scenarios involving schema generation, script execution, and inter-process communication within the system.

### Rules

- **R-SCHEMA-001** SHOULD: Generated schemas SHOULD be cached or memoized when the same payload structure is processed multiple times.

### Verify

```bash
# Check for process isolation patterns in code
grep -r "child_process\|worker_threads" --include="*.ts" --include="*.js" | grep -E "(spawn|fork|exec)" | wc -l

# Verify schema generation and process isolation test coverage
grep -r "safelyRunScript\|runInitSeparateProcess" test/ --include="*.ts" | wc -l

# Run integration tests for process isolation and schema generation
npm test -- --grep "(process isolation|schema generation|event-driven)" 2>&1 | grep -E "(passing|✓)"
```

**Accept when:**
- All script execution involving dynamic code uses process isolation mechanisms (child_process or worker_threads)
- Schema generation tests pass with 100% coverage for payload transformation scenarios
- Integration tests demonstrate proper error boundary handling across process boundaries
- No instances of unguarded eval() or Function() constructor usage in integration code
- Generated schemas are cached or memoized to avoid redundant generation for identical payload structures

<enforcement>
Claude Code MUST NOT skip or defer verification. All rules in this file are mandatory for code review and CI pipeline validation.
</enforcement>