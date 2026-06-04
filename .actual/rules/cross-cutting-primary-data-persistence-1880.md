# Adopt MongoDB as Primary Datastore with Version Migration Support: Primary Data Persistence

These rules are ALWAYS ACTIVE for all files that perform primary data persistence operations, including database connection configuration, data access layers, migration utilities, and any code that reads from or writes to the primary datastore.

### Rules

- **R-MONGO-001** MUST: All primary data persistence operations MUST use MongoDB as the datastore.

### Verify

```bash
# Check for MongoDB connection strings in configuration
grep -r "mongodb://" --include="*.ts" --include="*.js" --include="*.json" . | wc -l

# Find MongoDB-specific migration files
find . -path "*/migrations/*mongo*" -type f | wc -l

# Verify MongoDB client library is declared as a dependency
grep -r "mongoose\|mongodb" package.json | grep -v node_modules
```

**Accept when:**
- MongoDB connection strings or configuration are present in environment configuration files
- At least one MongoDB-specific migration file exists in the migrations directory structure
- MongoDB client library (mongoose, mongodb, or similar) is declared as a dependency in package.json

<enforcement>
Claude Code MUST NOT skip or defer verification. All primary data persistence code MUST be verified to use MongoDB. Pull requests introducing non-MongoDB datastores without architectural approval are blocked. Code review feedback MUST redirect implementation to MongoDB-based solutions. Escalation to the architecture team is required for cases where MongoDB limitations are identified.
</enforcement>