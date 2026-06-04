# Standardize Core Library Module Imports and Organization: Core Library Dependencies

These rules are ALWAYS ACTIVE for all TypeScript and JavaScript files in packages/payload/src, all test files under test/ directory, all UI component files in packages/ui/src, workflow and queue management modules, and utility and helper modules across all packages.

### Rules

- **R-CORE-LIB-001** MUST: Core library dependencies MUST be declared in the appropriate package.json with explicit version constraints.

### Verify

```bash
# Check for import statements in scope
grep -r "^import" packages/payload/src test packages/ui/src | head -20

# Validate ESLint import/order rules
eslint --ext .ts,.tsx --rule 'import/order: error' packages/payload/src

# Count files without import statements
find packages/payload/src test -name '*.ts' -exec grep -L '^import.*from' {} \; | wc -l
```

**Accept when:**
- ESLint import/order rules pass for all files in scope without errors
- Code review checklist includes verification of import pattern compliance
- At least 95% of files in scope follow the standardized import organization pattern
- New pull requests automatically validate import patterns via CI pipeline

<enforcement>
Claude Code MUST NOT skip or defer verification of core library dependency declarations and import organization patterns.
</enforcement>