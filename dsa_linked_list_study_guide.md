# 🔗 DSA Linked List — Complete Exam Study Guide

> Covering **Sessions 10, 11** of your FDSA syllabus. All 15 practice problems solved with step-by-step approach, Java 8 code, and complexity analysis.

---

## 📌 Concept Cheat Sheet

### ListNode Definition (Used in All Problems)

```java
class ListNode {
    public int val;
    public ListNode next;
    ListNode(int x) { val = x; next = null; }
}
```

### Core Linked List Patterns

| Pattern                      | When to Use                             | Key Idea                                       |
| ---------------------------- | --------------------------------------- | ---------------------------------------------- |
| **Dummy Head**               | Insert/delete at head might change head | `dummy.next = head`, return `dummy.next`       |
| **Two Pointers (Fast/Slow)** | Find middle, detect cycles              | Slow moves 1, fast moves 2                     |
| **Prev/Curr/Next**           | Reversing a linked list                 | Save `next`, point `curr.next = prev`, advance |
| **Carry propagation**        | Adding numbers                          | Same as binary string addition                 |
| **In-place reversal**        | Reverse section/groups                  | Track boundaries, reconnect                    |

### Critical Pitfalls

| Pitfall                      | Fix                                              |
| ---------------------------- | ------------------------------------------------ |
| **NullPointerException**     | Always check `node != null` before `.next`       |
| **Losing the head**          | Use a dummy node when head might change          |
| **Off-by-one in position**   | Clarify 0-based vs 1-based indexing              |
| **Not updating pointers**    | Draw the state diagram before coding             |
| **Modifying read-only list** | Use new nodes instead of modifying existing ones |

### The Reversal Template (Memorize This!)

```java
ListNode prev = null, curr = head;
while (curr != null) {
    ListNode next = curr.next;  // save next
    curr.next = prev;           // reverse link
    prev = curr;                // advance prev
    curr = next;                // advance curr
}
// prev is now the new head
```

---

## 📗 Session 10 — Basics: Traverse, Insert, Delete, Access

---

### Q1: Print Linked List

> Print all elements space-separated.

### 💡 Approach & Intuition

Simple traversal. Walk through each node and print its value.

### 🚀 Java 8 Solution

```java
public class Solution {
    public void solve(ListNode A) {
        ListNode curr = A;
        while (curr != null) {
            System.out.print(curr.val + " ");
            curr = curr.next;
        }
        System.out.println();
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)`
- **Space:** `O(1)`

---

### Q2: Access K-th Node

> Return value at index K (0-based). Return -1 if out of bounds.

### 💡 Approach & Intuition

Walk K steps from the head. If we hit null before reaching K, return -1.

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ListNode A, int B) {
        ListNode curr = A;
        int index = 0;
        while (curr != null) {
            if (index == B) return curr.val;
            curr = curr.next;
            index++;
        }
        return -1; // out of bounds
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(B)` worst case `O(N)`
- **Space:** `O(1)`

---

### Q3: Insert Node at Position

> Insert value B at position C (0-based). If C > length, insert at tail. If C = 0, insert at head.

### 💡 Approach & Intuition

Use a **dummy node** to simplify head insertion. Walk to position `C-1` (the node before insertion point), then splice in the new node.

### 🚀 Java 8 Solution

```java
public class Solution {
    public ListNode solve(ListNode A, int B, int C) {
        ListNode newNode = new ListNode(B);

        // Insert at head
        if (C == 0) {
            newNode.next = A;
            return newNode;
        }

        ListNode curr = A;
        // Walk to position C-1 or end of list
        for (int i = 0; i < C - 1 && curr.next != null; i++) {
            curr = curr.next;
        }

        // Insert after curr
        newNode.next = curr.next;
        curr.next = newNode;
        return A;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)`
- **Space:** `O(1)`

---

### Q4: Delete Node at Position (0-based)

> Delete the B-th node (0-based) from the linked list.

### 💡 Approach & Intuition

If B = 0, return `head.next`. Otherwise, walk to node at position `B-1` and skip the next node: `prev.next = prev.next.next`.

### 🚀 Java 8 Solution

```java
public class Solution {
    public ListNode solve(ListNode A, int B) {
        if (B == 0) return A.next; // delete head

        ListNode curr = A;
        for (int i = 0; i < B - 1; i++) {
            curr = curr.next;
        }
        // Skip the B-th node
        curr.next = curr.next.next;
        return A;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(B)`
- **Space:** `O(1)`

---

### Q5: Design Linked List (Insert, Delete, Print)

> Implement `insert_node(position, value)`, `delete_node(position)`, `print_ll()` with 1-based indexing.

### 💡 Approach & Intuition

Maintain a **static head** reference. Use standard insert/delete logic with boundary checks.

### 🚀 Java 8 Solution

```java
    static ListNode head = null;
    static int size = 0;

    public static void insert_node(int position, int value) {
        if (position < 1 || position > size + 1) return;

        ListNode newNode = new ListNode(value);

        if (position == 1) {
            newNode.next = head;
            head = newNode;
        } else {
            ListNode curr = head;
            for (int i = 1; i < position - 1; i++) {
                curr = curr.next;
            }
            newNode.next = curr.next;
            curr.next = newNode;
        }
        size++;
    }

    public static void delete_node(int position) {
        if (position < 1 || position > size) return;

        if (position == 1) {
            head = head.next;
        } else {
            ListNode curr = head;
            for (int i = 1; i < position - 1; i++) {
                curr = curr.next;
            }
            curr.next = curr.next.next;
        }
        size--;
    }

    public static void print_ll() {
        ListNode curr = head;
        StringBuilder sb = new StringBuilder();
        while (curr != null) {
            if (sb.length() > 0) sb.append(" ");
            sb.append(curr.val);
            curr = curr.next;
        }
        System.out.println(sb.toString());
    }
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` per operation
- **Space:** `O(1)` per operation

---

### Q6: Remove N-th Node From End

> Remove the B-th node from the end. If B > size, remove first node.

### 💡 Approach & Intuition

**Two-pointer technique:** Move `fast` pointer B steps ahead. Then move both `fast` and `slow` together. When `fast` reaches the end, `slow` is at the node before the target.

**Edge case:** If `B ≥ size`, remove the head node.

### 🚀 Java 8 Solution

```java
public class Solution {
    public ListNode removeNthFromEnd(ListNode A, int B) {
        // Count the size
        int size = 0;
        ListNode curr = A;
        while (curr != null) {
            size++;
            curr = curr.next;
        }

        // If B >= size, remove the head
        if (B >= size) return A.next;

        // Find the (size - B - 1)-th node (node before target)
        int target = size - B;
        curr = A;
        for (int i = 0; i < target - 1; i++) {
            curr = curr.next;
        }
        curr.next = curr.next.next;
        return A;
    }
}
```

### 🚀 Alternative: Two-Pointer (Constant Space, One Pass\*)

```java
public class Solution {
    public ListNode removeNthFromEnd(ListNode A, int B) {
        ListNode dummy = new ListNode(0);
        dummy.next = A;
        ListNode fast = dummy, slow = dummy;

        // Move fast B+1 steps ahead
        for (int i = 0; i <= B; i++) {
            if (fast == null) {
                // B >= size, remove head
                return A.next;
            }
            fast = fast.next;
        }

        // Move both until fast reaches end
        while (fast != null) {
            fast = fast.next;
            slow = slow.next;
        }

        slow.next = slow.next.next;
        return dummy.next;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — single/double pass
- **Space:** `O(1)`

---

### Q7: Add Two Numbers (Reversed Digits)

> Two linked lists represent numbers in reverse. Add them and return result as a linked list.

### 💡 Approach & Intuition

Exactly like adding binary strings! Walk both lists simultaneously, maintain a carry, and build the result list node by node.

**Walkthrough:** `342 + 465`:

- Lists: `2→4→3` and `5→6→4`
- `2+5=7`, `4+6=10` (write 0, carry 1), `3+4+1=8`
- Result: `7→0→8` = 807 ✅

### 🚀 Java 8 Solution

```java
public class Solution {
    public ListNode addTwoNumbers(ListNode A, ListNode B) {
        ListNode dummy = new ListNode(0);
        ListNode curr = dummy;
        int carry = 0;

        while (A != null || B != null || carry > 0) {
            int sum = carry;
            if (A != null) { sum += A.val; A = A.next; }
            if (B != null) { sum += B.val; B = B.next; }

            curr.next = new ListNode(sum % 10);
            curr = curr.next;
            carry = sum / 10;
        }

        return dummy.next;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(max(M, N))`
- **Space:** `O(max(M, N))` — for the result list

---

### Q8: Swap Adjacent Pairs

> Swap every two adjacent nodes (not values). Constant space.

### 💡 Approach & Intuition

Use a **dummy node**. For each pair `(first, second)`:

1. `prev.next = second`
2. `first.next = second.next`
3. `second.next = first`
4. Advance `prev` to `first` (which is now second in the pair)

### 🚀 Java 8 Solution

```java
public class Solution {
    public ListNode swapPairs(ListNode A) {
        ListNode dummy = new ListNode(0);
        dummy.next = A;
        ListNode prev = dummy;

        while (prev.next != null && prev.next.next != null) {
            ListNode first = prev.next;
            ListNode second = prev.next.next;

            // Swap
            prev.next = second;
            first.next = second.next;
            second.next = first;

            // Advance
            prev = first; // first is now the second node in the swapped pair
        }

        return dummy.next;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)`
- **Space:** `O(1)`

---

## 📘 Session 11 — Reverse, Deep Copy, Palindrome, Middle

---

### Q9: Reverse Linked List (Iterative)

> Reverse the entire linked list in-place, one pass.

### 💡 Approach & Intuition

The classic **three-pointer reversal**: `prev`, `curr`, `next`. At each step, reverse `curr`'s pointer, then advance all three.

**Walkthrough with `1→2→3→4→5`:**

```
Step 1: null←1  2→3→4→5   (prev=1, curr=2)
Step 2: null←1←2  3→4→5   (prev=2, curr=3)
Step 3: null←1←2←3  4→5   (prev=3, curr=4)
Step 4: null←1←2←3←4  5   (prev=4, curr=5)
Step 5: null←1←2←3←4←5    (prev=5, curr=null) → return prev
```

### 🚀 Java 8 Solution

```java
public class Solution {
    public ListNode reverseList(ListNode A) {
        ListNode prev = null, curr = A;

        while (curr != null) {
            ListNode next = curr.next; // save
            curr.next = prev;          // reverse
            prev = curr;               // advance prev
            curr = next;               // advance curr
        }

        return prev; // new head
    }
}
```

### 🚀 Recursive Alternative

```java
public class Solution {
    public ListNode reverseList(ListNode A) {
        if (A == null || A.next == null) return A;

        ListNode newHead = reverseList(A.next);
        A.next.next = A;  // make the next node point back
        A.next = null;    // break forward link
        return newHead;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — single pass
- **Space:** Iterative `O(1)`, Recursive `O(N)` call stack

---

### Q10: Middle Element of Linked List

> Return middle value. For even length N, return the `(N/2 + 1)`-th element.

### 💡 Approach & Intuition

**Fast/Slow pointer (Tortoise & Hare):** `slow` moves 1 step, `fast` moves 2 steps. When `fast` reaches the end, `slow` is at the middle.

For `1→2→3→4→5`: slow ends at 3 ✅
For `1→5→6→2→3→4`: slow ends at 2 (4th node, N/2+1 = 4th) ✅

### 🚀 Java 8 Solution

```java
public class Solution {
    public int solve(ListNode A) {
        ListNode slow = A, fast = A;

        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        return slow.val;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — single pass
- **Space:** `O(1)`

---

### Q11: Palindrome Linked List

> Check if the linked list is a palindrome.

### 💡 Approach & Intuition

1. **Find middle** using fast/slow pointers
2. **Reverse** the second half
3. **Compare** first half with reversed second half
4. (Optional) Restore the list

### 🚀 Java 8 Solution

```java
public class Solution {
    public int lPalin(ListNode A) {
        if (A == null || A.next == null) return 1;

        // Step 1: Find middle (slow will be at start of second half)
        ListNode slow = A, fast = A;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }

        // Step 2: Reverse second half starting from slow.next
        ListNode secondHalf = reverse(slow.next);

        // Step 3: Compare first half with reversed second half
        ListNode first = A, second = secondHalf;
        while (second != null) {
            if (first.val != second.val) return 0;
            first = first.next;
            second = second.next;
        }

        return 1;
    }

    private ListNode reverse(ListNode head) {
        ListNode prev = null, curr = head;
        while (curr != null) {
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)`
- **Space:** `O(1)` — in-place reversal

---

### Q12: Deep Copy of Linked List with Random Pointer

> Create a deep copy of a linked list where each node has a `next` and `random` pointer.

### 💡 Approach & Intuition

**Three-pass approach (O(1) extra space, no HashMap):**

1. **Interleave:** Insert cloned nodes between originals: `1→1'→2→2'→3→3'`
2. **Set random pointers:** For each original node, `clone.random = original.random.next` (the clone of the random target)
3. **Separate:** Split interleaved list into original and clone

### 🚀 Java 8 Solution

```java
public class Solution {
    public RandomListNode copyRandomList(RandomListNode head) {
        if (head == null) return null;

        // Pass 1: Interleave cloned nodes
        RandomListNode curr = head;
        while (curr != null) {
            RandomListNode clone = new RandomListNode(curr.label);
            clone.next = curr.next;
            curr.next = clone;
            curr = clone.next;
        }

        // Pass 2: Set random pointers for clones
        curr = head;
        while (curr != null) {
            if (curr.random != null) {
                curr.next.random = curr.random.next; // clone's random = original's random's clone
            }
            curr = curr.next.next;
        }

        // Pass 3: Separate the two lists
        RandomListNode cloneHead = head.next;
        curr = head;
        while (curr != null) {
            RandomListNode clone = curr.next;
            curr.next = clone.next;
            if (clone.next != null) {
                clone.next = clone.next.next;
            }
            curr = curr.next;
        }

        return cloneHead;
    }
}
```

### 🚀 Alternative: HashMap Approach (Simpler)

```java
public class Solution {
    public RandomListNode copyRandomList(RandomListNode head) {
        if (head == null) return null;
        HashMap<RandomListNode, RandomListNode> map = new HashMap<>();

        // Pass 1: Create all clone nodes
        RandomListNode curr = head;
        while (curr != null) {
            map.put(curr, new RandomListNode(curr.label));
            curr = curr.next;
        }

        // Pass 2: Set next and random pointers
        curr = head;
        while (curr != null) {
            map.get(curr).next = map.get(curr.next);
            map.get(curr).random = map.get(curr.random);
            curr = curr.next;
        }

        return map.get(head);
    }
}
```

### ⏱️ Complexity Analysis

- Interleave: **Time** `O(N)`, **Space** `O(1)` (excluding output)
- HashMap: **Time** `O(N)`, **Space** `O(N)`

---

### Q13: Reverse Linked List in Groups of K

> Reverse nodes in groups of B. B always divides the list length.

### 💡 Approach & Intuition

Reverse the first B nodes using the standard reversal. Then **recursively** reverse the remaining list and connect.

### 🚀 Java 8 Solution

```java
public class Solution {
    public ListNode reverseList(ListNode A, int B) {
        ListNode prev = null, curr = A, next = null;
        int count = 0;

        // Reverse first B nodes
        while (curr != null && count < B) {
            next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
            count++;
        }

        // next is now the (B+1)-th node
        // A is now the last node of the reversed group
        // Recursively reverse the rest and connect
        if (next != null) {
            A.next = reverseList(next, B);
        }

        return prev; // new head of this group
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — each node visited once
- **Space:** `O(N/B)` — recursion stack depth

---

### Q14: Reverse Linked List Between Positions B and C

> Reverse nodes from position B to C (1-based). One pass, in-place.

### 💡 Approach & Intuition

1. Walk to position `B-1` (the node before the reversal starts) — call it `preStart`
2. Standard reversal for `C - B + 1` nodes
3. Reconnect: `preStart.next = new head of reversed section`, `old start.next = node after reversed section`

Use a **dummy node** in case B = 1 (reversal starts at head).

### 🚀 Java 8 Solution

```java
public class Solution {
    public ListNode reverseBetween(ListNode A, int B, int C) {
        ListNode dummy = new ListNode(0);
        dummy.next = A;

        // Step 1: Walk to node before position B
        ListNode preStart = dummy;
        for (int i = 1; i < B; i++) {
            preStart = preStart.next;
        }

        // Step 2: Reverse C - B + 1 nodes starting from preStart.next
        ListNode start = preStart.next; // first node in reversal range
        ListNode prev = null, curr = start;
        for (int i = 0; i <= C - B; i++) {
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }

        // Step 3: Reconnect
        preStart.next = prev;   // prev is new head of reversed section
        start.next = curr;      // curr is the node after position C

        return dummy.next;
    }
}
```

### ⏱️ Complexity Analysis

- **Time:** `O(N)` — single pass
- **Space:** `O(1)`

---

## 🎯 Exam Strategy & Pattern Recognition

| If you see...                 | Think...                                    | Key Technique                   |
| ----------------------------- | ------------------------------------------- | ------------------------------- |
| "Reverse a linked list"       | Three-pointer: prev/curr/next               | Memorized template              |
| "Reverse between positions"   | Walk to start, reverse section, reconnect   | Dummy node + reversal           |
| "Reverse in groups of K"      | Reverse K, recurse on rest                  | Recursive reversal              |
| "Find middle / N-th from end" | Fast/slow pointers                          | Slow=1 step, Fast=2 steps       |
| "Check palindrome"            | Find middle → reverse second half → compare | Fast/slow + reversal            |
| "Add two number lists"        | Walk both with carry                        | Like binary string addition     |
| "Insert at position"          | Walk to pos-1, splice in                    | Handle head case separately     |
| "Delete at position"          | Walk to pos-1, skip next                    | `prev.next = prev.next.next`    |
| "Deep copy with random"       | Interleave or HashMap                       | 3-pass interleave is O(1) space |
| "Swap pairs"                  | Dummy node + pointer dance                  | `prev→second→first→rest`        |

---

### 🔑 The Three Must-Know Templates

#### 1. Reversal (Iterative)

```
prev=null, curr=head
while curr:  save next → reverse → advance
return prev
```

#### 2. Fast/Slow Pointers

```
slow=head, fast=head
while fast && fast.next:  slow+1, fast+2
slow is at middle
```

#### 3. Dummy Head Pattern

```
dummy = new ListNode(0)
dummy.next = head
... (operations that might change head) ...
return dummy.next
```

---

> 💪 **Pro tip for the exam:** Always draw a small diagram (3-4 nodes) before coding pointer operations. Most bugs in linked list problems come from incorrect pointer ordering. The reversal template appears in 4+ problems — **memorize it cold**. Use the dummy head pattern whenever the head itself might change (deletions, reversals starting at position 1, swaps).
