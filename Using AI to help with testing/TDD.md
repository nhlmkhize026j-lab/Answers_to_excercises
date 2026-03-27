# TDD Practice: Task Score Calculation & Bug Fix

## 1. Feature Addition: Current User Boost (+12)

**Goal:** Tasks assigned to the current user should get a +12 score boost.

### Step 1: Write a failing test

```python
import unittest
from datetime import datetime

# Dummy enums and current_user placeholder
class TaskPriority:
    LOW = "low"
    MEDIUM = "medium"
    HIGH = "high"
    URGENT = "urgent"

class TaskStatus:
    TODO = "todo"

current_user = "user_1"

class Task:
    def __init__(self, assigned_to, priority=TaskPriority.LOW, status=TaskStatus.TODO,
                 due_date=None, tags=None, updated_at=None):
        self.assigned_to = assigned_to
        self.priority = priority
        self.status = status
        self.due_date = due_date
        self.tags = tags or []
        self.updated_at = updated_at or datetime.now()

# Minimal function to start failing test
def calculate_task_score(task):
    priority_weights = {
        TaskPriority.LOW: 1,
        TaskPriority.MEDIUM: 2,
        TaskPriority.HIGH: 4,
        TaskPriority.URGENT: 6
    }
    score = priority_weights.get(task.priority, 0) * 10
    return score

class TestTaskScore(unittest.TestCase):
    def test_current_user_score_boost(self):
        task = Task(assigned_to=current_user, priority=TaskPriority.MEDIUM)
        expected_score = 20 + 12  # Base 20 + current user boost
        self.assertEqual(calculate_task_score(task), expected_score)

if __name__ == "__main__":
    unittest.main()
```

### Step 2: Minimal code to make it pass

```python
def calculate_task_score(task):
    priority_weights = {
        TaskPriority.LOW: 1,
        TaskPriority.MEDIUM: 2,
        TaskPriority.HIGH: 4,
        TaskPriority.URGENT: 6
    }
    score = priority_weights.get(task.priority, 0) * 10

    # Minimal implementation for current user boost
    if task.assigned_to == current_user:
        score += 12

    return score
```

### Step 3: Next test ideas

- Combine current user boost with tags.
- Combine current user boost with due date.
- Task not assigned to current user should not get boost.

---

## 2. Bug Fix: `daysSinceUpdate` Calculation

**Goal:** Fix incorrect calculation for recently updated tasks.

### Step 1: Failing test to demonstrate the bug

```javascript
// calculateTaskScore.test.js
const { calculateTaskScore } = require("./calculateTaskScore");

const TaskPriority = { LOW: "low", MEDIUM: "medium", HIGH: "high", URGENT: "urgent" };
const TaskStatus = { TODO: "todo", DONE: "done", REVIEW: "review" };

describe("calculateTaskScore - daysSinceUpdate bug", () => {
  test("recently updated task should get +5 boost", () => {
    const now = new Date();
    const twoHoursAgo = new Date(now.getTime() - 2 * 60 * 60 * 1000); // 2 hours ago

    const task = {
      priority: TaskPriority.MEDIUM,
      status: TaskStatus.TODO,
      tags: [],
      updatedAt: twoHoursAgo,
    };

    const score = calculateTaskScore(task);

    expect(score).toBe(20 + 5); // Base 20 + recent update boost
  });
});
```

### Step 2: Minimal code fix

```javascript
const now = new Date();
const updatedAt = new Date(task.updatedAt);
const diffMs = now - updatedAt;
const daysSinceUpdate = diffMs / (1000 * 60 * 60 * 24); // float
if (daysSinceUpdate >= 0 && daysSinceUpdate < 1) {
    score += 5;
}
```

### Step 3: Regression tests

- Updated 23 hours ago → should get boost.
- Updated 25 hours ago → should NOT get boost.
- Updated in the future → should NOT get boost.

### Step 4: Full JS test file including regression

```javascript
// calculateTaskScore_full.js
const TaskPriority = { LOW: "low", MEDIUM: "medium", HIGH: "high", URGENT: "urgent" };
const TaskStatus = { TODO: "todo", DONE: "done", REVIEW: "review" };

function calculateTaskScore(task) {
  const priorityWeights = {
    [TaskPriority.LOW]: 1,
    [TaskPriority.MEDIUM]: 2,
    [TaskPriority.HIGH]: 3,
    [TaskPriority.URGENT]: 4
  };

  let score = (priorityWeights[task.priority] || 0) * 10;

  if (task.dueDate) {
    const now = new Date();
    const dueDate = new Date(task.dueDate);
    const daysUntilDue = Math.ceil((dueDate - now) / (1000 * 60 * 60 * 24));
    if (daysUntilDue < 0) score += 30;
    else if (daysUntilDue === 0) score += 20;
    else if (daysUntilDue <= 2) score += 15;
    else if (daysUntilDue <= 7) score += 10;
  }

  if (task.status === TaskStatus.DONE) score -= 50;
  else if (task.status === TaskStatus.REVIEW) score -= 15;

  if (task.tags && task.tags.some(tag => ["blocker", "critical", "urgent"].includes(tag))) score += 8;

  const now = new Date();
  const updatedAt = new Date(task.updatedAt);
  const diffMs = now - updatedAt;
  const daysSinceUpdate = diffMs / (1000 * 60 * 60 * 24);
  if (daysSinceUpdate >= 0 && daysSinceUpdate < 1) score += 5;

  return score;
}

if (require.main === module) {
  const { test, expect, describe } = require('@jest/globals');

  describe("calculateTaskScore - daysSinceUpdate bug fix", () => {

    test("recently updated task should get +5 boost", () => {
      const now = new Date();
      const twoHoursAgo = new Date(now.getTime() - 2 * 60 * 60 * 1000);
      const task = { priority: TaskPriority.MEDIUM, status: TaskStatus.TODO, tags: [], updatedAt: twoHoursAgo };
      const score = calculateTaskScore(task);
      expect(score).toBe(20 + 5);
    });

    test("updated 23 hours ago should get +5 boost", () => {
      const now = new Date();
      const twentyThreeHoursAgo = new Date(now.getTime() - 23 * 60 * 60 * 1000);
      const task = { priority: TaskPriority.MEDIUM, status: TaskStatus.TODO, tags: [], updatedAt: twentyThreeHoursAgo };
      const score = calculateTaskScore(task);
      expect(score).toBe(20 + 5);
    });

    test("updated 25 hours ago should NOT get boost", () => {
      const now = new Date();
      const twentyFiveHoursAgo = new Date(now.getTime() - 25 * 60 * 60 * 1000);
      const task = { priority: TaskPriority.MEDIUM, status: TaskStatus.TODO, tags: [], updatedAt: twentyFiveHoursAgo };
      const score = calculateTaskScore(task);
      expect(score).toBe(20);
    });

    test("updated in the future should NOT get boost", () => {
      const now = new Date();
      const future = new Date(now.getTime() + 2 * 60 * 60 * 1000);
      const task = { priority: TaskPriority.MEDIUM, status: TaskStatus.TODO, tags: [], updatedAt: future };
      const score = calculateTaskScore(task);
      expect(score).toBe(20);
    });

  });
}

module.exports = { calculateTaskScore, TaskPriority, TaskStatus };
```

---

### ✅ **Summary**

- We practiced **TDD for feature addition** (current user +12 boost).
- We wrote **a failing test for a bug** in `daysSinceUpdate`.
- We implemented a **minimal fix** to make the test pass.
- We added **regression tests** to prevent future breakage.
- The workflow demonstrates **failing → minimal fix → refactor → next test**.

