# Adopt Message Queue Integration Pattern for Asynchronous Event Processing: Message Queue Implementations

These rules are ALWAYS ACTIVE for all UI components, plugin hooks, and cross-package integration boundaries that handle asynchronous event processing, file uploads, drag-and-drop operations, and backend service communication.

### Rules

- **R-MQ-001** SHOULD: Message queue implementations SHOULD support event ordering guarantees for operations that require sequential processing (e.g., drag-and-drop reordering).

### Verify

```bash
# Check for message queue and event-driven patterns
grep -r "message.*queue\|event.*queue\|pub.*sub" packages/ui packages/plugin-cloud-storage --include='*.ts' --include='*.tsx'

# Check for async hooks in plugin-cloud-storage
grep -r "afterChange.*async\|onChange.*queue" packages/plugin-cloud-storage/src/hooks --include='*.ts'

# Find async handlers and queue usage in UI elements
find packages/ui/src/elements -name '*.tsx' -exec grep -l 'async.*handler\|queue\|emit' {} \;
```

**Accept when:**
- All UI components that trigger backend operations use message queue patterns for asynchronous communication
- After-change hooks in plugin-cloud-storage implement message-based event handling
- No direct synchronous blocking calls exist between UI components and backend services for long-running operations
- Message queue infrastructure is documented with clear producer-consumer contracts
- Event ordering is preserved for sequential operations like drag-and-drop reordering
- Correlation IDs are implemented for message tracking across component boundaries

<enforcement>
Claude Code MUST NOT skip or defer verification. All UI-to-backend integration points MUST be reviewed for compliance with message queue patterns. Performance regression tests MUST flag operations blocking the UI thread for more than 50ms. CI pipeline MUST fail if synchronous blocking patterns are detected in cross-package integration code.
</enforcement>