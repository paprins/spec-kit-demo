# Quickstart: Todo List Application

**Feature**: 001-todo-list-app
**Date**: 2025-10-15

This guide walks through setting up and running the todo list application from scratch following the MCP-first workflow mandated by Constitution III.

---

## Prerequisites

- Python 3.12+
- Supabase account (free tier sufficient)
- Supabase MCP server configured in Claude Code
- Modern web browser (Chrome, Firefox, Safari, Edge)

---

## Step 1: Database Setup (MCP-First Workflow)

Per Constitution III, use the Supabase MCP server to create the database schema BEFORE writing application code.

### 1.1 Create Supabase Project
1. Go to https://supabase.com and create a new project
2. Note your project URL and anon/public API key (found in Settings → API)

### 1.2 Create Table via MCP
Use Supabase MCP tools in Claude Code:

```sql
CREATE TABLE todos (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    title VARCHAR(200) NOT NULL CHECK (length(trim(title)) > 0),
    completed BOOLEAN NOT NULL DEFAULT false,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

COMMENT ON TABLE todos IS 'User todo items with title and completion status';
```

### 1.3 Verify Schema via MCP
Use MCP inspection tools to confirm:
- Table `todos` exists
- All columns present with correct types
- CHECK constraint on `title` is active
- Default values work correctly

### 1.4 Test CRUD Operations via MCP
Before touching application code, test database operations:

```sql
-- Test INSERT
INSERT INTO todos (title) VALUES ('Test todo');

-- Test SELECT
SELECT * FROM todos;

-- Test UPDATE
UPDATE todos SET completed = true WHERE title = 'Test todo';

-- Test DELETE
DELETE FROM todos WHERE title = 'Test todo';
```

Verify all operations succeed and constraints work:
- Empty string title should be rejected by CHECK constraint
- `completed` should default to `false`
- `created_at` should auto-populate

---

## Step 2: Backend Setup

### 2.1 Create Project Structure
```bash
cd /Users/pprins/projects/ai/spec-kit-demo
mkdir -p src/backend src/frontend
```

### 2.2 Create Environment Configuration
Create `src/backend/.env`:
```bash
SUPABASE_URL=https://YOUR_PROJECT_ID.supabase.co
SUPABASE_KEY=your_anon_public_key_here
```

**Security**: Add `.env` to `.gitignore` immediately:
```bash
echo "src/backend/.env" >> .gitignore
```

### 2.3 Install Python Dependencies
Create `src/backend/requirements.txt`:
```
fastapi==0.104.1
uvicorn[standard]==0.24.0
supabase==2.0.3
python-dotenv==1.0.0
pydantic-settings==2.0.3
```

Install dependencies:
```bash
cd src/backend
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### 2.4 Create Backend Application
Implementation files will be created during Phase 2 (tasks execution):
- `src/backend/database.py` - Supabase client initialization
- `src/backend/main.py` - FastAPI application with all routes

---

## Step 3: Frontend Setup

### 3.1 Create Frontend File
Implementation will be created during Phase 2:
- `src/frontend/index.html` - Complete UI with inline JavaScript and Tailwind CDN

---

## Step 4: Running the Application

### 4.1 Start Backend Server
```bash
cd src/backend
source venv/bin/activate  # If not already activated
uvicorn main:app --reload --port 8000
```

Expected output:
```
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Started reloader process
INFO:     Started server process
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```

### 4.2 Start Frontend Server
Open a new terminal:
```bash
cd src/frontend
python -m http.server 8001
```

Expected output:
```
Serving HTTP on :: port 8001 (http://[::]:8001/) ...
```

### 4.3 Access Application
Open browser to: http://localhost:8001

---

## Step 5: Testing the Application

### 5.1 Manual Test Checklist

**View Todos (FR-001)**:
- [ ] Open application, see empty state message if no todos exist
- [ ] Add todos, refresh page, todos still visible (persistence)

**Add Todos (FR-002, FR-003)**:
- [ ] Enter title and submit form
- [ ] New todo appears in list as incomplete
- [ ] Input field clears after submission (FR-011)
- [ ] Try submitting empty title → validation error (FR-004)
- [ ] Try submitting whitespace-only title → validation error (FR-004)
- [ ] Try submitting 201+ character title → validation error (FR-013)
- [ ] Add duplicate titles → both should appear (FR-014)

**Toggle Completion (FR-005, FR-006)**:
- [ ] Click incomplete todo → becomes complete with visual styling
- [ ] Click complete todo → becomes incomplete
- [ ] Rapid clicking works (FR-015)
- [ ] Refresh page → completion status persists (FR-009)

**Delete Todos (FR-007, FR-008)**:
- [ ] Click delete button → todo immediately removed
- [ ] No confirmation dialog appears (FR-008)
- [ ] Delete last todo → empty state message appears (FR-010)
- [ ] Refresh page → deleted todo stays deleted (FR-009)

### 5.2 Browser Developer Tools Checks
Open DevTools (F12):

**Console Tab**:
- No JavaScript errors
- API requests logged correctly

**Network Tab**:
- GET /todos returns 200 with JSON array
- POST /todos returns 201 with new todo object
- PATCH /todos/{id} returns 200 with updated todo
- DELETE /todos/{id} returns 204 (no content)
- CORS headers present on all responses

---

## Step 6: API Documentation

FastAPI automatically generates interactive API docs:

**Swagger UI**: http://localhost:8000/docs
**ReDoc**: http://localhost:8000/redoc

Use these to:
- Explore available endpoints
- Test API directly without frontend
- View request/response schemas
- See validation rules

---

## Common Issues & Solutions

### Issue: CORS Error in Browser Console
**Error**: `Access to fetch at 'http://localhost:8000/todos' from origin 'http://localhost:8001' has been blocked by CORS policy`

**Solution**: Verify CORS middleware in `main.py` includes both:
- `http://localhost:8001`
- `http://127.0.0.1:8001`

### Issue: Supabase Connection Error
**Error**: `supabase.exceptions.APIError: Invalid API key`

**Solution**:
1. Verify `.env` file has correct `SUPABASE_URL` and `SUPABASE_KEY`
2. Ensure you're using the **anon/public** key, not service_role key
3. Check project is not paused in Supabase dashboard

### Issue: Database Constraint Violation
**Error**: `new row for relation "todos" violates check constraint`

**Solution**: This is expected behavior for:
- Empty titles
- Whitespace-only titles
- Verify Pydantic validation is catching these before database layer

### Issue: Frontend Shows Blank Page
**Solution**:
1. Check browser console for JavaScript errors
2. Verify backend is running on port 8000
3. Check Network tab for failed API requests
4. Ensure `fetch` URLs use correct port (8000 for API)

---

## Development Workflow

### Adding a New Feature
1. Update `spec.md` with new requirements
2. Modify database schema via Supabase MCP if needed
3. Update `data-model.md` to reflect changes
4. Update `contracts/openapi.yaml` with new endpoints
5. Implement backend changes in `main.py`
6. Implement frontend changes in `index.html`
7. Test manually with browser DevTools

### Database Changes
**Always use MCP-first workflow**:
1. Test SQL changes via Supabase MCP tools
2. Verify with SELECT queries via MCP
3. Update application code to use new schema
4. Never write raw SQL in application code (Constitution III)

---

## Production Considerations (Out of Scope)

Per Constitution V, the following are **explicitly NOT implemented** in this workshop demo:

- Authentication/authorization
- Automated testing (unit, integration, e2e)
- Error monitoring and logging
- Performance optimization
- Containerization (Docker)
- CI/CD pipelines
- Database migrations
- Multi-user support
- API rate limiting
- HTTPS/SSL certificates

---

## Resources

**Documentation**:
- FastAPI: https://fastapi.tiangolo.com/
- Supabase Python: https://supabase.com/docs/reference/python/introduction
- Tailwind CSS: https://tailwindcss.com/docs

**Spec Files**:
- Feature Spec: `specs/001-todo-list-app/spec.md`
- Implementation Plan: `specs/001-todo-list-app/plan.md`
- Data Model: `specs/001-todo-list-app/data-model.md`
- API Contract: `specs/001-todo-list-app/contracts/openapi.yaml`
- Research: `specs/001-todo-list-app/research.md`

---

## Next Steps

After completing this quickstart:

1. **Run `/speckit.tasks`** to generate `tasks.md` with implementation tasks
2. **Run `/speckit.implement`** to execute tasks and build the application
3. **Experiment** with the application and add your own features
4. **Review** generated code to understand FastAPI and Supabase patterns

This workshop demo demonstrates:
- MCP-first database workflow (Constitution III)
- Single-file architecture (Constitution I)
- Modern async Python patterns
- RESTful API design
- Clean JavaScript organization
- Manual testing workflows
