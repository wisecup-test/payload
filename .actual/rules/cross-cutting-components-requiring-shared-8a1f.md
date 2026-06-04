# Adopt React Context Providers for Component State Management in UI Boundaries: Components Requiring Shared

These rules are ALWAYS ACTIVE for all UI component development within the packages/ui module and all middleware boundary implementations requiring state management.

### Rules

- **R-CONTEXT-001** MUST: UI components requiring shared state across multiple child components MUST use React Context Providers to establish middleware boundaries.

### Verify

```bash
# Count createContext usage in UI elements
grep -r "createContext" packages/ui/src/elements --include="*.tsx" | wc -l

# Find all exported Providers
grep -r "export.*Provider" packages/ui/src/elements --include="*.tsx" --include="*Provider.tsx"

# Find Context files with createContext
find packages/ui/src/elements -name "*Context.tsx" -o -name "*Provider.tsx" | xargs grep -l "createContext"
```

**Accept when:**
- All complex UI components with shared state have dedicated Context Provider implementations
- Each Context Provider exports both the Provider component and a custom consumption hook
- No instances of prop drilling beyond 2 levels for state that could be managed by Context
- Context implementations follow consistent naming and file organization patterns (ComponentNameContext.tsx or Provider.tsx within component directory)
- Custom hooks are exported with descriptive names (e.g., useDrawerState, useTreeFocus) rather than generic useContext calls
- Context values are properly memoized to prevent unnecessary re-renders

<enforcement>
Claude Code MUST NOT skip or defer verification of Context Provider implementations. All complex UI components with shared state must be reviewed for proper Context usage during code review and architecture validation.
</enforcement>