# Adopt Event-Driven Integration Pattern with Process Isolation for Schema Generation and Script Execution: Process Isolation Mechanisms

These rules are ALWAYS ACTIVE for all script execution involving user-provided code or dynamic evaluation, schema generation from runtime payloads or API responses, inter-process communication for initialization and configuration, integration points between core application and external modules, and test harnesses that execute code in isolated contexts.

### Rules

- **R-PROC-001** SHOULD: Process isolation mechanisms SHOULD include timeout handling, resource limits, and graceful termination capabilities.

### Verify

```bash
# Check for process isolation patterns in code
grep -r "child_process\|worker_threads" --include="*.ts" --include="*.js" | grep -E "(spawn|fork|exec)" | wc -l

# Verify process isolation test coverage
grep -r "safelyRunScript\|runInitSeparateProcess" test/ --include="*.ts" | wc -l

# Run integration and process isolation tests
npm test -- --grep "(process isolation|schema generation|event-driven)" 2>&1 | grep -E "(passing|✓)"
```

**Accept when:**
- All script execution involving dynamic code uses process isolation mechanisms (child_process or worker_threads)
- Schema generation tests pass with 100% coverage for payload transformation scenarios
- Integration tests demonstrate proper error boundary handling across process boundaries
- No instances of unguarded eval() or Function() constructor usage in integration code
- Timeout mechanisms are implemented for all isolated script execution (recommended: 30 seconds default, configurable per use case)
- Event contracts include explicit versioning and backward compatibility considerations
- Process lifecycle management includes health checks and automatic cleanup

<enforcement>
Claude Code MUST NOT skip or defer verification. All script execution patterns must be validated against process isolation requirements before approval.
</enforcement>