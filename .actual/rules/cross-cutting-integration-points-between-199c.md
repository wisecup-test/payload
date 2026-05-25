# Adopt Message Queue Integration Pattern for Asynchronous Event Processing: Integration Points Between

These rules are ALWAYS ACTIVE for all UI components, plugin hooks, and cross-package integration boundaries that handle asynchronous event processing, file uploads, drag-and-drop operations, and backend service communication.

### Rules

- **R-MQ-001** MUST: Integration points between UI components and backend services MUST implement message-based communication to prevent blocking operations.

### Verify

```bash
# Check for message queue and event-driven patterns
grep -r "message.*queue\|event.*queue\|pub.*sub" packages/ui packages/plugin-cloud-storage --include='*.ts' --include='*.tsx'

# Check for async afterChange hooks
grep -r "afterChange.*async\|onChange.*queue" packages/plugin-cloud-storage/src/hooks --include='*.ts'

# Find async handlers in UI elements
find packages/ui/src/elements -name '*.tsx' -exec grep -l 'async.*handler\|queue\|emit' {} \;

# Detect synchronous blocking patterns in UI-to-backend calls
grep -r "await.*fetch\|await.*axios" packages/ui/src --include='*.tsx' | grep -v "queue\|message\|event"
```

**Accept when:**
- All UI components that trigger backend operations use message queue patterns for asynchronous communication
- After-change hooks in plugin-cloud-storage implement message-based event handling
- No direct synchronous blocking calls exist between UI components and backend services for long-running operations
- Message queue infrastructure is documented with clear producer-consumer contracts
- DraggableSortable, Dropzone, and hierarchical tree components use async message patterns
- Cross-package integration boundaries between ui and plugin-cloud-storage use message-based communication

<enforcement>
Claude Code MUST NOT skip or defer verification. All integration points crossing package boundaries MUST be reviewed for compliance with R-MQ-001. CI pipeline MUST fail if synchronous blocking patterns are detected in UI-to-backend integration code. Performance regression tests MUST flag operations that block UI thread for more than 50ms.
</enforcement>