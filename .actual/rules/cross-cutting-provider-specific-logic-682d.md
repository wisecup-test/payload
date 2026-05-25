# Standardize Authentication Provider Integration Pattern: Provider Specific Logic

These rules are ALWAYS ACTIVE for all UI components in the packages/ui directory that handle authentication, authentication debugging and testing interfaces, edit interfaces (EditMany, DrawerContent) that require authentication context, and any new components that interact with authentication providers.

### Rules

- **R-AUTHN-001** SHOULD: Provider-specific logic SHOULD be abstracted behind a common interface to facilitate adding new providers.

### Verify

```bash
# Count provider-related patterns in UI components
grep -r "authn.providers" packages/ui/src --include="*.tsx" --include="*.ts" | wc -l

# Find authentication provider components and check for pattern compliance
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
Claude Code MUST NOT skip or defer verification. All authentication provider integrations MUST comply with the standardized pattern before production deployment. Security team MUST approve any exceptions to this pattern.
</enforcement>