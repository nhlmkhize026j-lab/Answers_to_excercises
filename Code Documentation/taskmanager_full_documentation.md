# TaskManager Complete Documentation

This document combines all insights, explanations, and documentation for the `TaskManager` class, including the detailed breakdown from both Prompt 1 (function-level documentation) and Prompt 2 (logic, intent, assumptions, improvements).

---

# 1. High-Level Overview

The `TaskManager` class is a comprehensive task management system. It allows developers to:

- Perform CRUD operations on tasks (create, retrieve, update, delete).
- Manage task tags.
- Compute statistics including total tasks, counts by status and priority, overdue tasks, and recently completed tasks.
- Persist tasks using a backend storage (`TaskStorage`).

It is intended for productivity apps, CLI task managers, or any Python system that requires task organization.

---

# 2. Constructor

```python
TaskManager(storage_path="tasks.json")
```

**Description:**
Initializes a `TaskManager` instance and links it to persistent storage.

**Parameters:**
- `storage_path` (str): Path to JSON file for storing tasks. Defaults to `"tasks.json"`.

**Returns:** `TaskManager` instance

**Example:**
```python
tm = TaskManager("my_tasks.json")
```

**Notes:** `TaskStorage` handles file creation if missing.

---

# 3. Task Creation

```python
def create_task(self, title, description="", priority_value=2, due_date_str=None, tags=None)
```

**Description:**
Creates a new task, converts priority and due date, stores it, and returns the task ID.

**Parameters:**
- `title` (str): Task title.
- `description` (str): Optional task details.
- `priority_value` (int): Optional priority (defaults to 2).
- `due_date_str` (str): Optional due date in YYYY-MM-DD.
- `tags` (list[str]): Optional tags.

**Returns:**
- `int` | `None`: Task ID or `None` if invalid date format.

**Logic:**
1. Convert `priority_value` to `TaskPriority` enum.
2. Parse `due_date_str` into datetime; return None if invalid.
3. Create `Task` instance.
4. Add task to storage.
5. Return task ID.

**Edge Cases:**
- Invalid date strings prevent task creation.
- `tags` may be None; `Task` must handle.

**Example:**
```python
task_id = tm.create_task("Finish project", "Complete report", priority_value=1, due_date_str="2026-04-01", tags=["work", "urgent"])
```

**Potential Improvements:**
- Raise exceptions instead of printing errors.
- Validate non-empty titles.

**Inline Comments Suggestion:**
```python
# Convert priority value to enum
# Parse due date string if provided
# Create task instance
# Store task and return ID
```

---

# 4. Listing Tasks

```python
def list_tasks(self, status_filter=None, priority_filter=None, show_overdue=False)
```

**Description:**
Retrieves tasks from storage optionally filtered by status, priority, or overdue.

**Logic:**
1. If `show_overdue` is True, return overdue tasks.
2. Else, filter by status if provided.
3. Else, filter by priority if provided.
4. Else, return all tasks.

**Edge Cases:**
- Filters are mutually exclusive; cannot combine.
- Invalid enum values will raise `ValueError`.

**Example:**
```python
all_tasks = tm.list_tasks()
completed = tm.list_tasks(status_filter=3)
high_priority = tm.list_tasks(priority_filter=1)
overdue = tm.list_tasks(show_overdue=True)
```

**Potential Improvements:**
- Allow combined filters.

**Inline Comments Suggestion:**
```python
# Overdue filter has highest priority
# Filter by status if provided
# Filter by priority if provided
# Return all tasks if no filters
```

---

# 5. Update Task Status

```python
def update_task_status(self, task_id, new_status_value)
```

**Description:**
Updates the status of a task. DONE status marks completion timestamp.

**Logic:**
1. Convert new_status_value to `TaskStatus`.
2. If DONE, fetch task and call `mark_as_done()`, save storage.
3. Else, update status via storage.

**Returns:** `bool` – True if successful.

**Edge Cases:**
- Non-existent `task_id` returns None (DONE) or False (others).
- Only DONE sets `completed_at` timestamp.

**Potential Improvements:**
- Ensure consistent boolean returns.
- Handle invalid task IDs explicitly.

**Inline Comments:**
```python
# Convert input to enum
# Special handling for DONE
# Update other statuses
```

---

# 6. Update Task Priority / Due Date

**update_task_priority** converts to enum and calls `storage.update_task`.

**update_task_due_date** parses date and calls `update_task`; prints error if invalid.

**Edge Cases:**
- Invalid dates print errors.
- Non-existent task IDs may return False.

**Potential Improvements:**
- Validate `task_id` exists.
- Raise exceptions instead of printing.

---

# 7. Delete and Get Task

```python
def delete_task(self, task_id)
```
- Deletes task by ID. Returns True/False.

```python
def get_task_details(self, task_id)
```
- Retrieves task object or None.

---

# 8. Tag Management

**add_tag_to_task:**
- Adds a tag if not already present.
- Returns True if tag added or already exists, False if task missing.

**remove_tag_from_task:**
- Removes a tag if exists.
- Returns True if removed, False otherwise.

**Edge Cases:**
- Idempotent operations.
- Assumes `task.tags` is always a list.

**Potential Improvements:**
- Return detailed status (`added`, `exists`, `task missing`).

---

# 9. Statistics

```python
def get_statistics(self)
```

**Description:**
Aggregates task metrics:
- Total tasks
- Counts by status
- Counts by priority
- Overdue tasks
- Tasks completed in last 7 days

**Edge Cases:**
- Tasks may have `completed_at = None`.
- Overdue calculation depends on current datetime.

**Potential Improvements:**
- Cache results for performance.
- Allow custom time windows.

**Inline Comments:**
```python
# Count tasks by status
# Count tasks by priority
# Compute overdue tasks
# Compute tasks completed in last 7 days
```

---

# 10. Assumptions & Edge Cases

- Dates must be YYYY-MM-DD.
- Task IDs are valid integers.
- Filters are mutually exclusive.
- Tags always exist as lists.
- Storage handles missing files.
- Statistics calculated dynamically.

---

# 11. Potential Improvements (Summary)

1. Raise exceptions instead of printing errors.
2. Support combined filters in `list_tasks`.
3. Consistent boolean returns for update/delete.
4. Validate task IDs before updates/deletes.
5. Detailed tag operation return status.
6. Cache statistics for large task lists.
7. Flexible date parsing.

---

# 12. Inline Comment Suggestions (Summary)

- `# Convert priority/value/status to enum`
- `# Parse date string and handle invalid formats`
- `# Special handling for DONE status to mark completion`
- `# Filters applied: overdue > status > priority`
- `# Count tasks by status/priority dynamically`
- `# Count overdue and recently completed tasks`

