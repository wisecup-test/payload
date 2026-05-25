# Standardize Authentication Provider Integration Pattern: Components Extend Base

These rules are ALWAYS ACTIVE for all UI components in the packages/ui directory that handle authentication, authentication debugging and testing interfaces, edit interfaces (EditMany, DrawerContent) that require authentication context, and any new components that interact with authentication providers.

### Rules

- **R-AUTHN-001** MAY: Components MAY extend the base authentication provider pattern with component-specific functionality when necessary.
- **R-AUTHN-002** MUST: All UI components that handle authentication providers use the standardized integration pattern.
- **R-AUTHN-003** MUST: Authentication provider integration code passes security review and has test coverage above 80%.
- **R-AUTHN-004** SHOULD: Review the existing implementation in packages/ui/src/elements/EditMany/DrawerContent.tsx as the reference pattern.
- **R-AUTHN-005** SHOULD: Use the AuthDebug component (test/auth/ui/AuthDebug.tsx) as a template for testing new provider integrations.
- **R-AUTHN-006** MUST: All authentication provider integrations include proper error handling and fallback states.
- **R-AUTHN-007** SHOULD: Document provider-specific configuration requirements in a central authentication configuration guide.
- **R-AUTHN-008** SHOULD: Consider creating a shared hook or context provider for authentication state management across components.

### Verify

```bash
# Count authentication provider references
grep -r "authn.providers" packages/ui/src --include="*.tsx" --include="*.ts" | wc -l

# Find components handling authentication and check for pattern compliance
find packages/ui/src -name "*.tsx" -exec grep -l "authentication.*provider" {} \; | xargs grep -L "standardized.*pattern" || echo "Check for pattern compliance"

# Run authentication-specific tests with coverage threshold
npm test -- --testPathPattern="auth" --coverage --coverageThreshold='{"global":{"branches":80}}'
```

**Accept when:**
- All UI components that handle authentication providers use the standardized integration pattern
- Authentication provider integration code passes security review and has test coverage above 80%
- AuthDebug tool successfully validates all configured authentication providers
- No authentication-related code duplication detected across UI components

<enforcement>
Claude Code MUST NOT skip or defer verification. All authentication provider integrations MUST pass security review and coverage thresholds before production deployment. CI build MUST fail if authentication tests do not meet coverage thresholds. Pull requests with non-compliant authentication code MUST be flagged for architecture review.
</enforcement>