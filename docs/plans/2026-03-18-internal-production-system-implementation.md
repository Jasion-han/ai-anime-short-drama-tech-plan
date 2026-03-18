# Internal Production System Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build the first internal production system iteration for AI anime short drama production, with database-backed project orchestration, task dispatch, approvals, audit logs, and repository mirror export.

**Architecture:** Use a single FastAPI backend as the control plane, PostgreSQL as the source of truth, Redis-backed workers for asynchronous execution, and a mirror sync worker that exports project state into the existing `project/` repository structure. Keep the first release monolithic at the API layer and asynchronous at the execution layer.

**Tech Stack:** Python, FastAPI, SQLAlchemy, Alembic, PostgreSQL, Redis, Celery or RQ, Pydantic, pytest, Docker Compose

---

### Task 1: Scaffold the backend control plane

**Files:**
- Create: `backend/pyproject.toml`
- Create: `backend/app/main.py`
- Create: `backend/app/config.py`
- Create: `backend/app/api/health.py`
- Create: `backend/tests/test_health.py`
- Create: `docker-compose.yml`

**Step 1: Write the failing test**

```python
from fastapi.testclient import TestClient

from app.main import app


def test_healthcheck_returns_ok():
    client = TestClient(app)
    response = client.get("/health")
    assert response.status_code == 200
    assert response.json() == {"status": "ok"}
```

**Step 2: Run test to verify it fails**

Run: `cd backend && pytest tests/test_health.py -v`
Expected: FAIL because the FastAPI app and route do not exist yet.

**Step 3: Write minimal implementation**

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/health")
def healthcheck():
    return {"status": "ok"}
```

**Step 4: Run test to verify it passes**

Run: `cd backend && pytest tests/test_health.py -v`
Expected: PASS

**Step 5: Commit**

```bash
git add backend docker-compose.yml
git commit -m "feat: scaffold backend control plane"
```

### Task 2: Add core project and stage data model

**Files:**
- Create: `backend/app/db/base.py`
- Create: `backend/app/db/session.py`
- Create: `backend/app/models/project.py`
- Create: `backend/app/schemas/project.py`
- Create: `backend/app/api/projects.py`
- Create: `backend/alembic.ini`
- Create: `backend/alembic/env.py`
- Create: `backend/alembic/versions/0001_create_projects.py`
- Create: `backend/tests/test_projects_api.py`

**Step 1: Write the failing test**

```python
def test_create_project_returns_project_id(client):
    payload = {
        "name": "Whisper Under The Desk",
        "target_platform": "Douyin",
        "target_aspect_ratio": "9:16",
        "target_duration_s": 31,
    }
    response = client.post("/projects", json=payload)
    assert response.status_code == 201
    data = response.json()
    assert data["name"] == payload["name"]
    assert data["current_phase"] == "planning"
```

**Step 2: Run test to verify it fails**

Run: `cd backend && pytest tests/test_projects_api.py -v`
Expected: FAIL because the model, migration, and endpoint do not exist.

**Step 3: Write minimal implementation**

Include:

- `Project` model with id, name, target_platform, target_aspect_ratio, target_duration_s, current_phase, status
- create-project API
- migration that creates the table

**Step 4: Run test to verify it passes**

Run: `cd backend && pytest tests/test_projects_api.py -v`
Expected: PASS

**Step 5: Commit**

```bash
git add backend
git commit -m "feat: add project model and project creation api"
```

### Task 3: Add script, scene, and shot entities

**Files:**
- Create: `backend/app/models/script.py`
- Create: `backend/app/models/scene.py`
- Create: `backend/app/models/shot.py`
- Create: `backend/app/schemas/script.py`
- Create: `backend/app/api/scripts.py`
- Create: `backend/alembic/versions/0002_create_script_scene_shot_tables.py`
- Create: `backend/tests/test_script_ingest.py`

**Step 1: Write the failing test**

```python
def test_create_scene_and_shot_for_project(client, project_id):
    payload = {
        "project_id": project_id,
        "script_title": "Whisper Under The Desk",
        "scenes": [
            {"scene_id": "sc01", "location": "classroom", "time_of_day": "day"},
        ],
        "shots": [
            {"shot_id": "sc01_sh01", "scene_id": "sc01", "duration_s": 4, "shot_type": "dialogue"},
        ],
    }
    response = client.post("/scripts/import", json=payload)
    assert response.status_code == 201
```

**Step 2: Run test to verify it fails**

Run: `cd backend && pytest tests/test_script_ingest.py -v`
Expected: FAIL because content entities and import endpoint do not exist.

**Step 3: Write minimal implementation**

Implement:

- script, scene, shot tables
- import endpoint that validates project ownership
- minimal enum validation for phase-safe fields

**Step 4: Run test to verify it passes**

Run: `cd backend && pytest tests/test_script_ingest.py -v`
Expected: PASS

**Step 5: Commit**

```bash
git add backend
git commit -m "feat: add script scene and shot entities"
```

### Task 4: Add task dispatch and run execution records

**Files:**
- Create: `backend/app/models/task.py`
- Create: `backend/app/models/run.py`
- Create: `backend/app/schemas/task.py`
- Create: `backend/app/api/tasks.py`
- Create: `backend/app/workers/queue.py`
- Create: `backend/app/workers/tasks.py`
- Create: `backend/alembic/versions/0003_create_tasks_and_runs.py`
- Create: `backend/tests/test_task_dispatch.py`

**Step 1: Write the failing test**

```python
def test_create_shot_generation_task(client, shot_id):
    payload = {
        "shot_id": shot_id,
        "task_type": "generate_animatic_take",
        "priority": "high",
    }
    response = client.post("/tasks", json=payload)
    assert response.status_code == 201
    assert response.json()["status"] == "queued"
```

**Step 2: Run test to verify it fails**

Run: `cd backend && pytest tests/test_task_dispatch.py -v`
Expected: FAIL because task storage and queue integration do not exist.

**Step 3: Write minimal implementation**

Implement:

- `tasks` and `runs` tables
- enqueue API
- worker stub that records a run row when a task is claimed

**Step 4: Run test to verify it passes**

Run: `cd backend && pytest tests/test_task_dispatch.py -v`
Expected: PASS

**Step 5: Commit**

```bash
git add backend
git commit -m "feat: add task dispatch and run records"
```

### Task 5: Add approvals, change requests, and audit logs

**Files:**
- Create: `backend/app/models/approval.py`
- Create: `backend/app/models/change_request.py`
- Create: `backend/app/models/audit_log.py`
- Create: `backend/app/api/approvals.py`
- Create: `backend/app/services/audit.py`
- Create: `backend/alembic/versions/0004_create_governance_tables.py`
- Create: `backend/tests/test_approval_flow.py`

**Step 1: Write the failing test**

```python
def test_freeze_request_requires_approval(client, project_id):
    payload = {
        "project_id": project_id,
        "approval_type": "milestone_freeze",
        "phase": "m1",
        "request_reason": "script and shot planning complete",
    }
    response = client.post("/approvals", json=payload)
    assert response.status_code == 201
    assert response.json()["status"] == "pending"
```

**Step 2: Run test to verify it fails**

Run: `cd backend && pytest tests/test_approval_flow.py -v`
Expected: FAIL because governance tables and endpoints do not exist.

**Step 3: Write minimal implementation**

Implement:

- approval and change request models
- audit log append helper
- freeze request creation endpoint

**Step 4: Run test to verify it passes**

Run: `cd backend && pytest tests/test_approval_flow.py -v`
Expected: PASS

**Step 5: Commit**

```bash
git add backend
git commit -m "feat: add approvals change requests and audit logs"
```

### Task 6: Add repository mirror export

**Files:**
- Create: `backend/app/services/mirror_export.py`
- Create: `backend/app/workers/mirror_sync.py`
- Create: `backend/tests/test_mirror_export.py`
- Modify: `project/01_script/master_script.md`
- Modify: `project/01_script/scene_list.csv`
- Modify: `project/01_script/shot_list.csv`
- Modify: `project/01_script/take_log.csv`

**Step 1: Write the failing test**

```python
def test_export_project_mirror_writes_expected_files(tmp_path, seeded_project):
    export_project_mirror(project_id=seeded_project.id, output_root=tmp_path)
    assert (tmp_path / "01_script" / "scene_list.csv").exists()
    assert (tmp_path / "01_script" / "shot_list.csv").exists()
```

**Step 2: Run test to verify it fails**

Run: `cd backend && pytest tests/test_mirror_export.py -v`
Expected: FAIL because the export service does not exist.

**Step 3: Write minimal implementation**

Implement:

- mirror export service that renders database state into the repository file layout
- worker entrypoint for async sync

**Step 4: Run test to verify it passes**

Run: `cd backend && pytest tests/test_mirror_export.py -v`
Expected: PASS

**Step 5: Commit**

```bash
git add backend project
git commit -m "feat: add project mirror export"
```

### Task 7: Add stage rules and budget gates

**Files:**
- Create: `backend/app/services/stage_rules.py`
- Create: `backend/app/services/budget_rules.py`
- Create: `backend/app/api/workflow.py`
- Create: `backend/tests/test_stage_rules.py`
- Create: `backend/tests/test_budget_rules.py`

**Step 1: Write the failing test**

```python
def test_project_can_enter_m1_review_when_required_entities_exist(seeded_project):
    result = evaluate_m1_readiness(seeded_project.id)
    assert result.ready is True
```

**Step 2: Run test to verify it fails**

Run: `cd backend && pytest tests/test_stage_rules.py tests/test_budget_rules.py -v`
Expected: FAIL because rule evaluators do not exist.

**Step 3: Write minimal implementation**

Implement:

- readiness evaluator for `M1`
- budget threshold evaluator
- workflow API that returns blockers and next actions

**Step 4: Run test to verify it passes**

Run: `cd backend && pytest tests/test_stage_rules.py tests/test_budget_rules.py -v`
Expected: PASS

**Step 5: Commit**

```bash
git add backend
git commit -m "feat: add stage and budget rule evaluators"
```

### Task 8: Add local developer runbook

**Files:**
- Create: `backend/README.md`
- Modify: `README.md`
- Modify: `docs/architecture/system_architecture.md`

**Step 1: Write the failing check**

Manual check: a new developer should be able to set up the API, database, worker, and mirror export without asking for missing commands.

**Step 2: Verify the gap exists**

Run: `rg -n "uvicorn|alembic|celery|docker compose" README.md backend/README.md`
Expected: missing or incomplete local setup instructions.

**Step 3: Write minimal implementation**

Document:

- local startup
- migrations
- test command
- worker startup
- mirror export command

**Step 4: Run verification**

Run: `rg -n "uvicorn|alembic|celery|docker compose" README.md backend/README.md`
Expected: all essential setup commands documented.

**Step 5: Commit**

```bash
git add README.md backend/README.md docs/architecture/system_architecture.md
git commit -m "docs: add local runbook for internal production system"
```
