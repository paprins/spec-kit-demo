# Data Model: Todo List Application

**Feature**: 001-todo-list-app
**Date**: 2025-10-15
**Database**: Supabase (PostgreSQL)

## Overview

The todo list application has a single entity: **Todo**. This data model supports all functional requirements for viewing, creating, updating completion status, and deleting todos.

---

## Entity: Todo

### Description
Represents a single task item in the user's todo list with a title and completion status.

### Fields

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | uuid | PRIMARY KEY, NOT NULL, DEFAULT uuid_generate_v4() | Unique identifier for the todo |
| `title` | varchar(200) | NOT NULL, CHECK (length(trim(title)) > 0) | The todo item's text content |
| `completed` | boolean | NOT NULL, DEFAULT false | Whether the todo is marked as complete |
| `created_at` | timestamptz | NOT NULL, DEFAULT now() | Timestamp when the todo was created |

### Field Details

**id** (uuid):
- Auto-generated on insert using Supabase's uuid_generate_v4()
- Used in API paths for update and delete operations (e.g., PATCH /todos/{id})
- Guarantees uniqueness across all todos

**title** (varchar(200)):
- Maximum length: 200 characters (FR-013: "System MUST enforce a maximum title length of 200 characters")
- Cannot be empty or whitespace-only (FR-004: "System MUST prevent creation of todos with empty or whitespace-only titles")
- Duplicates allowed (FR-014: "System MUST allow creation of todos with duplicate titles")
- CHECK constraint: `length(trim(title)) > 0` enforces no whitespace-only titles at database level

**completed** (boolean):
- Defaults to `false` on creation (FR-003: "System MUST create new todos with incomplete status by default")
- Toggled via PATCH endpoint (FR-005: "System MUST allow users to toggle completion status")
- Used for visual distinction in UI (FR-006: "System MUST visually distinguish between complete and incomplete todos")

**created_at** (timestamptz):
- Auto-set on insert to current timestamp
- Provides default ordering for todo list (newest last per spec assumption)
- Timezone-aware for future multi-timezone support (though not in current scope)

### Relationships
None. This is a single-table application with no foreign keys.

### Indexes

```sql
-- Primary key index (automatic)
-- No additional indexes needed for workshop demo scope
-- created_at ordering is fast enough for expected dataset size (<100 todos)
```

---

## Database Schema (SQL)

**Supabase Table Creation**:
```sql
CREATE TABLE todos (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    title VARCHAR(200) NOT NULL CHECK (length(trim(title)) > 0),
    completed BOOLEAN NOT NULL DEFAULT false,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

-- Optional: Add comment for documentation
COMMENT ON TABLE todos IS 'User todo items with title and completion status';
COMMENT ON COLUMN todos.title IS 'Todo text content, max 200 chars, cannot be empty/whitespace';
COMMENT ON COLUMN todos.completed IS 'Completion status, defaults to false (incomplete)';
```

---

## State Transitions

### Todo Lifecycle

```
[Created] ---> [Incomplete] ---> [Completed]
                   ^                  |
                   |__________________|
                     (toggle action)
```

**States**:
1. **Created**: New todo inserted with `completed = false`
2. **Incomplete**: Active todo that needs completion (default state)
3. **Completed**: Todo marked as done via PATCH /todos/{id} with `completed: true`

**Transitions**:
- Create: None → Incomplete (POST /todos)
- Toggle Complete: Incomplete ↔ Completed (PATCH /todos/{id})
- Delete: Any State → Deleted (DELETE /todos/{id})

**Validation Rules**:
- Cannot transition to Created state (no updates to created_at allowed)
- Can toggle between Incomplete/Completed unlimited times (FR-015: "System MUST process all toggle clicks immediately")
- Delete is permanent with no confirmation (FR-008: "System MUST permanently remove todos when delete button is clicked without requiring confirmation")

---

## Validation Rules

### Database-Level Validation
- `title` length: Enforced by VARCHAR(200)
- `title` not empty: Enforced by CHECK constraint `length(trim(title)) > 0`
- `id` uniqueness: Enforced by PRIMARY KEY
- `completed` type: Enforced by BOOLEAN type
- `created_at` presence: Enforced by NOT NULL + DEFAULT

### Application-Level Validation
- Title trimming: Remove leading/trailing whitespace before insert (Pydantic validator)
- Title length: Validate 1-200 characters in FastAPI Pydantic schema
- Empty title rejection: Validate in both Pydantic and database CHECK constraint

### Why Both Levels?
- **Defense in depth**: Database protects against bugs in application code
- **Better error messages**: Pydantic provides user-friendly validation errors before hitting database
- **Performance**: Pydantic validation fails fast without database round-trip

---

## Example Records

```json
[
    {
        "id": "550e8400-e29b-41d4-a716-446655440000",
        "title": "Buy groceries",
        "completed": false,
        "created_at": "2025-10-15T10:30:00Z"
    },
    {
        "id": "6ba7b810-9dad-11d1-80b4-00c04fd430c8",
        "title": "Finish project documentation",
        "completed": true,
        "created_at": "2025-10-15T11:45:00Z"
    },
    {
        "id": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
        "title": "Call dentist for appointment",
        "completed": false,
        "created_at": "2025-10-15T14:20:00Z"
    }
]
```

---

## Data Consistency Rules

### Referential Integrity
Not applicable - single table with no foreign keys.

### Constraints
1. `id` must be unique (PRIMARY KEY)
2. `title` must not be null or empty/whitespace-only
3. `title` must be ≤ 200 characters
4. `completed` must be boolean (true/false)
5. `created_at` must be valid timestamp

### Concurrency Handling
- **Optimistic**: No row-level locking required for workshop demo
- **Last-write-wins**: If multiple clients update same todo, last PATCH wins
- **Out of scope**: Conflict resolution, version numbers, optimistic locking

---

## MCP-First Workflow Compliance

Per Constitution III, the following workflow must be followed:

### Before Application Code
1. Use Supabase MCP server to create `todos` table with schema above
2. Verify table structure via MCP inspection tools
3. Test insert/select/update/delete operations via MCP
4. Validate CHECK constraints with edge cases (empty string, whitespace, 201 chars)

### Application Integration
1. Initialize supabase-py client with environment variables
2. Use `supabase.table("todos")` methods for all operations
3. Never use raw SQL strings in application code
4. Rely on Pydantic for pre-flight validation, database for enforcement

---

## Migration Strategy

**Initial Setup** (via Supabase MCP):
```sql
CREATE TABLE todos (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    title VARCHAR(200) NOT NULL CHECK (length(trim(title)) > 0),
    completed BOOLEAN NOT NULL DEFAULT false,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

**Future Enhancements** (out of scope for workshop):
- Add `user_id` foreign key if authentication added
- Add `updated_at` timestamp for audit trail
- Add `deleted_at` for soft deletes
- Add `priority` or `category` fields for organization

---

## Summary

This data model provides:
- **Simplicity**: Single table with 4 fields
- **Validation**: Database constraints + Pydantic schemas
- **Flexibility**: Supports all CRUD operations without complexity
- **Standards compliance**: Follows PostgreSQL best practices via Supabase
- **Constitution alignment**: MCP-first workflow, no raw SQL in app code

The model is deliberately minimal per Constitution V (workshop demo scope) while still demonstrating professional database design principles.
