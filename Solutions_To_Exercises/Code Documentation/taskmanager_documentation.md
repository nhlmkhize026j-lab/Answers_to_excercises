# TaskManager Code Documentation

## High-Level Overview

The `TaskManager` class is a task management system designed to:

- Perform CRUD operations on tasks (create, retrieve, update, delete).
- Manage tags for tasks.
- Compute statistics including total tasks, counts by status and priority, overdue tasks, and recently completed tasks.
- Persist tasks using a storage backend (`TaskStorage`).

---

## Constructor
```python
TaskManager(storage_path="tasks.json")
```
**Description:** Initializes a TaskManager instance with a storage backend.

**Parameters:**
- `storage_path` (str): Path to the JSON file used for storing tasks. Defaults to `"tasks.json"`.

**Returns:** TaskManager instance

**Example:**
```python
tm = TaskManager("my_tasks.json")
```

**Notes:** File creation is handled by `TaskStorage` if it doesn't exist.

---

## create_task
```python
def create_task(self, title, description="", priority_value=2, due_date_str=None, tags=None)
```
**Description:** Creates a new task and stores it.

**Parameters:**
- `title` (str): Task title.
- `description` (str, optional): Task details.
- `priority_value` (int, optional): Priority level. Defaults to 2.
- `due_date_str` (str, optional): Due date in YYYY-MM-DD format.
- `tags` (list[str], optional): List of tags.

**Returns:**
- `int` | `None`: Task ID if successful, None if invalid date.

**Example:**
```python
task_id = tm.create_task("Finish project", "Complete report", priority_value=1, due_date_str="2026-04-01", tags=["work", "urgent"])
```

---

## list_tasks
```python
def list_tasks(self, status_filter=None, priority_filter=None, show_overdue=False)
```
**Description:** Lists tasks with optional filters.

**Returns:** list[Task] – filtered task list.

**Notes:** Filters are mutually exclusive; `show_overdue` takes precedence.

---

## update_task_status
```python
def update_task_status(self, task_id, new_status_value)
```
**Description:** Updates a task's status.

**Returns:** bool – True if successful.

**Notes:** DONE status marks task completed with timestamp.

---

## update_task_priority / update_task_due_date
Updates priority or due date of a task.

**Edge Cases:** Invalid dates print errors.

---

## delete_task / get_task_details
Handles deletion or retrieval of tasks.

---

## Tag Management
- `add_tag_to_task`: adds a tag if not present.
- `remove_tag_from_task`: removes a tag if present.

**Edge Cases:** idempotent operations.

---

## get_statistics
Returns a dictionary with:
- Total tasks
- Counts by status and priority
- Overdue tasks
- Tasks completed in last 7 days

**Notes:** Statistics are calculated dynamically.

---

## Assumptions & Edge Cases
- Dates must be YYYY-MM-DD.
- Task IDs are valid integers.
- Filters are mutually exclusive.
- Tag lists exist.
- `TaskStorage` handles missing files.
- Statistics depend on current datetime.

---

## Suggested Inline Comments
- `# Convert priority/value/status to enum`
- `# Parse date string and handle invalid formats`
- `# Special handling for DONE status to mark completion`
- `# Filters applied: overdue > status > priority`
- `# Count tasks by status/priority dynamically`
- `# Count overdue and recently completed tasks`

---

## Potential Improvements
1. Raise exceptions instead of printing errors.
2. Allow combined filters in `list_tasks`.
3. Ensure consistent boolean returns for update/delete.
4. Validate `task_id` before updates/deletes.
5. Return detailed status for tag operations.
6. Cache statistics for large task lists.
7. Support flexible date parsing.

