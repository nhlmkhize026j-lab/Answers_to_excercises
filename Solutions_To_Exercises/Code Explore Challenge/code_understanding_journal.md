# Code Understanding Journal

## 1. Main Components
- TaskManager – Handles logic for task creation and updates  
- Task – Represents individual task data  
- TaskStorage – Handles saving and retrieving tasks  
- TaskStatus & TaskPriority – Define allowed values  

---

## 2. Execution Flow

### Task Creation:
1. User calls create_task()  
2. Input is processed (priority, due date)  
3. Task object is created  
4. Task is saved using TaskStorage  
5. Task ID is returned  

**Code Snippet:**
```python
def create_task(self, title, description="", priority_value=2,
               due_date_str=None, tags=None):
    priority = TaskPriority(priority_value)
    due_date = None
    if due_date_str:
        try:
            due_date = datetime.strptime(due_date_str, "%Y-%m-%d")
        except ValueError:
            print("Invalid date format. Use YYYY-MM-DD")
            return None

    task = Task(title, description, priority, due_date, tags)
    task_id = self.storage.add_task(task)
    return task_id
```

### Task Status Update:
- If DONE:
  1. Task is retrieved  
  2. mark_as_done() is called  
  3. Changes are saved  

- Otherwise:
  1. Status is updated via storage  
  2. Changes are saved  

**Code Snippet:**
```python
def update_task_status(self, task_id, new_status_value):
    new_status = TaskStatus(new_status_value)
    if new_status == TaskStatus.DONE:
        task = self.storage.get_task(task_id)
        if task:
            task.mark_as_done()
            self.storage.save()
            return True
    else:
        return self.storage.update_task(task_id, status=new_status)
```

---

## 3. Data Storage & Retrieval
- Tasks are stored in a JSON file (tasks.json)  
- TaskStorage handles:
  - Adding tasks  
  - Retrieving tasks  
  - Updating tasks  
  - Deleting tasks  

**Code Snippet:**
```python
def save(self):
    with open(self.storage_path, "w") as f:
        json.dump([task.to_dict() for task in self.tasks], f)
```

---

## 4. Design Patterns
- Service Layer Pattern (TaskManager)  
- Repository Pattern (TaskStorage)  
- Object-Oriented Design (Task class)  
- Enum usage for controlled values  

---

## 5. Task Prioritization

**Initial Understanding:**
- Priorities are numeric or enum values.
- Higher/lower numbers represent importance.
- Default priority = 2.
- Tasks can be created, filtered, and updated by priority.

**Discoveries from Guided Questions:**
- TaskPriority is an enum, so only valid numeric values are allowed.
- Priority affects filtering but sorting is handled elsewhere (not in TaskManager).
- Updating priority is straightforward—no special rules beyond enum validation.
- Default priority is explicitly set to 2 in create_task().

**Misconceptions Clarified:**
- Initially thought higher numbers = higher priority; the enum could define a different mapping (needs checking in TaskPriority).
- Sorting by priority is not automatic; filtering is the main built-in functionality.

---

## 6. Task Completion Data Flow (Circular Style)

```text
           ┌───────────────────────────┐
           │        [User]             │
           └────────────┬──────────────┘
                        │
                        ▼
           ┌───────────────────────────┐
           │ TaskManager.update_task_status(task_id, DONE) │
           └────────────┬──────────────┘
                        │
                        ▼
           ┌───────────────────────────┐
           │  TaskStatus enum validation │
           └────────────┬──────────────┘
                        │
                        ▼
           ┌───────────────────────────┐
           │    TaskStorage.get_task(task_id) │
           └────────────┬──────────────┘
                        │
                        ▼
           ┌───────────────────────────┐
           │       Task.mark_as_done()  │
           │ status → DONE             │
           │ completed_at → now        │
           └────────────┬──────────────┘
                        │
                        ▼
           ┌───────────────────────────┐
           │    TaskStorage.save()     │
           │ Persist changes to tasks.json │
           └────────────┬──────────────┘
                        │
                        ▼
           ┌───────────────────────────┐
           │      [User Confirmation]  │
           └────────────┴──────────────┘
```

**State Changes:**
- `status` changes from current value → DONE  
- `completed_at` set to current timestamp  

**Potential Points of Failure:**
- Task ID does not exist  
- Storage file inaccessible  
- Invalid status value  

**Persistence:**
- All changes are saved to `tasks.json` via TaskStorage.save()

**Code Snippet (mark_as_done):**
```python
def mark_as_done(self):
    self.status = TaskStatus.DONE
    self.completed_at = datetime.now()
```

