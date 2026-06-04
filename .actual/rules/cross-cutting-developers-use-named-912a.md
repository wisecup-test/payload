# Standardize Core Library Module Imports and Organization: Developers Use Named

These rules are ALWAYS ACTIVE for all TypeScript and JavaScript files in packages/payload/src, test files under test/ directory, UI component files in packages/ui/src, workflow and queue management modules, and utility and helper modules across all packages.

### Rules

- **R-IMPORT-001** SHOULD: Developers SHOULD use named imports rather than namespace imports where possible to enable better tree-shaking.

### Verify

```bash
# Check for import statements in scope
grep -r "^import" packages/payload/src test packages/ui/src | head -20

# Validate import/order rules with ESLint
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
Claude Code MUST NOT skip or defer verification. Import pattern compliance is mandatory and enforced via automated ESLint checks in CI pipeline, pre-commit hooks, and code review process.
</enforcement>