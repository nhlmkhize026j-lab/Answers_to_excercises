# Integration Test Plan: Task Priority Workflow

This document outlines the integration testing strategy for the task priority workflow, including scenarios, test data, assertions, structure, code examples, and reflection.

---

## 1. Integration Test Scenarios

The workflow involves `calculate_task_score`, `sort_tasks_by_importance`, and `get_top_priority_tasks`. Integration tests should cover:

1. **Priority Handling**  
   Verify that tasks of LOW, MEDIUM, HIGH, and URGENT priorities get the correct base score (priority weight × 10).

2. **Due Date Factor**  
   Check tasks due:  
   - In the past → +35 points  
   - Today → +20 points  
   - Next 2 days → +15 points  
   - Next 7 days → +10 points  
   - No due date → no bonus

3. **Status Reductions**  
   Confirm score reductions for:  
   - DONE → −50 points  
   - REVIEW → −15 points  
   - TODO → no reduction

4. **Tag Boosts**  
   Verify tasks with `blocker`, `critical`, or `urgent` tags receive +8 points.

5. **Recent Update Boost**  
   Confirm tasks updated less than 1 day ago get +5 points.

6. **Combined Factors**  
   Test tasks with multiple factors simultaneously to ensure correct cumulative scoring.

7. **Sorting & Top N**  
   Check that `sort_tasks_by_importance` orders tasks correctly and `get_top_priority_tasks` returns the top N tasks.

8. **Edge Cases**  
   Include tasks with:  
   - No due date  
   - Empty tags  
   - Unknown status or priority

---

## 2. Test Data Design

Create tasks to cover all scenarios:

| Task Name | Priority | Due Date | Status | Tags | Updated At | Notes |
|-----------|---------|---------|--------|------|-----------|-------|
| Task A | LOW | None | TODO | [] | 5 days ago | Base score 10 |
| Task B | HIGH | yesterday | TODO | [] | 3 days ago | Base 40 + overdue 35 = 75 |
| Task C | MEDIUM | today | REVIEW | ["critical"] | today | Base 20 −15 +20 +8 +5 = 38 |
| Task D | URGENT | in 1 day | TODO | ["blocker"] | 2 days ago | Base 60 +15 +8 = 83 |
| Task E | HIGH | in 5 days | DONE | [] | 10 days ago | Base 40 +10 −50 = 0 |
| Task F | MEDIUM | in 10 days | TODO | [] | today | Base 20 +5 = 25 |
| Task G | LOW | in 2 days | TODO | ["urgent"] | today | Base 10 +15 +8 +5 = 38 |

- Covers all priority levels, due dates, status reductions, tag boosts, and recent updates.  
- Provides combinations for sorting and top N testing.

---

## 3. Assertions for Verification

1. **Score Calculation Assertions**  
   Assert `calculate_task_score(task)` equals expected score for each task.

2. **Sorting Assertions**  
   Assert that `sort_tasks_by_importance(tasks)` returns tasks in correct descending order.

3. **Top N Assertions**  
   Assert that `get_top_priority_tasks(tasks, N)` returns the correct top N tasks.

4. **Edge Case Assertions**  
   Verify tasks with missing due dates, empty tags, or unknown statuses/priority behave as expected.

---

## 4. Test Structure & Maintainability

- Use a **helper function** to generate tasks:

```python
from datetime import datetime, timedelta

def make_task(name, priority, due_in_days=None, status=TaskStatus.TODO, tags=None, updated_days_ago=0):
    due_date = datetime.now() + timedelta(days=due_in_days) if due_in_days is not None else None
    updated_at = datetime.now() - timedelta(days=updated_days_ago)
    return Task(name=name, priority=priority, due_date=due_date, status=status, tags=tags or [], updated_at=updated_at)
```

- Structure tests into three sections:  
  1. **Score calculation** – verify individual task scores  
  2. **Sorting verification** – verify sorted order  
  3. **Top N verification** – verify correct top tasks

- Optional: split into **smaller test functions per scenario** for readability.

---

## 5. Example Integration Test Code

```python
import unittest
from datetime import datetime, timedelta

# Task, TaskPriority, TaskStatus classes assumed defined above
# Also, the workflow functions: calculate_task_score, sort_tasks_by_importance, get_top_priority_tasks

class TestTaskPriorityWorkflowParameterized(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        cls.task_data = [
            {"name": "Task A", "priority": TaskPriority.LOW, "due_in_days": None, "status": TaskStatus.TODO, "tags": [], "updated_days_ago": 5, "expected_score": 10},
            {"name": "Task B", "priority": TaskPriority.HIGH, "due_in_days": -1, "status": TaskStatus.TODO, "tags": [], "updated_days_ago": 3, "expected_score": 75},
            {"name": "Task C", "priority": TaskPriority.MEDIUM, "due_in_days": 0, "status": TaskStatus.REVIEW, "tags": ["critical"], "updated_days_ago": 0, "expected_score": 38},
            {"name": "Task D", "priority": TaskPriority.URGENT, "due_in_days": 1, "status": TaskStatus.TODO, "tags": ["blocker"], "updated_days_ago": 2, "expected_score": 83},
            {"name": "Task E", "priority": TaskPriority.HIGH, "due_in_days": 5, "status": TaskStatus.DONE, "tags": [], "updated_days_ago": 10, "expected_score": 0},
            {"name": "Task F", "priority": TaskPriority.MEDIUM, "due_in_days": 10, "status": TaskStatus.TODO, "tags": [], "updated_days_ago": 0, "expected_score": 25},
            {"name": "Task G", "priority": TaskPriority.LOW, "due_in_days": 2, "status": TaskStatus.TODO, "tags": ["urgent"], "updated_days_ago": 0, "expected_score": 38},
        ]
        cls.tasks = [make_task(**data) for data in cls.task_data]

    def test_scores(self):
        for data, task in zip(self.task_data, self.tasks):
            score = calculate_task_score(task)
            self.assertEqual(score, data["expected_score"], f"Score mismatch for {task.name}")

    def test_sorting(self):
        sorted_tasks = sort_tasks_by_importance(self.tasks)
        sorted_names = [t.name for t in sorted_tasks]
        expected_order = ["Task D", "Task B", "Task C", "Task G", "Task F", "Task A", "Task E"]
        self.assertEqual(sorted_names, expected_order, "Tasks not sorted correctly by importance")

    def test_top_n(self):
        top_n = 3
        top_tasks = get_top_priority_tasks(self.tasks, limit=top_n)
        top_names = [t.name for t in top_tasks]
        expected_top = ["Task D", "Task B", "Task C"]
        self.assertEqual(top_names, expected_top, "Top N tasks not returned correctly")

if __name__ == "__main__":
    unittest.main()
```

---

## 6. Reflection on Testing

Working through this integration test exercise provided several insights:

1. **Integration Tests Focus on Workflow**  
   Testing the interactions between `calculate_task_score`, `sort_tasks_by_importance`, and `get_top_priority_tasks` highlighted the importance of verifying the full workflow, not just individual units.

2. **Test Data Design is Critical**  
   Carefully designing tasks with various priorities, due dates, statuses, tags, and update times ensures that edge cases and interactions are fully covered.

3. **Parameterized Testing Improves Maintainability**  
   Using a data-driven approach makes it easy to add new scenarios without modifying test logic, keeping tests scalable and readable.

4. **Assertions Must Reflect Real Expectations**  
   Thinking through expected scores and sorting orders ensures that tests accurately verify intended business logic.

5. **Integration Testing Reveals Interdependencies**  
   Small changes in scoring rules affect sorting and top-N selection, which integration tests help catch early.

6. **Readable Structure Matters**  
   Structuring tests with clear sections and descriptive task names improves debugging and maintainability.

**Overall**, this exercise reinforced that robust testing requires designing scenarios that reflect realistic use cases, verifying interactions, and structuring tests for clarity and maintainability.

