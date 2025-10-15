# Research: Todo List Application

**Feature**: 001-todo-list-app
**Date**: 2025-10-15
**Purpose**: Phase 0 design research for FastAPI + Supabase + Tailwind todo list application

## Overview

This document consolidates research findings for building a workshop-focused CRUD application with:
- Backend: Single-file FastAPI application (main.py)
- Frontend: Single-file HTML with inline JavaScript and Tailwind CDN
- Database: Supabase with MCP-first workflow
- Architecture: Minimalist single-file approach per Constitution I

---

## 1. FastAPI Single-File Application Structure

**Decision**: Use grouped route definitions with clear section organization within main.py

**Rationale**:
- Workshop-appropriate: Section comments make code easy to navigate for learners
- Separation of concerns within one file: Pydantic models at top, routes grouped below
- FastAPI conventions: Uses response_model for automatic validation and OpenAPI docs
- Async by default: Demonstrates modern Python async patterns
- RESTful patterns: Path parameters for single resources (DELETE /todos/{id})

**Alternatives Considered**:
- Multiple files with APIRouter: Standard for production but violates Constitution I single-file requirement
- Sync functions only: Would work but async is FastAPI best practice
- Procedural with global state: Simpler but doesn't teach dependency injection

**Implementation Pattern**:
```python
# Pydantic Models Section
class TodoCreate(BaseModel): ...
class TodoUpdate(BaseModel): ...
class Todo(BaseModel): ...

# CRUD Routes Section
@app.get("/todos", response_model=List[Todo])
async def get_todos(): ...

@app.post("/todos", response_model=Todo)
async def create_todo(todo: TodoCreate): ...
```

---

## 2. FastAPI CORS Configuration for Local Development

**Decision**: Use CORSMiddleware with explicit localhost origins for frontend port

**Rationale**:
- Explicit origins over wildcards: Teaches CORS concepts without security anti-patterns
- Both localhost and 127.0.0.1: Browsers treat these as different origins
- allow_credentials=True: Demonstrates full CORS configuration
- Wildcard methods/headers: Simplifies workshop setup while maintaining origin restrictions
- Middleware ordering: CORS must be added before route definitions

**Alternatives Considered**:
- Wildcard origins (allow_origins=["*"]): Simpler but doesn't work with credentials and teaches bad habits
- No CORS (serve frontend from FastAPI): Violates single-file frontend principle
- Proxy-based approach: Adds unnecessary complexity for workshop demo

**Implementation Pattern**:
```python
from fastapi.middleware.cors import CORSMiddleware

origins = [
    "http://localhost:8001",      # Frontend serving port
    "http://127.0.0.1:8001",      # Alternative localhost format
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

---

## 3. Supabase-py Client Library Usage

**Decision**: Create singleton client instance with explicit error handling for all operations

**Rationale**:
- Singleton client: Supabase-py best practice is single instance, not per-request creation
- Execute pattern: All operations end with .execute() returning response with .data and .error
- Chaining syntax: table().select().eq().execute() is more readable than nested calls
- Error handling via response inspection: Supabase-py returns errors in response object
- Type hints on wrapper functions: Demonstrates Python typing

**Alternatives Considered**:
- Async client (acreate_client): Only needed for realtime features, adds complexity
- Raw SQL via .rpc(): Violates Constitution III (no raw SQL in application code)
- Exception-based error handling with .throwOnError(): Less explicit than checking response

**Implementation Pattern**:
```python
from supabase import create_client, Client

supabase: Client = create_client(supabase_url, supabase_key)

def get_all_todos():
    response = supabase.table("todos").select("*").order("created_at").execute()
    return response.data

def create_todo(title: str):
    response = supabase.table("todos").insert({
        "title": title,
        "completed": False
    }).execute()
    return response.data[0]

def update_todo(todo_id: str, completed: bool):
    response = supabase.table("todos").update({
        "completed": completed
    }).eq("id", todo_id).execute()
    return response.data[0] if response.data else None
```

---

## 4. Environment Variable Management

**Decision**: Use python-dotenv with .env file and Pydantic Settings for validation

**Rationale**:
- Pydantic Settings over os.getenv: Provides validation, type conversion, clear error messages
- Explicit Field(...) with env parameter: Makes environment variable names visible in code
- load_dotenv() for local development: Automatically loads .env file
- Settings singleton pattern: Create once, import everywhere
- Type safety: Settings class provides autocomplete and type checking

**Alternatives Considered**:
- Plain os.getenv(): Simpler but no validation, can fail silently with None values
- Config file (JSON/YAML): More complex, secrets in files harder to secure
- Hard-coded values: Absolutely forbidden for credentials

**Implementation Pattern**:
```python
from dotenv import load_dotenv
from pydantic_settings import BaseSettings
from pydantic import Field

load_dotenv()

class Settings(BaseSettings):
    supabase_url: str = Field(..., env="SUPABASE_URL")
    supabase_key: str = Field(..., env="SUPABASE_KEY")

    class Config:
        env_file = ".env"
        case_sensitive = False

settings = Settings()
supabase: Client = create_client(settings.supabase_url, settings.supabase_key)
```

---

## 5. FastAPI Response Models and Pydantic Schemas

**Decision**: Use separate Pydantic models for Create, Update, and Response with clear inheritance

**Rationale**:
- Three-schema pattern (Create/Update/Response): Standard FastAPI practice
- Field validation with min/max: Enforces FR-013 (200 char limit) at API layer
- Custom validators: FR-004 (no empty/whitespace titles) enforced via Pydantic validator
- Optional fields in Update: PATCH semantics with exclude_unset=True
- response_model parameter: Automatic serialization, validation, and OpenAPI docs
- BaseModel inheritance: DRY principle for shared validation logic

**Alternatives Considered**:
- Single schema for everything: Simpler but loses semantic meaning and validation control
- Dataclasses instead of Pydantic: No automatic validation or FastAPI integration
- Dict-based approach: No type safety, no validation, poor developer experience

**Implementation Pattern**:
```python
from pydantic import BaseModel, Field, field_validator

class TodoBase(BaseModel):
    title: str = Field(..., min_length=1, max_length=200)

class TodoCreate(TodoBase):
    @field_validator('title')
    @classmethod
    def title_not_empty_whitespace(cls, v: str) -> str:
        if not v.strip():
            raise ValueError('Title cannot be empty or whitespace only')
        return v.strip()

class TodoUpdate(BaseModel):
    title: Optional[str] = Field(None, min_length=1, max_length=200)
    completed: Optional[bool] = None

class Todo(TodoBase):
    id: str
    completed: bool
    created_at: datetime
```

---

## 6. Frontend Fetch API Patterns

**Decision**: Use async/await fetch with consistent error handling wrapper function

**Rationale**:
- Async/await over .then() chains: More readable, easier error handling with try/catch
- Centralized error handling: apiRequest wrapper prevents repetition
- Response.ok checking: Essential - fetch doesn't reject on HTTP errors (4xx, 5xx)
- JSON error parsing with fallback: FastAPI returns {"detail": "message"}
- 204 No Content handling: DELETE endpoints often return no body
- Content-Type header: Required for FastAPI to parse JSON request body
- Separated API functions from UI logic: Testable, reusable, clear responsibilities

**Alternatives Considered**:
- Promise chains (.then/.catch): Works but less readable
- XMLHttpRequest: Legacy API, verbose, unnecessary
- Axios library: Adds dependency, unnecessary for simple fetch needs
- Global error toast: Loses context for specific operation failures

**Implementation Pattern**:
```javascript
const API_BASE_URL = 'http://localhost:8000';

async function apiRequest(endpoint, options = {}) {
    try {
        const response = await fetch(`${API_BASE_URL}${endpoint}`, {
            headers: {
                'Content-Type': 'application/json',
                ...options.headers,
            },
            ...options,
        });

        if (!response.ok) {
            const errorData = await response.json().catch(() => ({}));
            throw new Error(errorData.detail || `HTTP ${response.status}`);
        }

        if (response.status === 204) return null;
        return await response.json();
    } catch (error) {
        console.error('API request failed:', error);
        throw error;
    }
}

async function fetchTodos() {
    return await apiRequest('/todos');
}

async function createTodo(title) {
    return await apiRequest('/todos', {
        method: 'POST',
        body: JSON.stringify({ title: title.trim() }),
    });
}
```

---

## 7. Inline JavaScript Organization

**Decision**: Use modular function organization with clear sections and init pattern

**Rationale**:
- Clear section comments: Creates visual structure in long file
- Separation of concerns: API, rendering, event handlers, initialization in distinct sections
- Function-per-responsibility: Small, focused functions
- JSDoc comments: Documents for workshop reference
- Init pattern: Single entry point prevents race conditions
- Local state caching: Reduces API calls, enables optimistic UI updates
- XSS prevention: escapeHtml() prevents injection attacks
- Inline event handlers: onclick in HTML simpler for demos
- DOMContentLoaded check: Handles both deferred and non-deferred script execution

**Alternatives Considered**:
- No sections/organization: Quick to write but unmaintainable beyond 100 lines
- Module pattern with IIFE: More encapsulation but adds complexity
- All event listeners in init(): More "proper" but harder to trace for beginners
- Framework approach (React/Vue): Violates Constitution II (no frameworks)
- Separate script tags per section: Increases HTML noise without benefit

**Implementation Pattern**:
```javascript
// CONFIGURATION
const API_BASE_URL = 'http://localhost:8000';

// STATE MANAGEMENT
let todos = [];

// API LAYER
async function apiRequest(endpoint, options) { ... }
async function fetchTodos() { ... }

// UI RENDERING
function renderTodo(todo) { ... }
function renderAllTodos() { ... }
function escapeHtml(text) { ... }

// EVENT HANDLERS
async function handleAddTodo(event) { ... }
async function handleToggleTodo(todoId, completed) { ... }

// INITIALIZATION
async function init() {
    todos = await fetchTodos();
    renderAllTodos();
    document.getElementById('todo-form').addEventListener('submit', handleAddTodo);
}

if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', init);
} else {
    init();
}
```

---

## Summary

All patterns prioritize **clarity and workshop learning objectives** over production best practices:

- Single-file architectures make the entire stack visible without navigation
- Explicit error handling teaches defensive programming without overwhelming complexity
- Type validation with Pydantic demonstrates modern Python practices
- Supabase-py client patterns align with Constitution III (MCP-first workflow)
- Modular JavaScript organization proves inline scripts don't have to be unmaintainable
- Modern async patterns (async/await, fetch) teach current web development standards

All patterns comply with the project constitution while demonstrating professional development practices adapted for educational contexts.
