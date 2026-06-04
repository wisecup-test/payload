# Standardize Core Library Module Imports and Organization: Core Library Modules

These rules are ALWAYS ACTIVE for all TypeScript and JavaScript files in packages/payload/src, test files under test/ directory, UI component files in packages/ui/src, workflow and queue management modules, and utility and helper modules across all packages.

### Rules

- **R-CORE-LIB-001** MUST: All core library modules MUST be imported using the established pattern detected across the codebase (signature: 352185f1ce9ffb39c86c964ce8959ec4).

### Verify

```bash
# Show first 20 import statements to verify pattern compliance
grep -r "^import" packages/payload/src test packages/ui/src | head -20

# Run ESLint import/order rules to validate import organization
eslint --ext .ts,.tsx --rule 'import/order: error' packages/payload/src

# Count files without import statements (should be minimal)
find packages/payload/src test -name '*.ts' -exec grep -L '^import.*from' {} \; | wc -l
```

**Accept when:**
- ESLint import/order rules pass for all files in scope without errors
- Code review checklist includes verification of import pattern compliance
- At least 95% of files in scope follow the standardized import organization pattern
- New pull requests automatically validate import patterns via CI pipeline

<enforcement>
Claude Code MUST NOT skip or defer verification. Import pattern compliance is mandatory and must be validated before accepting changes.
</enforcement>