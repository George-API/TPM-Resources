# FastAPI Quick Reference (Intermediate) — Syntax + Patterns

**Goal**: Copy/paste ready snippets for day-to-day FastAPI work.

**Focus**: routing, models, validation, dependencies, auth, DB patterns, background tasks, middleware, errors, pagination, file upload, websockets, testing, config, lifespan.

## Table of Contents

- [Installation & Setup](#installation--setup)
- [0. App + Lifespan (startup/shutdown)](#0-app--lifespan-startupshutdown)
- [1. Router organization](#1-router-organization)
- [2. Pydantic Models (request/response)](#2-pydantic-models-requestresponse)
- [3. Basic endpoints (path/query/body/headers/cookies)](#3-basic-endpoints-pathquerybodyheaderscookies)
- [4. Response control (headers/cookies/status)](#4-response-control-headerscookiesstatus)
- [5. Error handling (raise HTTPException + custom handler)](#5-error-handling-raise-httpexception--custom-handler)
- [6. Dependencies (DI) — common patterns](#6-dependencies-di--common-patterns)
- [7. Auth (Bearer token) — quick pattern](#7-auth-bearer-token--quick-pattern)
- [8. Background tasks](#8-background-tasks)
- [9. File upload + multipart form](#9-file-upload--multipart-form)
- [10. Pagination pattern (offset/limit)](#10-pagination-pattern-offsetlimit)
- [11. Middleware (request timing / logging)](#11-middleware-request-timing--logging)
- [12. WebSockets (quick pattern)](#12-websockets-quick-pattern)
- [13. OpenAPI docs tweaks](#13-openapi-docs-tweaks)
- [14. DB patterns (sync/async) — minimal templates](#14-db-patterns-syncasync--minimal-templates)
- [15. Testing (pytest + httpx TestClient)](#15-testing-pytest--httpx-testclient)
- [16. Config / settings pattern (Pydantic)](#16-config--settings-pattern-pydantic)

---

## Installation & Setup

**Install (typical):**

```bash
pip install fastapi uvicorn[standard] pydantic sqlalchemy asyncpg httpx pytest
```

**Run:**

```bash
uvicorn main:app --reload
```

**Open docs:**

- `/docs` (Swagger UI)
- `/redoc` (ReDoc)

---

## 0. App + Lifespan (startup/shutdown)

```python
from __future__ import annotations

from typing import Any, Optional, Literal
from datetime import datetime
from contextlib import asynccontextmanager

from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

@asynccontextmanager
async def lifespan(app: FastAPI):
    # startup: open DB pools, warm caches, etc.
    app.state.ready_at = datetime.utcnow()
    yield
    # shutdown: close pools, flush metrics, etc.

app = FastAPI(
    title="My API",
    version="1.0.0",
    lifespan=lifespan,
)

# CORS (example)
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:3000"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

---

## 1. Router organization

```python
from fastapi import APIRouter

router = APIRouter(prefix="/v1", tags=["v1"])
```

---

## 2. Pydantic Models (request/response)

```python
from pydantic import BaseModel, Field, ConfigDict, EmailStr

class UserIn(BaseModel):
    # Field validation + docs
    email: EmailStr
    name: str = Field(min_length=2, max_length=80)
    age: Optional[int] = Field(default=None, ge=0, le=130)
    role: Literal["user", "admin"] = "user"

    # Pydantic v2 config
    model_config = ConfigDict(extra="forbid")  # reject unknown fields

class UserOut(BaseModel):
    id: int
    email: EmailStr
    name: str
    created_at: datetime

# Reusable error payload
class APIError(BaseModel):
    code: str
    message: str
    details: Optional[dict[str, Any]] = None
```

---

## 3. Basic endpoints (path/query/body/headers/cookies)

```python
from fastapi import APIRouter, Path, Query, Header, Cookie, Body
from fastapi.responses import PlainTextResponse

@router.get("/health", response_class=PlainTextResponse)
def health() -> str:
    return "ok"

@router.get("/items/{item_id}")
def get_item(
    item_id: int = Path(..., ge=1, description="Numeric item id"),
    q: Optional[str] = Query(default=None, min_length=1, max_length=50),
    x_request_id: Optional[str] = Header(default=None, alias="X-Request-Id"),
    session: Optional[str] = Cookie(default=None),
) -> dict[str, Any]:
    return {
        "item_id": item_id,
        "q": q,
        "x_request_id": x_request_id,
        "session": session,
    }

@router.post("/users", response_model=UserOut, status_code=status.HTTP_201_CREATED)
def create_user(user: UserIn) -> UserOut:
    # In real code: insert into DB and return saved user
    return UserOut(
        id=1,
        email=user.email,
        name=user.name,
        created_at=datetime.utcnow(),
    )

@router.patch("/users/{user_id}")
def update_user_partial(
    user_id: int,
    payload: dict[str, Any] = Body(..., description="Partial update payload"),
) -> dict[str, Any]:
    # Minimalist partial update; prefer a dedicated Patch model in real APIs
    return {"user_id": user_id, "updated_fields": payload}
```

---

## 4. Response control (headers/cookies/status)

```python
from fastapi import Response
from fastapi.responses import FileResponse

@router.get("/download")
def download_example() -> FileResponse:
    # Serves a file; ensure path is safe / not user-controlled
    return FileResponse("somefile.pdf", filename="report.pdf")

@router.post("/set-cookie")
def set_cookie(resp: Response) -> dict[str, str]:
    resp.set_cookie(key="session", value="abc123", httponly=True, samesite="lax")
    return {"status": "cookie set"}
```

---

## 5. Error handling (raise HTTPException + custom handler)

```python
from fastapi import HTTPException, status, Request
from fastapi.responses import JSONResponse

def must_be_positive(n: int) -> None:
    if n <= 0:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail={"code": "BAD_INPUT", "message": "n must be > 0"},
        )

@router.get("/math/invert")
def invert(n: int = Query(...)) -> dict[str, float]:
    must_be_positive(n)
    return {"inv": 1.0 / n}

@app.exception_handler(HTTPException)
async def http_exception_handler(request: Request, exc: HTTPException):
    # Normalize error shape (optional pattern)
    # If detail is already structured, pass it through; else wrap it
    if isinstance(exc.detail, dict) and "code" in exc.detail and "message" in exc.detail:
        payload = exc.detail
    else:
        payload = {"code": "HTTP_ERROR", "message": str(exc.detail)}
    return JSONResponse(status_code=exc.status_code, content=payload)
```

---

## 6. Dependencies (DI) — common patterns

```python
from fastapi import Depends, Header

def get_request_id(x_request_id: Optional[str] = Header(default=None, alias="X-Request-Id")) -> str:
    # Derive or enforce request id
    return x_request_id or "generated-id"

def require_api_key(x_api_key: Optional[str] = Header(default=None, alias="X-Api-Key")) -> str:
    # Simple header auth example
    if x_api_key != "secret":
        raise HTTPException(status_code=401, detail={"code": "UNAUTHORIZED", "message": "Invalid API key"})
    return x_api_key

@router.get("/whoami")
def whoami(
    request_id: str = Depends(get_request_id),
    _api_key: str = Depends(require_api_key),
) -> dict[str, str]:
    return {"request_id": request_id, "auth": "ok"}
```

---

## 7. Auth (Bearer token) — quick pattern

```python
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials

bearer = HTTPBearer(auto_error=False)

def require_bearer_token(
    creds: Optional[HTTPAuthorizationCredentials] = Depends(bearer),
) -> str:
    if not creds or creds.scheme.lower() != "bearer" or not creds.credentials:
        raise HTTPException(status_code=401, detail={"code": "UNAUTHORIZED", "message": "Missing bearer token"})
    token = creds.credentials
    # Validate token here (JWT verify, introspection, etc.)
    return token

@router.get("/protected")
def protected_route(token: str = Depends(require_bearer_token)) -> dict[str, str]:
    return {"status": "ok", "token_hint": token[:6] + "..."}
```

---

## 8. Background tasks

```python
from fastapi import BackgroundTasks, Body
from pydantic import EmailStr

def send_email_sync(to: str, subject: str) -> None:
    # Replace with real email sending
    pass

@router.post("/jobs/email")
def queue_email(background: BackgroundTasks, to: EmailStr = Body(...), subject: str = Body(...)) -> dict[str, str]:
    background.add_task(send_email_sync, str(to), subject)
    return {"queued": "true"}
```

---

## 9. File upload + multipart form

```python
from fastapi import UploadFile, File, Form

@router.post("/upload")
async def upload_file(
    file: UploadFile = File(..., description="Uploaded file"),
    note: str = Form(default="", description="Optional note"),
) -> dict[str, Any]:
    # file.file is a SpooledTemporaryFile
    content = await file.read()  # careful: large files -> stream instead
    return {"filename": file.filename, "content_type": file.content_type, "bytes": len(content), "note": note}
```

---

## 10. Pagination pattern (offset/limit)

```python
@router.get("/users")
def list_users(
    limit: int = Query(default=20, ge=1, le=200),
    offset: int = Query(default=0, ge=0),
) -> dict[str, Any]:
    # In real code: SELECT ... LIMIT :limit OFFSET :offset
    data = [{"id": i, "email": f"u{i}@x.com"} for i in range(offset, offset + limit)]
    return {"limit": limit, "offset": offset, "data": data}
```

---

## 11. Middleware (request timing / logging)

```python
@app.middleware("http")
async def add_timing_header(request: Request, call_next):
    start = datetime.utcnow()
    resp = await call_next(request)
    duration_ms = int((datetime.utcnow() - start).total_seconds() * 1000)
    resp.headers["X-Process-Time-ms"] = str(duration_ms)
    return resp
```

---

## 12. WebSockets (quick pattern)

```python
from fastapi import WebSocket, WebSocketDisconnect

@app.websocket("/ws")
async def ws_endpoint(ws: WebSocket):
    await ws.accept()
    try:
        while True:
            msg = await ws.receive_text()
            await ws.send_text(f"echo: {msg}")
    except WebSocketDisconnect:
        # client disconnected
        pass
```

---

## 13. OpenAPI docs tweaks

```python
@router.get(
    "/docs-example",
    summary="Example endpoint",
    description="Shows how docs metadata is added.",
    responses={
        200: {"description": "OK"},
        400: {"model": APIError, "description": "Bad input"},
    },
)
def docs_example() -> dict[str, str]:
    return {"hello": "docs"}
```

---

## 14. DB patterns (sync/async) — minimal templates

### SQLAlchemy (sync) skeleton

```python
from sqlalchemy import create_engine, text
from sqlalchemy.orm import sessionmaker

engine = create_engine(DB_URL, pool_pre_ping=True)
SessionLocal = sessionmaker(bind=engine, autoflush=False, autocommit=False)

def get_db():
    db = SessionLocal()
    try: 
        yield db
    finally: 
        db.close()

@router.get("/x")
def x(db=Depends(get_db)):
    rows = db.execute(text("SELECT 1")).all()
    return {"rows": [dict(r._mapping) for r in rows]}
```

### Async (SQLAlchemy 2.0 / asyncpg)

```python
from sqlalchemy.ext.asyncio import create_async_engine, async_sessionmaker

engine = create_async_engine(ASYNC_DB_URL, pool_pre_ping=True)
AsyncSessionLocal = async_sessionmaker(engine, expire_on_commit=False)

async def get_db():
    async with AsyncSessionLocal() as session:
        yield session
```

---

## 15. Testing (pytest + httpx TestClient)

### Basic pytest

```python
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

def test_health():
    r = client.get("/v1/health")
    assert r.status_code == 200
    assert r.text == "ok"
```

### Async tests (httpx)

```python
import pytest
from httpx import AsyncClient

@pytest.mark.anyio
async def test_async():
    async with AsyncClient(app=app, base_url="http://test") as ac:
        r = await ac.get("/v1/health")
        assert r.status_code == 200
```

---

## 16. Config / settings pattern (Pydantic)

**Pydantic v2 settings (separate package):**

```bash
pip install pydantic-settings
```

```python
from pydantic_settings import BaseSettings, SettingsConfigDict

class Settings(BaseSettings):
    model_config = SettingsConfigDict(env_file=".env", extra="ignore")
    db_url: str
    env: str = "dev"

settings = Settings()
```

---

## Register router

```python
# Register router (must happen after router definitions)
app.include_router(router)
```

## Minimal "main" entry (optional)

```python
if __name__ == "__main__":
    import uvicorn
    uvicorn.run("main:app", host="0.0.0.0", port=8000, reload=True)
```
