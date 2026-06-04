# Adopt MongoDB as Primary Datastore with Version Migration Support: Database Connection Configuration

These rules are ALWAYS ACTIVE for all database connection configuration, migration utilities, and datastore access patterns across the codebase.

### Rules

- **R-MONGO-001** MUST: Database connection configuration MUST be centralized and environment-aware.
- **R-MONGO-002** MUST: All database operations MUST use MongoDB client libraries (mongoose, mongodb, or equivalent).
- **R-MONGO-003** MUST: Migration scripts MUST be implemented in the `/versions/migrations` directory following the established pattern with separate `mongo/up.ts` and `shared.ts` files.
- **R-MONGO-004** SHOULD: Connection pooling SHOULD be configured appropriately for the application's concurrency requirements (typically 10-100 connections).
- **R-MONGO-005** SHOULD: Naming conventions for collections, indexes, and migration files SHOULD be established and maintained consistently across the codebase.
- **R-MONGO-006** SHOULD: MongoDB-specific query patterns and best practices SHOULD be documented in the team's technical documentation.
- **R-MONGO-007** MUST: MongoDB driver version MUST be compatible with the target MongoDB server version (verify against compatibility matrix).
- **R-MONGO-008** SHOULD: Query performance monitoring SHOULD be implemented to detect inadequate indexing strategies early.
- **R-MONGO-009** SHOULD: Index review process SHOULD be established during code review to maintain query performance.

### Verify

```bash
# Check for MongoDB connection strings in configuration
grep -r "mongodb://" --include="*.ts" --include="*.js" --include="*.json" . | wc -l

# Verify MongoDB-specific migration files exist
find . -path "*/migrations/*mongo*" -type f | wc -l

# Confirm MongoDB client library is declared as dependency
grep -r "mongoose\|mongodb" package.json | grep -v node_modules
```

**Accept when:**
- MongoDB connection strings or configuration are present in environment configuration files
- At least one MongoDB-specific migration file exists in the migrations directory structure
- MongoDB client library (mongoose, mongodb, or similar) is declared as a dependency in package.json
- All database access patterns use MongoDB client libraries exclusively
- Connection configuration is centralized in a single configuration module

<enforcement>
Claude Code MUST NOT skip or defer verification. All pull requests introducing database operations MUST pass these verification checks before approval. Any introduction of non-MongoDB datastores without architectural approval MUST be blocked.
</enforcement>