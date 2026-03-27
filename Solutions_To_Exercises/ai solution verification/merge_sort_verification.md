# Merge Sort Bug Fix and Verification Report

## 1️⃣ Identifying the Bug

While looking at my merge sort, I noticed the `merge` function had a small but critical mistake:

```javascript
while (i < left.length) {
  result.push(left[i]);
  j++; // ❌ This is wrong
}
```

I realized I was incrementing `j` instead of `i`. This would make the function skip elements from the `left` array and could even cause infinite loops in some cases.

---

## 2️⃣ Corrected Merge Function

I fixed the increment so leftover elements from the left array are correctly added:

```javascript
function merge(left, right) {
  let result = [];
  let i = 0;
  let j = 0;

  while (i < left.length && j < right.length) {
    if (left[i] < right[j]) {
      result.push(left[i]);
      i++;
    } else {
      result.push(right[j]);
      j++;
    }
  }

  while (i < left.length) {
    result.push(left[i]);
    i++; // ✅ fixed
  }

  while (j < right.length) {
    result.push(right[j]);
    j++;
  }

  return result;
}
```

---

## 3️⃣ Collaborative Solution Verification

I double-checked my fix by:

- Testing with arrays like `[5,2,9,1,5,6]` and `[10, -1, 2, 8]`.
- Comparing my results with other people’s merge sort implementations.
- Using AI to verify the logic.

All confirmed that my merge function now works correctly.

---

## 4️⃣ Learning Through Alternative Approaches

To make sure my solution was solid, I:

- Compared it to JavaScript’s built-in `Array.sort()`:

```javascript
[5,2,9,1,5,6].sort((a,b) => a-b);
```

- Tried an iterative version of merge sort to understand other ways to merge arrays.
- Verified that both approaches gave the same result as my corrected version.

This helped me understand the core logic of splitting and merging arrays.

---

## 5️⃣ Developing a Critical Eye

I looked closely at my code to avoid subtle mistakes:

- Checked all loops and index increments. ✅
- Made sure it works for empty arrays, single-element arrays, duplicates, and negative numbers. ✅
- Thought about edge cases and confirmed the final sorted array is always correct.

---

## 6️⃣ Verified Merge Sort – Full Implementation

```javascript
function mergeSort(arr) {
  if (arr.length <= 1) return arr;

  const mid = Math.floor(arr.length / 2);
  const left = mergeSort(arr.slice(0, mid));
  const right = mergeSort(arr.slice(mid));

  return merge(left, right);
}

function merge(left, right) {
  let result = [];
  let i = 0;
  let j = 0;

  while (i < left.length && j < right.length) {
    if (left[i] < right[j]) {
      result.push(left[i]);
      i++;
    } else {
      result.push(right[j]);
      j++;
    }
  }

  while (i < left.length) {
    result.push(left[i]);
    i++;
  }

  while (j < right.length) {
    result.push(right[j]);
    j++;
  }

  return result;
}

// ✅ Test cases
console.log(mergeSort([5,2,9,1,5,6]));  // [1,2,5,5,6,9]
console.log(mergeSort([10, -1, 2, 8])); // [-1,2,8,10]
```

---

## 7️⃣ What I Learned

1. Small mistakes like wrong index increments can completely break a recursive algorithm.  
2. Verification is key: testing, comparing with built-in methods, and thinking critically help catch hidden bugs.  
3. Exploring alternative approaches strengthens understanding and confidence in my solution.

