# Adopt MongoDB as Primary Datastore with Version Migration Support: Database Schema Changes

These rules are ALWAYS ACTIVE for all database schema changes, migration scripts, and datastore configuration across the project.

### Rules

- **R-MONGO-001** MUST: Database schema changes MUST be implemented through versioned migration scripts.

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

<enforcement>
Claude Code MUST NOT skip or defer verification. All database schema changes must be validated against these rules before approval.
</enforcement>