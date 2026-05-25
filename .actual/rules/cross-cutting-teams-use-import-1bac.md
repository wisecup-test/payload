# Standardize Core Library Module Imports and Organization: Teams Use Import

These rules are ALWAYS ACTIVE for all TypeScript and JavaScript files in packages/payload/src, all test files under test/ directory, all UI component files in packages/ui/src, workflow and queue management modules, and utility and helper modules across all packages.

### Rules

- **R-IMPORT-001** MAY: Teams MAY use import aliases for commonly used library modules to improve code readability, provided aliases are documented.

### Verify

```bash
# Check for import statements across scope
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
Claude Code MUST NOT skip or defer verification of import pattern compliance. All files in scope MUST be checked against ESLint rules before acceptance.
</enforcement>