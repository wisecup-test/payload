# Adopt Message Queue Integration Pattern for Asynchronous Event Processing: Components Not Directly

These rules are ALWAYS ACTIVE for all UI components, plugin hooks, and cross-package integration boundaries that handle asynchronous event processing, file uploads, drag-and-drop operations, and backend service communication.

### Rules

- **R-MQ-001** MUST NOT: UI components MUST NOT directly invoke synchronous backend operations that could block rendering or user interactions.
- **R-MQ-002** MUST: All UI components handling drag-and-drop operations (DraggableSortable) MUST use message queue patterns for asynchronous communication with backend services.
- **R-MQ-003** MUST: File upload components (Dropzone) MUST implement message-based event handling for asynchronous processing.
- **R-MQ-004** MUST: Plugin hooks for data mutations (afterChange hooks) MUST implement message-based event handling rather than direct synchronous invocation.
- **R-MQ-005** MUST: Cross-package integration boundaries between ui and plugin-cloud-storage MUST use message queue patterns for communication.
- **R-MQ-006** SHOULD: Implement correlation IDs to track message flows across component boundaries for debugging and monitoring.
- **R-MQ-007** SHOULD: Define clear message schemas and contracts between producers and consumers to prevent integration issues.
- **R-MQ-008** MAY: Use established message queue libraries appropriate for the runtime environment (e.g., RabbitMQ, Redis Pub/Sub, or in-memory queues for development).
- **R-MQ-EXC-001** Exception: Critical error handling requires immediate synchronous feedback to prevent data loss (time-bound and requires documentation).
- **R-MQ-EXC-002** Exception: Performance profiling demonstrates that message queue overhead exceeds 100ms for high-frequency operations (requires approval and re-evaluation).

### Verify

```bash
# Search for message queue and event-driven patterns
grep -r "message.*queue\|event.*queue\|pub.*sub" packages/ui packages/plugin-cloud-storage --include='*.ts' --include='*.tsx'

# Verify afterChange hooks use async/queue patterns
grep -r "afterChange.*async\|onChange.*queue" packages/plugin-cloud-storage/src/hooks --include='*.ts'

# Find async handlers in UI elements
find packages/ui/src/elements -name '*.tsx' -exec grep -l 'async.*handler\|queue\|emit' {} \;

# Check for direct synchronous blocking calls between UI and backend
grep -r "await.*fetch\|axios\.get\|axios\.post" packages/ui/src --include='*.tsx' | grep -v "queue\|message\|event"
```

**Accept when:**
- All UI components that trigger backend operations use message queue patterns for asynchronous communication
- After-change hooks in plugin-cloud-storage implement message-based event handling
- No direct synchronous blocking calls exist between UI components and backend services for long-running operations
- Message queue infrastructure is documented with clear producer-consumer contracts
- Drag-and-drop components (DraggableSortable) emit events through message queues rather than direct callbacks
- File upload components (Dropzone) process uploads asynchronously through message-based handlers
- Hierarchical tree components with server-side rendering use message queues for state synchronization
- Performance testing confirms UI responsiveness meets defined thresholds (no blocking operations exceeding 50ms)

<enforcement>
Claude Code MUST NOT skip or defer verification. All UI-to-backend integration points MUST be reviewed for compliance with message queue patterns. CI pipeline MUST fail if synchronous blocking patterns are detected. Pull requests introducing direct synchronous calls across package boundaries MUST require architecture review approval.
</enforcement>