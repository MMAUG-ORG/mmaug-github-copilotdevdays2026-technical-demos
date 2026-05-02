# MMAUG Session 1 – Task API

A minimal FastAPI service for managing tasks, with filtering by due date and priority.

---

## Getting Started

```bash
pip install -r requirements.txt
uvicorn app.main:app --reload
```

The API is then available at `http://localhost:8000`.  
Interactive docs: `http://localhost:8000/docs`

---

## Running Tests

```bash
pytest -v
```

---

## API Endpoints

### `GET /health`

Returns service liveness.

```bash
curl http://localhost:8000/health
# {"status":"ok"}
```

---

### `GET /tasks`

Returns the list of tasks. Supports two optional query parameters that can be used independently or combined.

| Parameter  | Type   | Format     | Description                          |
|------------|--------|------------|--------------------------------------|
| `due_date` | string | YYYY-MM-DD | Return only tasks due on this date   |
| `priority` | string | high \| medium \| low | Return only tasks with this priority |

#### Example 1 – Filter by due date

```bash
curl "http://localhost:8000/tasks?due_date=2026-04-30"
```

```python
import requests
resp = requests.get("http://localhost:8000/tasks", params={"due_date": "2026-04-30"})
print(resp.json())
# [{"id": 1, "title": "Create session plan", "priority": "high",
#   "completed": false, "due_date": "2026-04-30"}]
```

#### Example 2 – Filter by priority

```bash
curl "http://localhost:8000/tasks?priority=medium"
```

```python
import requests
resp = requests.get("http://localhost:8000/tasks", params={"priority": "medium"})
print(resp.json())
# [{"id": 2, "title": "Prepare demo repo", "priority": "medium",
#   "completed": false, "due_date": "2026-05-01"}]
```

#### Example 3 – Combine both filters

```bash
curl "http://localhost:8000/tasks?due_date=2026-05-01&priority=medium"
```

```python
import requests
resp = requests.get(
    "http://localhost:8000/tasks",
    params={"due_date": "2026-05-01", "priority": "medium"},
)
print(resp.json())
# [{"id": 2, "title": "Prepare demo repo", "priority": "medium",
#   "completed": false, "due_date": "2026-05-01"}]
```

#### Example 4 – No filters (return all tasks)

```bash
curl http://localhost:8000/tasks
```

```python
import requests
resp = requests.get("http://localhost:8000/tasks")
print(resp.json())
# Returns all 3 tasks
```

---

## Task Model

```json
{
  "id": 1,
  "title": "Create session plan",
  "priority": "high",
  "completed": false,
  "due_date": "2026-04-30"
}
```

| Field       | Type    | Notes                              |
|-------------|---------|------------------------------------|
| `id`        | integer | Unique task identifier             |
| `title`     | string  | Task description                   |
| `priority`  | string  | `high`, `medium`, or `low`         |
| `completed` | boolean | Defaults to `false`                |
| `due_date`  | string  | ISO 8601 date (`YYYY-MM-DD`), optional |
