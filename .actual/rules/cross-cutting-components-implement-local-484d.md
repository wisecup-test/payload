# Adopt Message Queue Integration Pattern for Asynchronous Event Processing: Components Implement Local

These rules are ALWAYS ACTIVE for all files in the UI components layer, plugin-cloud-storage package, and cross-package integration boundaries where asynchronous event processing and component communication occur.

### Rules

- **R-MQ-001** MAY: Components MAY implement local event queues for managing internal state transitions before publishing to shared message queues.
- **R-MQ-002** MUST: All UI components that trigger backend operations (file uploads, drag-and-drop, hierarchical tree updates) use message queue patterns for asynchronous communication.
- **R-MQ-003** MUST: After-change hooks in plugin-cloud-storage implement message-based event handling rather than direct synchronous calls.
- **R-MQ-004** MUST NOT: Direct synchronous blocking calls exist between UI components and backend services for long-running operations.
- **R-MQ-005** SHOULD: Implement correlation IDs to track message flows across component boundaries for debugging and monitoring.
- **R-MQ-006** SHOULD: Define clear message schemas and contracts between producers and consumers to prevent integration issues.
- **R-MQ-007** SHOULD: Consider implementing circuit breakers for message queue connections to handle infrastructure failures gracefully.
- **R-MQ-008** MUST: Message queue infrastructure is documented with clear producer-consumer contracts.

### Verify

```bash
# Search for message queue and event queue patterns
grep -r "message.*queue\|event.*queue\|pub.*sub" packages/ui packages/plugin-cloud-storage --include='*.ts' --include='*.tsx'

# Check for async afterChange hooks
grep -r "afterChange.*async\|onChange.*queue" packages/plugin-cloud-storage/src/hooks --include='*.ts'

# Find async handlers in UI elements
find packages/ui/src/elements -name '*.tsx' -exec grep -l 'async.*handler\|queue\|emit' {} \;

# Detect synchronous blocking patterns in UI-to-backend integration
grep -r "await.*fetch\|await.*api" packages/ui/src --include='*.tsx' | grep -v "queue\|message\|event"
```

**Accept when:**
- All UI components that trigger backend operations use message queue patterns for asynchronous communication
- After-change hooks in plugin-cloud-storage implement message-based event handling
- No direct synchronous blocking calls exist between UI components and backend services for long-running operations
- Message queue infrastructure is documented with clear producer-consumer contracts
- Performance testing confirms UI responsiveness meets defined thresholds (no blocking operations exceed 50ms)
- Correlation IDs are implemented for message tracking across component boundaries

<enforcement>
Claude Code MUST NOT skip or defer verification. All verification commands MUST execute successfully before accepting changes to UI components, plugin-cloud-storage integration points, or cross-package communication patterns. CI pipeline MUST fail if synchronous blocking patterns are detected. Pull requests introducing direct synchronous calls across package boundaries MUST require architecture review approval.
</enforcement>