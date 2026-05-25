# Adopt Event-Driven Integration Pattern with Process Isolation for Schema Generation and Script Execution: Schema Generation Runtime

These rules are ALWAYS ACTIVE for all integration scenarios involving schema generation, script execution, and inter-process communication within the system, including all script execution involving user-provided code or dynamic evaluation, schema generation from runtime payloads or API responses, inter-process communication for initialization and configuration, integration points between core application and external modules, and test harnesses that execute code in isolated contexts.

### Rules

- **R-SCHEMA-001** MUST: Schema generation from runtime payloads MUST produce deterministic, type-safe structures that can be validated against expected formats.
- **R-SCHEMA-002** MUST: All script execution involving dynamic code MUST use process isolation mechanisms (child_process or worker_threads).
- **R-SCHEMA-003** MUST: Implement timeout mechanisms for all isolated script execution (recommended: 30 seconds default, configurable per use case).
- **R-SCHEMA-004** MUST: Design event contracts with explicit versioning and backward compatibility considerations.
- **R-SCHEMA-005** SHOULD: Use Node.js child_process for untrusted code execution over worker_threads.
- **R-SCHEMA-006** SHOULD: Implement process pooling and reuse strategies to mitigate spawning overhead.
- **R-SCHEMA-007** SHOULD: Use structured logging to correlate events across process boundaries for debugging.
- **R-SCHEMA-008** SHOULD: Implement health checks and monitoring for process lifecycle management.
- **R-SCHEMA-009** SHOULD: Consider using libraries like 'execa' for enhanced process management capabilities.
- **R-SCHEMA-010** MUST: No instances of unguarded eval() or Function() constructor usage in integration code.

### Verify

```bash
# Check for process isolation patterns in code
grep -r "child_process\|worker_threads" --include="*.ts" --include="*.js" | grep -E "(spawn|fork|exec)" | wc -l

# Check for schema generation and process isolation test coverage
grep -r "safelyRunScript\|runInitSeparateProcess" test/ --include="*.ts" | wc -l

# Run integration and schema generation tests
npm test -- --grep "(process isolation|schema generation|event-driven)" 2>&1 | grep -E "(passing|✓)"

# Verify no unsafe eval patterns
grep -r "eval(\|Function(" --include="*.ts" --include="*.js" | grep -v "node_modules" | wc -l
```

**Accept when:**
- All script execution involving dynamic code uses process isolation mechanisms (child_process or worker_threads)
- Schema generation tests pass with 100% coverage for payload transformation scenarios
- Integration tests demonstrate proper error boundary handling across process boundaries
- No instances of unguarded eval() or Function() constructor usage in integration code
- Process lifecycle management includes timeouts, health checks, and automatic cleanup
- Event contracts are versioned and documented with backward compatibility considerations

<enforcement>
Claude Code MUST NOT skip or defer verification. All rules in this file are mandatory for code review and CI pipeline validation. Violations must be addressed before merge, or documented exceptions must be approved by the architecture review board.
</enforcement>