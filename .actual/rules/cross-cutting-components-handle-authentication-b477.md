# Standardize Authentication Provider Integration Pattern: Components Handle Authentication

These rules are ALWAYS ACTIVE for all UI components in the packages/ui directory that handle authentication, authentication debugging and testing interfaces, edit interfaces that require authentication context, and any new components that interact with authentication providers.

### Rules

- **R-AUTHN-001** MUST: Components MUST handle authentication provider state consistently across edit interfaces and debugging tools.

### Verify

```bash
# Count authentication provider references
grep -r "authn.providers" packages/ui/src --include="*.tsx" --include="*.ts" | wc -l

# Find components handling authentication and check for pattern compliance
find packages/ui/src -name "*.tsx" -exec grep -l "authentication.*provider" {} \; | xargs grep -L "standardized.*pattern" || echo "Check for pattern compliance"

# Run authentication-specific tests with coverage requirements
npm test -- --testPathPattern="auth" --coverage --coverageThreshold='{"global":{"branches":80}}'
```

**Accept when:**
- All UI components that handle authentication providers use the standardized integration pattern
- Authentication provider integration code passes security review and has test coverage above 80%
- AuthDebug tool successfully validates all configured authentication providers
- No authentication-related code duplication detected across UI components

<enforcement>
Claude Code MUST NOT skip or defer verification. All authentication provider integrations MUST be reviewed against this pattern before approval. Security team MUST be notified of any violations. Non-compliant code MUST be refactored before production deployment.
</enforcement>