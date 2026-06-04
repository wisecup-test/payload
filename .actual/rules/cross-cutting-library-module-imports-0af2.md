# Standardize Core Library Module Imports and Organization: Library Module Imports

These rules are ALWAYS ACTIVE for all TypeScript and JavaScript files in packages/payload/src, test files under test/ directory, UI component files in packages/ui/src, workflow and queue management modules, and utility and helper modules across all packages.

### Rules

- **R-LIB-001** SHOULD: Library module imports SHOULD be grouped by source (external libraries, internal packages, relative imports) with blank lines separating groups.

### Verify

```bash
# Check for import statements in scope
grep -r "^import" packages/payload/src test packages/ui/src | head -20

# Validate import ordering with ESLint
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
Claude Code MUST NOT skip or defer verification of import organization patterns. ESLint checks MUST pass in CI before merge.
</enforcement>