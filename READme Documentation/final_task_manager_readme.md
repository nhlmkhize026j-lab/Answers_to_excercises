# [Name]

## Description
[Brief description]

A Python-based Task Management system that allows users to create, update, track, and organize tasks efficiently. It supports priorities, due dates, tags, status tracking, and provides insightful statistics about your tasks.

## Key Features
- Create tasks with titles, descriptions, priorities, due dates, and tags.
- Update task status, priority, due date, or tags.
- List tasks filtered by status, priority, or overdue status.
- Delete tasks or retrieve detailed task information.
- Generate statistics including counts by status, priority, overdue tasks, and recently completed tasks.
- Persistent storage using JSON files.

## Technologies Used
- Python 3.10+
- argparse
- datetime
- Custom modules: `models.py`, `storage.py`

## Installation
1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/task-manager.git
   cd task-manager
   ```
2. (Optional) Create a virtual environment:
   ```bash
   python -m venv venv
   source venv/bin/activate  # Windows: venv\Scripts\activate
   ```
3. Install dependencies (if any):
   ```bash
   pip install -r requirements.txt
   ```

## Usage Examples
```python
from task_manager import TaskManager

manager = TaskManager()
task_id = manager.create_task(
    title="Finish project report",
    description="Complete the final report for the client",
    priority_value=1,
    due_date_str="2026-04-01",
    tags=["work", "urgent"]
)
tasks = manager.list_tasks(status_filter="PENDING")
manager.update_task_status(task_id, "DONE")
stats = manager.get_statistics()
print(stats)
```

## Features Overview
- Task Creation – Assign priority, due dates, and tags.
- Task Filtering – Filter by status, priority, or overdue tasks.
- Task Updates – Easily update status, priority, due dates, and tags.
- Persistent Storage – Tasks are stored in JSON for persistence.
- Statistics – Track overall tasks, overdue tasks, completed tasks in the past week, and distribution by priority/status.

## Configuration Options
- Storage File – Default storage file is `tasks.json`. Can be customized.
- Task Priority – Integer values (1 = High, 2 = Medium, 3 = Low).
- Task Status – PENDING, IN_PROGRESS, DONE.
- Due Date Format – YYYY-MM-DD.

## Troubleshooting
- Invalid Date Format – Ensure all dates use `YYYY-MM-DD`.
- Task Not Found – Verify task ID exists.
- JSON Errors – Ensure `tasks.json` is accessible and valid.

## Contributing Guidelines
1. Fork the repository.
2. Create a new branch for your feature or bugfix.
3. Make changes with clear, documented code.
4. Submit a pull request explaining improvements.

## License
MIT License. See [LICENSE](LICENSE) file.

