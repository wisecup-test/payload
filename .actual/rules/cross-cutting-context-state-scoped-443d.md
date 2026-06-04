# Adopt React Context Providers for Component State Management in UI Boundaries: Context State Scoped

These rules are ALWAYS ACTIVE for all UI component development within the packages/ui module and all middleware boundary implementations requiring state management.

### Rules

- **R-CONTEXT-001** SHOULD: Context state SHOULD be scoped to the smallest component subtree that requires access to avoid unnecessary re-renders.

### Verify

```bash
# Count createContext usage in UI elements
grep -r "createContext" packages/ui/src/elements --include="*.tsx" | wc -l

# Find all Provider exports in UI elements
grep -r "export.*Provider" packages/ui/src/elements --include="*.tsx" --include="*Provider.tsx"

# Find Context files with createContext definitions
find packages/ui/src/elements -name "*Context.tsx" -o -name "*Provider.tsx" | xargs grep -l "createContext"
```

**Accept when:**
- All complex UI components with shared state have dedicated Context Provider implementations
- Each Context Provider exports both the Provider component and a custom consumption hook
- No instances of prop drilling beyond 2 levels for state that could be managed by Context
- Context implementations follow consistent naming and file organization patterns (ComponentNameContext.tsx or Provider.tsx within component directory)
- Custom hooks are exported with descriptive names (e.g., useDrawerState, useTreeFocus) rather than generic useContext calls
- Context state values are properly memoized to prevent unnecessary re-renders

<enforcement>
Claude Code MUST NOT skip or defer verification of Context Provider implementations. Code review checklist MUST include verification of Context usage for components with shared state. Automated linting rules MUST detect prop drilling patterns that should use Context. Architecture review MUST validate middleware boundary implementation for new UI components.
</enforcement>