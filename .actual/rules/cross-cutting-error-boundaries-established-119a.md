# Adopt Event-Driven Integration Pattern with Process Isolation for Schema Generation and Script Execution: Error Boundaries Established

These rules are ALWAYS ACTIVE for all integration scenarios involving schema generation, script execution, and inter-process communication within the system, including all script execution involving user-provided code or dynamic evaluation, schema generation from runtime payloads or API responses, inter-process communication for initialization and configuration, integration points between core application and external modules, and test harnesses that execute code in isolated contexts.

### Rules

- **R-EDRV-001** MUST: Error boundaries MUST be established at integration points to prevent error propagation across component boundaries.

### Verify

```bash
# Check for process isolation patterns in code
grep -r "child_process\|worker_threads" --include="*.ts" --include="*.js" | grep -E "(spawn|fork|exec)" | wc -l

# Check for safe script execution patterns in tests
grep -r "safelyRunScript\|runInitSeparateProcess" test/ --include="*.ts" | wc -l

# Run integration tests for process isolation and schema generation
npm test -- --grep "(process isolation|schema generation|event-driven)" 2>&1 | grep -E "(passing|✓)"
```

**Accept when:**
- All script execution involving dynamic code uses process isolation mechanisms (child_process or worker_threads)
- Schema generation tests pass with 100% coverage for payload transformation scenarios
- Integration tests demonstrate proper error boundary handling across process boundaries
- No instances of unguarded eval() or Function() constructor usage in integration code
- Process lifecycle management includes timeout mechanisms (recommended: 30 seconds default)
- Event contracts are explicitly versioned with backward compatibility considerations
- Structured logging correlates events across process boundaries for debugging

<enforcement>
Claude Code MUST NOT skip or defer verification. All script execution involving dynamic code or untrusted payloads MUST use process isolation. Error boundaries MUST be established at all integration points. Violations block merge without documented exception approval from tech lead.
</enforcement>