# 🧠 DSA Bit Manipulation — Complete Exam Study Guide

> Covering **Sessions 1, 2, 3** of your FDSA syllabus. All 19 practice problems solved with step-by-step approach, Java 8 code, and complexity analysis.

---

## 📌 Concept Cheat Sheet

### Core Operators

| Operator    | Symbol | Rule                     | Example                           |
| ----------- | ------ | ------------------------ | --------------------------------- |
| AND         | `&`    | Both bits 1 → 1          | `5 & 3` = `1` (101 & 011 = 001)   |
| OR          | `\|`   | Either bit 1 → 1         | `5 \| 3` = `7` (101 \| 011 = 111) |
| XOR         | `^`    | Different bits → 1       | `5 ^ 3` = `6` (101 ^ 011 = 110)   |
| NOT         | `~`    | Flip all bits            | `~5` = `-6` (inverts all 32 bits) |
| Left Shift  | `<<`   | Shift bits left, fill 0s | `5 << 1` = `10` (101 → 1010)      |
| Right Shift | `>>`   | Shift bits right         | `5 >> 1` = `2` (101 → 10)         |

### Essential Properties

| Property                   | Formula                                                   |
| -------------------------- | --------------------------------------------------------- |
| **XOR with self**          | `a ^ a = 0`                                               |
| **XOR with zero**          | `a ^ 0 = a`                                               |
| **Check i-th bit**         | `(a >> i) & 1`                                            |
| **Set i-th bit**           | `a \| (1 << i)`                                           |
| **Unset i-th bit**         | `a & ~(1 << i)`                                           |
| **Flip / Toggle i-th bit** | `a ^ (1 << i)`                                            |
| **Count set bits**         | Brian Kernighan: `a = a & (a - 1)` removes lowest set bit |
| **XOR + ADD relation**     | `a + b = a ^ b` only when `a & b = 0` (no carry)          |
| **Negative numbers**       | Java uses 2's complement for signed 32-bit ints           |

### When to use what

| Pattern                   | Use When                                                   |
| ------------------------- | ---------------------------------------------------------- |
| **XOR all elements**      | Finding unique element(s) among duplicates                 |
| **Bit-by-bit counting**   | Summing/counting across pairs (bit contribution technique) |
| **Mask construction**     | Setting/unsetting specific bit positions                   |
| **Binary representation** | Magic numbers, binary strings, power-of-2 relationships    |

---

## 📗 Session 1 — Bit Manipulations Basics

---

### Q1: Add Binary Strings

> Given two binary strings A and B, return their sum as a binary string.

### 💡 Approach & Intuition

**Brute Force:** Convert to integers, add, convert back — but strings can be up to 10⁵ digits, far exceeding `long` range.

**Optimal Strategy:** Simulate binary addition from right to left, just like grade-school addition, maintaining a carry.

**Walkthrough with `A = "100"`, `B = "11"`:**

- Position 2: `0 + 1 + carry(0)` = `1`, carry = `0`
- Position 1: `0 + 1 + 0` = `1`, carry = `0`
- Position 0: `1 + 0 + 0` = `1`, carry = `0`
- Result: `"111"` ✅

### 🚀 Java 8 Solution

```java
public class Solution {
    public String addBinary(String A, String B) {
        StringBuilder result = new StringBuilder();
        int i = A.length() - 1, j = B.length() - 1;
        int carry = 0;

        while (i >= 0 || j >= 0 || carry > 0) {
            int sum = carry;
            if (i >= 0) sum += A.charAt(i--) - '0';
            if (j >= 0) sum += B.charAt(j--) - '0';
            result.append(sum % 2);   // current bit
            carry = sum / 2;          // carry for next position
        }

        return result.reverse().toString();
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(max(M, N))` — single pass through both strings
- **Space:** `O(max(M, N))` — for the result string

---

### Q2: Magic Number (Sum of Unique Powers of 5)

> The A-th magic number where magic numbers are sums of unique powers of 5.

### 💡 Approach & Intuition

**Key Insight:** Magic numbers map exactly to binary representations! The sequence `5, 25, 30, 125, 130, ...` corresponds to binary `1, 10, 11, 100, 101, ...`

For A = 3 → binary `11` → `5¹ + 5²` = `5 + 25` = `30` ✅

**Strategy:** Read bits of A from LSB. If bit is `1`, add the corresponding power of 5.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(int A) {
        int result = 0;
        int power = 5; // start with 5^1

        while (A > 0) {
            if ((A & 1) == 1) { // if current bit is set
                result += power;
            }
            power *= 5;   // next power of 5
            A >>= 1;      // move to next bit
        }

        return result;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(log A)` — number of bits in A
- **Space:** `O(1)`

---

### Q3: XOR Sum Equals Sum (Find X and Y, return X ^ Y)

> X = greatest number < A where `X + A = X ^ A`. Y = smallest number > A where `Y + A = Y ^ A`.

### 💡 Approach & Intuition

**Key Math:** `a + b = a ^ b` only when `a & b = 0` (no overlapping set bits → no carry).

So X and Y must have `0` in every bit position where A has `1`. They can only have set bits where A has `0` bits.

- **X (greatest < A):** Set all the `0`-bits of A that are below the MSB of A. For A = 5 (`101`), complement bits below MSB within same bit-length → X = `010` = 2.
- **Y (smallest > A):** Set the very next `0`-bit above A's MSB. For A = 5 (`101`), next power of 2 above → Y = `1000` = 8.

**Result:** `X ^ Y = 2 ^ 8 = 10` ✅

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(int A) {
        // Find the number of bits in A
        int bits = 0;
        int temp = A;
        while (temp > 0) {
            bits++;
            temp >>= 1;
        }

        // X: complement of A within its bit length (flips all bits within range)
        // This gives us all the 0-bits of A set to 1 within its bit-width
        int X = ((1 << bits) - 1) ^ A;

        // Y: the next power of 2 beyond A's MSB
        int Y = (1 << bits);

        return X ^ Y;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(log A)` — counting bits
- **Space:** `O(1)`

---

## 📘 Session 2 — Check / Set / Unset / Flip Bits, Subarrays

---

### Q4: Set Two Bits in Zero

> Set the A-th and B-th bit in 0, return the decimal result.

### 💡 Approach & Intuition

Simply use the set-bit formula twice: `result = (1 << A) | (1 << B)`.

For A=3, B=5: `(1<<3) | (1<<5)` = `8 | 32` = `40` → binary `101000` ✅

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(int A, int B) {
        return (1 << A) | (1 << B);
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(1)` — constant-time bitwise ops
- **Space:** `O(1)`

---

### Q5: Unset i-th Bit

> If B-th bit in A is set, unset it. Otherwise leave as is.

### 💡 Approach & Intuition

Use the unset-bit formula: `A & ~(1 << B)`. This creates a mask with all bits `1` except position B, then ANDs with A.

For A=5 (`101`), B=2: mask = `~(100)` = `...011` → `101 & 011` = `001` = 1 ✅

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(int A, int B) {
        return A & ~(1 << B); // unset B-th bit
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(1)`
- **Space:** `O(1)`

---

### Q6: Toggle / Flip i-th Bit

> If B-th bit in A is set, unset it. If unset, set it.

### 💡 Approach & Intuition

XOR with a mask: `A ^ (1 << B)`. XOR with `1` flips a bit.

For A=4 (`100`), B=1: `100 ^ 010` = `110` = 6 ✅

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(int A, int B) {
        return A ^ (1 << B); // toggle B-th bit
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(1)`
- **Space:** `O(1)`

---

### Q7: Unset B Rightmost Bits

> Given A and B, unset the rightmost B bits of A.

### 💡 Approach & Intuition

Create a mask that has `0`s in the last B positions and `1`s everywhere else: `~((1L << B) - 1)`. Then AND with A.

For A=25 (`11001`), B=3: mask = `...11000` → `11001 & 11000` = `11000` = 24 ✅

**Watch out:** A can be up to 10¹⁸, so use `Long`.

### 🚀 Java 8 Solution

```java
public class Solution {
    public Long solve(Long A, int B) {
        // Mask: all 1s except last B bits
        Long mask = ~((1L << B) - 1L);
        return A & mask;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(1)`
- **Space:** `O(1)`

---

### Q8: Max AND Pair

> Find the maximum `A[i] & A[j]` for all pairs `(i, j)` where `i != j`.

### 💡 Approach & Intuition

**Brute Force:** Check all O(N²) pairs — too slow for N = 10⁵.

**Optimal (Greedy bit-by-bit from MSB):** Start from the highest bit (bit 29). For each bit, check how many numbers have this bit set. If ≥ 2 numbers have it set, keep only those numbers and lock this bit into the answer. Repeat for the next lower bit.

This works because **a higher bit contributes more than all lower bits combined**.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A) {
        int ans = 0;

        for (int bit = 29; bit >= 0; bit--) {
            // Count numbers that have all currently locked bits + this bit set
            int count = 0;
            int candidate = ans | (1 << bit);
            for (int num : A) {
                if ((num & candidate) == candidate) {
                    count++;
                }
            }
            // If at least 2 numbers match, lock this bit
            if (count >= 2) {
                ans = candidate;
            }
        }

        return ans;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(30 * N)` = `O(N)` — 30 bits × N elements
- **Space:** `O(1)`

---

### Q9: Sum of OR of All Subarrays

> Return sum of `BITWISE OR` of all subarrays, mod 10⁹+7.

### 💡 Approach & Intuition

**Bit Contribution Technique:** For each bit position `b`, calculate how many subarrays have that bit set in their OR result.

A subarray has bit `b` set in its OR ↔ **at least one element** in the subarray has bit `b` set.

**Complement counting:** Total subarrays − subarrays where **no element** has bit `b` set. Subarrays with all 0s at bit `b` form contiguous groups of length `len`, contributing `len*(len+1)/2` zero-subarrays.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A) {
        long MOD = 1000000007;
        long totalSum = 0;
        int N = A.size();
        long totalSubs = (long) N * (N + 1) / 2;

        for (int bit = 0; bit < 27; bit++) {
            // Count subarrays where this bit is NOT set in any element
            long zeroSubs = 0;
            long consecutive = 0; // consecutive elements without this bit

            for (int i = 0; i < N; i++) {
                if ((A.get(i) & (1 << bit)) == 0) {
                    consecutive++;
                } else {
                    zeroSubs += consecutive * (consecutive + 1) / 2;
                    consecutive = 0;
                }
            }
            zeroSubs += consecutive * (consecutive + 1) / 2;

            // Subarrays where OR has this bit set
            long oneSubs = totalSubs - zeroSubs;
            totalSum = (totalSum + (oneSubs % MOD * ((1L << bit) % MOD)) % MOD) % MOD;
        }

        return (int) (totalSum % MOD);
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(27 * N)` = `O(N)` — 27 bits × N elements
- **Space:** `O(1)`

---

### Q10: Subarrays with OR = 1 (Binary Array)

> Count subarrays in a binary array where bitwise OR = 1.

### 💡 Approach & Intuition

OR of a subarray is `1` if **at least one element** is `1`. Complement: total subarrays − subarrays with all `0`s.

Count groups of consecutive 0s. A group of length `k` contributes `k*(k+1)/2` all-zero subarrays.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int subarraysWithOR1(ArrayList<Integer> A) {
        int N = A.size();
        long total = (long) N * (N + 1) / 2;
        long zeroSubs = 0;
        long consecutive = 0;

        for (int val : A) {
            if (val == 0) {
                consecutive++;
            } else {
                zeroSubs += consecutive * (consecutive + 1) / 2;
                consecutive = 0;
            }
        }
        zeroSubs += consecutive * (consecutive + 1) / 2;

        return (int) (total - zeroSubs);
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — single pass
- **Space:** `O(1)`

---

### Q11: Sum of f(Ai, Aj) — Count Different Bits for All Pairs

> `f(X, Y)` = number of bit positions where X and Y differ. Find `Σ f(Ai, Aj)` for all pairs including `(i,i)`.

### 💡 Approach & Intuition

**Bit Contribution Technique:** For each bit position, count how many numbers have that bit `set` (say `c`) and how many have it `unset` (say `N - c`). Each pair with different bits at this position contributes 1.

Number of **ordered** pairs with different bits = `c * (N - c) * 2` (since `f(i,j)` and `f(j,i)` are both counted, and the problem counts all `i,j` including both orders).

### 🚀 Java 8 Solution

```java
public class Solution {
    public int cntBits(ArrayList<Integer> A) {
        long MOD = 1000000007;
        int N = A.size();
        long ans = 0;

        for (int bit = 0; bit < 31; bit++) {
            long count = 0; // numbers with this bit set
            for (int num : A) {
                if ((num & (1 << bit)) != 0) {
                    count++;
                }
            }
            // Ordered pairs with different bits at this position
            ans = (ans + (2 * count % MOD * ((N - count) % MOD)) % MOD) % MOD;
        }

        return (int) (ans % MOD);
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(31 * N)` = `O(N)`
- **Space:** `O(1)`

---

### Q12: Minimum XOR Value Pair

> Find the pair in the array with minimum XOR.

### 💡 Approach & Intuition

**Key Insight:** If you **sort** the array, the minimum XOR pair must be **adjacent** elements. Why? XOR measures "difference" — close values have fewer differing upper bits. Sorting brings similar values together.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int findMinXor(ArrayList<Integer> A) {
        Collections.sort(A);
        int minXor = Integer.MAX_VALUE;

        for (int i = 1; i < A.size(); i++) {
            minXor = Math.min(minXor, A.get(i) ^ A.get(i - 1));
        }

        return minXor;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N log N)` — dominated by sort
- **Space:** `O(1)` (ignoring sort's internal space)

---

### Q13: Reduce Array to Zero (Split & XOR Merge)

> Can you reduce the array to a single element `0` using split (B = C + D) and merge (R = P ^ Q)?

### 💡 Approach & Intuition

**Key Insight:** Any **even number** can eventually be split into two equal halves, and `x ^ x = 0`. So every even number can be reduced to 0.

Any **odd number** can be split into an even part + 1, and the even part can be zeroed out — leaving a `1`. So each odd number contributes exactly one `1`.

If we have an even count of `1`s, they all cancel via XOR. If odd count → impossible.

**Therefore:** Count odd numbers. If the count is even → `"Yes"`, otherwise → `"No"`.

### 🚀 Java 8 Solution

```java
public class Solution {
    public String solve(ArrayList<Integer> A) {
        int oddCount = 0;
        for (int num : A) {
            if (num % 2 != 0) {
                oddCount++;
            }
        }
        return (oddCount % 2 == 0) ? "Yes" : "No";
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — single pass
- **Space:** `O(1)`

---

## 📙 Session 3 — Count Set Bits, Single Number Variants, Advanced

---

### Q14: Count Set Bits (Number of 1 Bits)

> Count the number of 1s in the binary representation of A.

### 💡 Approach & Intuition

**Brian Kernighan's Algorithm:** `A & (A - 1)` removes the lowest set bit. Count how many times you can do this before A becomes 0.

For A = 11 (`1011`): 1011 → 1010 → 1000 → 0000 = **3 iterations** ✅

### 🚀 Java 8 Solution

```java
public class Solution {
    public int numSetBits(int A) {
        int count = 0;
        while (A > 0) {
            A = A & (A - 1); // remove lowest set bit
            count++;
        }
        return count;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(number of set bits)` — at most O(32)
- **Space:** `O(1)`

---

### Q15: Single Number (Every element twice, one unique)

> Every element appears twice except one. Find it.

### 💡 Approach & Intuition

XOR all elements. Pairs cancel out (`a ^ a = 0`), leaving only the unique element.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int singleNumber(final List<Integer> A) {
        int result = 0;
        for (int num : A) {
            result ^= num;
        }
        return result;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — single pass
- **Space:** `O(1)` — no extra memory

---

### Q16: Single Number II (Every element thrice, one unique)

> Every element appears three times except one. Find it.

### 💡 Approach & Intuition

XOR alone won't work since `a ^ a ^ a = a` (not zero for odd repetitions).

**Bit Counting Approach:** For each of the 32 bit positions, count total set bits across all elements. If a bit appears in multiples of 3, it belongs to triplicated elements. The remainder (`count % 3`) gives the unique element's bit.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int singleNumber(final List<Integer> A) {
        int result = 0;

        for (int bit = 0; bit < 32; bit++) {
            int count = 0;
            for (int num : A) {
                if ((num & (1 << bit)) != 0) {
                    count++;
                }
            }
            if (count % 3 != 0) {
                result |= (1 << bit); // this bit belongs to the unique element
            }
        }

        return result;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(32 * N)` = `O(N)`
- **Space:** `O(1)`

---

### Q17: Single Number III (Every element twice, TWO unique)

> Two elements appear once, rest appear twice. Find both.

### 💡 Approach & Intuition

1. **XOR all** → gives `x ^ y` (the two uniques XORed together)
2. **Find any set bit** in `x ^ y` — this bit differs between x and y
3. **Partition** all elements into two groups based on that bit
4. **XOR each group separately** → one group yields x, the other yields y

### 🚀 Java 8 Solution

```java
public class Solution {
    public ArrayList<Integer> solve(ArrayList<Integer> A) {
        // Step 1: XOR all to get x ^ y
        int xorAll = 0;
        for (int num : A) xorAll ^= num;

        // Step 2: Find rightmost set bit (differentiating bit)
        int diffBit = xorAll & (-xorAll); // isolates lowest set bit

        // Step 3: Partition and XOR separately
        int group1 = 0, group2 = 0;
        for (int num : A) {
            if ((num & diffBit) != 0) {
                group1 ^= num;
            } else {
                group2 ^= num;
            }
        }

        // Step 4: Return in ascending order
        ArrayList<Integer> result = new ArrayList<>();
        result.add(Math.min(group1, group2));
        result.add(Math.max(group1, group2));
        return result;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — two passes
- **Space:** `O(1)` — excluding output

---

### Q18: Minimum Help from Sam (Count Set Bits = Help Count)

> Alex starts at 0, can double his score or add 1 (with Sam's help). Min help to reach A?

### 💡 Approach & Intuition

Doubling = left shift, adding 1 = setting the current bit. This is just **building A in binary from MSB to LSB**!

Each `1` in A's binary means Sam helped once. **Answer = number of set bits in A.**

A=5 (`101`) → 2 set bits → help = 2 ✅

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(int A) {
        int count = 0;
        while (A > 0) {
            count += (A & 1); // count set bits
            A >>= 1;
        }
        return count;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(log A)`
- **Space:** `O(1)`

---

### Q19: Boomer's Food Days (Count Set Bits)

> Day 1: 1 unit, Day 2: 2 units, Day 3: 4 units... Total = A. How many days did Boomer eat?

### 💡 Approach & Intuition

Each day's food is a power of 2. The total A is a sum of distinct powers of 2 — that's literally the **binary representation** of A! Number of days = number of set bits.

A=5 (`101`) → Day 1 (1 unit) + Day 3 (4 units) = 2 days ✅

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(int A) {
        int count = 0;
        while (A > 0) {
            A = A & (A - 1); // Brian Kernighan
            count++;
        }
        return count;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(number of set bits)`
- **Space:** `O(1)`

---

### Q20: Sum of XOR of All Pairs

> Find sum of `A[i] ^ A[j]` for all pairs `(i < j)`, mod 10⁹+7.

### 💡 Approach & Intuition

**Bit Contribution Technique again!** For each bit position:

- `c` = count of numbers with this bit set
- `N - c` = count without
- Number of pairs with different bits = `c * (N - c)`
- Each such pair contributes `2^bit` to the XOR sum

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ArrayList<Integer> A) {
        long MOD = 1000000007;
        int N = A.size();
        long ans = 0;

        for (int bit = 0; bit < 30; bit++) {
            long count = 0;
            for (int num : A) {
                if ((num & (1 << bit)) != 0) count++;
            }
            // Each pair contributes (1 << bit)
            long pairs = count * (N - count);
            ans = (ans + (pairs % MOD * ((1L << bit) % MOD)) % MOD) % MOD;
        }

        return (int) (ans % MOD);
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(30 * N)` = `O(N)`
- **Space:** `O(1)`

---

### Q21: Two Missing Numbers

> Array of N distinct integers from [1, N+2]. Find the 2 missing numbers.

### 💡 Approach & Intuition

Combine the array elements with numbers 1 to N+2. In this combined set, every number appears twice **except** the two missing ones which appear once. This reduces to **Single Number III**!

### 🚀 Java 8 Solution

```java
public class Solution {
    public ArrayList<Integer> solve(ArrayList<Integer> A) {
        int N = A.size();
        // XOR all array elements and all numbers from 1 to N+2
        int xorAll = 0;
        for (int num : A) xorAll ^= num;
        for (int i = 1; i <= N + 2; i++) xorAll ^= i;

        // xorAll = missing1 ^ missing2
        int diffBit = xorAll & (-xorAll); // rightmost set bit

        int group1 = 0, group2 = 0;
        for (int num : A) {
            if ((num & diffBit) != 0) group1 ^= num;
            else group2 ^= num;
        }
        for (int i = 1; i <= N + 2; i++) {
            if ((i & diffBit) != 0) group1 ^= i;
            else group2 ^= i;
        }

        ArrayList<Integer> result = new ArrayList<>();
        result.add(Math.min(group1, group2));
        result.add(Math.max(group1, group2));
        return result;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — two passes through N and N+2 elements
- **Space:** `O(1)` — excluding output

---

### Q22: Divide Two Integers Without \*, /, %

> Return `floor(A / B)` without using multiplication, division, or mod. Handle overflow.

### 💡 Approach & Intuition

**Bit Manipulation Division:** Use repeated doubling (left shift) of the divisor. Find the largest `B * 2^k` that fits into the remaining dividend, subtract it, and add `2^k` to the quotient.

**Overflow case:** `Integer.MIN_VALUE / -1` overflows because `|MIN_VALUE| > MAX_VALUE` in 32-bit signed.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int divide(int A, int B) {
        // Handle overflow: -2^31 / -1 = 2^31 > INT_MAX
        if (A == Integer.MIN_VALUE && B == -1) return Integer.MAX_VALUE;

        // Work with positive longs to avoid overflow
        long dividend = Math.abs((long) A);
        long divisor = Math.abs((long) B);
        boolean negative = (A < 0) ^ (B < 0); // result is negative if signs differ

        long quotient = 0;
        while (dividend >= divisor) {
            long tempDivisor = divisor;
            long multiple = 1;
            // Double the divisor until it would exceed the dividend
            while (dividend >= (tempDivisor << 1)) {
                tempDivisor <<= 1;
                multiple <<= 1;
            }
            dividend -= tempDivisor;
            quotient += multiple;
        }

        long result = negative ? -quotient : quotient;
        // Clamp to int range
        return (int) Math.min(Math.max(result, Integer.MIN_VALUE), Integer.MAX_VALUE);
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(log²(A/B))` — outer loop is O(log(A/B)), inner doubling is O(log(A/B))
- **Space:** `O(1)`

---

## 🎯 Exam Strategy & Pattern Recognition

| If you see...                      | Think...                      | Key Technique                     |
| ---------------------------------- | ----------------------------- | --------------------------------- |
| "Find unique element"              | XOR everything                | `a ^ a = 0`                       |
| "Every element thrice, one unique" | Bit counting mod 3            | Count bits at each position       |
| "Two unique elements"              | XOR + partition by diff bit   | Single Number III pattern         |
| "Sum/count across all pairs"       | Bit contribution technique    | `set_count * unset_count` per bit |
| "Sum of all subarrays"             | Bit contribution + complement | Total − zero-subarrays            |
| "Set/Unset/Toggle a bit"           | Mask operations               | `\|`, `& ~`, `^` with `(1 << i)`  |
| "Without \*, /, %"                 | Bit shifting                  | Left shift = ×2, Right shift = ÷2 |
| "Powers of 2 sum"                  | Binary representation         | Count/read set bits               |
| "Minimum XOR pair"                 | Sort first                    | Adjacent elements in sorted array |
| "a + b = a ^ b"                    | No carry condition            | `a & b = 0`                       |

---

> 💪 **Pro tip for the exam:** Most bit manipulation problems boil down to one of three patterns: **(1)** XOR cancellation for finding uniques, **(2)** Bit contribution technique for aggregate calculations, or **(3)** Mask operations for individual bit manipulation. Master these three and you'll cover 90% of questions.
