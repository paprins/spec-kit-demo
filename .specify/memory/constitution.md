<!--
Sync Impact Report:
Version: 0.0.0 → 1.0.0
Rationale: Initial constitution creation for Simple CRUD Web Application demo project

Added Principles:
- I. Simplicity First (single-file architecture, no build tools)
- II. Technology Stack Constraints (FastAPI, Supabase, Tailwind CDN)
- III. Supabase-First Database Workflow (NON-NEGOTIABLE - MCP server mandatory)
- IV. Code Clarity (type hints, docstrings, comments)
- V. Workshop Demo Scope (no auth, no testing, no production concerns)

Added Sections:
- Project Structure (mandated src/backend and src/frontend layout)
- Development Workflow (database-first sequence)

Templates Status:
- plan-template.md: ✅ Updated with constitution checks, technical context, and project structure
- spec-template.md: ✅ Validated (no changes needed - technology-agnostic user stories)
- tasks-template.md: ✅ Updated with constitution reminders, Supabase MCP requirements, single-file tasks
- commands/*.md: N/A (no command files found in repository)

Template Changes Made:
1. plan-template.md:
   - Added complete Constitution Check section with all 5 principles
   - Updated Technical Context with fixed values (Python 3.12, FastAPI, Supabase, no testing)
   - Replaced generic project structure with constitution-mandated structure

2. tasks-template.md:
   - Updated header to clarify NO automated testing (Constitution V)
   - Changed Path Conventions to reflect single-file architecture
   - Added Supabase MCP requirements in Phase 2 (Foundational)
   - Removed all test task examples from user story phases
   - Updated user story task examples to reflect single-file editing
   - Modified Polish phase to focus on code clarity, not production concerns
   - Updated Dependencies section to emphasize MCP-first workflow
   - Adjusted Parallel Example to reflect single-file limitations
   - Enhanced Notes section with constitution reminders

3. spec-template.md:
   - No changes needed (template is correctly technology-agnostic)

Follow-up TODOs: None - all templates synchronized with constitution principles
-->

# Simple CRUD Web Application Constitution

## Core Principles

### I. Simplicity First

**Architecture MUST be minimal:**
- Backend: Single `main.py` file with FastAPI routes
- Frontend: Single `index.html` file with inline JavaScript
- No build tools, no npm, no bundlers, no compilation steps
- All functionality in minimal files

**Rationale:** This is a workshop demo prioritizing learning and clarity over scalability. Complex tooling and multi-file architectures create unnecessary friction for demonstration purposes.

### II. Technology Stack Constraints

**The stack is fixed and NON-NEGOTIABLE:**
- Backend: FastAPI with Python 3.12
- Frontend: Plain HTML + Tailwind CSS (via CDN only)
- Database: Supabase
- HTTP client: Browser native `fetch` API
- No React, Vue, Angular, or any JavaScript frameworks
- No preprocessors, transpilers, or build pipelines

**Rationale:** Constraints reduce decision fatigue and ensure all participants work with identical tooling. CDN-based Tailwind eliminates build complexity while providing modern styling capabilities.

### III. Supabase-First Database Workflow (NON-NEGOTIABLE)

**Before writing ANY application code that touches the database, you MUST:**
1. Use the Supabase MCP server to create and verify database schemas
2. Validate table structures, relationships, and constraints via MCP
3. Test queries and inspect data using MCP tools

**Application integration MUST:**
- Use the `supabase-py` client library for all database operations
- Connect via environment variables: `SUPABASE_URL`, `SUPABASE_KEY`
- Perform all CRUD operations using `supabase.table()` methods
- Never use raw SQL strings in application code

**MCP server usage is MANDATORY for:**
- Schema creation and migrations
- Table structure verification
- Data inspection and debugging
- Query testing before implementation

**Rationale:** The Supabase MCP server provides schema-first development and catches database issues early. Requiring its use before implementation prevents schema mismatches and ensures database structure is validated independently of application code.

### IV. Code Clarity

**Python code MUST include:**
- Type hints on all function signatures
- Docstrings for non-trivial functions
- Comments explaining business logic (not obvious syntax)

**JavaScript code MUST include:**
- Comments for API endpoint calls
- Comments for non-obvious DOM manipulations
- Clear function names that explain intent

**Rationale:** Workshop code serves as reference material. Clarity and readability are more important than cleverness or brevity.

### V. Workshop Demo Scope

**The following are explicitly OUT OF SCOPE:**
- Authentication and authorization
- Automated testing (unit, integration, e2e)
- Error handling beyond basic try/catch
- Production deployment considerations
- Performance optimization
- Accessibility beyond semantic HTML

**Rationale:** This is a demonstration project for teaching CRUD concepts with modern tools. Production concerns would obscure the learning objectives and add unnecessary complexity.

## Project Structure

**The repository MUST follow this exact structure:**

```
src/
├── backend/
│   ├── main.py          # FastAPI application with all routes
│   ├── database.py      # Supabase client initialization
│   └── requirements.txt # Python dependencies
└── frontend/
    └── index.html       # Complete UI with inline JS and Tailwind CDN
```

**Rationale:** A flat, predictable structure makes it trivial for workshop participants to navigate and understand the entire codebase in minutes.

## Development Workflow

**Database-first development sequence:**
1. Define data model requirements
2. Use Supabase MCP server to create tables and relationships
3. Verify schema structure via MCP inspection tools
4. Implement backend routes using `supabase-py` client
5. Build frontend UI with `fetch` calls to backend
6. Manual testing via browser

**Code changes MUST:**
- Be tested manually in browser before considering complete
- Include inline comments for non-obvious logic
- Use descriptive variable and function names

**Rationale:** Manual workflows are appropriate for demo projects and teach debugging skills. The database-first sequence ensures data integrity from the start.

## Governance

**This constitution is the authoritative source for all project decisions.**

**Amendments:**
- Require explicit justification for why a principle must change
- Must update this document with new version number and amendment date
- Must propagate changes to dependent templates and documentation

**Compliance:**
- All feature implementations must align with these principles
- Deviations require documented exception with rationale
- Complexity beyond these constraints must be justified or rejected

**Version**: 1.0.0 | **Ratified**: 2025-10-15 | **Last Amended**: 2025-10-15
