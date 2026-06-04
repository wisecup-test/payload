# Adopt MongoDB as Primary Datastore with Version Migration Support: Collections Use Mongodb

These rules are ALWAYS ACTIVE for all files that define, access, or migrate data collections in this project.

### Rules

- **R-MONGO-001** SHOULD: Collections SHOULD use MongoDB's native document structure rather than attempting to emulate relational patterns.

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
Claude Code MUST NOT skip or defer verification of MongoDB datastore adoption. All collection implementations MUST be reviewed against R-MONGO-001 during code review and CI pipeline checks.
</enforcement>