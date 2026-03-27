# Task Functions Testing Plan

**Functions Covered:**  
- `calculate_task_score(task)`  
- `sort_tasks_by_importance(tasks)`  
- `get_top_priority_tasks(tasks, limit=5)`  

**Purpose:**  
Ensure that task scores are calculated correctly, tasks are sorted accurately, and top-priority tasks are selected as expected, including handling of edge cases.

---

## 1. Function Behaviors

### `calculate_task_score(task)`
- **Priority affects score:** HIGH > MEDIUM > LOW.  
- **Due date affects score:**  
  - Overdue ? +35  
  - Due today ? +20  
  - Due in next 2 days ? +15  
  - Due in next week ? +10  
- **Status affects score:**  
  - DONE ? -50  
  - REVIEW ? -15  
- **Tags affect score:** `"blocker"`, `"critical"`, `"urgent"` ? +8  
- **Recently updated tasks** ? +5 if updated today  
- **Handles missing info:** no priority, no tags, no due date ? should not crash  

### `sort_tasks_by_importance(tasks)`
- Calls `calculate_task_score` for each task  
- Sorts tasks **highest score first**  
- Returns the sorted list  

### `get_top_priority_tasks(tasks, limit=5)`
- Sorts tasks using `sort_tasks_by_importance`  
- Returns **top N tasks** based on the limit  
- Default limit is 5  
- Handles **fewer tasks than limit**  

---

## 2. Edge Cases

- Task with **no priority**  
- Task with **no due date**  
- Task with **no tags**  
- Task that is **DONE but urgent**  
- Task **updated today** vs. updated before today  
- Empty task list  
- Limit greater than number of tasks  
- Tasks with **same score**  

**Expected behavior for edge cases:**  
- Missing priority ? score uses 0  
- Missing due date ? no bonus  
- Missing tags ? no bonus  
- DONE task ? score drops as expected  
- Urgent + DONE ? penalty applied after tag bonus  
- Recently updated ? small bonus added correctly  
- Empty list ? returns empty list  
- Limit > number of tasks ? returns all tasks  

---

## 3. Test Case Priority

- **High:** Core functionality; wrong scoring or sorting breaks main behavior  
- **Medium:** Dependent on scoring; affects output ordering  
- **Low:** Rare edge cases; important but less likely to fail  

---

## 4. Types of Tests

- **Unit Tests:**  
  - `calculate_task_score` ? Test each factor (priority, due date, status, tags, updated_at)  
  - `sort_tasks_by_importance` ? Test sorting logic with known scores  
  - `get_top_priority_tasks` ? Test slicing top N tasks  

- **Integration Tests:**  
  - Ensure `sort_tasks_by_importance` + `get_top_priority_tasks` work end-to-end with multiple tasks  

---

## 5. Test Dependencies

- Score tests ? Task object with various attributes (priority, due_date, status, tags, updated_at)  
- Sorting tests ? Correct calculation of task scores  
- Top N tests ? Correct sorting of tasks and handling of `limit` parameter  

---

## 6. Test Cases and Expected Outcomes

### Test Case 1: Base Priority Scoring
- **Priority:** High  
- **Type:** Unit  
- **Input / Setup:** Task with LOW, MEDIUM, HIGH, URGENT priority; no due date, no tags, status = TODO  
- **Expected Outcome:** Score = priority_weight * 10  

---

### Test Case 2: Due Date Bonuses
- **Priority:** High  
- **Type:** Unit  
- **Input / Setup:** Task due today, overdue, next 2 days, next 7 days, beyond 7 days  
- **Expected Outcome:** Score increases according to rules:  
  - Overdue ? +35  
  - Today ? +20  
  - Next 2 days ? +15  
  - Next 7 days ? +10  
  - Beyond 7 days ? no bonus  

---

### Test Case 3: Status Penalties
- **Priority:** High  
- **Type:** Unit  
- **Input / Setup:** Task status = DONE or REVIEW  
- **Expected Outcome:**  
  - DONE ? -50  
  - REVIEW ? -15 applied after other bonuses  

---

### Test Case 4: Tag Boosts
- **Priority:** High  
- **Type:** Unit  
- **Input / Setup:** Task with tags = ["blocker"], ["critical"], ["urgent"], multiple tags  
- **Expected Outcome:** +8 if any tag matches; multiple tags do not stack  

---

### Test Case 5: Recently Updated Boost
- **Priority:** High  
- **Type:** Unit  
- **Input / Setup:** Task updated today vs updated earlier  
- **Expected Outcome:** Updated today ? +5 bonus; otherwise no bonus  

---

### Test Case 6: Sorting by Score
- **Priority:** Medium  
- **Type:** Unit  
- **Input / Setup:** List of tasks with known scores  
- **Expected Outcome:** Tasks returned sorted descending by score  

---

### Test Case 7: Top N Tasks
- **Priority:** Medium  
- **Type:** Unit  
- **Input / Setup:** List of tasks, limit = 1, 3, 5, greater than task count  
- **Expected Outcome:** Top N tasks returned correctly; if limit > len(tasks), return all tasks  

---

### Test Case 8: Missing Fields Edge Case
- **Priority:** High  
- **Type:** Unit  
- **Input / Setup:** Task missing priority, due_date, or tags  
- **Expected Outcome:** Function does not crash; score calculated using defaults  

---

### Test Case 9: DONE but Urgent
- **Priority:** Medium  
- **Type:** Unit  
- **Input / Setup:** Task status = DONE, tag = "urgent"  
- **Expected Outcome:** Score penalty (-50) still applied; tag boost (+8) added correctly  

---

### Test Case 10: Empty Task List
- **Priority:** Medium  
- **Type:** Integration  
- **Input / Setup:** Empty list of tasks  
- **Expected Outcome:** Sorting and top N functions return empty list  

---

### Test Case 11: Tasks with Identical Scores
- **Priority:** Low  
- **Type:** Integration  
- **Input / Setup:** Multiple tasks with the same calculated score  
- **Expected Outcome:** Order is consistent; top N includes correct tasks  

---

## 7. Notes / Guidance

- For `calculate_task_score`, isolate **one factor per test** when possible; then combine factors to test interactions.  
- For sorting and top N, create **tasks with known scores** to verify order.  
- Edge cases like missing fields, empty lists, and identical scores are **critical for robustness**.  
- Unit tests should **not depend on external systems**; mock datetime if needed for due_date and updated_at tests.  
- Prioritize tests that are **most likely to break** or affect user-visible behavior first.  

---

**End of Test Plan**