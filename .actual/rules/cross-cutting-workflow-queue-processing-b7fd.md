# Adopt Configuration-Driven Data Access with Inline Processing Pattern: Workflow Queue Processing

These rules are ALWAYS ACTIVE for all workflow and queue processing systems, field traversal and data transformation utilities, authentication strategy implementations, relationship and data access layer operations, and task execution engines with retry and backoff requirements.

### Rules

- **R-30-001** SHOULD: Workflow and queue processing SHOULD support queue-specific concurrency configuration to enable fine-grained resource control.

### Verify

```bash
# Verify configuration-driven retry mechanisms in workflow definitions
grep -r "retries.*:" test/queues/workflows/ | grep -v "undefined" | wc -l

# Verify configuration objects in field traversal utilities
grep -r "config.*:.*{" packages/payload/src/utilities/ | wc -l

# Verify traverseFields accepts configuration parameters
find . -name "*.ts" -exec grep -l "traverseFields.*config" {} \; | wc -l
```

**Accept when:**
- Configuration-driven retry mechanisms are present in workflow definitions with explicit retry counts or backoff strategies
- Field traversal utilities accept configuration objects as parameters rather than using hard-coded behavior
- At least 15 files demonstrate the configuration-driven pattern across different subsystems (queues, auth, relationships, utilities)

<enforcement>
Claude Code MUST NOT skip or defer verification. Automated code review checks MUST scan for hard-coded retry logic or concurrency limits in data access code. CI pipeline validation MUST ensure configuration schemas are defined and validated for new data access components. Architecture review MUST verify alignment with configuration-driven approach for new data access patterns.
</enforcement>