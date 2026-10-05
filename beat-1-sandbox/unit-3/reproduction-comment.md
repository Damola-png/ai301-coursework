I am working on #61 and reproduced the raw-SQL failure through the unchanged health handler and the production /health router.

Base: `codepath/pathreview-ai301-fa26-s1` commit `f89c06fc3ff292df2a04a39ac51319d32a76b779`.
Environment: Windows 11 (build 26200), Python 3.12.14, SQLAlchemy 2.0.54, aiosqlite 0.22.1, FastAPI 0.142.2, pytest 9.1.1, pytest-asyncio 1.4.0, httpx 0.28.1.

The database fixture is a live in-memory SQLite session through SQLAlchemy's async API. The control `await session.execute(text("SELECT 1"))` returns 1. Calling the real `health.health_check` with that same session logs the textual-SQL coercion error and raises HTTP 503; a GET to the real router with a database dependency override also returns 503.

I ran:
```powershell
.\.venv\Scripts\python.exe -X utf8 -m pytest tests/unit/test_health.py -q --tb=short
```

Observed output (excerpt from the unchanged code):
```text
FF.                                                                      [100%]
postgres_health_check_failed error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"
E   fastapi.exceptions.HTTPException: 503: {'status': 'unhealthy', 'dependencies': {'postgres': 'unhealthy', 'redis': 'healthy', 'vector_db': 'healthy'}, 'safety_events_last_hour': 0, 'timestamp': '2026-10-05T15:32:34.203148'}
E   assert 503 == 200
2 failed, 1 passed, 4 warnings in 7.47s
```

Expected: the handler and router report a healthy database and HTTP 200 when the other dependencies are healthy. The negative database-error control already passes: an OperationalError still produces an unhealthy PostgreSQL status and HTTP 503.

Isolation/limits: Redis and configuration are test-controlled to keep the separate #62 configuration bug from masking #61. This is not a claim that a live PostgreSQL/Redis deployment is healthy. The rejection occurs during SQLAlchemy statement coercion, before backend execution.

<details>
<summary>Reproduction tests (save as tests/unit/test_health.py at the base above)</summary>

```python
"""Regression tests for the SQLAlchemy-backed health-check database probe."""

from collections.abc import AsyncGenerator
from types import SimpleNamespace
from unittest.mock import AsyncMock, Mock

import pytest
import pytest_asyncio
import redis
from fastapi import FastAPI, HTTPException
from httpx import ASGITransport, AsyncClient
from sqlalchemy import text
from sqlalchemy.exc import OperationalError
from sqlalchemy.ext.asyncio import AsyncSession, create_async_engine

from api.routes import health
from core import config
from core.database import get_db


@pytest.fixture
def healthy_dependencies(monkeypatch: pytest.MonkeyPatch) -> None:
    """Keep the Redis/configuration issue separate from the database probe."""
    monkeypatch.setattr(
        config,
        "settings",
        SimpleNamespace(
            redis_host="localhost",
            redis_port=6379,
            vector_db_url="http://localhost:8001",
        ),
    )
    monkeypatch.setattr(redis, "Redis", Mock(return_value=Mock(ping=Mock(return_value=True))))


@pytest_asyncio.fixture
async def database_session() -> AsyncGenerator[AsyncSession, None]:
    """Provide a live SQLAlchemy session without requiring external services."""
    engine = create_async_engine("sqlite+aiosqlite:///:memory:")
    try:
        async with AsyncSession(engine) as session:
            yield session
    finally:
        await engine.dispose()


@pytest.mark.asyncio
async def test_health_check_accepts_live_sqlalchemy_session(
    database_session: AsyncSession, healthy_dependencies: None
) -> None:
    """A reachable database must not be reported down by SQL text coercion."""
    control = await database_session.execute(text("SELECT 1"))
    assert control.scalar_one() == 1

    result = await health.health_check(db=database_session)

    assert result["status"] == "healthy"
    assert result["dependencies"]["postgres"] == "healthy"


@pytest.mark.asyncio
async def test_health_endpoint_returns_200_for_reachable_database(
    database_session: AsyncSession, healthy_dependencies: None
) -> None:
    """Exercise the production router with a live database dependency."""
    app = FastAPI()
    app.include_router(health.router)

    async def override_db() -> AsyncGenerator[AsyncSession, None]:
        yield database_session

    app.dependency_overrides[get_db] = override_db
    async with AsyncClient(transport=ASGITransport(app=app), base_url="http://test") as client:
        response = await client.get("/health")

    assert response.status_code == 200
    assert response.json()["dependencies"]["postgres"] == "healthy"


@pytest.mark.asyncio
async def test_health_check_preserves_503_for_database_failure(
    healthy_dependencies: None,
) -> None:
    """A real database error must still produce an unhealthy 503 response."""
    database = AsyncMock(spec=AsyncSession)
    database.execute.side_effect = OperationalError(
        "SELECT 1", {}, RuntimeError("database unavailable")
    )

    with pytest.raises(HTTPException) as raised:
        await health.health_check(db=database)

    assert raised.value.status_code == 503
    assert raised.value.detail["status"] == "unhealthy"
    assert raised.value.detail["dependencies"]["postgres"] == "unhealthy"
    assert raised.value.detail["dependencies"]["redis"] == "healthy"
```

Install the test dependencies in a virtual environment:
```powershell
python -m pip install "SQLAlchemy==2.0.54" "aiosqlite==0.22.1" "fastapi==0.142.2" "pytest==9.1.1" "pytest-asyncio==1.4.0" "httpx==0.28.1" "redis==8.1.0" "asyncpg==0.31.0" "pydantic-settings==2.15.0" "structlog==26.1.0"
```
</details>

AI assistance: OpenAI Codex helped inspect the source, prepare these tests, run the commands, and draft this report. The results above are from executing the repository's handler and router, not a copied implementation.

