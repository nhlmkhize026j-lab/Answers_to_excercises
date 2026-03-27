# calculate_task_score_tests.py

import unittest

# Assume calculateTaskScore is imported from your module
# from your_module import calculateTaskScore

class TestCalculateTaskScore(unittest.TestCase):
    
    # ===== Base Score Tests =====

    def test_easy_task_returns_base_score(self):
        """Easy tasks should return the base score."""
        self.assertLess(calculateTaskScore("easy task"), calculateTaskScore("medium task"))

    def test_medium_task_returns_base_score(self):
        """Medium tasks should return a higher score than easy tasks but less than hard tasks."""
        self.assertGreater(calculateTaskScore("medium task"), calculateTaskScore("easy task"))
        self.assertLess(calculateTaskScore("medium task"), calculateTaskScore("hard task"))

    def test_hard_task_returns_base_score(self):
        """Hard tasks should return the highest base score among normal tasks."""
        self.assertGreater(calculateTaskScore("hard task"), calculateTaskScore("medium task"))

    # ===== Priority Bonus Tests =====

    def test_priority_adds_to_easy_task(self):
        """Tasks marked 'priority' should increase the base score."""
        self.assertGreater(calculateTaskScore("easy priority task"), calculateTaskScore("easy task"))

    def test_priority_adds_to_hard_task(self):
        """Priority increases the score even for hard tasks."""
        self.assertGreater(calculateTaskScore("hard priority task"), calculateTaskScore("hard task"))

    def test_multiple_priority_words(self):
        """Multiple 'priority' keywords should not break the scoring system."""
        score_single = calculateTaskScore("easy priority task")
        score_multiple = calculateTaskScore("easy priority priority task")
        self.assertGreaterEqual(score_multiple, score_single)

    # ===== Input Robustness Tests =====

    def test_unknown_task_returns_default_score(self):
        """Unknown tasks should return the default minimal score."""
        self.assertEqual(calculateTaskScore("some random task"), 5)

    def test_empty_string_returns_zero(self):
        """Empty string input should return zero."""
        self.assertEqual(calculateTaskScore(""), 0)

    def test_none_input_returns_zero(self):
        """None input should return zero."""
        self.assertEqual(calculateTaskScore(None), 0)

    def test_whitespace_only_string(self):
        """Strings containing only whitespace should be treated as empty and return zero."""
        self.assertEqual(calculateTaskScore("   "), 0)

    # ===== Case & Format Handling =====

    def test_case_insensitivity(self):
        """The function should treat uppercase and lowercase equally."""
        self.assertEqual(
            calculateTaskScore("EaSy PrIoRiTy task"),
            calculateTaskScore("easy priority task")
        )

    def test_string_with_extra_spaces(self):
        """Extra spaces should not affect the score."""
        self.assertEqual(
            calculateTaskScore("  medium task  "),
            calculateTaskScore("medium task")
        )

if __name__ == '__main__':
    unittest.main(argv=['first-arg-is-ignored'], exit=False)

