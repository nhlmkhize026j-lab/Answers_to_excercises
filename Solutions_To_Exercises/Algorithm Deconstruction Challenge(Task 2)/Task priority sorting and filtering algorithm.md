# Task Priority Sorting and Filtering Algorithm

## 1. Purpose
This explains how we calculate and sort tasks by importance.

- **Goal:** Give each task a score based on priority, due date, status, tags, and updates.
- **Why:** Helps find the most important tasks quickly.

---

## 2. Key Sections of the Algorithm

### Section A: Base Priority Score
- Gives a starting score using the task priority (LOW, MEDIUM, HIGH, URGENT).
```python
priority_weights = {
    TaskPriority.LOW: 1,
    TaskPriority.MEDIUM: 2,
    TaskPriority.HIGH: 4,
    TaskPriority.URGENT: 6
}
score = priority_weights.get(task.priority, 0) * 10
```

### Section B: Due Date Score
- Adds points if the task is due soon or overdue, so urgent tasks are higher.
```python
if task.due_date:
    days_until_due = (task.due_date - datetime.now()).days
    if days_until_due < 0:  # Overdue
        score += 35
    elif days_until_due == 0:  # Due today
        score += 20
    elif days_until_due <= 2:  # Due soon
        score += 15
    elif days_until_due <= 7:  # Due in week
        score += 10
```

### Section C: Status Penalty
- Reduces score for DONE or REVIEW tasks, so completed tasks do not appear at the top.
```python
if task.status == TaskStatus.DONE:
    score -= 50
elif task.status == TaskStatus.REVIEW:
    score -= 15
```

### Section D: Tag Bonus
- Adds extra points for important tags like blocker, critical, urgent.
```python
if any(tag in ["blocker", "critical", "urgent"] for tag in task.tags):
    score += 8
```

### Section E: Recent Update Bonus
- Gives a small bonus if the task was updated today, keeping list current.
```python
days_since_update = (datetime.now() - task.updated_at).days
if days_since_update < 1:
    score += 5
```

### Section F: Sorting and Top N
- Calculates score for each task, sorts tasks from highest to lowest, and can return top N tasks.
```python
task_scores = [(calculate_task_score(task), task) for task in tasks]
sorted_tasks = [task for _, task in sorted(task_scores, reverse=True)]
```

---

## 3. Example
- Task: HIGH priority, due in 1 day, tag critical, updated today.
- Score calculation: 40 + 15 + 8 + 5 = 68
- This shows how different factors add up.
```python
from datetime import datetime, timedelta

task = Task(
    title="Finish report",
    priority=TaskPriority.HIGH,
    due_date=datetime.now() + timedelta(days=1),
    status=TaskStatus.IN_PROGRESS,
    tags=["critical"],
    updated_at=datetime.now()
)
```

---

## 4. How it Works
- Score combines multiple factors.
- Sort tasks by score to find most important.
- Dynamic: list changes when tasks are updated or due dates change.

---

## 5. Tricks and Notes
- DONE tasks get negative points so they are not at top.
- Tag bonus helps important tasks without changing priority.
- Sorting uses (score, task) for efficiency.
- Recent updates keep the task list fresh.

---

## 6. Insights and Learning Points
- Multi-factor scoring balances priority, urgency, and status.
- Completed tasks are hidden from top lists.
- Calculating scores first is faster than re-calculating repeatedly.
- Small bonuses can change task order a lot.
- Algorithm adapts to updates automatically.
- Knowing each factor helps predict task order.

