# Product Combination Optimization Report

## 1. Original Code
```python
# inventory_analysis.py
def find_product_combinations(products, target_price, price_margin=10):
    """
    Find all pairs of products where the combined price is within
    the target_price ± price_margin range.

    Args:
        products: List of dictionaries with 'name' and 'price'
        target_price: Desired combined price
        price_margin: Allowed deviation from target_price

    Returns:
        List of tuples: (product1, product2)
    """
    combinations = []
    for i in range(len(products)):
        for j in range(i + 1, len(products)):
            total_price = products[i]['price'] + products[j]['price']
            if target_price - price_margin <= total_price <= target_price + price_margin:
                combinations.append((products[i], products[j]))
    return combinations
```

---

## 2. Identified Bottlenecks
- Nested loops create **O(n²)** complexity.
- Repeated dictionary lookups inside loops.
- No early exit or optimization based on price ordering.

---

## 3. Optimization Strategy
1. Sort products by price.
2. Use two-pointer technique to find valid combinations.
3. Precompute price-product pairs to avoid repeated dictionary access.

---

## 4. Optimized Code
```python
def find_product_combinations_optimized(products, target_price, price_margin=10):
    """
    Optimized version using sorting + two-pointer technique.
    """
    # Precompute list of (price, product)
    priced_products = [(p['price'], p) for p in products]
    priced_products.sort(key=lambda x: x[0])  # O(n log n)

    result = []
    left = 0
    right = len(priced_products) - 1

    while left < right:
        left_price, left_product = priced_products[left]
        right_price, right_product = priced_products[right]
        total = left_price + right_price

        if target_price - price_margin <= total <= target_price + price_margin:
            result.append((left_product, right_product))
            # Move left pointer to find other combinations with current right
            l_temp = left + 1
            while l_temp < right and target_price - price_margin <= priced_products[l_temp][0] + right_price <= target_price + price_margin:
                result.append((priced_products[l_temp][1], right_product))
                l_temp += 1
            left += 1
        elif total < target_price - price_margin:
            left += 1
        else:  # total > target_price + price_margin
            right -= 1

    return result
```

---

## 5. Performance Benchmarking
```python
import time
import random

# Generate large dataset
products = [{'name': f'Product{i}', 'price': random.randint(5, 1000)} for i in range(10000)]
target_price = 1500

# Original
start = time.time()
find_product_combinations(products, target_price)
end = time.time()
print("Original Time:", end - start)

# Optimized
start = time.time()
find_product_combinations_optimized(products, target_price)
end = time.time()
print("Optimized Time:", end - start)
```

**Expected Results:**
- Original: ~20–50 seconds for 10,000 products.
- Optimized: <1 second for same dataset.

---

## 6. Key Learnings
1. Algorithmic complexity dominates performance; improving from O(n²) to O(n log n) yields massive gains.
2. Preprocessing data (sorting, unpacking) reduces repeated operations.
3. Two-pointer technique is highly efficient for range-sum problems on sorted arrays.
4. Benchmarking before and after optimization is essential for measuring real improvement.

