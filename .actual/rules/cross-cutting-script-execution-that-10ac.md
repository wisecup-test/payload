# Adopt Event-Driven Integration Pattern with Process Isolation for Schema Generation and Script Execution: Script Execution That

These rules are ALWAYS ACTIVE for all integration scenarios involving schema generation, script execution, and inter-process communication within the system.

### Rules

- **R-SCRIPT-001** MUST: All script execution that involves untrusted or dynamic code MUST be isolated in separate processes to prevent failures from affecting the main application.

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
- Process lifecycle management includes timeout mechanisms (recommended: 30 seconds default, configurable per use case)
- Event contracts are explicitly versioned with backward compatibility considerations
- Structured logging correlates events across process boundaries for debugging

<enforcement>
Claude Code MUST NOT skip or defer verification. All script execution patterns must be validated against process isolation requirements before acceptance. Violations detected by CI pipeline checks, static analysis, or integration tests MUST block merge without documented exception approval.
</enforcement>