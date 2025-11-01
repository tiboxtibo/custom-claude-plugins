# Database Schema - {{SERVICE_NAME}}

> 🗄️ Documentazione schema database e modelli dati
> Ultimo aggiornamento: {{TIMESTAMP}}

## 📋 Overview

**Database Type**: {{DB_TYPE}}
**Version**: {{DB_VERSION}}
**ORM/Query Builder**: {{ORM_NAME}}
**Schema Management**: {{SCHEMA_MANAGEMENT_TOOL}}

## 🗃️ Database Configuration

**Connection**:
```env
{{DB_CONNECTION_ENV_VARS}}
```

**Connection Pooling**:
- Min connections: {{MIN_CONNECTIONS}}
- Max connections: {{MAX_CONNECTIONS}}
- Idle timeout: {{IDLE_TIMEOUT}}

## 📊 Entity-Relationship Diagram

```
{{ERD_DIAGRAM}}
```

<!-- Usa tool come dbdiagram.io, draw.io per creare diagrammi -->

## 📋 Tables

### `users`

Tabella utenti del sistema.

**Columns**:

| Column | Type | Constraints | Default | Description |
|--------|------|-------------|---------|-------------|
| `id` | {{ID_TYPE}} | PRIMARY KEY | {{ID_DEFAULT}} | User unique identifier |
| `email` | VARCHAR(255) | UNIQUE, NOT NULL | - | User email address |
| `password_hash` | VARCHAR(255) | NOT NULL | - | Hashed password |
| `name` | VARCHAR(100) | NOT NULL | - | User full name |
| `role` | ENUM | NOT NULL | 'user' | User role (user, admin) |
| `email_verified` | BOOLEAN | NOT NULL | FALSE | Email verification status |
| `created_at` | TIMESTAMP | NOT NULL | CURRENT_TIMESTAMP | Creation timestamp |
| `updated_at` | TIMESTAMP | NOT NULL | CURRENT_TIMESTAMP | Last update timestamp |
| `deleted_at` | TIMESTAMP | NULL | NULL | Soft delete timestamp |

**Indexes**:
- `idx_users_email` on `email` (UNIQUE)
- `idx_users_role` on `role`
- `idx_users_created_at` on `created_at`

**Foreign Keys**: None

**Model** ({{ORM_NAME}}):
```{{PRIMARY_LANGUAGE}}
{{USER_MODEL_CODE}}
```

**Validation Rules**:
- `email`: Valid email format, max 255 chars
- `password_hash`: Bcrypt/Argon2 hashed, never plain text
- `name`: 2-100 characters
- `role`: One of: 'user', 'admin', 'moderator'

---

### `{{TABLE_2_NAME}}`

{{TABLE_2_DESCRIPTION}}

**Columns**:

| Column | Type | Constraints | Default | Description |
|--------|------|-------------|---------|-------------|
| {{COLUMN_1}} | {{TYPE_1}} | {{CONSTRAINTS_1}} | {{DEFAULT_1}} | {{DESC_1}} |
| {{COLUMN_2}} | {{TYPE_2}} | {{CONSTRAINTS_2}} | {{DEFAULT_2}} | {{DESC_2}} |

**Indexes**:
-

**Foreign Keys**:
- `{{FK_NAME}}` references `{{REF_TABLE}}({{REF_COLUMN}})`
  - ON DELETE: {{ON_DELETE_ACTION}}
  - ON UPDATE: {{ON_UPDATE_ACTION}}

**Model** ({{ORM_NAME}}):
```{{PRIMARY_LANGUAGE}}
{{MODEL_CODE}}
```

---

<!-- TODO: Documentare altre tabelle -->

## 🔗 Relationships

### One-to-Many

**`users` → `posts`**
- One user can have many posts
- Foreign key: `posts.user_id` → `users.id`
- Cascade delete: Yes

### Many-to-Many

**`users` ↔ `roles`**
- Through junction table: `user_roles`
- Fields: `user_id`, `role_id`, `assigned_at`

### One-to-One

**`users` → `profiles`**
- Each user has one profile
- Foreign key: `profiles.user_id` → `users.id`
- Unique constraint on `profiles.user_id`

## 📐 Enums

### `user_role`

```sql
CREATE TYPE user_role AS ENUM (
  'user',
  'admin',
  'moderator',
  'guest'
);
```

**Values**:
- `user`: Standard user (default)
- `admin`: Administrator with full access
- `moderator`: Content moderator
- `guest`: Limited access guest

---

<!-- TODO: Documentare altri enum -->

## 🔄 Migrations

### Migration Strategy

**Tool**: {{MIGRATION_TOOL}}

**Commands**:
```bash
# Create new migration
{{CREATE_MIGRATION_CMD}}

# Run migrations
{{RUN_MIGRATIONS_CMD}}

# Rollback last migration
{{ROLLBACK_MIGRATION_CMD}}

# Migration status
{{MIGRATION_STATUS_CMD}}
```

### Migration History

#### {{MIGRATION_VERSION_1}} - {{MIGRATION_DATE_1}}

**Description**: {{MIGRATION_DESC_1}}

**Changes**:
- Created table `users`
- Created table `posts`
- Added index on `users.email`

**File**: `{{MIGRATION_FILE_1}}`

---

#### {{MIGRATION_VERSION_2}} - {{MIGRATION_DATE_2}}

**Description**: {{MIGRATION_DESC_2}}

**Changes**:
- Added column `users.email_verified`
- Created table `user_sessions`

**File**: `{{MIGRATION_FILE_2}}`

---

<!-- TODO: Aggiungere altre migrazioni -->

## 🌱 Seeding

### Development Seeds

```bash
# Seed database
{{SEED_CMD}}
```

**Seed Data**:
- {{SEED_DATASET_1}}: {{SEED_COUNT_1}} records
- {{SEED_DATASET_2}}: {{SEED_COUNT_2}} records

### Test Seeds

```bash
# Seed test database
{{SEED_TEST_CMD}}
```

## 🔍 Queries Common

### Get User with Posts

```{{PRIMARY_LANGUAGE}}
{{QUERY_EXAMPLE_1}}
```

```sql
-- Raw SQL
SELECT u.*, COUNT(p.id) as post_count
FROM users u
LEFT JOIN posts p ON p.user_id = u.id
WHERE u.id = $1
GROUP BY u.id;
```

### Pagination Query

```{{PRIMARY_LANGUAGE}}
{{PAGINATION_QUERY_EXAMPLE}}
```

### Full-Text Search

```{{PRIMARY_LANGUAGE}}
{{SEARCH_QUERY_EXAMPLE}}
```

## ⚡ Indexes e Performance

### Existing Indexes

| Table | Index Name | Columns | Type | Purpose |
|-------|-----------|---------|------|---------|
| `users` | `idx_users_email` | email | UNIQUE | Fast email lookup |
| `users` | `idx_users_created_at` | created_at | BTREE | Date range queries |
| `posts` | `idx_posts_user_id` | user_id | BTREE | User posts lookup |
| `posts` | `idx_posts_published` | published_at | BTREE | Published posts query |

### Query Performance Tips

- ✅ Always use indexes for WHERE clauses
- ✅ Avoid SELECT * in production
- ✅ Use EXPLAIN ANALYZE for slow queries
- ✅ Consider database views for complex queries
- ✅ Use connection pooling

### Slow Query Example

**Before optimization**:
```sql
-- 2.5s execution time
SELECT * FROM posts WHERE user_id = 123;
```

**After adding index**:
```sql
-- 0.05s execution time
CREATE INDEX idx_posts_user_id ON posts(user_id);
SELECT * FROM posts WHERE user_id = 123;
```

## 🔒 Security

### Password Hashing

```{{PRIMARY_LANGUAGE}}
{{PASSWORD_HASHING_EXAMPLE}}
```

**Algorithm**: {{HASHING_ALGORITHM}}
**Salt rounds**: {{SALT_ROUNDS}}

### SQL Injection Prevention

- ✅ Always use parameterized queries
- ✅ Never concatenate user input in SQL
- ✅ Use ORM escaping

**Bad**:
```{{PRIMARY_LANGUAGE}}
// ❌ VULNERABLE
{{SQL_INJECTION_BAD_EXAMPLE}}
```

**Good**:
```{{PRIMARY_LANGUAGE}}
// ✅ SAFE
{{SQL_INJECTION_GOOD_EXAMPLE}}
```

### Row-Level Security (se applicabile)

```sql
-- Enable RLS
ALTER TABLE posts ENABLE ROW LEVEL SECURITY;

-- Policy: Users can only see their own posts
CREATE POLICY user_posts_policy ON posts
  FOR SELECT
  USING (user_id = current_user_id());
```

## 🧹 Data Maintenance

### Soft Deletes

```{{PRIMARY_LANGUAGE}}
{{SOFT_DELETE_EXAMPLE}}
```

### Data Cleanup Jobs

- **Old sessions**: Cleanup weekly
- **Soft deleted records**: Archive after 30 days
- **Logs**: Rotate daily, keep 90 days

### Backup Strategy

**Schedule**:
- Full backup: {{FULL_BACKUP_SCHEDULE}}
- Incremental: {{INCREMENTAL_BACKUP_SCHEDULE}}
- Point-in-time recovery: Enabled

**Command**:
```bash
{{BACKUP_COMMAND}}
```

## 📊 Database Statistics

**Current Status** (as of {{TIMESTAMP}}):
- Total tables: {{TABLE_COUNT}}
- Total records: {{TOTAL_RECORDS}}
- Database size: {{DB_SIZE}}
- Largest table: {{LARGEST_TABLE}}

### Growth Metrics

| Month | Size | Growth | Records |
|-------|------|--------|---------|
| {{MONTH_1}} | {{SIZE_1}} | - | {{RECORDS_1}} |
| {{MONTH_2}} | {{SIZE_2}} | {{GROWTH_2}} | {{RECORDS_2}} |

## 🐛 Troubleshooting

### Connection Issues

**Error**: `too many connections`

**Solution**:
```bash
# Check current connections
{{CHECK_CONNECTIONS_CMD}}

# Adjust max_connections in config
{{FIX_CONNECTIONS_CMD}}
```

### Performance Issues

**Symptom**: Slow queries

**Debug**:
```sql
-- Find slow queries
{{SLOW_QUERY_LOG_CMD}}

-- Analyze query plan
EXPLAIN ANALYZE {{YOUR_QUERY}};
```

## 📚 Related Documentation

- [Setup Guide](./setup.md) - Database setup instructions
- [API Endpoints](./endpoints.md) - API that uses this database
- [Troubleshooting](./troubleshooting.md) - Common database issues

## 🔗 External Resources

- [{{DB_TYPE}} Documentation]({{DB_DOCS_URL}})
- [{{ORM_NAME}} Documentation]({{ORM_DOCS_URL}})
- [Database Design Best Practices]({{DB_BEST_PRACTICES_URL}})

---
*Generato automaticamente da Toduba System v{{TODUBA_VERSION}}*
