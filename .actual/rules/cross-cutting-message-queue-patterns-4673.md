# Adopt Message Queue Integration Pattern for Asynchronous Event Processing: Message Queue Patterns

These rules are ALWAYS ACTIVE for all files in the UI layer, plugin-cloud-storage package, and cross-package integration boundaries where asynchronous event processing and component communication occur.

### Rules

- **R-MQ-001** SHOULD: Message queue patterns SHOULD be used for cross-package communication between ui and plugin layers.
- **R-MQ-002** MUST: UI components handling drag-and-drop operations (DraggableSortable) MUST use message queue patterns for state synchronization.
- **R-MQ-003** MUST: File upload components (Dropzone) MUST use message queue patterns for asynchronous upload processing.
- **R-MQ-004** MUST: Plugin hooks for data mutations (afterChange hooks) MUST implement message-based event handling.
- **R-MQ-005** MUST: Hierarchical tree components with server-side rendering MUST use message queues for cross-boundary updates.
- **R-MQ-006** MUST NOT: Direct synchronous blocking calls MUST NOT exist between UI components and backend services for long-running operations.
- **R-MQ-007** SHOULD: Correlation IDs SHOULD be implemented to track message flows across component boundaries for debugging and monitoring.
- **R-MQ-008** SHOULD: Message schemas and contracts SHOULD be defined between producers and consumers to prevent integration issues.
- **R-MQ-009** MAY: Circuit breakers MAY be implemented for message queue connections to handle infrastructure failures gracefully.
- **R-MQ-EXC-001** Exception: Critical error handling MAY require immediate synchronous feedback to prevent data loss (requires documentation).
- **R-MQ-EXC-002** Exception: High-frequency operations MAY bypass message queues if performance profiling demonstrates queue overhead exceeds 100ms (requires documentation).

### Verify

```bash
# Search for message queue and event-driven patterns
grep -r "message.*queue\|event.*queue\|pub.*sub" packages/ui packages/plugin-cloud-storage --include='*.ts' --include='*.tsx'

# Verify afterChange hooks use async/queue patterns
grep -r "afterChange.*async\|onChange.*queue" packages/plugin-cloud-storage/src/hooks --include='*.ts'

# Find async handlers in UI elements
find packages/ui/src/elements -name '*.tsx' -exec grep -l 'async.*handler\|queue\|emit' {} \;

# Check for synchronous blocking patterns that violate the rule
grep -r "await.*fetch\|await.*api" packages/ui/src/elements --include='*.tsx' | grep -v "queue\|message\|event"
```

**Accept when:**
- All UI components that trigger backend operations use message queue patterns for asynchronous communication
- After-change hooks in plugin-cloud-storage implement message-based event handling
- No direct synchronous blocking calls exist between UI components and backend services for long-running operations
- Message queue infrastructure is documented with clear producer-consumer contracts
- Correlation IDs are present in message queue implementations for traceability
- Performance testing confirms UI responsiveness meets defined thresholds (no blocking > 50ms)
- All exceptions are documented with justification and performance data

<enforcement>
Claude Code MUST NOT skip or defer verification. All R-MQ rules MUST be checked during code review. Violations in R-MQ-006 (synchronous blocking calls) are critical and MUST trigger CI pipeline failure. Pull requests introducing direct synchronous calls across package boundaries MUST require architecture review approval. Performance regression tests MUST flag operations blocking the UI thread for more than 50ms.
</enforcement>