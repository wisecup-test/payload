# Adopt React Context Providers for Component State Management in UI Boundaries: Context Implementations Export

These rules are ALWAYS ACTIVE for all UI component development within the packages/ui module and all middleware boundary implementations requiring state management.

### Rules

- **R-CTX-001** MUST: Context implementations MUST export both the Provider component and a custom hook for consuming the context.

### Verify

```bash
# Count createContext usage in UI elements
grep -r "createContext" packages/ui/src/elements --include="*.tsx" | wc -l

# Find all Provider exports
grep -r "export.*Provider" packages/ui/src/elements --include="*.tsx" --include="*Provider.tsx"

# Find Context files with createContext
find packages/ui/src/elements -name "*Context.tsx" -o -name "*Provider.tsx" | xargs grep -l "createContext"
```

**Accept when:**
- All complex UI components with shared state have dedicated Context Provider implementations
- Each Context Provider exports both the Provider component and a custom consumption hook
- No instances of prop drilling beyond 2 levels for state that could be managed by Context
- Context implementations follow consistent naming and file organization patterns
- Exception EXC-001 applies: A component has only 1-2 props being passed to a single child component
- Exception EXC-002 applies: Performance profiling demonstrates Context re-renders are causing measurable performance degradation

<enforcement>
Clause Code MUST NOT skip or defer verification of Context Provider exports and custom hook presence during code review.
</enforcement>