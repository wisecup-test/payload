# Adopt Message Queue Integration Pattern for Asynchronous Event Processing: After Change Hooks

These rules are ALWAYS ACTIVE for all UI components, plugin hooks, and cross-package integration code that handles asynchronous event processing, file uploads, drag-and-drop operations, and after-change side effects.

### Rules

- **R-MQ-001** MUST: After-change hooks and side effects MUST be triggered through message queue mechanisms rather than synchronous callbacks.
- **R-MQ-002** MUST: All UI components handling drag-and-drop operations (DraggableSortable), file uploads (Dropzone), and hierarchical tree updates MUST use message queue patterns for asynchronous communication.
- **R-MQ-003** MUST: Plugin hooks for data mutations (afterChange hooks) MUST implement message-based event handling.
- **R-MQ-004** MUST: No direct synchronous blocking calls SHALL exist between UI components and backend services for long-running operations.
- **R-MQ-005** SHOULD: Implement correlation IDs to track message flows across component boundaries for debugging and monitoring.
- **R-MQ-006** SHOULD: Define clear message schemas and contracts between producers and consumers to prevent integration issues.
- **R-MQ-007** SHOULD: Consider implementing circuit breakers for message queue connections to handle infrastructure failures gracefully.
- **R-MQ-008** MAY: Use established message queue libraries appropriate for the runtime environment (e.g., RabbitMQ, Redis Pub/Sub, or in-memory queues for development).

### Exceptions

- **EXC-001**: Critical error handling requires immediate synchronous feedback to prevent data loss.
- **EXC-002**: Performance profiling demonstrates that message queue overhead exceeds 100ms for high-frequency operations.

### Verify

```bash
# Check for message queue and event-driven patterns
grep -r "message.*queue\|event.*queue\|pub.*sub" packages/ui packages/plugin-cloud-storage --include='*.ts' --include='*.tsx'

# Check for async afterChange hooks
grep -r "afterChange.*async\|onChange.*queue" packages/plugin-cloud-storage/src/hooks --include='*.ts'

# Find async handlers in UI components
find packages/ui/src/elements -name '*.tsx' -exec grep -l 'async.*handler\|queue\|emit' {} \;
```

**Accept when:**
- All UI components that trigger backend operations use message queue patterns for asynchronous communication
- After-change hooks in plugin-cloud-storage implement message-based event handling
- No direct synchronous blocking calls exist between UI components and backend services for long-running operations
- Message queue infrastructure is documented with clear producer-consumer contracts
- UI responsiveness meets defined thresholds (no blocking operations exceeding 50ms on UI thread)

<enforcement>
Claude Code MUST NOT skip or defer verification. Automated code review checks for synchronous blocking patterns in UI components are mandatory. Architecture review of new integration points between packages is required. Performance testing to ensure UI responsiveness meets defined thresholds is mandatory. CI pipeline MUST fail if synchronous blocking patterns are detected in UI-to-backend integration code. Pull requests introducing direct synchronous calls across package boundaries require architecture review approval. Performance regression tests MUST flag operations that block UI thread for more than 50ms.
</enforcement>