# Adopt MongoDB as Primary Datastore with Version Migration Support: Version Tracking Status

These rules are ALWAYS ACTIVE for all database access patterns, migration utilities, and datastore configuration files in the project.

### Rules

- **R-MONGO-001** SHOULD: Version tracking and status localization SHOULD be implemented using MongoDB-specific migration utilities.
- **R-MONGO-002** MUST: All database operations MUST use MongoDB client libraries (mongoose, mongodb, or compatible drivers).
- **R-MONGO-003** SHOULD: Migration scripts SHOULD follow the established pattern with separate mongo/up.ts and shared.ts files in the /versions/migrations directory.
- **R-MONGO-004** SHOULD: Connection pooling SHOULD be configured appropriately for the application's concurrency requirements (typically 10-100 connections).
- **R-MONGO-005** SHOULD: Naming conventions for collections, indexes, and migration files SHOULD be established and maintained consistently across the codebase.
- **R-MONGO-006** MUST: MongoDB driver version MUST be compatible with the target MongoDB server version per the compatibility matrix.
- **R-MONGO-007** SHOULD: Query performance monitoring SHOULD be implemented to prevent degradation as data volume grows.
- **R-MONGO-008** SHOULD: Data models SHOULD be designed to minimize cross-document updates and leverage MongoDB transactions where available (4.0+).

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
- All database access patterns use MongoDB client libraries without mixing alternative datastores

<enforcement>
Claude Code MUST NOT skip or defer verification. Pull requests introducing non-MongoDB datastores without architectural approval are blocked. Code review feedback MUST redirect implementation to MongoDB-based solutions. Escalation to architecture team is required for cases where MongoDB limitations are identified.
</enforcement>