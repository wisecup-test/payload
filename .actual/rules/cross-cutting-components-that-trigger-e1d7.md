# Adopt Message Queue Integration Pattern for Asynchronous Event Processing: Components That Trigger

These rules are ALWAYS ACTIVE for all UI components handling drag-and-drop operations, file upload components, plugin hooks for data mutations, hierarchical tree components, and cross-package integration boundaries between ui and plugin-cloud-storage packages.

### Rules

- **R-MQ-001** MUST: Components that trigger asynchronous operations (file uploads, drag-and-drop, data mutations) MUST use message queue patterns to decouple event producers from consumers.

### Verify

```bash
# Check for message queue or event queue patterns in UI and plugin packages
grep -r "message.*queue\|event.*queue\|pub.*sub" packages/ui packages/plugin-cloud-storage --include='*.ts' --include='*.tsx'

# Check for async afterChange hooks in plugin-cloud-storage
grep -r "afterChange.*async\|onChange.*queue" packages/plugin-cloud-storage/src/hooks --include='*.ts'

# Find UI components with async handlers, queue usage, or event emission
find packages/ui/src/elements -name '*.tsx' -exec grep -l 'async.*handler\|queue\|emit' {} \;
```

**Accept when:**
- All UI components that trigger backend operations use message queue patterns for asynchronous communication
- After-change hooks in plugin-cloud-storage implement message-based event handling
- No direct synchronous blocking calls exist between UI components and backend services for long-running operations
- Message queue infrastructure is documented with clear producer-consumer contracts

<enforcement>
Claude Code MUST NOT skip or defer verification. Violations are caught by CI pipeline checks for synchronous blocking patterns, architecture review of new integration points, and performance regression tests flagging operations blocking the UI thread for more than 50ms.
</enforcement>