# 🔍 DSA Binary Search — Complete Exam Study Guide

> Covering **Sessions 4, 5, 6** of your FDSA syllabus. All 17 practice problems solved with step-by-step approach, Java 8 code, and complexity analysis.

---

## 📌 Concept Cheat Sheet

### Binary Search Template

```java
int low = 0, high = N - 1;
while (low <= high) {
    int mid = low + (high - low) / 2;  // prevents overflow!
    if (A[mid] == target) return mid;
    else if (A[mid] < target) low = mid + 1;
    else high = mid - 1;
}
```

### Binary Search on Answer Template

```java
// When answer is in range [lo, hi] and you need min valid answer:
int lo = minPossible, hi = maxPossible, ans = -1;
while (lo <= hi) {
    int mid = lo + (hi - lo) / 2;
    if (isValid(mid)) {
        ans = mid;       // mid works, try smaller
        hi = mid - 1;
    } else {
        lo = mid + 1;    // mid doesn't work, try bigger
    }
}
// For max valid answer: swap lo/hi updates
```

### Key Patterns

| Pattern                     | When to Use                          | Search Space                         |
| --------------------------- | ------------------------------------ | ------------------------------------ |
| **Standard BS**             | Find element in sorted array         | Array indices                        |
| **First/Last occurrence**   | Sorted array with duplicates         | Array indices, continue after find   |
| **Binary Search on Answer** | Minimize/maximize a value            | Range of possible answers            |
| **Rotated Array**           | Sorted then rotated                  | One half always sorted               |
| **Matrix BS**               | Sorted matrix                        | Treat as 1D or row+col binary search |
| **BS + Greedy check**       | Painter's partition, Aggressive cows | Answer space + feasibility check     |

### Critical Pitfalls

| Pitfall                   | Fix                                               |
| ------------------------- | ------------------------------------------------- |
| Integer overflow in `mid` | Use `mid = low + (high - low) / 2`                |
| Infinite loop             | Ensure search space shrinks every iteration       |
| Off-by-one in first/last  | Use separate `findFirst` and `findLast` helpers   |
| `long` overflow in sqrt   | Cast to `long` before squaring: `(long)mid * mid` |

---

## 📗 Session 4 — Basic Binary Search, Peak Element, Matrix Search

---

### Q1: First and Last Occurrence of B in Sorted Array

> Find leftmost and rightmost index of B. Return `[-1, -1]` if not found.

### 💡 Approach & Intuition

Run binary search **twice** — once biased left (for first occurrence), once biased right (for last).

**First occurrence:** When `A[mid] == B`, don't return — record `mid` as answer and continue searching **left** (`high = mid - 1`).

**Last occurrence:** When `A[mid] == B`, record `mid` and continue searching **right** (`low = mid + 1`).

### 🚀 Java 8 Solution

```java
public class Solution {
    public ArrayList<Integer> searchRange(final List<Integer> A, int B) {
        ArrayList<Integer> result = new ArrayList<>();
        result.add(findFirst(A, B));
        result.add(findLast(A, B));
        return result;
    }

    private int findFirst(List<Integer> A, int B) {
        int low = 0, high = A.size() - 1, ans = -1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (A.get(mid) == B) {
                ans = mid;
                high = mid - 1; // keep searching left
            } else if (A.get(mid) < B) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return ans;
    }

    private int findLast(List<Integer> A, int B) {
        int low = 0, high = A.size() - 1, ans = -1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (A.get(mid) == B) {
                ans = mid;
                low = mid + 1; // keep searching right
            } else if (A.get(mid) < B) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return ans;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(log N)` — two binary searches
- **Space:** `O(1)`

---

### Q2: Search Insert Position

> Find index of B, or the index where B should be inserted (lower bound).

### 💡 Approach & Intuition

This is the classic **lower bound** binary search. Track the answer as the position of the smallest element ≥ B.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int searchInsert(ArrayList<Integer> A, int B) {
        int low = 0, high = A.size() - 1;
        int ans = A.size(); // default: insert at end

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (A.get(mid) >= B) {
                ans = mid;         // candidate position
                high = mid - 1;    // try to find earlier position
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(log N)`
- **Space:** `O(1)`

---

### Q3: Find Peak Element

> Array has a single peak. Find it in O(log N).

### 💡 Approach & Intuition

**Binary search on slope:** Compare `A[mid]` with `A[mid+1]`.

- If `A[mid] < A[mid+1]` → we're on the ascending slope → peak is to the **right** → `low = mid + 1`
- If `A[mid] >= A[mid+1]` → we're on the descending slope or at peak → `high = mid`

When `low == high`, we've found the peak.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A) {
        int low = 0, high = A.size() - 1;

        while (low < high) {
            int mid = low + (high - low) / 2;
            if (A.get(mid) < A.get(mid + 1)) {
                low = mid + 1;   // peak is to the right
            } else {
                high = mid;      // peak is at mid or to the left
            }
        }
        return A.get(low);
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(log N)`
- **Space:** `O(1)`

---

### Q4: Search in Sorted Matrix

> Rows sorted, first element of row ≥ last of previous row. Find B.

### 💡 Approach & Intuition

The entire matrix can be treated as a **single sorted 1D array** of size `N*M`. Use standard binary search with index mapping:

- `row = index / M`, `col = index % M`

### 🚀 Java 8 Solution

```java
public class Solution {
    public int searchMatrix(ArrayList<ArrayList<Integer>> A, int B) {
        int N = A.size(), M = A.get(0).size();
        int low = 0, high = N * M - 1;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            int row = mid / M, col = mid % M;
            int val = A.get(row).get(col);

            if (val == B) return 1;
            else if (val < B) low = mid + 1;
            else high = mid - 1;
        }
        return 0;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(log(N * M))`
- **Space:** `O(1)`

---

### Q5: Minimum Cost Array from Matrix (Pick One Per Row)

> Pick one element per row from a 2D matrix. Minimize the maximum adjacent absolute difference.

### 💡 Approach & Intuition

**Sort each row.** For adjacent rows, we want to minimize `max(|X[i] - X[i+1]|)` over all consecutive pairs. The overall minimum cost across the array is determined by the worst adjacent pair.

**Key insight:** Sort each row. For each pair of adjacent rows, find the minimum absolute difference between any element in row `i` and any element in row `i+1` using a **two-pointer merge-like scan** (both rows sorted). The answer is the maximum of these per-pair minimums.

Wait — actually, the cost is defined as the **minimum** possible value of `max(|adjacent diffs|)`. This requires a more careful approach: for each adjacent pair of rows, find the minimum absolute difference by sorting both and using two pointers. The answer is the max of all these minimum adjacent differences.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(int A, int B, ArrayList<ArrayList<Integer>> C) {
        // Sort each row
        for (ArrayList<Integer> row : C) {
            Collections.sort(row);
        }

        int result = 0;

        // For each pair of adjacent rows, find min absolute difference
        for (int i = 0; i < A - 1; i++) {
            ArrayList<Integer> row1 = C.get(i);
            ArrayList<Integer> row2 = C.get(i + 1);
            int minDiff = Integer.MAX_VALUE;

            // Two pointer on sorted rows
            int p = 0, q = 0;
            while (p < B && q < B) {
                minDiff = Math.min(minDiff, Math.abs(row1.get(p) - row2.get(q)));
                if (minDiff == 0) break;
                if (row1.get(p) < row2.get(q)) p++;
                else q++;
            }

            result = Math.max(result, minDiff);
        }

        return result;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(A * B * log B)` — sorting each row + two-pointer per pair
- **Space:** `O(1)` — in-place sorting

---

## 📘 Session 5 — Single Element, Rotated Array, Square Root, Median

---

### Q6: Single Element in Sorted Array (O(log N))

> Sorted array, every element appears twice except one. Find it in O(log N).

### 💡 Approach & Intuition

**Key observation:** Before the single element, pairs start at even indices `(0,1), (2,3), ...`. After the single element, pairs shift to odd-starting indices `(odd, even)`.

At `mid`:

- If `mid` is even: partner should be `mid+1`. If `A[mid] == A[mid+1]`, single is to the **right**.
- If `mid` is odd: partner should be `mid-1`. If `A[mid] == A[mid-1]`, single is to the **right**.
- Otherwise, single is to the **left** (or at `mid`).

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A) {
        int N = A.size();
        // Edge cases
        if (N == 1) return A.get(0);
        if (!A.get(0).equals(A.get(1))) return A.get(0);
        if (!A.get(N - 1).equals(A.get(N - 2))) return A.get(N - 1);

        int low = 0, high = N - 1;
        while (low <= high) {
            int mid = low + (high - low) / 2;

            // Check if mid is the single element
            if (!A.get(mid).equals(mid > 0 ? A.get(mid - 1) : -1) &&
                !A.get(mid).equals(mid < N - 1 ? A.get(mid + 1) : -1)) {
                return A.get(mid);
            }

            // Determine which side the single element is on
            boolean leftPairIntact;
            if (mid % 2 == 0) {
                leftPairIntact = (mid + 1 < N && A.get(mid).equals(A.get(mid + 1)));
            } else {
                leftPairIntact = (mid - 1 >= 0 && A.get(mid).equals(A.get(mid - 1)));
            }

            if (leftPairIntact) {
                low = mid + 1;   // single element is on the right
            } else {
                high = mid - 1;  // single element is on the left
            }
        }
        return -1;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(log N)`
- **Space:** `O(1)`

---

### Q7: Maximum Height Staircase

> Given A blocks, find max height where stair `i` needs `i` blocks. Total = `h*(h+1)/2 ≤ A`.

### 💡 Approach & Intuition

**Binary search on answer.** Search space: `[0, A]`. For a candidate height `mid`, check if `mid*(mid+1)/2 ≤ A`. Use `long` to prevent overflow.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(int A) {
        long low = 0, high = A;
        long ans = 0;

        while (low <= high) {
            long mid = low + (high - low) / 2;
            long blocksNeeded = mid * (mid + 1) / 2;

            if (blocksNeeded <= A) {
                ans = mid;       // valid, try higher
                low = mid + 1;
            } else {
                high = mid - 1;  // too many blocks
            }
        }
        return (int) ans;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(log A)`
- **Space:** `O(1)`

---

### Q8: Search in Rotated Sorted Array

> Find target B in rotated sorted array. O(log N).

### 💡 Approach & Intuition

**Key insight:** At any `mid`, one half `[low, mid]` or `[mid, high]` is **always sorted**.

1. Find which half is sorted by comparing `A[low]` with `A[mid]`
2. Check if target lies in the sorted half
3. Eliminate the appropriate half

### 🚀 Java 8 Solution

```java
public class Solution {
    public int search(final List<Integer> A, int B) {
        int low = 0, high = A.size() - 1;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (A.get(mid) == B) return mid;

            // Left half is sorted
            if (A.get(low) <= A.get(mid)) {
                if (B >= A.get(low) && B < A.get(mid)) {
                    high = mid - 1;  // target in sorted left half
                } else {
                    low = mid + 1;   // target in right half
                }
            }
            // Right half is sorted
            else {
                if (B > A.get(mid) && B <= A.get(high)) {
                    low = mid + 1;   // target in sorted right half
                } else {
                    high = mid - 1;  // target in left half
                }
            }
        }
        return -1;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(log N)`
- **Space:** `O(1)`

---

### Q9: Square Root (Floor)

> Compute `floor(sqrt(A))` without using `sqrt`. O(log A).

### 💡 Approach & Intuition

**Binary search on answer.** Search `[0, A]`. For candidate `mid`, check if `mid * mid ≤ A`. Use `long` to avoid overflow.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int sqrt(int A) {
        if (A == 0) return 0;
        long low = 1, high = A;
        long ans = 0;

        while (low <= high) {
            long mid = low + (high - low) / 2;
            if (mid * mid <= A) {
                ans = mid;       // valid, try higher
                low = mid + 1;
            } else {
                high = mid - 1;  // too large
            }
        }
        return (int) ans;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(log A)`
- **Space:** `O(1)`

---

### Q10: Median of Two Sorted Arrays

> Find median of merged sorted arrays in O(log(min(N, M))).

### 💡 Approach & Intuition

**Binary search on the smaller array's partition.** We partition both arrays such that all elements in the left halves ≤ all elements in the right halves. Binary search the partition point on the smaller array.

**Partition logic:** If we take `i` elements from A and `j = (N+M+1)/2 - i` from B for the left half:

- Valid when: `A[i-1] ≤ B[j]` and `B[j-1] ≤ A[i]`

### 🚀 Java 8 Solution

```java
public class Solution {
    public double findMedianSortedArrays(final List<Integer> a, final List<Integer> b) {
        // Ensure a is the smaller array
        if (a.size() > b.size()) return findMedianSortedArrays(b, a);

        int n1 = a.size(), n2 = b.size();
        int low = 0, high = n1;
        int total = n1 + n2;
        int half = (total + 1) / 2;

        while (low <= high) {
            int i = low + (high - low) / 2; // partition in a
            int j = half - i;               // partition in b

            int aLeft  = (i > 0)  ? a.get(i - 1) : Integer.MIN_VALUE;
            int aRight = (i < n1) ? a.get(i)     : Integer.MAX_VALUE;
            int bLeft  = (j > 0)  ? b.get(j - 1) : Integer.MIN_VALUE;
            int bRight = (j < n2) ? b.get(j)     : Integer.MAX_VALUE;

            if (aLeft <= bRight && bLeft <= aRight) {
                // Valid partition found
                if (total % 2 == 1) {
                    return Math.max(aLeft, bLeft);
                } else {
                    return (Math.max(aLeft, bLeft) + Math.min(aRight, bRight)) / 2.0;
                }
            } else if (aLeft > bRight) {
                high = i - 1; // too many from a
            } else {
                low = i + 1;  // too few from a
            }
        }
        return 0.0;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(log(min(N, M)))`
- **Space:** `O(1)`

---

### Q11: Median of Row-Wise Sorted Matrix

> Each row is sorted. Find overall median. N\*M is odd.

### 💡 Approach & Intuition

**Binary search on the answer value.** The median is the element with exactly `(N*M)/2` elements smaller.

Search space: `[min element, max element]`. For candidate `mid`, count how many elements in the matrix are ≤ `mid` using **upper bound binary search on each row**. If count ≤ `N*M/2`, search right; else search left.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int findMedian(ArrayList<ArrayList<Integer>> A) {
        int N = A.size(), M = A.get(0).size();
        int low = Integer.MAX_VALUE, high = Integer.MIN_VALUE;

        // Find search range
        for (ArrayList<Integer> row : A) {
            low = Math.min(low, row.get(0));
            high = Math.max(high, row.get(M - 1));
        }

        int desired = (N * M) / 2; // number of elements that should be < median

        while (low <= high) {
            int mid = low + (high - low) / 2;
            int count = 0; // count of elements <= mid

            for (ArrayList<Integer> row : A) {
                count += upperBound(row, mid);
            }

            if (count <= desired) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
        return low;
    }

    // Count of elements <= val in sorted list
    private int upperBound(ArrayList<Integer> row, int val) {
        int lo = 0, hi = row.size() - 1, ans = 0;
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            if (row.get(mid) <= val) {
                ans = mid + 1;
                lo = mid + 1;
            } else {
                hi = mid - 1;
            }
        }
        return ans;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N * log M * log(max - min))` — binary search on value × N rows × binary search per row
- **Space:** `O(1)`

---

### Q12: Max Occurrences After B Increments

> Increase elements at most B times (each +1). Find max frequency and the minimum such number.

### 💡 Approach & Intuition

**Sort + Sliding Window + Binary Search.** Sort the array. For each right endpoint, use binary search (or sliding window) to find the furthest left endpoint such that the cost of raising all elements in `[left, right]` to `A[right]` is ≤ B.

Cost = `A[right] * windowSize - sum(window)`.

We want to maximize window size. Use **prefix sums** for fast range sums.

### 🚀 Java 8 Solution

```java
public class Solution {
    public ArrayList<Integer> solve(ArrayList<Integer> A, int B) {
        Collections.sort(A);
        int N = A.size();
        long[] prefix = new long[N + 1];
        for (int i = 0; i < N; i++) {
            prefix[i + 1] = prefix[i] + A.get(i);
        }

        int bestCount = 0;
        int bestVal = Integer.MAX_VALUE;
        int left = 0;

        for (int right = 0; right < N; right++) {
            // Cost to bring A[left..right] all up to A[right]
            // = A[right] * (right - left + 1) - sum(A[left..right])
            while (true) {
                long windowSize = right - left + 1;
                long windowSum = prefix[right + 1] - prefix[left];
                long cost = (long) A.get(right) * windowSize - windowSum;
                if (cost <= B) break;
                left++;
            }

            int count = right - left + 1;
            if (count > bestCount || (count == bestCount && A.get(right) < bestVal)) {
                bestCount = count;
                bestVal = A.get(right);
            }
        }

        ArrayList<Integer> result = new ArrayList<>();
        result.add(bestCount);
        result.add(bestVal);
        return result;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N log N)` — sort + sliding window O(N)
- **Space:** `O(N)` — prefix sums

---

## 📙 Session 6 — Binary Search on Answer: Painter's Partition, Aggressive Cows, Allocation

---

### Q13: Aggressive Cows (Max Minimum Distance)

> Place B cows in N stalls to maximize the minimum distance between any two cows.

### 💡 Approach & Intuition

**Binary Search on Answer.** The answer (minimum distance) lies in `[1, max - min]`.

**Feasibility check:** For candidate distance `d`, greedily place cows left to right. Place first cow at stall[0], then each next cow at the first stall ≥ `lastPlaced + d`. If you can place all B cows → feasible.

**Search:** If feasible, try larger distance (`low = mid + 1`). Otherwise try smaller (`high = mid - 1`).

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A, int B) {
        Collections.sort(A);
        int low = 1, high = A.get(A.size() - 1) - A.get(0);
        int ans = 0;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (canPlace(A, B, mid)) {
                ans = mid;       // feasible, try larger distance
                low = mid + 1;
            } else {
                high = mid - 1;  // not feasible, try smaller
            }
        }
        return ans;
    }

    private boolean canPlace(ArrayList<Integer> stalls, int cows, int minDist) {
        int placed = 1;
        int lastPos = stalls.get(0);

        for (int i = 1; i < stalls.size(); i++) {
            if (stalls.get(i) - lastPos >= minDist) {
                placed++;
                lastPos = stalls.get(i);
                if (placed >= cows) return true;
            }
        }
        return placed >= cows;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N log N + N log(max - min))` — sort + BS × greedy check
- **Space:** `O(1)`

---

### Q14: Painter's Partition (Minimum Time)

> A painters, B time per unit, C[] board lengths. Minimize max time. Return mod 10000003.

### 💡 Approach & Intuition

**Binary Search on Answer.** Search for the minimum `maxBoardLength` a single painter handles.

- **low** = max(C) (one painter must handle at least the largest board)
- **high** = sum(C) (one painter paints everything)

**Feasibility check:** Greedily assign boards left to right. When adding a board would exceed `mid`, start a new painter. If painters needed ≤ A → feasible.

**Final answer** = `bestMid * B % 10000003`. Use `long` for all intermediate computations.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int paint(int A, int B, ArrayList<Integer> C) {
        long MOD = 10000003;
        long low = 0, high = 0;

        for (int len : C) {
            low = Math.max(low, len);  // at least the biggest board
            high += len;               // at most all boards
        }

        long ans = high;
        while (low <= high) {
            long mid = low + (high - low) / 2;
            if (canPaint(C, A, mid)) {
                ans = mid;
                high = mid - 1;  // try smaller max
            } else {
                low = mid + 1;   // need more room
            }
        }

        return (int) ((ans % MOD * (B % MOD)) % MOD);
    }

    private boolean canPaint(ArrayList<Integer> boards, int painters, long maxLen) {
        int needed = 1;
        long current = 0;

        for (int board : boards) {
            if (current + board > maxLen) {
                needed++;
                current = board;
                if (needed > painters) return false;
            } else {
                current += board;
            }
        }
        return true;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N log(sum))` — BS on answer × linear feasibility
- **Space:** `O(1)`

---

### Q15: Book Allocation (Minimize Maximum Pages)

> Allocate N books to B students contiguously. Minimize the max pages any student gets.

### 💡 Approach & Intuition

This is **identical** in structure to Painter's Partition! Binary search on the maximum pages allowed per student.

- **low** = max(A) (each book must be fully assigned)
- **high** = sum(A) (one student gets everything)
- **Feasibility:** Greedily assign books; when adding a book exceeds `mid`, assign to next student. Count students needed.

**Edge case:** If B > N, return -1 (not enough books).

### 🚀 Java 8 Solution

```java
public class Solution {
    public int books(ArrayList<Integer> A, int B) {
        int N = A.size();
        if (B > N) return -1; // more students than books

        long low = 0, high = 0;
        for (int pages : A) {
            low = Math.max(low, pages);
            high += pages;
        }

        long ans = high;
        while (low <= high) {
            long mid = low + (high - low) / 2;
            if (canAllocate(A, B, mid)) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return (int) ans;
    }

    private boolean canAllocate(ArrayList<Integer> books, int students, long maxPages) {
        int needed = 1;
        long current = 0;

        for (int pages : books) {
            if (current + pages > maxPages) {
                needed++;
                current = pages;
                if (needed > students) return false;
            } else {
                current += pages;
            }
        }
        return true;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N log(sum))`
- **Space:** `O(1)`

---

### Q16: Bth Smallest Triplet Sum

> Find the B-th smallest sum among all triplets `(i < j < k)`.

### 💡 Approach & Intuition

**Sort + Binary Search on Answer.** Sort the array. Binary search on the answer value `X`: for each candidate, count how many triplet sums ≤ X. If count ≥ B, search lower; otherwise higher.

**Counting triplets ≤ X:** Fix `i` and `j`, then the max `A[k]` is `X - A[i] - A[j]`. Use binary search (upper bound) on the remaining subarray `[j+1, N-1]` to count valid `k`.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A, int B) {
        Collections.sort(A);
        int N = A.size();

        long low = (long) A.get(0) + A.get(1) + A.get(2);
        long high = (long) A.get(N - 1) + A.get(N - 2) + A.get(N - 3);
        long ans = high;

        while (low <= high) {
            long mid = low + (high - low) / 2;
            if (countTripletsLE(A, mid) >= B) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return (int) ans;
    }

    private long countTripletsLE(ArrayList<Integer> A, long target) {
        int N = A.size();
        long count = 0;

        for (int i = 0; i < N - 2; i++) {
            for (int j = i + 1; j < N - 1; j++) {
                long remaining = target - A.get(i) - A.get(j);
                // Upper bound in A[j+1 .. N-1]
                int lo = j + 1, hi = N - 1, ub = j; // ub = last index where A[k] <= remaining
                while (lo <= hi) {
                    int m = lo + (hi - lo) / 2;
                    if (A.get(m) <= remaining) {
                        ub = m;
                        lo = m + 1;
                    } else {
                        hi = m - 1;
                    }
                }
                if (ub >= j + 1) count += (ub - j);
            }
        }
        return count;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N² log N * log(range))` — BS on answer × N² pairs × BS per pair
- **Space:** `O(1)`

---

### Q17: Max Subarray Length with Sum ≤ B

> Find max K such that no subarray of size K has sum > B.

### 💡 Approach & Intuition

**Binary Search on K.** If a length K is valid (no subarray of that size exceeds B), then K-1 is also valid. So the answer is monotonic — perfect for binary search.

**Check function:** For candidate `K`, compute all window sums of size K using prefix sums. If any window sum > B, K is invalid.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A, int B) {
        int N = A.size();
        long[] prefix = new long[N + 1];
        for (int i = 0; i < N; i++) {
            prefix[i + 1] = prefix[i] + A.get(i);
        }

        int low = 1, high = N, ans = 0;

        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (isValid(prefix, N, mid, B)) {
                ans = mid;       // valid, try larger K
                low = mid + 1;
            } else {
                high = mid - 1;  // some window exceeds B
            }
        }
        return ans;
    }

    private boolean isValid(long[] prefix, int N, int K, int B) {
        for (int i = 0; i + K <= N; i++) {
            long windowSum = prefix[i + K] - prefix[i];
            if (windowSum > B) return false;
        }
        return true;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N log N)` — BS on K × linear window check
- **Space:** `O(N)` — prefix sums

---

## 🎯 Exam Strategy & Pattern Recognition

| If you see...                     | Think...                            | Template                             |
| --------------------------------- | ----------------------------------- | ------------------------------------ |
| "Sorted array, find element"      | Standard binary search              | `low ≤ high`, compare `A[mid]`       |
| "First / last occurrence"         | Modified BS, continue after find    | Update `ans`, search left/right      |
| "Insert position" / "lower bound" | Find first `≥ B`                    | Track `ans`, search left on match    |
| "Peak element"                    | Compare `A[mid]` with neighbors     | `low < high` loop, slope-based       |
| "Rotated sorted array"            | One half always sorted              | Check which half, narrow accordingly |
| "Sorted matrix"                   | Flatten to 1D or row-by-row BS      | `row = mid/M, col = mid%M`           |
| "Minimize the maximum"            | **BS on answer** + greedy check     | Painter/Books pattern                |
| "Maximize the minimum"            | **BS on answer** + greedy check     | Aggressive Cows pattern              |
| "`floor(sqrt(A))`"                | BS on answer, check `mid*mid ≤ A`   | Use `long` for overflow              |
| "Median of sorted arrays"         | Partition-based BS on smaller array | Balance left/right halves            |
| "Kth smallest"                    | BS on answer + count function       | Count elements ≤ mid                 |

---

### 🔑 The "BS on Answer" Family (Most Exam-Critical!)

These three problems are **structurally identical**:

| Problem                 | What to Minimize/Maximize          | Low        | High        | Feasibility Check               |
| ----------------------- | ---------------------------------- | ---------- | ----------- | ------------------------------- |
| **Painter's Partition** | Minimize max work per painter      | max(board) | sum(boards) | Greedy assign, count painters   |
| **Book Allocation**     | Minimize max pages per student     | max(pages) | sum(pages)  | Greedy assign, count students   |
| **Aggressive Cows**     | Maximize min distance between cows | 1          | max - min   | Greedy place, count cows placed |

> 💪 **Pro tip for the exam:** If a problem asks "minimize the maximum" or "maximize the minimum", it's almost certainly **binary search on the answer**. Write the feasibility check as a greedy function, and the binary search wrapper is always the same template. Master one of these and you've automatically mastered all three.
