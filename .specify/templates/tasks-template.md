---
description: "Task list template for feature implementation"
---

# Tasks: [FEATURE NAME]

**Input**: Design documents from `/specs/[###-feature-name]/`
**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

**Tests**: Per Constitution Principle V (Workshop Demo Scope), automated testing is OUT OF SCOPE. Do NOT include test tasks. Manual browser testing only.

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`
- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Path Conventions (Constitution-Mandated)
- **Backend**: `src/backend/main.py` (single file for all FastAPI routes)
- **Database**: `src/backend/database.py` (Supabase client initialization)
- **Frontend**: `src/frontend/index.html` (single file with inline JavaScript)
- **Dependencies**: `src/backend/requirements.txt`
- **No test directories** (testing out of scope per Constitution V)

<!-- 
  ============================================================================
  IMPORTANT: The tasks below are SAMPLE TASKS for illustration purposes only.
  
  The /speckit.tasks command MUST replace these with actual tasks based on:
  - User stories from spec.md (with their priorities P1, P2, P3...)
  - Feature requirements from plan.md
  - Entities from data-model.md
  - Endpoints from contracts/
  
  Tasks MUST be organized by user story so each story can be:
  - Implemented independently
  - Tested independently
  - Delivered as an MVP increment
  
  DO NOT keep these sample tasks in the generated tasks.md file.
  ============================================================================
-->

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Project initialization and basic structure per constitution

- [ ] T001 Create src/backend/ and src/frontend/ directory structure
- [ ] T002 Create src/backend/requirements.txt with fastapi, supabase-py, uvicorn
- [ ] T003 Initialize Supabase project and obtain SUPABASE_URL and SUPABASE_KEY
- [ ] T004 [P] Create src/backend/database.py with Supabase client initialization

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Core infrastructure that MUST be complete before ANY user story can be implemented

**⚠️ CRITICAL**: No user story work can begin until this phase is complete
**🔧 SUPABASE MCP REQUIRED**: Database schema MUST be created via Supabase MCP server before implementation (Constitution III)

Foundational tasks (adjust based on your feature requirements):

- [ ] T005 **[MCP REQUIRED]** Use Supabase MCP server to create database tables and schema
- [ ] T006 **[MCP REQUIRED]** Verify table structures and relationships via Supabase MCP inspection
- [ ] T007 [P] Create src/backend/main.py with FastAPI app initialization and CORS
- [ ] T008 [P] Create src/frontend/index.html with Tailwind CSS CDN and basic HTML structure
- [ ] T009 Add environment variable handling for SUPABASE_URL and SUPABASE_KEY

**Checkpoint**: Foundation ready - user story implementation can now begin in parallel

**Constitution Notes**:
- No authentication/authorization (Constitution V - Workshop Demo Scope)
- No automated test setup (Constitution V - out of scope)
- Database schema created via MCP server BEFORE writing application code (Constitution III - NON-NEGOTIABLE)

---

## Phase 3: User Story 1 - [Title] (Priority: P1) 🎯 MVP

**Goal**: [Brief description of what this story delivers]

**Independent Test**: Manual browser testing - [How to verify this story works on its own]

### Implementation for User Story 1

**Constitution Reminder**: All code in single files (main.py for backend, index.html for frontend)

- [ ] T010 [US1] Add FastAPI route for [operation] in src/backend/main.py with type hints and docstring
- [ ] T011 [US1] Implement Supabase query using supabase.table() methods in src/backend/main.py
- [ ] T012 [US1] Add try/catch error handling for [operation] route
- [ ] T013 [US1] Add HTML UI section for [feature] in src/frontend/index.html
- [ ] T014 [US1] Add JavaScript fetch() call to backend endpoint (with comments) in src/frontend/index.html
- [ ] T015 [US1] Add Tailwind CSS styling for [feature] UI components
- [ ] T016 [US1] Manual browser test: [specific test scenario]

**Checkpoint**: At this point, User Story 1 should be fully functional and testable manually in browser

---

## Phase 4: User Story 2 - [Title] (Priority: P2)

**Goal**: [Brief description of what this story delivers]

**Independent Test**: Manual browser testing - [How to verify this story works on its own]

### Implementation for User Story 2

**Constitution Reminder**: All code in single files (main.py for backend, index.html for frontend)

- [ ] T017 [US2] Add FastAPI route for [operation] in src/backend/main.py with type hints and docstring
- [ ] T018 [US2] Implement Supabase query using supabase.table() methods in src/backend/main.py
- [ ] T019 [US2] Add try/catch error handling for [operation] route
- [ ] T020 [US2] Add HTML UI section for [feature] in src/frontend/index.html
- [ ] T021 [US2] Add JavaScript fetch() call to backend endpoint (with comments) in src/frontend/index.html
- [ ] T022 [US2] Add Tailwind CSS styling for [feature] UI components
- [ ] T023 [US2] Manual browser test: [specific test scenario]

**Checkpoint**: At this point, User Stories 1 AND 2 should both work independently in browser

---

## Phase 5: User Story 3 - [Title] (Priority: P3)

**Goal**: [Brief description of what this story delivers]

**Independent Test**: Manual browser testing - [How to verify this story works on its own]

### Implementation for User Story 3

**Constitution Reminder**: All code in single files (main.py for backend, index.html for frontend)

- [ ] T024 [US3] Add FastAPI route for [operation] in src/backend/main.py with type hints and docstring
- [ ] T025 [US3] Implement Supabase query using supabase.table() methods in src/backend/main.py
- [ ] T026 [US3] Add try/catch error handling for [operation] route
- [ ] T027 [US3] Add HTML UI section for [feature] in src/frontend/index.html
- [ ] T028 [US3] Add JavaScript fetch() call to backend endpoint (with comments) in src/frontend/index.html
- [ ] T029 [US3] Add Tailwind CSS styling for [feature] UI components
- [ ] T030 [US3] Manual browser test: [specific test scenario]

**Checkpoint**: All user stories should now be independently functional in browser

---

[Add more user story phases as needed, following the same pattern]

---

## Phase N: Polish & Cross-Cutting Concerns

**Purpose**: Final touches and code clarity improvements

**Constitution Reminder**: No production concerns, no automated testing, no authentication

- [ ] TXXX Review all Python functions for type hints and docstrings (Constitution IV)
- [ ] TXXX Review all JavaScript for API call comments (Constitution IV)
- [ ] TXXX Verify all Supabase queries use supabase.table() methods (Constitution III)
- [ ] TXXX Manual browser testing across all user stories
- [ ] TXXX Code readability review (Constitution IV - clarity over cleverness)
- [ ] TXXX Verify single-file architecture maintained (Constitution I)

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - can start immediately
- **Foundational (Phase 2)**: Depends on Setup completion - BLOCKS all user stories
- **User Stories (Phase 3+)**: All depend on Foundational phase completion
  - User stories can then proceed in parallel (if staffed)
  - Or sequentially in priority order (P1 → P2 → P3)
- **Polish (Final Phase)**: Depends on all desired user stories being complete

### User Story Dependencies

- **User Story 1 (P1)**: Can start after Foundational (Phase 2) - No dependencies on other stories
- **User Story 2 (P2)**: Can start after Foundational (Phase 2) - May integrate with US1 but should be independently testable
- **User Story 3 (P3)**: Can start after Foundational (Phase 2) - May integrate with US1/US2 but should be independently testable

### Within Each User Story

- **NO automated tests** (Constitution V - Workshop Demo Scope)
- Database schema (via Supabase MCP) before application code (Constitution III - NON-NEGOTIABLE)
- Backend routes before frontend UI
- Inline JavaScript after HTML structure
- Manual browser testing after each story implementation

### Parallel Opportunities

- All Setup tasks marked [P] can run in parallel
- All Foundational tasks marked [P] can run in parallel (within Phase 2)
- Once Foundational phase completes, all user stories can start in parallel (if team capacity allows)
- All tests for a user story marked [P] can run in parallel
- Models within a story marked [P] can run in parallel
- Different user stories can be worked on in parallel by different team members

---

## Parallel Example: User Story 1

```bash
# Example: Multiple backend routes that don't conflict can be added in parallel:
Task: "Add FastAPI GET route for [resource] in src/backend/main.py"
Task: "Add FastAPI POST route for [resource] in src/backend/main.py"

# Example: UI sections and styling can be worked on in parallel (if by different people):
Task: "Add HTML structure for [feature] in src/frontend/index.html"
Task: "Add Tailwind CSS classes for [feature] in src/frontend/index.html"
```

**Note**: In practice, parallel work is limited by single-file architecture. Most tasks will be sequential within each file.

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup
2. Complete Phase 2: Foundational (CRITICAL - blocks all stories)
3. Complete Phase 3: User Story 1
4. **STOP and VALIDATE**: Test User Story 1 independently
5. Deploy/demo if ready

### Incremental Delivery

1. Complete Setup + Foundational → Foundation ready
2. Add User Story 1 → Test independently → Deploy/Demo (MVP!)
3. Add User Story 2 → Test independently → Deploy/Demo
4. Add User Story 3 → Test independently → Deploy/Demo
5. Each story adds value without breaking previous stories

### Parallel Team Strategy

With multiple developers:

1. Team completes Setup + Foundational together
2. Once Foundational is done:
   - Developer A: User Story 1
   - Developer B: User Story 2
   - Developer C: User Story 3
3. Stories complete and integrate independently

---

## Notes

- [P] tasks = different files/sections, no dependencies (limited in single-file architecture)
- [Story] label maps task to specific user story for traceability
- Each user story should be independently completable and testable via browser
- **NO automated tests** - manual browser testing only (Constitution V)
- **Database schema via Supabase MCP FIRST** before any application code (Constitution III - NON-NEGOTIABLE)
- All code clarity requirements: type hints, docstrings, comments (Constitution IV)
- Commit after each task or logical group
- Stop at any checkpoint to validate story independently in browser
- Avoid: vague tasks, cross-story dependencies that break independence, adding build tools or frameworks



