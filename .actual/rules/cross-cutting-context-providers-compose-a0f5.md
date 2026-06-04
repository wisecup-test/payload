# Adopt React Context Providers for Component State Management in UI Boundaries: Context Providers Compose

These rules are ALWAYS ACTIVE for all UI component development within the packages/ui module and all middleware boundary implementations requiring state management.

### Rules

- **R-CTX-001** MAY: Context Providers MAY compose multiple contexts when managing orthogonal concerns within the same component boundary.
- **R-CTX-002** MUST: All complex UI components in packages/ui/src/elements with shared state across multiple child components MUST implement dedicated Context Provider implementations.
- **R-CTX-003** MUST: Each Context Provider MUST export both the Provider component and a custom consumption hook with a descriptive name (e.g., useDrawerState, useTreeFocus).
- **R-CTX-004** SHOULD: Context files SHOULD be named consistently (e.g., ComponentNameContext.tsx or Provider.tsx within component directory).
- **R-CTX-005** SHOULD: Context implementations SHOULD use React.memo, useMemo, and useCallback to optimize Context values and prevent unnecessary re-renders.
- **R-CTX-006** SHOULD: State shape and available actions SHOULD be documented in JSDoc comments on the custom hook.
- **R-CTX-007** MUST NOT: Prop drilling beyond 2 levels for state that could be managed by Context MUST NOT be used.

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
- Context implementations follow consistent naming and file organization patterns
- Context values are properly memoized to prevent unnecessary re-renders
- Custom hooks are exported with descriptive names rather than generic useContext calls

<enforcement>
Claude Code MUST NOT skip or defer verification of these rules during code review. All complex UI components with shared state MUST implement Context Providers following the patterns defined in R-CTX-002 through R-CTX-007. Violations identified during review MUST be addressed before merge, or documented as exceptions with architectural team approval.
</enforcement>