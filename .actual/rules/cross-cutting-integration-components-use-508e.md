# Adopt Event-Driven Integration Pattern with Process Isolation for Schema Generation and Script Execution: Integration Components Use

These rules are ALWAYS ACTIVE for all integration scenarios involving schema generation, script execution, and inter-process communication within the system, including all script execution involving user-provided code or dynamic evaluation, schema generation from runtime payloads or API responses, inter-process communication for initialization and configuration, integration points between core application and external modules, and test harnesses that execute code in isolated contexts.

### Rules

- **R-INTEGRATION-001** MAY: Integration components MAY use message queues or event buses for asynchronous communication when immediate responses are not required.
- **R-INTEGRATION-002** MUST: All script execution involving dynamic code or user-provided code MUST use process isolation mechanisms (child_process or worker_threads).
- **R-INTEGRATION-003** MUST: All isolated script execution MUST implement timeout mechanisms (recommended: 30 seconds default, configurable per use case).
- **R-INTEGRATION-004** SHOULD: Event contracts SHOULD be designed with explicit versioning and backward compatibility considerations.
- **R-INTEGRATION-005** SHOULD: Structured logging SHOULD be used to correlate events across process boundaries for debugging.
- **R-INTEGRATION-006** SHOULD: Health checks and monitoring SHOULD be implemented for process lifecycle management.
- **R-INTEGRATION-007** MUST: No instances of unguarded eval() or Function() constructor usage are permitted in integration code.
- **R-INTEGRATION-008** SHOULD: Process pooling and reuse strategies SHOULD be considered to mitigate process spawning overhead.
- **R-INTEGRATION-009** MUST: Process lifecycle management MUST include robust cleanup with timeouts and automatic resource deallocation to prevent orphaned processes.
- **R-INTEGRATION-010** SHOULD: Schema generation from dynamic payloads SHOULD implement validation and versioning with deterministic generation algorithms.

### Verify

```bash
# Count process isolation patterns in use
grep -r "child_process\|worker_threads" --include="*.ts" --include="*.js" | grep -E "(spawn|fork|exec)" | wc -l

# Count isolated script execution test coverage
grep -r "safelyRunScript\|runInitSeparateProcess" test/ --include="*.ts" | wc -l

# Run integration and process isolation tests
npm test -- --grep "(process isolation|schema generation|event-driven)" 2>&1 | grep -E "(passing|✓)"

# Verify no unsafe eval patterns in integration code
grep -r "eval(\|new Function(" --include="*.ts" --include="*.js" | grep -v node_modules | wc -l
```

**Accept when:**
- All script execution involving dynamic code uses process isolation mechanisms (child_process or worker_threads)
- Schema generation tests pass with 100% coverage for payload transformation scenarios
- Integration tests demonstrate proper error boundary handling across process boundaries
- No instances of unguarded eval() or Function() constructor usage in integration code
- Process lifecycle management includes timeout mechanisms and automatic cleanup
- Event contracts are versioned and documented with backward compatibility considerations
- Structured logging correlates events across process boundaries

<enforcement>
Claude Code MUST NOT skip or defer verification. All rules in this file are mandatory for integration components. Violations detected by CI pipeline checks, static analysis tools, or integration test suite MUST block merge without documented exception approval from the architecture review board.
</enforcement>