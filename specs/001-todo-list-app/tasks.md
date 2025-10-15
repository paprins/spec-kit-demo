# Tasks: Todo List Application

**Feature**: 001-todo-list-app
**Branch**: `001-todo-list-app`
**Date**: 2025-10-15

**IMPORTANT**: NO automated testing per Constitution V (workshop demo scope). All validation is manual via browser testing.

## Overview

This task list implements a simple CRUD todo list application following a user story-driven approach. Each phase after Setup and Foundational corresponds to one complete user story that can be independently tested.

**Total Tasks**: 26
**Estimated Completion**: 4-6 hours for full implementation

## Task Format

Tasks follow this strict format:
```
- [ ] [TaskID] [P?] [Story?] Description with file path
```

Where:
- `TaskID`: Sequential number (T001, T002...)
- `[P]`: Parallelizable marker (different files, no dependencies)
- `[Story]`: User story label ([US1], [US2], etc.) - only in user story phases
- Description includes exact file path for implementation

---

## Phase 1: Setup (Project Initialization)

**Goal**: Create project structure, install dependencies, configure environment

**Tasks**:

- [ ] T001 [P] Create directory structure src/backend/ and src/frontend/
- [ ] T002 [P] Create requirements.txt in src/backend/requirements.txt with dependencies: fastapi==0.104.1, uvicorn[standard]==0.24.0, supabase==2.0.3, python-dotenv==1.0.0, pydantic-settings==2.0.3
- [ ] T003 Create Python virtual environment and install dependencies in src/backend/
- [ ] T004 Create .env file template in src/backend/.env with SUPABASE_URL and SUPABASE_KEY placeholders
- [ ] T005 Add src/backend/.env to .gitignore to prevent committing credentials

**Completion Criteria**:
- [ ] Directory structure matches constitution: src/backend/ and src/frontend/ exist
- [ ] requirements.txt contains all 5 dependencies
- [ ] Virtual environment created and dependencies installed without errors
- [ ] .env file exists with placeholder values
- [ ] .env is in .gitignore

---

## Phase 2: Foundational (Database & Backend Foundation)

**Goal**: Set up database schema via Supabase MCP and create backend foundation files

**CRITICAL**: Constitution III mandates MCP-first workflow - database MUST be created via Supabase MCP BEFORE writing application code.

**Tasks**:

- [ ] T006 Use Supabase MCP server to create todos table with schema: id UUID PRIMARY KEY DEFAULT uuid_generate_v4(), title VARCHAR(200) NOT NULL CHECK (length(trim(title)) > 0), completed BOOLEAN NOT NULL DEFAULT false, created_at TIMESTAMPTZ NOT NULL DEFAULT now()
- [ ] T007 Verify todos table structure via Supabase MCP inspection tools
- [ ] T008 Test CRUD operations via Supabase MCP: INSERT, SELECT, UPDATE, DELETE on todos table
- [ ] T009 Validate CHECK constraint via MCP by attempting to insert empty/whitespace-only titles (should fail)
- [ ] T010 Create database.py in src/backend/database.py with Supabase client initialization using pydantic-settings Settings class
- [ ] T011 Create main.py skeleton in src/backend/main.py with FastAPI app initialization, CORS middleware configuration for localhost:8001, and empty Pydantic model definitions (TodoBase, TodoCreate, TodoUpdate, Todo)

**Completion Criteria**:
- [ ] Supabase todos table exists with correct schema (verified via MCP)
- [ ] CHECK constraint validated (empty title rejected)
- [ ] database.py exports configured supabase client singleton
- [ ] main.py has FastAPI app with CORS configured for both http://localhost:8001 and http://127.0.0.1:8001
- [ ] All 4 Pydantic models defined with correct field types and validators per research.md

**Dependencies**: Phase 1 must be complete

---

## Phase 3: User Story 1 - View All Todos (P1)

**User Story**: A user opens the todo list application and immediately sees all their existing todos displayed in a list format, showing which items are completed and which are still pending.

**Independent Test Criteria**:
- Open application with pre-existing todos in database (created via Supabase MCP)
- All todos display in list format
- Completed vs incomplete todos have visual distinction
- Empty state message appears when no todos exist

**Tasks**:

- [ ] T012 [US1] Implement GET /todos endpoint in src/backend/main.py using supabase.table("todos").select("*").order("created_at").execute() pattern from research.md
- [ ] T013 [US1] Create index.html in src/frontend/index.html with HTML structure: head with Tailwind CDN script, body with todo-list container div, empty state message placeholder
- [ ] T014 [US1] Implement API layer in src/frontend/index.html: API_BASE_URL constant, apiRequest() wrapper function, fetchTodos() function per research.md patterns
- [ ] T015 [US1] Implement UI rendering functions in src/frontend/index.html: renderTodo() to create list item DOM elements with completed styling, renderAllTodos() to clear and repopulate list, escapeHtml() for XSS prevention
- [ ] T016 [US1] Implement init() function in src/frontend/index.html to fetch and render todos on page load with DOMContentLoaded check

**Completion Criteria**:
- [ ] GET /todos returns 200 with JSON array of todos ordered by created_at
- [ ] Frontend displays all todos from backend
- [ ] Completed todos have strikethrough styling (Tailwind: line-through text-gray-500)
- [ ] Empty state message "No todos yet. Add one above!" displays when list is empty
- [ ] Page load under 1 second (SC-002)
- [ ] No console errors in browser DevTools

**Manual Test Steps**:
1. Use Supabase MCP to insert 3 test todos (2 incomplete, 1 complete)
2. Start backend: `cd src/backend && uvicorn main:app --reload --port 8000`
3. Start frontend: `cd src/frontend && python -m http.server 8001`
4. Open http://localhost:8001 in browser
5. Verify all 3 todos display with correct styling
6. Use Supabase MCP to delete all todos
7. Refresh browser and verify empty state message appears

**Dependencies**: Phase 2 must be complete

---

## Phase 4: User Story 2 - Add New Todos (P2)

**User Story**: A user wants to add a new task to their list. They enter the todo title in a form input field and submit it, which adds the new todo to their list as an incomplete item.

**Independent Test Criteria**:
- User can type in input field and submit form
- New todo appears in list with incomplete status
- Input field clears after submission (FR-011)
- Empty/whitespace titles rejected with validation message (FR-004)
- Titles over 200 characters rejected (FR-013)
- Duplicate titles allowed (FR-014)

**Tasks**:

- [ ] T017 [US2] Implement POST /todos endpoint in src/backend/main.py with TodoCreate validation, supabase.table("todos").insert() call, and 201 status code response
- [ ] T018 [US2] Add form HTML to src/frontend/index.html: form element with id="todo-form", input with id="todo-title" and maxlength="200", submit button
- [ ] T019 [US2] Implement createTodo() API function in src/frontend/index.html using POST method with JSON body per research.md
- [ ] T020 [US2] Implement handleAddTodo() event handler in src/frontend/index.html: prevent default, validate input, call createTodo(), update local state, re-render list, clear input
- [ ] T021 [US2] Wire up form submit event listener in init() function in src/frontend/index.html

**Completion Criteria**:
- [ ] POST /todos with valid title returns 201 with created todo object
- [ ] POST /todos with empty title returns 422 validation error
- [ ] POST /todos with whitespace-only title returns 422 validation error
- [ ] POST /todos with 201-char title returns 422 validation error
- [ ] Frontend form adds todo to list on submit
- [ ] Input field clears after successful submission
- [ ] Duplicate titles can be added (no uniqueness check)
- [ ] User can add todo in under 5 seconds (SC-001)

**Manual Test Steps**:
1. Enter "Buy groceries" and submit → verify appears in list
2. Submit empty form → verify validation message
3. Enter only spaces and submit → verify rejected
4. Enter 201 characters and submit → verify rejected
5. Enter "Buy groceries" again → verify both appear (duplicates allowed)
6. Refresh page → verify todos persist (FR-009)

**Dependencies**: Phase 3 (US1) must be complete (depends on rendering functions)

---

## Phase 5: User Story 3 - Toggle Completion Status (P3)

**User Story**: A user can mark todos as complete by clicking on them, which visually indicates completion. Clicking a completed todo marks it as incomplete again, allowing users to reopen tasks.

**Independent Test Criteria**:
- Clicking incomplete todo marks it complete with visual change
- Clicking complete todo marks it incomplete
- Changes persist across page refreshes (FR-009)
- All clicks processed immediately without debouncing (FR-015)

**Tasks**:

- [ ] T022 [US3] Implement PATCH /todos/{todo_id} endpoint in src/backend/main.py with TodoUpdate validation, supabase.table("todos").update().eq("id", todo_id).execute() call, 404 handling for missing todos
- [ ] T023 [US3] Implement toggleTodo() API function in src/frontend/index.html using PATCH method with completed boolean in JSON body
- [ ] T024 [US3] Implement handleToggleTodo() event handler in src/frontend/index.html: call toggleTodo(), update local state, re-render list
- [ ] T025 [US3] Add onclick handler to todo spans in renderTodo() function in src/frontend/index.html to call handleToggleTodo with negated completion state

**Completion Criteria**:
- [ ] PATCH /todos/{id} with completed=true returns 200 with updated todo
- [ ] PATCH /todos/{id} with completed=false returns 200 with updated todo
- [ ] PATCH /todos/{invalid-id} returns 404 error
- [ ] Clicking incomplete todo adds strikethrough and gray color
- [ ] Clicking complete todo removes strikethrough and restores normal color
- [ ] Rapid clicking (3+ times in 1 second) processes all clicks (FR-015)
- [ ] Toggle persists after page refresh
- [ ] Single click toggles status (SC-003)

**Manual Test Steps**:
1. Click an incomplete todo → verify becomes complete with strikethrough
2. Click the same todo again → verify returns to incomplete state
3. Click rapidly 5 times → verify all clicks register (odd number = complete)
4. Refresh page → verify completion state persists
5. Open Network tab in DevTools → verify each click sends PATCH request

**Dependencies**: Phase 3 (US1) must be complete (depends on rendering), Phase 4 (US2) optional but recommended for adding test todos

---

## Phase 6: User Story 4 - Delete Todos (P4)

**User Story**: A user can permanently remove todos from their list by clicking a delete button associated with each todo item. This helps users maintain a clean list by removing completed or irrelevant tasks.

**Independent Test Criteria**:
- Delete button appears for each todo
- Clicking delete removes todo immediately (FR-008)
- No confirmation dialog appears (FR-008)
- Changes persist across page refreshes (FR-009)
- Empty state appears when last todo deleted

**Tasks**:

- [ ] T026 [US4] Implement DELETE /todos/{todo_id} endpoint in src/backend/main.py with supabase.table("todos").delete().eq("id", todo_id).execute() call, 204 no content response, 404 handling
- [ ] T027 [US4] Implement deleteTodo() API function in src/frontend/index.html using DELETE method and handling 204 response
- [ ] T028 [US4] Implement handleDeleteTodo() event handler in src/frontend/index.html: call deleteTodo(), filter from local state, re-render list
- [ ] T029 [US4] Add delete button to renderTodo() function in src/frontend/index.html with onclick handler calling handleDeleteTodo, styled with Tailwind: text-red-500 hover:text-red-700

**Completion Criteria**:
- [ ] DELETE /todos/{id} returns 204 no content
- [ ] DELETE /todos/{invalid-id} returns 404 error
- [ ] Clicking delete button removes todo from list immediately
- [ ] No confirmation dialog appears
- [ ] Deletion persists after page refresh
- [ ] Deleting last todo shows empty state message
- [ ] Delete button visible and clickable for all todos

**Manual Test Steps**:
1. Add 3 todos via form
2. Click delete on middle todo → verify immediately disappears
3. Verify no confirmation dialog appeared
4. Refresh page → verify todo still deleted (only 2 remain)
5. Delete remaining 2 todos → verify empty state appears
6. Check Network tab → verify DELETE requests returned 204

**Dependencies**: Phase 3 (US1) must be complete (depends on rendering), Phase 4 (US2) optional but recommended for adding test todos

---

## Phase 7: Polish & Cross-Cutting Concerns

**Goal**: Improve code clarity, styling, and user experience per Constitution IV

**Tasks**:

- [ ] T030 [P] Add Python type hints to all functions in src/backend/main.py and src/backend/database.py per Constitution IV
- [ ] T031 [P] Add Python docstrings to non-trivial functions in src/backend/main.py (route handlers, validation logic)
- [ ] T032 [P] Add JSDoc comments to API functions in src/frontend/index.html (apiRequest, fetchTodos, createTodo, etc.)
- [ ] T033 [P] Add comments for DOM manipulation logic in src/frontend/index.html (renderTodo, renderAllTodos)
- [ ] T034 Improve Tailwind styling in src/frontend/index.html for clean, modern interface (FR-012): card layout with shadow, proper spacing, hover states, responsive design
- [ ] T035 Add error handling UI in src/frontend/index.html: display user-friendly error messages instead of alert() for API failures
- [ ] T036 Test edge cases: 200-character title (should work), rapid form submissions, network errors, concurrent todo modifications

**Completion Criteria**:
- [ ] All Python functions have type hints
- [ ] Non-trivial Python functions have docstrings
- [ ] JavaScript API functions have JSDoc comments
- [ ] DOM manipulation has explanatory comments
- [ ] Interface looks clean and modern with proper spacing/colors (FR-012)
- [ ] Error messages display in UI instead of browser alerts
- [ ] Edge cases handled gracefully

**Manual Test Steps**:
1. Review code for type hints, docstrings, and comments per Constitution IV
2. Test interface on different screen sizes (1920x1080, 1366x768)
3. Test 200-character title (max allowed) → should work
4. Disconnect internet and try operations → verify error messages display
5. Submit form rapidly 5 times → verify all todos created
6. Open in Firefox, Safari, Chrome → verify consistent appearance

**Dependencies**: All user story phases complete

---

## Dependencies & Execution Order

### Critical Path (Must Complete in Order):

1. **Phase 1 (Setup)** → Foundation for all work
2. **Phase 2 (Foundational)** → Database + backend skeleton required before any features
3. **Phase 3 (US1 - View)** → Core rendering must exist before add/toggle/delete can work
4. **Phase 4 (US2 - Add)** → Can start after US1 complete
5. **Phase 5 (US3 - Toggle)** → Can start after US1 complete (independent from US2)
6. **Phase 6 (US4 - Delete)** → Can start after US1 complete (independent from US2/US3)
7. **Phase 7 (Polish)** → Only after all features implemented

### Parallel Opportunities:

**After Phase 2 Complete**:
- US2, US3, US4 can be implemented in parallel EXCEPT:
  - All depend on US1's rendering functions being complete first
  - Once US1 complete, US2/US3/US4 are independent and can be done in any order or concurrently

**Within Phase 7**:
- T030, T031, T032, T033 can be done in parallel (different concerns)
- T034, T035, T036 should be sequential (UI → Error handling → Testing)

### User Story Dependency Graph:

```
Phase 1 (Setup)
    ↓
Phase 2 (Foundational - Database + Backend Skeleton)
    ↓
Phase 3 (US1 - View Todos) ← BLOCKING for all other stories
    ↓
    ├─→ Phase 4 (US2 - Add Todos) [Independent]
    ├─→ Phase 5 (US3 - Toggle Completion) [Independent]
    └─→ Phase 6 (US4 - Delete Todos) [Independent]
            ↓
        Phase 7 (Polish)
```

---

## Implementation Strategy

### MVP Scope (Minimum Viable Product):

**Deliver value with just Phase 3 (US1)**:
- Users can view existing todos
- Demonstrates database integration
- Validates end-to-end architecture
- Testable independently

**Recommended MVP**: Phases 1, 2, 3, 4 (View + Add)
- Provides basic utility (can add and see todos)
- ~2-3 hours of work
- Delivers SC-001 and SC-002 success criteria

### Incremental Delivery:

1. **Milestone 1** (Phases 1-3): View todos - 2 hours
2. **Milestone 2** (+Phase 4): Add todos - 1 hour
3. **Milestone 3** (+Phase 5): Toggle completion - 1 hour
4. **Milestone 4** (+Phase 6): Delete todos - 1 hour
5. **Milestone 5** (+Phase 7): Polish - 1-2 hours

Each milestone is independently testable and delivers incremental value.

---

## Constitution Compliance Reminders

**Per Constitution III (Supabase-First Workflow)**:
- T006-T009 MUST use Supabase MCP server (NOT SQL in application code)
- Database must be created and validated BEFORE writing application code
- All CRUD operations use supabase.table() methods, never raw SQL

**Per Constitution I (Simplicity First)**:
- All backend code in single main.py file (plus database.py for client)
- All frontend code in single index.html file
- No build tools, npm, or bundlers

**Per Constitution V (Workshop Demo Scope)**:
- NO automated tests (manual browser testing only)
- NO authentication/authorization
- Basic error handling only (try/catch, alert or simple UI messages)

**Per Constitution IV (Code Clarity)**:
- Type hints on all Python functions (Phase 7, T030)
- Docstrings for non-trivial functions (Phase 7, T031)
- Comments for API calls and DOM manipulation (Phase 7, T032-T033)

---

## Validation Checklist

Before considering implementation complete, verify:

**Functional Requirements**:
- [ ] FR-001: All todos display in list format with title and completion status
- [ ] FR-002: Input form exists for entering todo titles
- [ ] FR-003: New todos created with incomplete status by default
- [ ] FR-004: Empty/whitespace-only titles rejected
- [ ] FR-013: Titles over 200 characters rejected
- [ ] FR-014: Duplicate titles allowed
- [ ] FR-005: Clicking todo toggles completion status
- [ ] FR-015: Rapid clicks all processed (no debouncing)
- [ ] FR-006: Complete vs incomplete todos visually distinguished
- [ ] FR-007: Delete button exists for each todo
- [ ] FR-008: Delete removes todo without confirmation
- [ ] FR-009: Changes persist across page refreshes
- [ ] FR-010: Empty state displays when no todos exist
- [ ] FR-011: Input clears after adding todo
- [ ] FR-012: Interface looks clean and modern

**Success Criteria**:
- [ ] SC-001: Can add todo in under 5 seconds
- [ ] SC-002: Todos load in under 1 second
- [ ] SC-003: Single click toggles completion
- [ ] SC-004: Changes persist across browser sessions
- [ ] SC-005: Interface renders cleanly on desktop browsers
- [ ] SC-006: Intuitive first-use experience (subjective)

**Technical Criteria**:
- [ ] Database schema matches data-model.md specification
- [ ] All 4 API endpoints implemented per openapi.yaml contract
- [ ] CORS configured for both localhost and 127.0.0.1
- [ ] No console errors in browser DevTools
- [ ] No Python errors in uvicorn server logs
- [ ] Code follows patterns documented in research.md
- [ ] Type hints and docstrings per Constitution IV

---

## File Manifest

**Files Created by This Implementation**:

```
src/backend/
├── .env                 # Environment configuration (T004)
├── requirements.txt     # Python dependencies (T002)
├── database.py          # Supabase client initialization (T010)
└── main.py              # FastAPI app with all routes and models (T011-T026)

src/frontend/
└── index.html           # Complete UI with inline JS and Tailwind (T013-T029)

.gitignore               # Updated to exclude .env (T005)
```

**Total Lines of Code (Estimated)**:
- main.py: ~200 lines (models + 4 routes + CORS)
- database.py: ~20 lines (settings + client)
- index.html: ~300 lines (HTML + CSS + ~200 lines JS)
- **Total: ~520 lines** across 3 implementation files

---

## Notes

- All task IDs (T001-T036) are unique and sequential
- [P] marker indicates tasks that can run in parallel with others in the same phase
- [US1-US4] markers indicate which user story each task implements
- File paths are absolute from repository root
- Manual testing steps provided for each user story phase
- No automated tests per Constitution V (workshop demo scope)
- MCP-first workflow enforced in Phase 2 per Constitution III

**Next Steps After Task Generation**:
1. Review this task list for completeness
2. Run `/speckit.implement` to execute all tasks automatically
3. Or manually implement tasks following the order specified in Dependencies section
