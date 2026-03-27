# 🧾 Error Analysis Report

## Inventory System Debugging Case Study

------------------------------------------------------------------------

## 📌 Overview

This report analyzes an `IndexError` encountered in a Python inventory
management script. It explains the error, identifies the root cause,
provides solutions, and highlights key learning points.

------------------------------------------------------------------------

## ❗ Error Type

**IndexError: list index out of range**

------------------------------------------------------------------------

## 🧠 Error Description

This error occurs when a program attempts to access an index in a list
that does not exist.

In Python: - Lists start at index **0** - The last valid index is
**length - 1** - Accessing any index beyond that range causes the
program to crash

------------------------------------------------------------------------

## 🔍 Root Cause Analysis

### Problematic Code:

``` python
for i in range(len(items) + 1):
```

### Why This Fails:

-   If `len(items) = 3`
-   Valid indexes: `0, 1, 2`
-   Loop runs: `0, 1, 2, 3` ❌

When `i = 3`, the code attempts:

``` python
items[3]
```

This index does not exist → causes the error.

------------------------------------------------------------------------

## ✅ Solutions

### ✔ Solution 1: Fix Loop Boundary

Remove the unnecessary `+1`:

``` python
for i in range(len(items)):
    print(f"Item {i+1}: {items[i]['name']} - Quantity: {items[i]['quantity']}")
```

------------------------------------------------------------------------

### ✔ Solution 2: Use Pythonic Iteration (Recommended)

Avoid index-based access entirely:

``` python
for i, item in enumerate(items):
    print(f"Item {i+1}: {item['name']} - Quantity: {item['quantity']}")
```

**Advantages:** - Eliminates index errors - Improves readability -
Follows Python best practices

------------------------------------------------------------------------

## 🎯 Learning Points

### 1. Off-by-One Errors

Small boundary mistakes (like `+1`) can cause major issues.

------------------------------------------------------------------------

### 2. Zero-Based Indexing

Always remember: \> Last index = length - 1

------------------------------------------------------------------------

### 3. Prefer Safer Iteration

Use:

``` python
for item in items
```

or:

``` python
enumerate(items)
```

------------------------------------------------------------------------

### 4. Trust Tracebacks

Error messages show: - Where the error occurred - What caused it

Use them as your primary debugging tool.

------------------------------------------------------------------------

### 5. Write Defensive Code

Validate assumptions about data to avoid crashes.

------------------------------------------------------------------------

## 🚀 Conclusion

This issue highlights a classic programming mistake: exceeding list
boundaries. By understanding indexing rules and adopting safer iteration
methods, such errors can be prevented entirely.

------------------------------------------------------------------------

## 📎 Recommendation

In future projects: - Avoid manual index handling unless necessary -
Review loop boundaries carefully - Use built-in Python tools like
`enumerate()` for cleaner and safer code

------------------------------------------------------------------------

**End of Report**
