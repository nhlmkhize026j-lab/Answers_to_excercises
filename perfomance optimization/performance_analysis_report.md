# Performance Analysis Report: Product Combination Algorithm

## 1. Why the Code is Slow

The code processes 5,000 products using a nested loop, resulting in approximately 25 million comparisons.  
Additionally, it performs duplicate checks inside the loop, which significantly increases execution time.

---

## 2. Key Performance Issues

### Nested Loops (O(n²))
The algorithm compares every product with every other product:

```python
for i in range(len(products)):
    for j in range(len(products)):
```

This leads to quadratic growth:
- Double input → 4× slower
- Large datasets become extremely slow

---

### Duplicate Checking (O(n))
Each valid pair triggers this check:

```python
if not any(... for r in results):
```

This scans the entire results list repeatedly, making performance worse as results grow.

---

### Redundant Work
The code evaluates both:
- (A, B)
- (B, A)

Then removes duplicates afterward, which wastes computation.

---

## 3. Improvements

### Improvement 1: Avoid Duplicate Pairs

```python
for i in range(len(products)):
    for j in range(i + 1, len(products)):
```

✔ Removes duplicate comparisons  
✔ Cuts work nearly in half  
✔ Eliminates need for duplicate checks  

---

### Improvement 2: Remove Duplicate Check

Delete:

```python
if not any(...)
```

With proper looping, duplicates cannot occur.

---

### Improvement 3: Use Two-Pointer Technique

Sort products and use two pointers:

```python
def find_product_combinations_fast(products, target_price, price_margin=10):
    products = sorted(products, key=lambda x: x['price'])
    results = []

    left = 0
    right = len(products) - 1

    while left < right:
        p1 = products[left]
        p2 = products[right]
        combined_price = p1['price'] + p2['price']

        if (target_price - price_margin) <= combined_price <= (target_price + price_margin):
            results.append({
                'product1': p1,
                'product2': p2,
                'combined_price': combined_price,
                'price_difference': abs(target_price - combined_price)
            })
            left += 1
            right -= 1

        elif combined_price < target_price - price_margin:
            left += 1
        else:
            right -= 1

    results.sort(key=lambda x: x['price_difference'])
    return results
```

✔ Reduces complexity from **O(n²)** → **O(n log n)**  
✔ Massive performance improvement  

---

## 4. Key Performance Concepts

### Big-O Notation
- O(n²): Dangerous for large datasets  
- O(n log n): Scalable  
- O(n): Ideal  

---

### Algorithm vs Implementation
A slow algorithm stays slow in any language.  
Optimization starts with choosing the right approach.

---

### Avoid Repeated Work
Don’t recompute or re-check things unnecessarily.

---

### Data Structures Matter
- List lookup: O(n)  
- Set lookup: O(1)  

---

### Trade-offs
You can trade:
- Memory → Speed  
- Preprocessing → Faster queries  

---

## 5. Profiling Tools

### cProfile

```bash
python -m cProfile inventory_analysis.py
```

---

### line_profiler

```bash
pip install line_profiler
```

Usage:

```python
@profile
def find_product_combinations(...):
```

Run:

```bash
kernprof -l script.py
python -m line_profiler script.py.lprof
```

---

### Timing Small Sections

```python
import time

start = time.perf_counter()
# code
end = time.perf_counter()
```

---

## Conclusion

The slowdown is caused by inefficient algorithm design rather than Python itself.  
By improving the algorithm, execution time can drop from tens of seconds to near-instant.

