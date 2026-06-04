# Adopt React Context Providers for Component State Management in UI Boundaries: Context Providers Colocated

Status: proposed
Date: 2024-01-20
Deciders: Detection Pipeline (automated)

## Activation

This ADR is always active for all UI component development within the packages/ui module and applies to all middleware boundary implementations requiring state management.

## Context

- The UI package contains multiple complex interactive components (Drawer, ListDrawer, Hierarchy/Tree) that require coordinated state management across component boundaries
- React Context API provides a native mechanism for sharing state across component trees without prop drilling, establishing clear middleware boundaries
- Pattern detected in 3 files with 89.43% confidence, indicating consistent adoption of Context-based state management for UI element boundaries
- Components like TreeFocusContext, Drawer, and ListDrawer Provider demonstrate the need for isolated state management domains within the UI architecture
- The boundaries.middleware facet indicates these patterns serve as architectural boundaries between presentation and state management concerns

## Problem Statement

Complex UI components require coordinated state management across multiple child components and layers. Without a structured approach to state boundaries, components become tightly coupled through prop drilling, making them difficult to maintain, test, and reuse. The system needs a consistent pattern for establishing middleware boundaries that isolate state management concerns while maintaining component composability.

## Decision

1. SHOULD: Context Providers SHOULD be colocated with their related UI components in the same directory structure

## Policy Block

- SHOULD Context Providers SHOULD be colocated with their related UI components in the same directory structure

In scope:
- All UI components in packages/ui/src/elements requiring shared state management
- Interactive components with multiple child components (Drawer, ListDrawer, Tree, etc.)
- Components implementing focus management, selection state, or coordinated animations
- Middleware boundaries between presentation and state management layers

Out of scope:
- Simple presentational components with no shared state requirements
- Global application state managed by external state management libraries
- Server-side state management or API data fetching patterns
- Component-local state that doesn't cross component boundaries

Exceptions:
- EXC-001: A component has only 1-2 props being passed to a single child component
- EXC-002: Performance profiling demonstrates Context re-renders are causing measurable performance degradation

## Rationale

- Pattern detected with 89.43% confidence across 3 representative files (Drawer, ListDrawer Provider, TreeFocusContext), indicating established architectural practice
- React Context API provides native, type-safe middleware boundaries without external dependencies, reducing complexity and bundle size
- Context-based boundaries enable better separation of concerns between UI presentation and state management logic, improving testability
- The boundaries.middleware facet classification confirms this pattern serves as an architectural boundary layer within the internal structure

## Consequences

Positive:
- Eliminates prop drilling, making component trees cleaner and easier to refactor
- Establishes clear architectural boundaries between state management and presentation concerns
- Improves component reusability by decoupling state management from component hierarchy
- Provides type-safe state access through custom hooks with TypeScript integration
- Enables easier testing by allowing state to be mocked at the Provider boundary

Negative:
- Adds slight complexity for developers unfamiliar with React Context patterns
- Can cause unnecessary re-renders if context values are not properly memoized
- May be over-engineering for very simple components with minimal state sharing
- Requires additional boilerplate code for Provider setup and custom hooks

## Alternatives

- Use prop drilling to pass state through component hierarchies (rejected)
  Rejected because: Creates tight coupling between components, makes refactoring difficult, and leads to verbose component interfaces with many pass-through props
  When valid: Only valid for simple cases with 1-2 props passed to a single child component
- Adopt external state management library (Redux, MobX, Zustand) for all component state (rejected)
  Rejected because: Adds external dependencies and complexity for component-local state that doesn't need global management; React Context is sufficient for UI boundary state
  When valid: Valid for true global application state that spans multiple feature domains
- Use render props or higher-order components (HOCs) for state sharing (rejected)
  Rejected because: Creates wrapper hell and is less ergonomic than hooks-based Context consumption; considered legacy pattern in modern React
  When valid: May be valid for supporting legacy class components that cannot use hooks

## Risks

- Performance degradation from excessive re-renders when Context values change frequently
  Mitigation: Use React.memo, useMemo, and useCallback to optimize Context values; split contexts by update frequency; implement context selectors if needed
  Owner: UI Engineering Team
- Inconsistent Context patterns across different components leading to maintenance burden
  Mitigation: Establish and document standard Context Provider template; conduct code reviews to ensure consistency; create reusable Context factory utilities
  Owner: Architecture Team
- Developers may overuse Context for state that should remain component-local
  Mitigation: Provide clear guidelines on when to use Context vs local state; include decision tree in documentation; review Context additions during code review
  Owner: Engineering Team Leads

## Implementation Notes

- Create a standard template for Context Providers that includes TypeScript types, default values, custom hook, and error handling for missing Provider
- Name Context files consistently (e.g., ComponentNameContext.tsx or Provider.tsx within component directory)
- Export custom hooks with descriptive names (e.g., useDrawerState, useTreeFocus) rather than generic useContext calls
- Document the state shape and available actions in JSDoc comments on the custom hook
- Consider creating a Context factory utility function to reduce boilerplate for common patterns

## Continuation Context


Verify commands:
- grep -r "createContext" packages/ui/src/elements --include="*.tsx" | wc -l
- grep -r "export.*Provider" packages/ui/src/elements --include="*.tsx" --include="*Provider.tsx"
- find packages/ui/src/elements -name "*Context.tsx" -o -name "*Provider.tsx" | xargs grep -l "createContext"

Accept when:
- All complex UI components with shared state have dedicated Context Provider implementations
- Each Context Provider exports both the Provider component and a custom consumption hook
- No instances of prop drilling beyond 2 levels for state that could be managed by Context
- Context implementations follow consistent naming and file organization patterns

## Enforcement

- Verified by: Code review checklist includes verification of Context usage for components with shared state
- Verified by: Automated linting rules detect prop drilling patterns that should use Context
- Verified by: Architecture review for new UI components validates middleware boundary implementation
- Violation handling: Code review feedback requests refactoring to use Context Providers for identified prop drilling
- Violation handling: Pull requests with violations are marked as requiring changes before merge
- Violation handling: Existing violations are tracked as technical debt items for gradual refactoring
- Exception process: Developer documents rationale for exception in component file or PR description
- Exception process: Component maintainer or architecture team member reviews and approves exception
- Exception process: Exception is logged in architecture decision log with justification and review date