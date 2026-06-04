# Adopt One-Way Hashing for Sensitive Data Protection: Hashing Implementations Include

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ALWAYS ACTIVE for all components handling sensitive data including telemetry, user identifiers, payment information, and personally identifiable information (PII).

## Context

- The codebase handles sensitive data including telemetry information, user identifiers, cart data, and relationship data that may contain personally identifiable information
- Direct storage or transmission of sensitive data creates security vulnerabilities and compliance risks under regulations like GDPR, CCPA, and PCI-DSS
- One-way hashing provides irreversible transformation of sensitive data while maintaining the ability to verify data integrity and perform equality comparisons
- Pattern detected across 3 files with 88.67% confidence indicates consistent application of cryptographic hashing for data protection
- The pattern appears in critical paths including telemetry collection, e-commerce cart operations, and relationship testing, suggesting organization-wide security requirements

## Problem Statement

Systems that store, transmit, or process sensitive data in plaintext expose organizations to data breaches, regulatory non-compliance, and privacy violations. Without a standardized approach to protecting sensitive data through cryptographic transformation, different components may implement inconsistent or inadequate protection mechanisms, creating security gaps and audit failures.

## Decision

1. MUST: Hashing implementations MUST include appropriate salt values for password hashing and SHOULD use unique salts per data item to prevent rainbow table attacks

## Policy Block

- MUST Hashing implementations MUST include appropriate salt values for password hashing and SHOULD use unique salts per data item to prevent rainbow table attacks

In scope:
- Telemetry data containing user or system identifiers
- User authentication credentials (passwords, API keys, tokens)
- Personally Identifiable Information (PII) including email addresses, phone numbers, and user IDs
- Payment card information and financial data in e-commerce systems
- Session identifiers and tracking tokens
- Any data subject to privacy regulations (GDPR, CCPA, HIPAA)

Out of scope:
- Public data that requires no protection
- Data that must be retrieved in original form (use encryption instead)
- Non-sensitive system logs and debugging information
- Configuration values that are not secrets
- Display data that users need to view in plaintext

Exceptions:
- EXC-001: Data must be retrieved in original form for business operations (e.g., displaying user email addresses)
- EXC-002: Development and testing environments require readable data for debugging

## Rationale

- Pattern detected with 88.67% confidence across 3 files (oneWayHash.ts, beforeChange.ts, int.spec.ts) demonstrates established practice of using one-way hashing for data protection
- One-way hashing provides irreversible data transformation that satisfies data minimization principles required by GDPR Article 5 and similar privacy regulations
- Cryptographic hashing enables data integrity verification and equality comparisons without exposing original sensitive values
- Centralized hashing utilities (as evidenced by oneWayHash.ts) promote consistent security implementation and reduce the risk of weak or incorrect cryptographic usage
- Application in beforeChange hooks and data transformation layers ensures protection occurs before persistence, preventing sensitive data from reaching storage systems

## Consequences

Positive:
- Significantly reduces risk of data breaches by ensuring sensitive data is never stored in plaintext
- Achieves compliance with data protection regulations (GDPR, CCPA, PCI-DSS) through cryptographic protection of sensitive information
- Enables safe telemetry collection and analytics without exposing user identities or sensitive attributes
- Provides defense-in-depth security by making stolen data useless to attackers without the ability to reverse hashes

Negative:
- Hashed data cannot be retrieved in original form, requiring careful analysis of business requirements before applying hashing
- Performance overhead from cryptographic operations, particularly for high-volume data processing or real-time systems
- Increased complexity in data debugging and troubleshooting when developers cannot view original values
- Potential for implementation errors if developers choose weak hash algorithms or fail to use proper salting techniques

## Alternatives

- Store sensitive data in plaintext with database-level encryption (rejected)
  Rejected because: Database encryption protects data at rest but does not prevent exposure through application logs, error messages, or compromised application credentials. Does not satisfy data minimization principles.
  When valid: May be combined with hashing for additional defense-in-depth, but not as a replacement
- Use symmetric encryption (AES) for all sensitive data (rejected)
  Rejected because: Encryption is reversible and requires secure key management infrastructure. If keys are compromised, all data is exposed. Does not satisfy the principle of irreversible data transformation for data that never needs to be retrieved.
  When valid: Use encryption when data must be retrieved in original form (e.g., user email for display purposes)
- Implement data masking or truncation without cryptographic hashing (rejected)
  Rejected because: Masking and truncation are weaker protection mechanisms that may still allow data inference or reconstruction. Does not provide cryptographic guarantees.
  When valid: May be used as an additional layer for display purposes, but not as primary protection mechanism

## Risks

- Developers may apply hashing to data that needs to be retrieved later, breaking business functionality
  Mitigation: Provide clear documentation and training on when to use hashing vs. encryption. Implement code review checkpoints for data protection decisions.
  Owner: Security team and engineering leads
- Use of weak or deprecated hash algorithms (MD5, SHA-1) may provide false sense of security
  Mitigation: Enforce approved cryptographic libraries through linting rules and dependency management. Centralize hashing in utility modules with security team oversight.
  Owner: Security team
- Missing or weak salt values in password hashing may enable rainbow table attacks
  Mitigation: Use established password hashing libraries (bcrypt, Argon2) that handle salting automatically. Include salt validation in security audits.
  Owner: Security team

## Implementation Notes

- Create or use existing centralized hashing utilities (e.g., oneWayHash.ts) that encapsulate approved cryptographic algorithms and proper salt handling
- Apply hashing in data transformation layers such as beforeChange hooks, service boundaries, or data access objects before persistence
- For password hashing, use specialized libraries like bcrypt or Argon2 with appropriate work factors (bcrypt cost factor ≥ 12)
- Document which fields are hashed in data models and schemas to prevent confusion during debugging and maintenance
- Implement logging that captures hashing operations for audit trails without logging the sensitive data itself

## Continuation Context


Verify commands:
- grep -r "oneWayHash\|createHash\|bcrypt\|argon2" --include="*.ts" --include="*.js" packages/ src/
- grep -r "beforeChange.*hash\|hash.*beforeChange" --include="*.ts" --include="*.js" packages/ src/
- npm audit | grep -i "crypto\|hash" || echo "No cryptographic vulnerabilities found"

Accept when:
- All sensitive data fields identified in policy scope are protected by cryptographic hashing before storage or transmission
- Hashing implementations use approved algorithms (SHA-256, SHA-512, bcrypt, Argon2) with no usage of deprecated algorithms (MD5, SHA-1)
- Password hashing includes proper salting with unique salts per user and appropriate work factors

## Enforcement

- Verified by: Automated security scanning in CI/CD pipeline checking for plaintext sensitive data patterns
- Verified by: Code review checklist requiring verification of data protection for all new fields handling sensitive information
- Verified by: Static analysis tools configured to detect usage of deprecated hash algorithms (MD5, SHA-1)
- Verified by: Quarterly security audits reviewing data protection implementations across all services
- Violation handling: CI/CD pipeline fails if deprecated cryptographic algorithms are detected
- Violation handling: Code review blocks merge if sensitive data handling lacks appropriate hashing or encryption
- Violation handling: Security team notified automatically for violations detected in production code
- Violation handling: Remediation required within 30 days for non-critical violations, 7 days for critical violations involving PII or payment data
- Exception process: Submit exception request to security team with business justification and alternative protection mechanism
- Exception process: Security team reviews within 3 business days and approves/rejects with documented rationale
- Exception process: Approved exceptions documented in security register with expiration date and review schedule
- Exception process: All exceptions require annual review and re-approval