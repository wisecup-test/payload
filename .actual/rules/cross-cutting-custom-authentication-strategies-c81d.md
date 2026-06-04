# Adopt MongoDB as Primary Datastore with Version Migration Support: Custom Authentication Strategies

These rules are ALWAYS ACTIVE for all files involving database access patterns, authentication strategies, migration utilities, and datastore configuration across the codebase.

### Rules

- **R-MONGO-001** MAY: Custom authentication strategies MAY integrate with MongoDB for user credential storage.
- **R-MONGO-002** MUST: All database operations use MongoDB client libraries (mongoose, mongodb, or compatible drivers).
- **R-MONGO-003** MUST: Migration scripts follow the established pattern with separate mongo/up.ts and shared.ts files in the /versions/migrations directory.
- **R-MONGO-004** SHOULD: Configure connection pooling appropriately for the application's concurrency requirements (typically 10-100 connections).
- **R-MONGO-005** SHOULD: Establish and maintain naming conventions for collections, indexes, and migration files across the codebase.
- **R-MONGO-006** SHOULD: Implement query performance monitoring and establish index review process during code review.
- **R-MONGO-007** SHOULD: Use MongoDB driver version compatible with the target MongoDB server version per compatibility matrix.
- **R-MONGO-008** SHOULD: Design data models to minimize cross-document updates and use MongoDB transactions where available (4.0+).
- **R-MONGO-009** MUST: Any new datastore introductions require architectural approval and cannot proceed without explicit ADR amendment.

### Verify

```bash
# Check for MongoDB connection strings in configuration
grep -r "mongodb://" --include="*.ts" --include="*.js" --include="*.json" . | wc -l

# Find MongoDB-specific migration files
find . -path "*/migrations/*mongo*" -type f | wc -l

# Verify MongoDB client library dependencies
grep -r "mongoose\|mongodb" package.json | grep -v node_modules
```

**Accept when:**
- MongoDB connection strings or configuration are present in environment configuration files
- At least one MongoDB-specific migration file exists in the migrations directory structure
- MongoDB client library (mongoose, mongodb, or similar) is declared as a dependency in package.json
- All database access patterns in pull requests use MongoDB client libraries
- Migration files follow the established pattern with separate mongo/up.ts and shared.ts files

<enforcement>
Claude Code MUST NOT skip or defer verification. All database operations must be validated against these rules during code review. Pull requests introducing non-MongoDB datastores without architectural approval MUST be blocked. Violations require escalation to the architecture team.
</enforcement>