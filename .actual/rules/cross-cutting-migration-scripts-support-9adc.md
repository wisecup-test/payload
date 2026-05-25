# Adopt MongoDB as Primary Datastore with Version Migration Support: Migration Scripts Support

These rules are ALWAYS ACTIVE for all database migration scripts, datastore configuration files, and database access patterns across the codebase.

### Rules

- **R-MONGO-001** MUST: Migration scripts MUST support both 'up' and 'down' operations for rollback capability.

### Verify

```bash
# Check for MongoDB connection strings in configuration
grep -r "mongodb://" --include="*.ts" --include="*.js" --include="*.json" . | wc -l

# Find MongoDB-specific migration files
find . -path "*/migrations/*mongo*" -type f | wc -l

# Verify MongoDB client library is declared as dependency
grep -r "mongoose\|mongodb" package.json | grep -v node_modules
```

**Accept when:**
- MongoDB connection strings or configuration are present in environment configuration files
- At least one MongoDB-specific migration file exists in the migrations directory structure
- MongoDB client library (mongoose, mongodb, or similar) is declared as a dependency in package.json
- Migration scripts in `/versions/migrations` directory follow the established pattern with separate `mongo/up.ts` and `shared.ts` files
- Both 'up' and 'down' migration operations are implemented for rollback capability

<enforcement>
Claude Code MUST NOT skip or defer verification of migration script bidirectionality and MongoDB dependency presence. All pull requests introducing or modifying migration scripts MUST be verified against these rules before approval.
</enforcement>