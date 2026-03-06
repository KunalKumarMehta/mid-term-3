# 🗂️ DSA Hashing — Complete Exam Study Guide

> Covering **Sessions 7, 8, 9** of your FDSA syllabus. All 22 practice problems solved with step-by-step approach, Java 8 code, and complexity analysis.

---

## 📌 Concept Cheat Sheet

### Core Data Structures

| Structure         | Java Class            | Key Operations                              | Avg Time |
| ----------------- | --------------------- | ------------------------------------------- | -------- |
| **HashMap**       | `HashMap<K, V>`       | `put`, `get`, `containsKey`, `getOrDefault` | O(1)     |
| **HashSet**       | `HashSet<E>`          | `add`, `contains`, `remove`                 | O(1)     |
| **LinkedHashMap** | `LinkedHashMap<K, V>` | Maintains insertion order                   | O(1)     |

### Essential Java 8 HashMap Methods

```java
map.getOrDefault(key, defaultValue)   // returns defaultValue if key absent
map.containsKey(key)                  // check existence
map.put(key, value)                   // insert/update
map.entrySet()                        // iterate over entries
map.computeIfAbsent(key, k -> new ArrayList<>())  // lazy initialization
```

### Key Hashing Patterns

| Pattern                   | When to Use                        | Core Idea                                        |
| ------------------------- | ---------------------------------- | ------------------------------------------------ |
| **Frequency Map**         | Count occurrences, find duplicates | `map.put(x, map.getOrDefault(x, 0) + 1)`         |
| **Prefix Sum + HashMap**  | Subarray sum problems              | Store `prefixSum → index` or `prefixSum → count` |
| **Two-pointer + HashSet** | Sliding window, unique elements    | HashSet tracks current window                    |
| **Pair Sum**              | Find pairs with target sum/diff    | Look up complement `B - A[i]` in HashMap         |
| **Seen Set**              | Check existence, remove duplicates | HashSet for O(1) lookups                         |

### The Prefix Sum Trick (Most Important for Exams!)

> **If `prefix[j] - prefix[i] = K`**, then subarray `(i, j]` has sum `K`.
>
> Store prefix sums in a HashMap. When computing `prefix[j]`, check if `prefix[j] - K` was seen before.
>
> **Special case:** `K = 0` → check if `prefix[j]` itself was seen before (same prefix sum repeated).

---

## 📗 Session 7 — HashMap & HashSet Basics

---

### Q1: Count of Unique/Distinct Elements

> Return the number of unique elements in array A.

### 💡 Approach & Intuition

Insert all elements into a `HashSet` — duplicates are automatically ignored. Return the set's size.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A) {
        HashSet<Integer> set = new HashSet<>(A);
        return set.size();
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — one pass to add all elements
- **Space:** `O(N)` — HashSet storage

---

### Q2: Count Elements with Frequency 1

> Return the count of elements with frequency exactly 1.

### 💡 Approach & Intuition

Build a frequency map. Then count entries where `value == 1`.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A) {
        HashMap<Integer, Integer> freq = new HashMap<>();
        for (int num : A) {
            freq.put(num, freq.getOrDefault(num, 0) + 1);
        }

        int count = 0;
        for (int f : freq.values()) {
            if (f == 1) count++;
        }
        return count;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)`
- **Space:** `O(N)`

---

### Q3: Frequency Queries

> Given arrays A and B, for each element in B, find its frequency in A.

### 💡 Approach & Intuition

Pre-compute a frequency map from A. For each query in B, look up `map.getOrDefault(b, 0)`.

### 🚀 Java 8 Solution

```java
public class Solution {
    public ArrayList<Integer> solve(ArrayList<Integer> A, ArrayList<Integer> B) {
        HashMap<Integer, Integer> freq = new HashMap<>();
        for (int num : A) {
            freq.put(num, freq.getOrDefault(num, 0) + 1);
        }

        ArrayList<Integer> result = new ArrayList<>();
        for (int q : B) {
            result.add(freq.getOrDefault(q, 0));
        }
        return result;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N + M)` — build map + answer queries
- **Space:** `O(N)` — frequency map

---

### Q4: First Repeating Element

> Find the element that occurs more than once and whose first occurrence index is smallest.

### 💡 Approach & Intuition

**Approach:** Traverse from **right to left**. Use a HashSet to track seen elements. If an element is already in the set, update the answer to this element (since we're going right-to-left, the last update will be the leftmost first-repeating element).

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A) {
        HashSet<Integer> seen = new HashSet<>();
        int result = -1;

        // Traverse right to left
        for (int i = A.size() - 1; i >= 0; i--) {
            if (seen.contains(A.get(i))) {
                result = A.get(i); // update — leftmost repeating
            } else {
                seen.add(A.get(i));
            }
        }
        return result;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — single pass
- **Space:** `O(N)` — HashSet

---

### Q5: Minimum Distance Between Equal Elements (Special Pair)

> Find minimum `|i - j|` where `A[i] == A[j]` and `i != j`.

### 💡 Approach & Intuition

Store the **last seen index** of each element in a HashMap. When we encounter an element again, compute `i - lastIndex` and update the minimum.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A) {
        HashMap<Integer, Integer> lastIndex = new HashMap<>();
        int minDist = Integer.MAX_VALUE;

        for (int i = 0; i < A.size(); i++) {
            int val = A.get(i);
            if (lastIndex.containsKey(val)) {
                minDist = Math.min(minDist, i - lastIndex.get(val));
            }
            lastIndex.put(val, i); // always update to latest index
        }

        return minDist == Integer.MAX_VALUE ? -1 : minDist;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — single pass
- **Space:** `O(N)` — HashMap

---

### Q6: Distinct Elements in Every Window of Size B

> Return array where element `i` = count of distinct elements in window `[i, i+B-1]`.

### 💡 Approach & Intuition

**Sliding window + frequency map.** Build frequency map for the first window. Then slide: remove the outgoing element (decrement count; if count drops to 0, remove key), add the incoming element. The map's size at each step = distinct count.

### 🚀 Java 8 Solution

```java
public class Solution {
    public ArrayList<Integer> dNums(ArrayList<Integer> A, int B) {
        ArrayList<Integer> result = new ArrayList<>();
        int N = A.size();
        if (B > N) return result;

        HashMap<Integer, Integer> freq = new HashMap<>();

        // Build first window
        for (int i = 0; i < B; i++) {
            freq.put(A.get(i), freq.getOrDefault(A.get(i), 0) + 1);
        }
        result.add(freq.size());

        // Slide the window
        for (int i = B; i < N; i++) {
            // Add incoming element
            int incoming = A.get(i);
            freq.put(incoming, freq.getOrDefault(incoming, 0) + 1);

            // Remove outgoing element
            int outgoing = A.get(i - B);
            freq.put(outgoing, freq.get(outgoing) - 1);
            if (freq.get(outgoing) == 0) {
                freq.remove(outgoing);
            }

            result.add(freq.size());
        }

        return result;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — each element added/removed once
- **Space:** `O(B)` — frequency map for window

---

### Q7: Common Elements in Two Arrays

> Find all common elements (with frequency matching both arrays).

### 💡 Approach & Intuition

Build a frequency map of A. Iterate through B: if element exists in map with count > 0, add it to result and decrement the count. This handles duplicates correctly.

### 🚀 Java 8 Solution

```java
public class Solution {
    public ArrayList<Integer> solve(ArrayList<Integer> A, ArrayList<Integer> B) {
        HashMap<Integer, Integer> freqA = new HashMap<>();
        for (int num : A) {
            freqA.put(num, freqA.getOrDefault(num, 0) + 1);
        }

        ArrayList<Integer> result = new ArrayList<>();
        for (int num : B) {
            if (freqA.getOrDefault(num, 0) > 0) {
                result.add(num);
                freqA.put(num, freqA.get(num) - 1);
            }
        }
        return result;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N + M)`
- **Space:** `O(N)` — frequency map of A

---

## 📘 Session 8 — Pair Sum, Subarray Sum Problems

---

### Q8: Pair Sum — Check Existence

> Check if there exists a pair `(i, j)` such that `B[i] + B[j] = A`.

### 💡 Approach & Intuition

Use a HashSet. For each element `x`, check if `A - x` is already in the set. If yes, pair found. Otherwise, add `x`.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(int A, ArrayList<Integer> B) {
        HashSet<Integer> seen = new HashSet<>();
        for (int num : B) {
            int complement = A - num;
            if (seen.contains(complement)) {
                return 1;
            }
            seen.add(num);
        }
        return 0;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — single pass
- **Space:** `O(N)` — HashSet

---

### Q9: Count of Pair Sum

> Count pairs `(i, j)` with `i < j` such that `A[i] + A[j] = B`. Return count mod 10⁹+7.

### 💡 Approach & Intuition

Build a frequency map. For each element `x`, its complement is `B - x`. Count = `freq[x] * freq[B-x]` for distinct pairs, or `C(freq[x], 2)` when `x == B - x`. To avoid double counting, handle both cases carefully.

**Simpler approach:** Iterate left to right. For each `A[i]`, count how many previous elements equal `B - A[i]` using a frequency map built as we go.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A, int B) {
        long MOD = 1000000007;
        HashMap<Integer, Long> freq = new HashMap<>();
        long count = 0;

        for (int num : A) {
            int complement = B - num;
            // How many times have we seen the complement before?
            count = (count + freq.getOrDefault(complement, 0L)) % MOD;
            freq.put(num, freq.getOrDefault(num, 0L) + 1);
        }

        return (int) count;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — single pass
- **Space:** `O(N)` — HashMap

---

### Q10: Count of Pair Difference

> Count pairs `(i, j)` where `A[i] - A[j] = B` and `i ≠ j`. Return mod 10⁹+7.

### 💡 Approach & Intuition

For each `A[i]`, we need `A[j] = A[i] - B`. Build a frequency map and for each element, look up `A[i] - B` in what we've seen so far. Since `A[i] - A[j] = B` is **directional**, also check `A[i] + B` (because `A[j] - A[i] = B` means `A[j] = A[i] + B`).

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A, int B) {
        long MOD = 1000000007;
        HashMap<Integer, Long> freq = new HashMap<>();
        long count = 0;

        for (int num : A) {
            // num - A[j] = B  →  A[j] = num - B  (already seen)
            count = (count + freq.getOrDefault(num - B, 0L)) % MOD;
            // A[j] - num = B  →  A[j] = num + B  (already seen)
            count = (count + freq.getOrDefault(num + B, 0L)) % MOD;
            freq.put(num, freq.getOrDefault(num, 0L) + 1);
        }

        return (int) count;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — single pass
- **Space:** `O(N)`

---

### Q11: Check if Subarray with Sum 0 Exists

> Return 1 if any subarray sums to zero.

### 💡 Approach & Intuition

**Prefix Sum + HashSet:** Compute running prefix sum. A zero-sum subarray exists if:

1. `prefix[i] == 0` (subarray from start), **OR**
2. `prefix[i]` was seen before (subarray between the two same prefix sums)

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A) {
        HashSet<Long> seen = new HashSet<>();
        long prefix = 0;
        seen.add(0L); // prefix sum 0 before array starts

        for (int num : A) {
            prefix += num;
            if (seen.contains(prefix)) {
                return 1;
            }
            seen.add(prefix);
        }
        return 0;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — single pass
- **Space:** `O(N)` — HashSet for prefix sums

---

### Q12: Longest Subarray with Sum 0

> Find the length of the longest subarray that sums to zero.

### 💡 Approach & Intuition

**Prefix Sum + HashMap (index tracking):** Store the **first occurrence** of each prefix sum. If `prefix[j] == prefix[i]`, then subarray `(i, j]` sums to 0. Length = `j - i`. We want the earliest `i` to maximize length.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A) {
        HashMap<Long, Integer> firstIndex = new HashMap<>();
        firstIndex.put(0L, -1); // prefix sum 0 "before" index 0

        long prefix = 0;
        int maxLen = 0;

        for (int i = 0; i < A.size(); i++) {
            prefix += A.get(i);
            if (firstIndex.containsKey(prefix)) {
                maxLen = Math.max(maxLen, i - firstIndex.get(prefix));
            } else {
                firstIndex.put(prefix, i);
            }
        }

        return maxLen;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — single pass
- **Space:** `O(N)` — HashMap

---

### Q13: Count of Subarrays with Sum 0

> Count all subarrays that sum to zero. Return mod 10⁹+7.

### 💡 Approach & Intuition

**Prefix Sum + HashMap (count tracking):** If a prefix sum `S` has appeared `k` times before, there are `k` subarrays ending here with sum 0. Add `k` to the count, then increment the frequency of `S`.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A) {
        long MOD = 1000000007;
        HashMap<Long, Long> freqMap = new HashMap<>();
        freqMap.put(0L, 1L); // prefix sum 0 occurs once initially

        long prefix = 0;
        long count = 0;

        for (int num : A) {
            prefix += num;
            // How many times has this prefix sum been seen?
            count = (count + freqMap.getOrDefault(prefix, 0L)) % MOD;
            freqMap.put(prefix, freqMap.getOrDefault(prefix, 0L) + 1);
        }

        return (int) count;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)`
- **Space:** `O(N)`

---

### Q14: Count of Subarrays with Sum = B

> Count all subarrays that sum to B.

### 💡 Approach & Intuition

**Generalized prefix sum counting.** We need `prefix[j] - prefix[i] = B`, so look up `prefix[j] - B` in the frequency map.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A, int B) {
        HashMap<Long, Integer> freqMap = new HashMap<>();
        freqMap.put(0L, 1); // empty prefix

        long prefix = 0;
        int count = 0;

        for (int num : A) {
            prefix += num;
            // How many prefix sums equal (prefix - B)?
            count += freqMap.getOrDefault(prefix - B, 0);
            freqMap.put(prefix, freqMap.getOrDefault(prefix, 0) + 1);
        }

        return count;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)`
- **Space:** `O(N)`

---

### Q15: First Continuous Subarray with Sum = B (Positive Integers)

> Find the first subarray that sums to B. Return its elements, or `[-1]` if none.

### 💡 Approach & Intuition

Since all elements are **positive**, use a **sliding window (two pointers)**: expand right to increase sum, shrink left to decrease. Alternatively, use a HashMap with prefix sums to find the exact range.

**Prefix sum approach:** Store `prefix → first index`. When `prefix[j] - B` is found in the map at index `i`, the subarray is `[i+1, j]`.

### 🚀 Java 8 Solution

```java
public class Solution {
    public ArrayList<Integer> solve(ArrayList<Integer> A, int B) {
        HashMap<Long, Integer> map = new HashMap<>();
        map.put(0L, -1);

        long prefix = 0;
        for (int i = 0; i < A.size(); i++) {
            prefix += A.get(i);
            if (map.containsKey(prefix - B)) {
                int start = map.get(prefix - B) + 1;
                ArrayList<Integer> result = new ArrayList<>();
                for (int k = start; k <= i; k++) {
                    result.add(A.get(k));
                }
                return result;
            }
            // Only store first occurrence for earliest subarray
            if (!map.containsKey(prefix)) {
                map.put(prefix, i);
            }
        }

        ArrayList<Integer> notFound = new ArrayList<>();
        notFound.add(-1);
        return notFound;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)`
- **Space:** `O(N)`

---

## 📙 Session 9 — Advanced: Longest Substring, Consecutive Sequence, Custom Sort

---

### Q16: Longest Substring Without Repeating Characters

> Find the length of the longest substring with all unique characters. Must be O(N).

### 💡 Approach & Intuition

**Sliding Window + HashMap:** Maintain a window `[left, right]` where all characters are unique. Use a HashMap `char → last index`. When a repeat is found, jump `left` to `max(left, lastIndex + 1)`.

**Walkthrough with `"abcabcbb"`:**

- `a(0)` `b(1)` `c(2)` → window `[0,2]`, len=3
- `a(3)` → repeat! left jumps to `0+1=1`, window `[1,3]`, len=3
- Continue... max length = 3 ✅

### 🚀 Java 8 Solution

```java
public class Solution {
    public int lengthOfLongestSubstring(String A) {
        HashMap<Character, Integer> lastIndex = new HashMap<>();
        int maxLen = 0;
        int left = 0;

        for (int right = 0; right < A.length(); right++) {
            char c = A.charAt(right);
            if (lastIndex.containsKey(c) && lastIndex.get(c) >= left) {
                left = lastIndex.get(c) + 1; // shrink window past the duplicate
            }
            lastIndex.put(c, right);
            maxLen = Math.max(maxLen, right - left + 1);
        }

        return maxLen;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — single pass, `right` moves once through string
- **Space:** `O(min(N, charset))` — HashMap for characters

---

### Q17: Longest Consecutive Sequence

> Find the length of the longest set of consecutive integers.

### 💡 Approach & Intuition

**HashSet approach:** Insert all into a HashSet. For each number, check if `num - 1` is NOT in the set (this means `num` is a **sequence start**). Then count consecutive numbers `num, num+1, num+2, ...` from that start.

This is O(N) because each element is visited at most twice (once as a start check, once in a chain).

### 🚀 Java 8 Solution

```java
public class Solution {
    public int longestConsecutive(final List<Integer> A) {
        HashSet<Integer> set = new HashSet<>(A);
        int maxLen = 0;

        for (int num : set) {
            // Only start counting from sequence beginnings
            if (!set.contains(num - 1)) {
                int current = num;
                int length = 1;
                while (set.contains(current + 1)) {
                    current++;
                    length++;
                }
                maxLen = Math.max(maxLen, length);
            }
        }

        return maxLen;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — each element visited at most twice
- **Space:** `O(N)` — HashSet

---

### Q18: Sort Array by Relative Order (Custom Sort with Hash)

> Sort A such that elements in B appear first (in B's order), remaining in sorted order.

### 💡 Approach & Intuition

1. Build frequency map of A
2. Iterate through B: for each element in B, add it `freq[element]` times to result, then remove from map
3. Collect remaining keys, sort them, add each `freq[key]` times

### 🚀 Java 8 Solution

```java
public class Solution {
    public ArrayList<Integer> solve(ArrayList<Integer> A, ArrayList<Integer> B) {
        HashMap<Integer, Integer> freq = new HashMap<>();
        for (int num : A) {
            freq.put(num, freq.getOrDefault(num, 0) + 1);
        }

        ArrayList<Integer> result = new ArrayList<>();

        // First: elements in B's order
        for (int num : B) {
            if (freq.containsKey(num)) {
                int count = freq.get(num);
                for (int i = 0; i < count; i++) {
                    result.add(num);
                }
                freq.remove(num);
            }
        }

        // Second: remaining elements in sorted order
        ArrayList<Integer> remaining = new ArrayList<>(freq.keySet());
        Collections.sort(remaining);
        for (int num : remaining) {
            int count = freq.get(num);
            for (int i = 0; i < count; i++) {
                result.add(num);
            }
        }

        return result;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N log N)` — sorting remaining elements dominates
- **Space:** `O(N)` — HashMap + result

---

### Q19: Colorful Number

> A number is COLORFUL if the product of every consecutive subsequence of its digits is unique.

### 💡 Approach & Intuition

Convert number to digit array. Generate all products of consecutive digit subsequences (like substrings). Use a HashSet to check if any product repeats.

For `3245`: subsequences → `3, 2, 4, 5, 3×2=6, 2×4=8, 4×5=20, 3×2×4=24, 2×4×5=40, 3×2×4×5=120` — all unique → COLORFUL ✅

### 🚀 Java 8 Solution

```java
public class Solution {
    public int colorful(int A) {
        // Convert number to digit array
        ArrayList<Integer> digits = new ArrayList<>();
        if (A == 0) {
            digits.add(0);
        }
        while (A > 0) {
            digits.add(0, A % 10);
            A /= 10;
        }

        HashSet<Long> products = new HashSet<>();
        int n = digits.size();

        for (int i = 0; i < n; i++) {
            long product = 1;
            for (int j = i; j < n; j++) {
                product *= digits.get(j);
                if (products.contains(product)) {
                    return 0; // duplicate product found
                }
                products.add(product);
            }
        }

        return 1;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(D²)` where D = number of digits (at most 10) — effectively constant
- **Space:** `O(D²)` — HashSet for products

---

### Q20: Count Subarrays with All Unique Elements

> Count subarrays where every element is distinct. Return mod 10⁹+7.

### 💡 Approach & Intuition

**Sliding Window:** Maintain window `[left, right]` with all unique elements using a HashMap. For each `right`, shrink `left` until all elements in window are unique. Then all subarrays ending at `right` within this window are valid: count += `right - left + 1`.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A) {
        long MOD = 1000000007;
        HashMap<Integer, Integer> freq = new HashMap<>();
        long count = 0;
        int left = 0;

        for (int right = 0; right < A.size(); right++) {
            int val = A.get(right);
            freq.put(val, freq.getOrDefault(val, 0) + 1);

            // Shrink window until all elements are unique
            while (freq.get(val) > 1) {
                int leftVal = A.get(left);
                freq.put(leftVal, freq.get(leftVal) - 1);
                if (freq.get(leftVal) == 0) freq.remove(leftVal);
                left++;
            }

            // All subarrays [left..right], [left+1..right], ..., [right..right] are valid
            count = (count + (right - left + 1)) % MOD;
        }

        return (int) count;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — each element added/removed at most once
- **Space:** `O(N)` — HashMap

---

## 🎯 Exam Strategy & Pattern Recognition

| If you see...                          | Think...                           | Key Technique                                     |
| -------------------------------------- | ---------------------------------- | ------------------------------------------------- |
| "Find frequency" / "count occurrences" | Frequency HashMap                  | `map.getOrDefault(x, 0) + 1`                      |
| "Find duplicates" / "first repeating"  | HashSet or HashMap                 | Track seen elements                               |
| "Pair sum = K"                         | Complement lookup                  | `seen.contains(K - x)`                            |
| "Pair difference = K"                  | Two lookups                        | `seen.contains(x - K)` and `seen.contains(x + K)` |
| "Subarray sum = 0"                     | Prefix sum + HashSet               | Repeated prefix sum                               |
| "Longest subarray sum = 0"             | Prefix sum + HashMap (first index) | `j - firstIndex[prefix]`                          |
| "Count subarrays sum = K"              | Prefix sum + HashMap (count)       | `count += freq[prefix - K]`                       |
| "Sliding window" / "window of size K"  | HashMap + add/remove               | Maintain frequency map                            |
| "Longest substring unique chars"       | Sliding window + HashMap           | `left = max(left, lastIndex + 1)`                 |
| "Longest consecutive sequence"         | HashSet + start detection          | Only expand from `num-1` not in set               |
| "All unique elements in subarray"      | Sliding window + freq map          | Count = `right - left + 1` per step               |

---

### 🔑 The Three Must-Know Prefix Sum Variations

| Variation                  | HashMap Stores         | When `prefix[j]` computed, check...      |
| -------------------------- | ---------------------- | ---------------------------------------- |
| **Exists sum=0 subarray**  | `HashSet<prefix>`      | `prefix ∈ set`                           |
| **Longest sum=0 subarray** | `prefix → first_index` | `prefix ∈ map` → `len = j - map[prefix]` |
| **Count sum=K subarrays**  | `prefix → frequency`   | `freq[prefix - K]` → add to count        |

---

> 💪 **Pro tip for the exam:** The **prefix sum + HashMap** pattern solves ~50% of hashing problems. When you see "subarray" + "sum", immediately think prefix sums. When you see "window" or "substring", think sliding window. When you see "pair", think complement lookup.
