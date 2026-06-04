# Adopt Message Queue Integration Pattern for Asynchronous Event Processing: Components Not Directly

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Context

- The system requires asynchronous processing capabilities to handle UI interactions (drag-and-drop, file uploads) and backend operations (cloud storage synchronization) without blocking user experience
- Multiple components across UI and plugin layers need to communicate state changes and trigger side effects in a decoupled manner
- The architecture spans multiple packages (ui, plugin-cloud-storage) requiring a consistent integration pattern for cross-boundary communication
- Event-driven operations such as file uploads, drag-and-drop reordering, and hierarchical tree updates require reliable message passing between components
- The facet 'boundaries.message_queues' indicates a deliberate architectural choice to use message queues for managing integration boundaries

## Problem Statement

How should the system handle asynchronous event processing and cross-component communication in a way that maintains loose coupling, supports UI responsiveness, and enables reliable integration between frontend components and backend services across package boundaries?

## Decision

1. MUST_NOT: UI components MUST NOT directly invoke synchronous backend operations that could block rendering or user interactions

## Policy Block

- MUST_NOT UI components MUST NOT directly invoke synchronous backend operations that could block rendering or user interactions

In scope:
- UI components handling drag-and-drop operations (DraggableSortable)
- File upload components (Dropzone)
- Plugin hooks for data mutations (afterChange hooks)
- Hierarchical tree components with server-side rendering
- Cross-package integration boundaries between ui and plugin-cloud-storage

Out of scope:
- Synchronous form validation logic
- Direct database queries within the same service boundary
- Simple prop-based parent-child component communication
- Pure computational functions without side effects

Exceptions:
- EXC-001: Critical error handling requires immediate synchronous feedback to prevent data loss
- EXC-002: Performance profiling demonstrates that message queue overhead exceeds 100ms for high-frequency operations

## Rationale

- Pattern detected across 4 files with 90.08% confidence, indicating consistent architectural approach to integration
- Message queue pattern enables UI responsiveness by preventing blocking operations during file uploads, drag-and-drop, and cloud storage synchronization
- Decoupling through message queues allows independent evolution of UI components and backend plugins without tight coupling
- The boundaries.message_queues facet explicitly signals this as an intentional architectural boundary mechanism

## Consequences

Positive:
- Improved UI responsiveness as user interactions are not blocked by backend processing
- Better separation of concerns between UI components and backend services
- Enhanced testability through ability to mock message queue interactions
- Increased system resilience through asynchronous error handling and retry mechanisms
- Simplified scaling as message queues can be distributed across multiple workers

Negative:
- Increased complexity in debugging as event flow is asynchronous and distributed
- Potential for message ordering issues if not properly managed
- Additional infrastructure overhead for maintaining message queue systems
- Learning curve for developers unfamiliar with event-driven architectures

## Alternatives

- Direct synchronous API calls from UI components to backend services (rejected)
  Rejected because: Would block UI rendering during long-running operations like file uploads and cloud storage synchronization, degrading user experience
  When valid: Only appropriate for simple CRUD operations with sub-100ms response times
- Promise-based async/await patterns without message queues (rejected)
  Rejected because: Tightly couples UI components to backend implementation details and does not provide the same level of decoupling for cross-package boundaries
  When valid: Acceptable for internal component state management that does not cross package boundaries
- WebSocket-based real-time bidirectional communication (deferred)
  Rejected because: May be complementary for real-time updates but does not replace the need for message queues for asynchronous processing
  When valid: Could be used in conjunction with message queues for pushing status updates back to UI

## Risks

- Message queue failures could result in lost events or incomplete operations
  Mitigation: Implement dead letter queues, message persistence, and retry mechanisms with exponential backoff
  Owner: Engineering team
- Debugging asynchronous message flows may be challenging without proper observability
  Mitigation: Implement distributed tracing, correlation IDs for message tracking, and comprehensive logging at queue boundaries
  Owner: Engineering team
- Message queue overhead may impact performance for high-frequency operations
  Mitigation: Establish performance benchmarks, implement batching for high-volume operations, and monitor queue latency metrics
  Owner: Engineering team

## Implementation Notes

- Use established message queue libraries appropriate for the runtime environment (e.g., RabbitMQ, Redis Pub/Sub, or in-memory queues for development)
- Implement correlation IDs to track message flows across component boundaries for debugging and monitoring
- Define clear message schemas and contracts between producers and consumers to prevent integration issues
- Consider implementing circuit breakers for message queue connections to handle infrastructure failures gracefully
- Document message queue topology and event flows in architecture diagrams for team reference

## Continuation Context


Verify commands:
- grep -r "message.*queue\|event.*queue\|pub.*sub" packages/ui packages/plugin-cloud-storage --include='*.ts' --include='*.tsx'
- grep -r "afterChange.*async\|onChange.*queue" packages/plugin-cloud-storage/src/hooks --include='*.ts'
- find packages/ui/src/elements -name '*.tsx' -exec grep -l 'async.*handler\|queue\|emit' {} \;

Accept when:
- All UI components that trigger backend operations use message queue patterns for asynchronous communication
- After-change hooks in plugin-cloud-storage implement message-based event handling
- No direct synchronous blocking calls exist between UI components and backend services for long-running operations
- Message queue infrastructure is documented with clear producer-consumer contracts

## Enforcement

- Verified by: Automated code review checks for synchronous blocking patterns in UI components
- Verified by: Architecture review of new integration points between packages
- Verified by: Performance testing to ensure UI responsiveness meets defined thresholds
- Violation handling: CI pipeline fails if synchronous blocking patterns are detected in UI-to-backend integration code
- Violation handling: Pull requests introducing direct synchronous calls across package boundaries require architecture review approval
- Violation handling: Performance regression tests flag operations that block UI thread for more than 50ms
- Exception process: Submit exception request to architecture review board with justification and performance data
- Exception process: Document approved exceptions in ADR amendments with specific scope and time-bound validity
- Exception process: Re-evaluate exceptions quarterly to determine if architectural improvements can eliminate the need