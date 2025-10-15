# Implementation Plan: Todo List Application

**Branch**: `001-todo-list-app` | **Date**: 2025-10-15 | **Spec**: [spec.md](./spec.md)
**Input**: Feature specification from `/specs/001-todo-list-app/spec.md`

**Note**: This template is filled in by the `/speckit.plan` command. See `.specify/templates/commands/plan.md` for the execution workflow.

## Summary

Build a simple CRUD todo list application with FastAPI backend and plain HTML/Tailwind frontend. Users can view, add, toggle completion, and delete todos. The system uses Supabase for persistence and follows a minimalist single-file architecture for both backend (main.py) and frontend (index.html) as a workshop demonstration.

## Technical Context

<!--
  ACTION REQUIRED: Replace the content in this section with the technical details
  for the project. The structure here is presented in advisory capacity to guide
  the iteration process.
-->

**Language/Version**: Python 3.12
**Primary Dependencies**: FastAPI, supabase-py, uvicorn
**Storage**: Supabase (PostgreSQL with REST API)
**Testing**: None (workshop demo - manual testing only per Constitution V)
**Target Platform**: Local development server (workshop demo)
**Project Type**: Simple CRUD web application (constitution-mandated structure)
**Performance Goals**: N/A (workshop demo scope)
**Constraints**: Single-file backend (main.py), single-file frontend (index.html), no build tools
**Scale/Scope**: Workshop demonstration - small dataset, single user, local development

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

**I. Simplicity First**
- ✓ Backend uses single main.py file
- ✓ Frontend uses single index.html file with inline JavaScript
- ✓ No build tools, npm, bundlers, or compilation steps

**II. Technology Stack Constraints**
- ✓ Backend: FastAPI with Python 3.12
- ✓ Frontend: Plain HTML + Tailwind CSS (CDN only)
- ✓ Database: Supabase
- ✓ No JavaScript frameworks (React/Vue/Angular)

**III. Supabase-First Database Workflow**
- ✓ Supabase MCP server used for schema creation before implementation
- ✓ Database schema validated via MCP tools
- ✓ Application uses supabase-py client library
- ✓ No raw SQL strings in application code

**IV. Code Clarity**
- ✓ Python: Type hints on all functions
- ✓ Python: Docstrings for non-trivial functions
- ✓ JavaScript: Comments for API calls and DOM manipulations

**V. Workshop Demo Scope**
- ✓ No authentication/authorization
- ✓ No automated testing
- ✓ Basic try/catch error handling only
- ✓ No production deployment considerations

## Project Structure

### Documentation (this feature)

```
specs/001-todo-list-app/
├── plan.md              # This file (/speckit.plan command output)
├── research.md          # Phase 0 output (/speckit.plan command)
├── data-model.md        # Phase 1 output (/speckit.plan command)
├── quickstart.md        # Phase 1 output (/speckit.plan command)
├── contracts/           # Phase 1 output (/speckit.plan command)
└── tasks.md             # Phase 2 output (/speckit.tasks command - NOT created by /speckit.plan)
```

### Source Code (repository root)

**Constitution-mandated structure for Simple CRUD Web Application:**

```
src/
├── backend/
│   ├── main.py          # FastAPI application with all routes
│   ├── database.py      # Supabase client initialization
│   └── requirements.txt # Python dependencies (fastapi, supabase-py, uvicorn)
└── frontend/
    └── index.html       # Complete UI with inline JavaScript and Tailwind CDN
```

**Structure Decision**: This project follows the Simple CRUD Web Application structure mandated by the constitution. The structure enforces:
- Single main.py for all backend logic
- Single index.html for all frontend code
- No test directories (testing explicitly out of scope per Constitution Principle V)
- No build tooling or transpilation steps

## Complexity Tracking

*Fill ONLY if Constitution Check has violations that must be justified*

| Violation | Why Needed | Simpler Alternative Rejected Because |
|-----------|------------|-------------------------------------|
| [e.g., 4th project] | [current need] | [why 3 projects insufficient] |
| [e.g., Repository pattern] | [specific problem] | [why direct DB access insufficient] |

