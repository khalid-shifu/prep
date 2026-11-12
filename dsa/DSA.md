# Data Structures and Algorithms Notes

Java-focused notes for interviews. Every algorithm lists its time and space complexity. Outputs in comments are worked out by hand, not copied from a run.

## Index

1. [Complexity](#1-complexity)
   - 1.1 [Big-O and common complexities](#11-big-o-and-common-complexities)
   - 1.2 [Reading complexity from code](#12-reading-complexity-from-code)
   - 1.3 [Amortized O(1)](#13-amortized-o1)
2. [Arrays and Strings](#2-arrays-and-strings)
   - 2.1 [Array basics](#21-array-basics)
   - 2.2 [In-place operations](#22-in-place-operations)
   - 2.3 [Two pointers](#23-two-pointers)
   - 2.4 [Sliding window](#24-sliding-window)
   - 2.5 [Prefix sum](#25-prefix-sum)
   - 2.6 [Strings in Java](#26-strings-in-java)
3. [Hashing](#3-hashing)
   - 3.1 [HashMap and HashSet for lookup](#31-hashmap-and-hashset-for-lookup)
   - 3.2 [Counting and grouping](#32-counting-and-grouping)
   - 3.3 [Collisions in one line](#33-collisions-in-one-line)
4. [Linked Lists](#4-linked-lists)
   - 4.1 [Singly and doubly linked lists](#41-singly-and-doubly-linked-lists)
   - 4.2 [Reversal](#42-reversal)
   - 4.3 [Fast and slow pointers](#43-fast-and-slow-pointers)
   - 4.4 [Cycle detection](#44-cycle-detection)
5. [Stack, Queue and Deque](#5-stack-queue-and-deque)
   - 5.1 [Which Java class to use](#51-which-java-class-to-use)
   - 5.2 [Parentheses matching](#52-parentheses-matching)
   - 5.3 [Monotonic stack](#53-monotonic-stack)
   - 5.4 [Queue for BFS](#54-queue-for-bfs)
6. [Trees and Heaps](#6-trees-and-heaps)
   - 6.1 [Binary tree and BST](#61-binary-tree-and-bst)
   - 6.2 [Depth-first traversals recursive](#62-depth-first-traversals-recursive)
   - 6.3 [Depth-first traversals iterative](#63-depth-first-traversals-iterative)
   - 6.4 [Breadth-first traversal level order](#64-breadth-first-traversal-level-order)
   - 6.5 [Height, depth and balanced tree](#65-height-depth-and-balanced-tree)
   - 6.6 [BST insert, search and validate](#66-bst-insert-search-and-validate)
   - 6.7 [Lowest common ancestor](#67-lowest-common-ancestor)
   - 6.8 [Heap and PriorityQueue](#68-heap-and-priorityqueue)
7. [Graphs](#7-graphs)
   - 7.1 [Graph basics and representations](#71-graph-basics-and-representations)
   - 7.2 [BFS](#72-bfs)
   - 7.3 [DFS](#73-dfs)
   - 7.4 [Connected components](#74-connected-components)
   - 7.5 [Cycle detection](#75-cycle-detection)
   - 7.6 [Topological sort](#76-topological-sort)
   - 7.7 [Shortest path in unweighted graph](#77-shortest-path-in-unweighted-graph)
   - 7.8 [Dijkstra](#78-dijkstra)
   - 7.9 [Grid problems](#79-grid-problems)
8. [Searching and Sorting](#8-searching-and-sorting)
   - 8.1 [Linear and binary search](#81-linear-and-binary-search)
   - 8.2 [Merge sort](#82-merge-sort)
   - 8.3 [Quick sort](#83-quick-sort)
   - 8.4 [Sorting in Java](#84-sorting-in-java)
9. [Recursion, Backtracking and Dynamic Programming](#9-recursion-backtracking-and-dynamic-programming)
   - 9.1 [Recursion basics](#91-recursion-basics)
   - 9.2 [Backtracking](#92-backtracking)
   - 9.3 [Dynamic programming idea](#93-dynamic-programming-idea)
   - 9.4 [Classic DP problems](#94-classic-dp-problems)
10. [Java Collections Mapped to Data Structures](#10-java-collections-mapped-to-data-structures)
    - 10.1 [Which class implements what](#101-which-class-implements-what)
    - 10.2 [Complexity table](#102-complexity-table)
    - 10.3 [Comparator for PriorityQueue](#103-comparator-for-priorityqueue)
11. [Problem-Solving Patterns Cheat Sheet](#11-problem-solving-patterns-cheat-sheet)
    - 11.1 [Clue to pattern table](#111-clue-to-pattern-table)
    - 11.2 [One bit trick note](#112-one-bit-trick-note)

---

## 1. Complexity

### 1.1 Big-O and common complexities

Big-O describes how the work (time) or memory (space) grows as the input size n grows. It ignores constants and small terms, so 3n + 10 is just O(n).

- Time complexity: number of basic steps as n grows.
- Space complexity: extra memory used beyond the input (a recursion stack counts).
- Usually we quote the worst case.

| Big-O | Name | What it feels like (n = 1,000,000) | Typical example |
|---|---|---|---|
| O(1) | constant | instant | array index, HashMap get |
| O(log n) | logarithmic | about 20 steps | binary search, heap push/pop |
| O(n) | linear | 1 million steps, fine | one loop, linear search |
| O(n log n) | linearithmic | about 20 million, fine | merge sort, Arrays.sort |
| O(n^2) | quadratic | 10^12 steps, too slow | nested loops, bubble sort |
| O(2^n) | exponential | only works for n up to about 20 | all subsets, naive Fibonacci |
| O(n!) | factorial | only works for n up to about 10 | all permutations |

Rule of thumb: about 10^8 simple operations fit in one second. If n is 10^5, aim for O(n log n) or better. If n is up to 20, exponential is fine.

```text
n:         10     100     1,000    1,000,000
log n:      3       7        10        20
n log n:   33     664    ~10,000   ~20,000,000
n^2:      100  10,000  1,000,000    10^12
```

### 1.2 Reading complexity from code

Count how many times the innermost statement runs as a function of n.

- One loop to n: O(n).
- Loop inside a loop, both to n: O(n^2).
- Two loops one after another: O(n + m), keep the bigger term.
- Loop that halves or doubles the variable: O(log n).
- Recursion: (number of calls) x (work per call). Two calls per level with depth n gives O(2^n).
- Drop constants and lower terms: O(2n + 5) is O(n).

```java
// O(n): single loop
int sum = 0;
for (int i = 0; i < n; i++) {
    sum += i;
}

// O(n^2): nested loops
int pairs = 0;
for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
        pairs++;
    }
}

// O(log n): variable is halved each time
int steps = 0;
for (int i = n; i > 1; i = i / 2) {
    steps++;
}
// n = 16 -> steps = 4 (16, 8, 4, 2)

// O(n) time, O(n) space: recursion depth n uses n stack frames
static int sumTo(int n) {
    if (n == 0) {
        return 0;
    }
    return n + sumTo(n - 1);
}
// sumTo(4) = 10

// O(2^n): each call makes two more calls
static int fib(int n) {
    if (n <= 1) {
        return n;
    }
    return fib(n - 1) + fib(n - 2);
}
// fib(6) = 8
```

Nested loop where the inner loop starts at i + 1 is still O(n^2) (n(n-1)/2 steps).

### 1.3 Amortized O(1)

Amortized cost is the average cost per operation over a long sequence, even if a single operation is sometimes expensive.

- `ArrayList.add` normally writes into the next free slot: O(1).
- When the internal array is full, it allocates a bigger array (about 1.5x) and copies all elements: O(n) for that one call.
- Resizes are rare and the sizes grow geometrically, so the total copy work for n adds is O(n). Per add that is O(1) amortized.
- `HashMap.put` is also amortized O(1) for the same reason (resize and rehash).

```text
capacity:  1 -> 2 -> 3 -> 4 -> 6 -> 9 -> 13 ...   (roughly x1.5 each resize)
add #:     cheap cheap COPY cheap COPY ...        copies get rarer as the list grows
```

```java
List<Integer> list = new ArrayList<>();
for (int i = 0; i < 1_000_000; i++) {
    list.add(i);
}
// total work is O(n), so each add is O(1) amortized
// tip: new ArrayList<>(expectedSize) avoids the resizes
```

---

## 2. Arrays and Strings

### 2.1 Array basics

An array is a fixed-size block of memory, so the element at an index is found directly.

- Access by index: O(1).
- Search an unsorted array: O(n).
- Insert or delete in the middle: O(n) because the elements after it shift.
- Size is fixed. `ArrayList` is a resizable array.
- Always check bounds: valid indexes are 0 to length - 1.

```text
index:   0    1    2    3    4
value: [ 5 ][ 8 ][ 2 ][ 9 ][ 1 ]

insert 7 at index 2 -> shift 2, 9, 1 one step right:
        [ 5 ][ 8 ][ 7 ][ 2 ][ 9 ][ 1 ]
```

```java
int[] a = {5, 8, 2, 9, 1};
int first = a[0];
int last = a[a.length - 1];
System.out.println(first + " " + last);
// 5 1
System.out.println(Arrays.toString(a));
// [5, 8, 2, 9, 1]
int[] copy = Arrays.copyOf(a, a.length);
```

### 2.2 In-place operations

In-place means the array is changed using only O(1) extra memory, with no second array.

- Typical tools: swap two elements, or a read pointer and a write pointer.
- Interviewers often add "do it in place" to see if you avoid a new array.

```java
// Reverse an array in place. Time O(n), space O(1).
static void reverse(int[] a) {
    int left = 0;
    int right = a.length - 1;
    while (left < right) {
        int tmp = a[left];
        a[left] = a[right];
        a[right] = tmp;
        left++;
        right--;
    }
}
// {1, 2, 3, 4, 5} -> [5, 4, 3, 2, 1]
```

```java
// Move all zeros to the end, keep order of the others.
// Time O(n), space O(1). "write" is where the next non-zero goes.
static void moveZeroes(int[] a) {
    int write = 0;
    for (int read = 0; read < a.length; read++) {
        if (a[read] != 0) {
            a[write] = a[read];
            write++;
        }
    }
    while (write < a.length) {
        a[write] = 0;
        write++;
    }
}
// {0, 1, 0, 3, 12} -> [1, 3, 12, 0, 0]
```

### 2.3 Two pointers

Use two indexes that move through the data, usually from both ends or one behind the other. It often turns an O(n^2) pair search into O(n).

- Works best on sorted arrays or when order of both ends matters (palindrome, pair sum).
- Decide the move rule: if the sum is too small, move the left pointer up; too big, move the right pointer down.

```text
sorted: [1, 2, 4, 7, 11, 15]   target = 15
         L                R        1 + 15 = 16 > 15 -> R--
         L            R            1 + 11 = 12 < 15 -> L++
            L         R            2 + 11 = 13 < 15 -> L++
               L      R            4 + 11 = 15 found, indexes 2 and 4
```

```java
// Pair with given sum in a SORTED array. Time O(n), space O(1).
static int[] pairWithSum(int[] a, int target) {
    int left = 0;
    int right = a.length - 1;
    while (left < right) {
        int sum = a[left] + a[right];
        if (sum == target) {
            return new int[] {left, right};
        }
        if (sum < target) {
            left++;
        } else {
            right--;
        }
    }
    return new int[] {-1, -1};
}
// pairWithSum({1, 2, 4, 7, 11, 15}, 15) -> [2, 4]
```

### 2.4 Sliding window

A window is a contiguous range [left, right]. Move right to add an element, move left to remove one, and update the answer without recomputing from scratch. It turns O(n*k) into O(n).

- Fixed size window: add the new element, subtract the one leaving.
- Variable size window: grow right; while the window breaks a rule, shrink from left.
- Clue: "longest/shortest/maximum subarray or substring" with a condition.

```text
a = [2, 1, 5, 1, 3, 2], k = 3
[2 1 5] 1 3 2   sum 8
 2 [1 5 1] 3 2  sum 8 - 2 + 1 = 7
 2 1 [5 1 3] 2  sum 7 - 1 + 3 = 9   <- max
 2 1 5 [1 3 2]  sum 9 - 5 + 2 = 6
```

```java
// Maximum sum of any subarray of size k. Time O(n), space O(1).
static int maxSumWindow(int[] a, int k) {
    int windowSum = 0;
    for (int i = 0; i < k; i++) {
        windowSum += a[i];
    }
    int best = windowSum;
    for (int i = k; i < a.length; i++) {
        windowSum += a[i];
        windowSum -= a[i - k];
        best = Math.max(best, windowSum);
    }
    return best;
}
// maxSumWindow({2, 1, 5, 1, 3, 2}, 3) -> 9
```

```java
// Longest substring without repeating characters (variable window).
// Time O(n) (each char added and removed at most once), space O(min(n, alphabet)).
static int longestUnique(String s) {
    Set<Character> window = new HashSet<>();
    int left = 0;
    int best = 0;
    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        while (window.contains(c)) {
            window.remove(s.charAt(left));
            left++;
        }
        window.add(c);
        best = Math.max(best, right - left + 1);
    }
    return best;
}
// longestUnique("abcabcbb") -> 3   (substring "abc")
```

### 2.5 Prefix sum

A prefix array stores running totals, so the sum of any range is one subtraction. Build once in O(n), then each range query is O(1).

- `prefix[i]` = sum of the first i elements, so `prefix[0] = 0`.
- Sum of indexes l..r = `prefix[r + 1] - prefix[l]`.
- Clue: many range-sum queries, or "subarray sum equals k" (combine with a HashMap).

```text
a      = [3, 1, 4, 1, 5]
prefix = [0, 3, 4, 8, 9, 14]
sum of a[1..3] = prefix[4] - prefix[1] = 9 - 3 = 6   (1 + 4 + 1)
```

```java
// Build: time O(n), space O(n). Query: time O(1).
static int[] buildPrefix(int[] a) {
    int[] prefix = new int[a.length + 1];
    for (int i = 0; i < a.length; i++) {
        prefix[i + 1] = prefix[i] + a[i];
    }
    return prefix;
}

static int rangeSum(int[] prefix, int l, int r) {
    return prefix[r + 1] - prefix[l];
}
// prefix = buildPrefix({3, 1, 4, 1, 5}) -> [0, 3, 4, 8, 9, 14]
// rangeSum(prefix, 1, 3) -> 6
```

### 2.6 Strings in Java

`String` is immutable: every change creates a new object. For many edits use `StringBuilder` (mutable) or a `char[]`.

- `s.charAt(i)`, `s.length()`, `s.toCharArray()`, `s.substring(from, to)` (to is exclusive).
- Compare content with `equals`, never `==`.
- `s += x` inside a loop is O(n^2) in total; use `StringBuilder.append` (O(1) amortized).
- Letters as array indexes: `c - 'a'` gives 0 to 25 for lowercase letters.
- `String.valueOf(char[])` or `new String(char[])` turns a char array back into a String.

```java
// Palindrome check with two pointers. Time O(n), space O(1).
static boolean isPalindrome(String s) {
    int left = 0;
    int right = s.length() - 1;
    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
// isPalindrome("racecar") -> true
// isPalindrome("abca") -> false
```

```java
// Anagram check with a 26-slot counter. Time O(n), space O(1).
static boolean isAnagram(String a, String b) {
    if (a.length() != b.length()) {
        return false;
    }
    int[] counts = new int[26];
    for (int i = 0; i < a.length(); i++) {
        counts[a.charAt(i) - 'a']++;
        counts[b.charAt(i) - 'a']--;
    }
    for (int count : counts) {
        if (count != 0) {
            return false;
        }
    }
    return true;
}
// isAnagram("listen", "silent") -> true
```

```java
// Reverse using StringBuilder, and char[] in place.
String s = "hello";
String reversed = new StringBuilder(s)
    .reverse()
    .toString();
// reversed = "olleh"

char[] chars = s.toCharArray();
Arrays.sort(chars);
String sorted = new String(chars);
// sorted = "ehllo"
```

---

## 3. Hashing

### 3.1 HashMap and HashSet for lookup

A hash table finds a key in O(1) on average by turning the key into an array index with `hashCode()`. A `HashSet` only stores keys; a `HashMap` stores key-value pairs.

- Use it to trade memory (O(n)) for speed: "have I seen this before?" becomes O(1).
- Internals (hashCode, equals, buckets, resize, treeify) are in the Java notes: [JAVA.md](../java/JAVA.md).

```java
// Two Sum on an UNSORTED array. Time O(n), space O(n).
// For each number, check if its complement was already seen.
static int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> seen = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int need = target - nums[i];
        if (seen.containsKey(need)) {
            return new int[] {seen.get(need), i};
        }
        seen.put(nums[i], i);
    }
    return new int[] {-1, -1};
}
// twoSum({2, 7, 11, 15}, 9) -> [0, 1]
```

```java
// Detect a duplicate. Time O(n), space O(n).
static boolean hasDuplicate(int[] nums) {
    Set<Integer> seen = new HashSet<>();
    for (int n : nums) {
        if (!seen.add(n)) {
            return true;
        }
    }
    return false;
}
// hasDuplicate({1, 2, 3, 1}) -> true
```

`Set.add` returns false when the element was already present, so no separate `contains` call is needed.

### 3.2 Counting and grouping

Two very common uses: count how often each item appears, and group items that share a key.

- Counting: `map.merge(key, 1, Integer::sum)` or `getOrDefault`.
- Grouping: `map.computeIfAbsent(key, k -> new ArrayList<>()).add(item)`.
- Use `LinkedHashMap` when you want a predictable print order (HashMap order is not guaranteed).

```java
// Count characters. Time O(n), space O(k) for k distinct keys.
String s = "banana";
Map<Character, Integer> counts = new LinkedHashMap<>();
for (char c : s.toCharArray()) {
    counts.merge(c, 1, Integer::sum);
}
System.out.println(counts);
// {b=1, a=3, n=2}
```

```java
// Group anagrams: words with the same sorted letters share a key.
// Time O(n * k log k) for n words of length k, space O(n * k).
static List<List<String>> groupAnagrams(String[] words) {
    Map<String, List<String>> groups = new LinkedHashMap<>();
    for (String word : words) {
        char[] chars = word.toCharArray();
        Arrays.sort(chars);
        String key = new String(chars);
        groups
            .computeIfAbsent(key, k -> new ArrayList<>())
            .add(word);
    }
    return new ArrayList<>(groups.values());
}
// groupAnagrams({"eat", "tea", "tan", "ate", "nat", "bat"})
// -> [[eat, tea, ate], [tan, nat], [bat]]
```

### 3.3 Collisions in one line

A collision is two different keys landing in the same bucket; Java keeps them in a linked list (turned into a balanced tree when long), so lookups stay fast if `hashCode` spreads keys well. Full details: [JAVA.md](../java/JAVA.md).

- Keys used in a HashMap must have consistent `equals` and `hashCode`, and should be immutable.
- Worst case with many collisions: O(log n) in Java 8+ (O(n) before treeification).

---

## 4. Linked Lists

### 4.1 Singly and doubly linked lists

A linked list is a chain of nodes, each holding a value and a reference to the next node. Nodes are not next to each other in memory, so there is no index access.

- Singly linked: each node has `next`. Doubly linked: each node has `next` and `prev`.
- Access by position: O(n). Insert or delete at the head: O(1). Insert or delete after a node you already hold: O(1).
- No resizing and no shifting, but poor memory locality compared with arrays.
- In Java, `LinkedList` is a doubly linked list.

```text
Singly:   head
           |
          [1|*]-->[2|*]-->[3|*]-->null

Doubly:   null<--[*|1|*]<-->[*|2|*]<-->[*|3|*]-->null
                  prev val next
```

```java
class ListNode {
    int val;
    ListNode next;

    ListNode(int val) {
        this.val = val;
    }
}

class DNode {
    int val;
    DNode prev;
    DNode next;

    DNode(int val) {
        this.val = val;
    }
}
```

```text
Remove node X from a doubly linked list (X is between A and B):

before:  A <--> X <--> B
step 1:  A.next = X.next      (A --> B)
step 2:  B.prev = X.prev      (A <-- B)
after:   A <--> B             O(1)
```

```java
// Build 1 -> 2 -> 3 and print it. Time O(n), space O(n) for the StringBuilder (O(1) if you print each value directly).
ListNode head = new ListNode(1);
head.next = new ListNode(2);
head.next.next = new ListNode(3);

static void print(ListNode head) {
    StringBuilder sb = new StringBuilder();
    ListNode curr = head;
    while (curr != null) {
        sb.append(curr.val);
        if (curr.next != null) {
            sb.append(" -> ");
        }
        curr = curr.next;
    }
    System.out.println(sb);
}
// print(head) -> 1 -> 2 -> 3
```

### 4.2 Reversal

Reverse a list by flipping every `next` pointer to point backwards. Use three references: `prev`, `curr` and a saved `next`.

- Save `curr.next` first, otherwise the rest of the list is lost.
- Return `prev` at the end; it is the new head.

```text
start:  prev=null  curr=1
        null     1 --> 2 --> 3 --> null

loop 1: next=2, 1.next=null, prev=1, curr=2
        null <-- 1     2 --> 3 --> null

loop 2: next=3, 2.next=1, prev=2, curr=3
        null <-- 1 <-- 2     3 --> null

loop 3: next=null, 3.next=2, prev=3, curr=null
        null <-- 1 <-- 2 <-- 3

return prev (3):   3 --> 2 --> 1 --> null
```

```java
// Iterative reverse. Time O(n), space O(1).
static ListNode reverse(ListNode head) {
    ListNode prev = null;
    ListNode curr = head;
    while (curr != null) {
        ListNode next = curr.next;
        curr.next = prev;
        prev = curr;
        curr = next;
    }
    return prev;
}
// print(reverse(1 -> 2 -> 3)) -> 3 -> 2 -> 1
```

```java
// Recursive reverse. Time O(n), space O(n) for the call stack.
static ListNode reverseRec(ListNode head) {
    if (head == null || head.next == null) {
        return head;
    }
    ListNode newHead = reverseRec(head.next);
    head.next.next = head;
    head.next = null;
    return newHead;
}
```

### 4.3 Fast and slow pointers

Move two pointers at different speeds: `slow` one step, `fast` two steps. When `fast` reaches the end, `slow` is at the middle. Gives the middle in one pass without knowing the length.

```text
1 --> 2 --> 3 --> 4 --> 5 --> null
S,F
      S     F                 (slow=2, fast=3)
            S           F     (slow=3, fast=5) fast.next is null -> stop
middle = 3

1 --> 2 --> 3 --> 4 --> null  (even length: second middle)
            S           F=null  middle = 3
```

```java
// Middle node. Time O(n), space O(1).
static ListNode middle(ListNode head) {
    ListNode slow = head;
    ListNode fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }
    return slow;
}
// 1 -> 2 -> 3 -> 4 -> 5 : returns node 3
// 1 -> 2 -> 3 -> 4      : returns node 3
```

Same idea finds the k-th node from the end: move `fast` k steps ahead first, then move both until `fast` reaches the end.

### 4.4 Cycle detection

If a list loops back on itself, a plain traversal never ends. With fast and slow pointers, the fast one eventually catches the slow one inside the loop (Floyd's algorithm).

- If `fast` reaches null, there is no cycle.
- If `fast == slow` at some point, there is a cycle.
- The simple alternative is a `HashSet` of visited nodes: O(n) space. Floyd uses O(1) space.

```text
1 --> 2 --> 3 --> 4
            ^     |
            |     v
            6 <-- 5

slow: 1 2 3 4 5 6 3 ...
fast: 1 3 5 3 5 3 ...        they meet inside the loop
```

```java
// Time O(n), space O(1).
static boolean hasCycle(ListNode head) {
    ListNode slow = head;
    ListNode fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) {
            return true;
        }
    }
    return false;
}
// 1 -> 2 -> 3 -> null : false
// 1 -> 2 -> 3 -> back to 2 : true
```

To find where the cycle starts: after they meet, put one pointer back at the head and move both one step at a time; they meet at the cycle start.

---

## 5. Stack, Queue and Deque

### 5.1 Which Java class to use

A stack is last-in first-out (LIFO); a queue is first-in first-out (FIFO); a deque (double-ended queue) allows adding and removing at both ends.

- Use `ArrayDeque` for stacks, queues and deques. It is faster than `LinkedList` and does not allow null.
- Avoid the old `Stack` class: it extends `Vector` and every method is synchronized (slow, legacy).
- All the operations below are O(1).

| Need | Declare | Add | Remove | Look at |
|---|---|---|---|---|
| Stack (LIFO) | `Deque<Integer> st = new ArrayDeque<>()` | `push(x)` | `pop()` | `peek()` |
| Queue (FIFO) | `Queue<Integer> q = new ArrayDeque<>()` | `offer(x)` | `poll()` | `peek()` |
| Deque | `Deque<Integer> d = new ArrayDeque<>()` | `offerFirst` / `offerLast` | `pollFirst` / `pollLast` | `peekFirst` / `peekLast` |

```text
Stack (top on the left):           Queue (front on the left):
push 1, push 2, push 3             offer 1, offer 2, offer 3
  top -> [3][2][1]                   front -> [1][2][3] <- back
pop() returns 3                    poll() returns 1
```

```java
Deque<Integer> stack = new ArrayDeque<>();
stack.push(1);
stack.push(2);
stack.push(3);
System.out.println(stack.pop());
// 3
System.out.println(stack.peek());
// 2

Queue<Integer> queue = new ArrayDeque<>();
queue.offer(1);
queue.offer(2);
queue.offer(3);
System.out.println(queue.poll());
// 1
```

`pop()` and `remove()` throw an exception on an empty structure; `poll()` and `peek()` return null. Check `isEmpty()` first.

### 5.2 Parentheses matching

Push every opening bracket; on a closing bracket the top of the stack must be its matching opener. At the end the stack must be empty.

```text
"{[()]}"   push { push [ push (     stack: ( [ {   (top is left)
           ")" matches "(" pop       stack: [ {
           "]" matches "[" pop       stack: {
           "}" matches "{" pop       stack: empty -> valid

"([)]"     push ( push [ ; ")" but top is "[" -> mismatch -> invalid
```

```java
// Time O(n), space O(n).
static boolean isValid(String s) {
    Deque<Character> stack = new ArrayDeque<>();
    for (char c : s.toCharArray()) {
        if (c == '(' || c == '[' || c == '{') {
            stack.push(c);
        } else {
            if (stack.isEmpty()) {
                return false;
            }
            char open = stack.pop();
            if (c == ')' && open != '(') {
                return false;
            }
            if (c == ']' && open != '[') {
                return false;
            }
            if (c == '}' && open != '{') {
                return false;
            }
        }
    }
    return stack.isEmpty();
}
// isValid("{[()]}") -> true
// isValid("([)]") -> false
// isValid("((") -> false
```

### 5.3 Monotonic stack

A monotonic stack keeps its elements in sorted order (always increasing or always decreasing). Before pushing a new element, pop everything that breaks the order. Each element is pushed and popped once, so it is O(n).

- Clue: "next greater element", "next warmer day", "largest rectangle in histogram".
- Store indexes in the stack so you can write answers into the result array.

```text
nums = [2, 1, 5, 3]    stack holds indexes of values still waiting for a greater one

i=0 (2): push 0                     stack: [0]
i=1 (1): 1 < 2, push 1              stack: [0, 1]
i=2 (5): 5 > 1 pop 1 -> res[1]=5
         5 > 2 pop 0 -> res[0]=5
         push 2                     stack: [2]
i=3 (3): 3 < 5, push 3              stack: [2, 3]
left in stack get -1:  res = [5, 5, -1, -1]
```

```java
// Next greater element to the right. Time O(n), space O(n).
static int[] nextGreater(int[] nums) {
    int[] res = new int[nums.length];
    Arrays.fill(res, -1);
    Deque<Integer> stack = new ArrayDeque<>();
    for (int i = 0; i < nums.length; i++) {
        while (!stack.isEmpty() && nums[i] > nums[stack.peek()]) {
            int idx = stack.pop();
            res[idx] = nums[i];
        }
        stack.push(i);
    }
    return res;
}
// nextGreater({2, 1, 5, 3}) -> [5, 5, -1, -1]
```

### 5.4 Queue for BFS

A queue processes items in the order they arrive, which is exactly what breadth-first search needs: finish all nodes at distance 1 before distance 2. The full BFS on trees and graphs is in [6.4](#64-breadth-first-traversal-level-order) and [7.2](#72-bfs).

- Pattern: offer the start, then loop `while (!queue.isEmpty())`: poll one, process it, offer its unvisited neighbours.
- Other queue uses: task scheduling, sliding window maximum (deque), "first non-repeating" in a stream.

```java
// Skeleton of every BFS.
Queue<Integer> queue = new ArrayDeque<>();
queue.offer(start);
while (!queue.isEmpty()) {
    int node = queue.poll();
    // process node, then offer its unvisited neighbours
}
```

---

## 6. Trees and Heaps

### 6.1 Binary tree and BST

A tree is a set of nodes connected without cycles, with one root. In a binary tree each node has at most two children (left and right). A binary search tree (BST) is a binary tree with an ordering rule.

- Leaf: node with no children. Edge: link between parent and child.
- BST rule: for every node, all values in the left subtree are smaller and all values in the right subtree are larger.
- Because of that rule, an inorder walk of a BST gives sorted values.
- BST search, insert, delete: O(h), where h is the height. Balanced: O(log n). Skewed (like a list): O(n).
- A plain binary tree has no ordering, so search is O(n).

```text
Binary tree (any values)        BST (ordered)

        1                              8
       / \                            / \
      2   3                          3   10
     / \                            / \    \
    4   5                          1   6    14
```

```java
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int val) {
        this.val = val;
    }
}

// The sample binary tree used in 6.2 to 6.7
TreeNode root = new TreeNode(1);
root.left = new TreeNode(2);
root.right = new TreeNode(3);
root.left.left = new TreeNode(4);
root.left.right = new TreeNode(5);
```

### 6.2 Depth-first traversals recursive

Depth-first traversal (DFS) goes all the way down one branch before moving to the next. The three variants differ only in when the node itself is visited relative to its subtrees.

- Inorder: left, node, right. For a BST this gives sorted order.
- Preorder: node, left, right. Used to copy or serialize a tree.
- Postorder: left, right, node. Used when children must be handled first (delete tree, compute height).
- All three: time O(n), space O(h) for the recursion stack (O(n) worst case for a skewed tree).

```text
        1
       / \
      2   3
     / \
    4   5

Inorder   (L, N, R): 4 2 5 1 3
Preorder  (N, L, R): 1 2 4 5 3
Postorder (L, R, N): 4 5 2 3 1
```

```text
Inorder walk, step by step (call stack shown):
inorder(1) -> inorder(2) -> inorder(4) -> no children -> visit 4
           back in 2 -> visit 2 -> inorder(5) -> visit 5
back in 1 -> visit 1 -> inorder(3) -> visit 3
Order: 4 2 5 1 3
```

```java
static void inorder(TreeNode node, List<Integer> out) {
    if (node == null) {
        return;
    }
    inorder(node.left, out);
    out.add(node.val);
    inorder(node.right, out);
}

static void preorder(TreeNode node, List<Integer> out) {
    if (node == null) {
        return;
    }
    out.add(node.val);
    preorder(node.left, out);
    preorder(node.right, out);
}

static void postorder(TreeNode node, List<Integer> out) {
    if (node == null) {
        return;
    }
    postorder(node.left, out);
    postorder(node.right, out);
    out.add(node.val);
}
// inorder -> [4, 2, 5, 1, 3]
// preorder -> [1, 2, 4, 5, 3]
// postorder -> [4, 5, 2, 3, 1]
```

### 6.3 Depth-first traversals iterative

The same traversals using your own `Deque` as a stack instead of the call stack. Interviewers ask for this to avoid stack overflow on deep trees.

- Inorder: go left as far as possible pushing nodes; pop and visit; then go to the right child.
- Preorder: pop, visit, push right then left (so left is popped first).
- Postorder: do preorder with right before left (node, right, left) and reverse the result.
- All: time O(n), space O(h).

```text
Iterative inorder on the sample tree (stack top on the left):
cur=1  push 1                 stack: [1]          cur -> 2
cur=2  push 2                 stack: [2,1]        cur -> 4
cur=4  push 4                 stack: [4,2,1]      cur -> null
pop 4  visit 4                stack: [2,1]        cur -> 4.right = null
pop 2  visit 2                stack: [1]          cur -> 5
cur=5  push 5                 stack: [5,1]        cur -> null
pop 5  visit 5                stack: [1]          cur -> null
pop 1  visit 1                stack: []           cur -> 3
cur=3  push 3, then pop 3 visit 3
Order: 4 2 5 1 3
```

```java
// Iterative inorder. Time O(n), space O(h).
static List<Integer> inorderIter(TreeNode root) {
    List<Integer> out = new ArrayList<>();
    Deque<TreeNode> stack = new ArrayDeque<>();
    TreeNode cur = root;
    while (cur != null || !stack.isEmpty()) {
        while (cur != null) {
            stack.push(cur);
            cur = cur.left;
        }
        cur = stack.pop();
        out.add(cur.val);
        cur = cur.right;
    }
    return out;
}
// -> [4, 2, 5, 1, 3]
```

```java
// Iterative preorder. Time O(n), space O(h).
static List<Integer> preorderIter(TreeNode root) {
    List<Integer> out = new ArrayList<>();
    if (root == null) {
        return out;
    }
    Deque<TreeNode> stack = new ArrayDeque<>();
    stack.push(root);
    while (!stack.isEmpty()) {
        TreeNode node = stack.pop();
        out.add(node.val);
        if (node.right != null) {
            stack.push(node.right);
        }
        if (node.left != null) {
            stack.push(node.left);
        }
    }
    return out;
}
// -> [1, 2, 4, 5, 3]
```

```java
// Iterative postorder: (node, right, left) then reverse. Time O(n), space O(h) + O(n) result.
static List<Integer> postorderIter(TreeNode root) {
    List<Integer> out = new ArrayList<>();
    if (root == null) {
        return out;
    }
    Deque<TreeNode> stack = new ArrayDeque<>();
    stack.push(root);
    while (!stack.isEmpty()) {
        TreeNode node = stack.pop();
        out.add(node.val);
        if (node.left != null) {
            stack.push(node.left);
        }
        if (node.right != null) {
            stack.push(node.right);
        }
    }
    Collections.reverse(out);
    return out;
}
// before reverse: [1, 3, 2, 5, 4]
// after reverse:  [4, 5, 2, 3, 1]
```

### 6.4 Breadth-first traversal level order

Breadth-first traversal (BFS) visits the tree level by level, top to bottom, using a queue. Process exactly the nodes that are in the queue at the start of each round to separate the levels.

- Time O(n), space O(w) where w is the widest level (up to n/2).
- Clue: "level by level", "minimum depth", "right side view", "zigzag".

```text
        1          level 0
       / \
      2   3        level 1
     / \
    4   5          level 2

Queue (front on the left):
start        [1]
poll 1, add 2 3        [2, 3]      level 0 done -> [1]
poll 2, add 4 5        [3, 4, 5]
poll 3                 [4, 5]      level 1 done -> [2, 3]
poll 4                 [5]
poll 5                 []          level 2 done -> [4, 5]

Level order: 1 2 3 4 5     grouped: [[1], [2, 3], [4, 5]]
```

```java
static List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) {
        return result;
    }
    Queue<TreeNode> queue = new ArrayDeque<>();
    queue.offer(root);
    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> level = new ArrayList<>();
        for (int i = 0; i < size; i++) {
            TreeNode node = queue.poll();
            level.add(node.val);
            if (node.left != null) {
                queue.offer(node.left);
            }
            if (node.right != null) {
                queue.offer(node.right);
            }
        }
        result.add(level);
    }
    return result;
}
// -> [[1], [2, 3], [4, 5]]
```

### 6.5 Height, depth and balanced tree

Height of a node is the longest path from it down to a leaf; depth of a node is the distance from the root to it. A balanced tree keeps the left and right heights close, so operations stay O(log n).

- Here height is counted in nodes: null is 0, a leaf is 1. (Some books count edges; say which one you use.)
- Balanced (height-balanced): at every node the left and right heights differ by at most 1.
- A BST built from sorted input becomes a chain with height n, so all operations degrade to O(n). Self-balancing trees (Red-Black tree in `TreeMap`, AVL) prevent that.

```text
Balanced              Skewed (inserted 1,2,3,4 in order)
    2                 1
   / \                 \
  1   3                 2
                         \
                          3
                           \
                            4
height 2, O(log n)         height 4, O(n)
```

```java
// Height. Time O(n), space O(h).
static int height(TreeNode node) {
    if (node == null) {
        return 0;
    }
    int left = height(node.left);
    int right = height(node.right);
    return 1 + Math.max(left, right);
}
// sample tree (1, 2, 3, 4, 5) -> 3
```

```java
// Balanced check in one pass: return -1 as soon as a subtree is unbalanced.
// Time O(n), space O(h).
static int checkHeight(TreeNode node) {
    if (node == null) {
        return 0;
    }
    int left = checkHeight(node.left);
    if (left == -1) {
        return -1;
    }
    int right = checkHeight(node.right);
    if (right == -1) {
        return -1;
    }
    if (Math.abs(left - right) > 1) {
        return -1;
    }
    return 1 + Math.max(left, right);
}

static boolean isBalanced(TreeNode root) {
    return checkHeight(root) != -1;
}
// sample tree -> true
```

### 6.6 BST insert, search and validate

Walk down from the root: go left if the value is smaller, right if larger. Insert puts the new node where the search ends.

- Time O(h) for insert and search; O(log n) if balanced, O(n) if skewed. Space O(1) iterative, O(h) recursive.
- Validate: it is not enough to compare a node with its direct children. Each node must lie in a (min, max) range inherited from all its ancestors.
- Alternative validation: inorder must be strictly increasing.
- Delete is longer (leaf, one child, two children: replace with inorder successor); rarely coded in interviews.

```text
Insert 8, 3, 10, 1, 6, 14 in this order:

        8
       / \
      3   10
     / \    \
    1   6    14

Search 6: 8 -> go left (6 < 8) -> 3 -> go right (6 > 3) -> 6 found
Inorder: 1 3 6 8 10 14  (sorted)

Invalid BST (5 is in the right subtree of 8 but smaller than 8):
        8
       / \
      3   10
         /
        5      <- passes the "parent only" check (5 < 10), fails the range check
```

```java
static TreeNode insert(TreeNode node, int val) {
    if (node == null) {
        return new TreeNode(val);
    }
    if (val < node.val) {
        node.left = insert(node.left, val);
    } else if (val > node.val) {
        node.right = insert(node.right, val);
    }
    return node;
}

static boolean search(TreeNode node, int target) {
    TreeNode cur = node;
    while (cur != null) {
        if (target == cur.val) {
            return true;
        }
        if (target < cur.val) {
            cur = cur.left;
        } else {
            cur = cur.right;
        }
    }
    return false;
}

// Validate with ranges. Use long so Integer.MIN_VALUE / MAX_VALUE nodes work.
static boolean isValidBst(TreeNode node, long min, long max) {
    if (node == null) {
        return true;
    }
    if (node.val <= min || node.val >= max) {
        return false;
    }
    boolean leftOk = isValidBst(node.left, min, node.val);
    boolean rightOk = isValidBst(node.right, node.val, max);
    return leftOk && rightOk;
}
```

```java
TreeNode bst = null;
int[] values = {8, 3, 10, 1, 6, 14};
for (int v : values) {
    bst = insert(bst, v);
}
List<Integer> sorted = new ArrayList<>();
inorder(bst, sorted);
System.out.println(sorted);
// [1, 3, 6, 8, 10, 14]
System.out.println(search(bst, 6));
// true
System.out.println(search(bst, 7));
// false
System.out.println(isValidBst(bst, Long.MIN_VALUE, Long.MAX_VALUE));
// true
```

### 6.7 Lowest common ancestor

The lowest common ancestor (LCA) of nodes p and q is the deepest node that has both p and q in its subtree (a node counts as its own descendant).

- Binary tree: recurse into both sides. If p is found on one side and q on the other, the current node is the LCA. Time O(n), space O(h).
- BST: use the ordering. If both values are smaller than the node go left, if both larger go right, otherwise the current node is the LCA. Time O(h), space O(1).

```text
        1
       / \
      2   3
     / \
    4   5

LCA(4, 5) = 2     (4 is left of 2, 5 is right of 2)
LCA(4, 3) = 1     (4 is under 2, 3 is under 1's right, so split at 1)
LCA(2, 4) = 2     (a node can be its own ancestor)
```

```java
// Binary tree LCA. Returns null if neither is found.
static TreeNode lca(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || root == p || root == q) {
        return root;
    }
    TreeNode left = lca(root.left, p, q);
    TreeNode right = lca(root.right, p, q);
    if (left != null && right != null) {
        return root;
    }
    if (left != null) {
        return left;
    }
    return right;
}
// lca(root, node4, node5) -> node 2
// lca(root, node4, node3) -> node 1

// BST LCA, iterative. Time O(h), space O(1).
static TreeNode lcaBst(TreeNode root, int p, int q) {
    TreeNode cur = root;
    while (cur != null) {
        if (p < cur.val && q < cur.val) {
            cur = cur.left;
        } else if (p > cur.val && q > cur.val) {
            cur = cur.right;
        } else {
            return cur;
        }
    }
    return null;
}
// BST 8,3,10,1,6,14: lcaBst(root, 1, 6) -> node 3
// BST 8,3,10,1,6,14: lcaBst(root, 6, 14) -> node 8
```

### 6.8 Heap and PriorityQueue

A heap is a complete binary tree stored in an array where every parent is smaller than (min-heap) or larger than (max-heap) its children. The top element is always the smallest (or largest). Java's `PriorityQueue` is a min-heap by default.

- Complete tree: all levels full except possibly the last, which fills from the left. That is why an array works without pointers.
- For index i: parent = `(i - 1) / 2`, left child = `2i + 1`, right child = `2i + 2`.
- `offer` (push) O(log n): add at the end, move it up while it is smaller than its parent.
- `poll` (pop top) O(log n): move the last element to the root, move it down. `peek` O(1).
- `contains` and `remove(Object)` are O(n). Building a heap from n items with `new PriorityQueue<>(collection)` is O(n).
- The heap is only partly ordered: iterating or printing a PriorityQueue does not give sorted order; only `poll` does.

```text
Min-heap as a tree:              Same heap as an array:

          1                      index: 0  1  2  3  4  5
        /   \                    value: 1  3  2  7  4  5
       3     2
      / \   /                    node 1 (value 3): parent idx 0, children idx 3 and 4
     7   4 5                     node 2 (value 2): parent idx 0, child idx 5
```

```text
offer(0) into the heap above:
add at the end:      [1, 3, 2, 7, 4, 5, 0]    index 6, parent idx 2 (value 2)
0 < 2 swap:          [1, 3, 0, 7, 4, 5, 2]    index 2, parent idx 0 (value 1)
0 < 1 swap:          [0, 3, 1, 7, 4, 5, 2]    reached the root, done
```

Top-K pattern: to find the K largest items keep a MIN-heap of size K. When it grows past K, remove the smallest. What is left are the K largest, and the top is the K-th largest. Time O(n log k), space O(k).

```text
nums = [3, 2, 1, 5, 6, 4], k = 2   (min-heap contents shown sorted)
add 3 -> {3}
add 2 -> {2, 3}
add 1 -> {1, 2, 3} size 3 > 2, poll 1 -> {2, 3}
add 5 -> {2, 3, 5} poll 2 -> {3, 5}
add 6 -> {3, 5, 6} poll 3 -> {5, 6}
add 4 -> {4, 5, 6} poll 4 -> {5, 6}
top = 5 -> the 2nd largest
```

```java
// Kth largest element. Time O(n log k), space O(k).
static int kthLargest(int[] nums, int k) {
    PriorityQueue<Integer> minHeap = new PriorityQueue<>();
    for (int n : nums) {
        minHeap.offer(n);
        if (minHeap.size() > k) {
            minHeap.poll();
        }
    }
    return minHeap.peek();
}
// kthLargest({3, 2, 1, 5, 6, 4}, 2) -> 5
```

```java
// Min-heap and max-heap.
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
int[] data = {4, 1, 7, 3};
for (int d : data) {
    minHeap.offer(d);
    maxHeap.offer(d);
}
System.out.println(minHeap.poll());
// 1
System.out.println(maxHeap.poll());
// 7
```

---

## 7. Graphs

### 7.1 Graph basics and representations

A graph is a set of vertices (nodes) connected by edges. Trees are graphs with no cycles and one path between any two nodes.

- Undirected: an edge works both ways. Directed: an edge has a direction (A to B only).
- Weighted: edges carry a cost. Unweighted: all edges count the same.
- Degree: number of edges at a vertex. Path: sequence of connected vertices. Cycle: path that returns to its start.
- Always keep a `visited` set in traversals, otherwise cycles cause infinite loops.
- V = number of vertices, E = number of edges.

| | Adjacency list | Adjacency matrix |
|---|---|---|
| Structure | list of neighbours per vertex | V x V table of 0/1 (or weights) |
| Space | O(V + E) | O(V^2) |
| Is there an edge u-v? | O(degree of u) | O(1) |
| List all neighbours of u | O(degree of u) | O(V) |
| Best for | sparse graphs (most interview graphs) | dense graphs, quick edge lookup |

```text
Sample undirected graph used in 7.2 to 7.7:

 0 --- 1
 |     |
 2 --- 3 --- 4

Adjacency list:           Adjacency matrix:
0: [1, 2]                      0 1 2 3 4
1: [0, 3]                  0 [ 0 1 1 0 0 ]
2: [0, 3]                  1 [ 1 0 0 1 0 ]
3: [1, 2, 4]               2 [ 1 0 0 1 0 ]
4: [3]                     3 [ 0 1 1 0 1 ]
                           4 [ 0 0 0 1 0 ]
```

```java
int n = 5;
List<List<Integer>> adj = new ArrayList<>();
for (int i = 0; i < n; i++) {
    adj.add(new ArrayList<>());
}
int[][] edges = {{0, 1}, {0, 2}, {1, 3}, {2, 3}, {3, 4}};
for (int[] e : edges) {
    adj.get(e[0]).add(e[1]);
    adj.get(e[1]).add(e[0]);
}
// for a directed graph, add only adj.get(e[0]).add(e[1])
// adj = [[1, 2], [0, 3], [0, 3], [1, 2, 4], [3]]
```

### 7.2 BFS

Breadth-first search explores a graph in rings: the start, then all its neighbours, then their neighbours, using a queue. Mark nodes visited when you add them to the queue so none is queued twice.

- Time O(V + E), space O(V).
- Finds the shortest path in terms of number of edges (unweighted graph).

```text
BFS from 0 on the sample graph (queue front on the left):

step  poll  visit order   queue after adding unvisited neighbours
init   -        -         [0]                 visited {0}
 1     0       0          [1, 2]              visited {0,1,2}
 2     1       0 1        [2, 3]              visited {0,1,2,3}
 3     2       0 1 2      [3]                 (0 and 3 already visited)
 4     3       0 1 2 3    [4]                 visited {0,1,2,3,4}
 5     4       0 1 2 3 4  []

BFS order: 0 1 2 3 4
```

```java
static List<Integer> bfs(List<List<Integer>> adj, int start) {
    List<Integer> order = new ArrayList<>();
    boolean[] visited = new boolean[adj.size()];
    Queue<Integer> queue = new ArrayDeque<>();
    queue.offer(start);
    visited[start] = true;
    while (!queue.isEmpty()) {
        int node = queue.poll();
        order.add(node);
        for (int next : adj.get(node)) {
            if (!visited[next]) {
                visited[next] = true;
                queue.offer(next);
            }
        }
    }
    return order;
}
// bfs(adj, 0) -> [0, 1, 2, 3, 4]
```

### 7.3 DFS

Depth-first search follows one path as deep as possible, then backs up and tries the next neighbour. Use recursion or an explicit stack.

- Time O(V + E), space O(V) (recursion depth up to V).
- Good for: connected components, cycle detection, topological sort, paths, flood fill.

```text
DFS from 0 (neighbours tried in list order). Call stack shown, top on the right:

[0]               visit 0
[0, 1]            visit 1 (neighbour 0 already visited)
[0, 1, 3]         visit 3
[0, 1, 3, 2]      visit 2 (neighbours 0 and 3 already visited), nothing left, back up
[0, 1, 3]         next neighbour of 3 is 4
[0, 1, 3, 4]      visit 4, nothing left
back to [0, 1, 3] -> [0, 1] -> [0] -> done

DFS order: 0 1 3 2 4
```

```java
// Recursive. Time O(V + E), space O(V).
static void dfs(List<List<Integer>> adj, int node, boolean[] visited, List<Integer> order) {
    visited[node] = true;
    order.add(node);
    for (int next : adj.get(node)) {
        if (!visited[next]) {
            dfs(adj, next, visited, order);
        }
    }
}
// dfs(adj, 0, new boolean[5], order) -> [0, 1, 3, 2, 4]
```

```java
// Iterative with a stack. Mark visited when popped, so skip repeats.
// Time O(V + E), space O(V).
static List<Integer> dfsIter(List<List<Integer>> adj, int start) {
    List<Integer> order = new ArrayList<>();
    boolean[] visited = new boolean[adj.size()];
    Deque<Integer> stack = new ArrayDeque<>();
    stack.push(start);
    while (!stack.isEmpty()) {
        int node = stack.pop();
        if (visited[node]) {
            continue;
        }
        visited[node] = true;
        order.add(node);
        for (int next : adj.get(node)) {
            if (!visited[next]) {
                stack.push(next);
            }
        }
    }
    return order;
}
// dfsIter(adj, 0) -> [0, 2, 3, 4, 1]
// Different order from the recursive one (the stack pops the LAST pushed neighbour first),
// but still a valid DFS.
```

### 7.4 Connected components

A connected component is a group of vertices that can reach each other. Count them by starting a DFS (or BFS) from every vertex not yet visited; each start is one new component.

```text
n = 6, edges: 0-1, 1-2, 3-4

 0 --- 1 --- 2        3 --- 4        5
   component 1          component 2     component 3

Components: 3
```

```java
// Time O(V + E), space O(V).
static int countComponents(int n, int[][] edges) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < n; i++) {
        adj.add(new ArrayList<>());
    }
    for (int[] e : edges) {
        adj.get(e[0]).add(e[1]);
        adj.get(e[1]).add(e[0]);
    }
    boolean[] visited = new boolean[n];
    int count = 0;
    for (int i = 0; i < n; i++) {
        if (!visited[i]) {
            dfs(adj, i, visited, new ArrayList<>());
            count++;
        }
    }
    return count;
}
// countComponents(6, {{0, 1}, {1, 2}, {3, 4}}) -> 3
```

### 7.5 Cycle detection

A cycle exists when a traversal reaches a vertex that is already on the current path. The check differs for directed and undirected graphs.

- Undirected (DFS): a visited neighbour that is not the node you just came from (the parent) means a cycle. Without the parent check every edge looks like a cycle.
- Directed (DFS): use three states: 0 = not visited, 1 = on the current path, 2 = fully done. Reaching a state-1 vertex means a cycle.
- Directed alternative: if Kahn's topological sort (7.6) cannot order all vertices, there is a cycle.
- Time O(V + E), space O(V).

```text
Directed cycle:  0 --> 1 --> 2
                 ^           |
                 +-----------+       0 -> 1 -> 2 -> 0 (0 is still on the path)
```

```java
// Directed graph cycle detection.
static boolean hasCycleDirected(List<List<Integer>> adj) {
    int[] state = new int[adj.size()];
    for (int i = 0; i < adj.size(); i++) {
        if (state[i] == 0 && dfsCycle(adj, i, state)) {
            return true;
        }
    }
    return false;
}

static boolean dfsCycle(List<List<Integer>> adj, int node, int[] state) {
    state[node] = 1;
    for (int next : adj.get(node)) {
        if (state[next] == 1) {
            return true;
        }
        if (state[next] == 0 && dfsCycle(adj, next, state)) {
            return true;
        }
    }
    state[node] = 2;
    return false;
}
// edges 0->1, 1->2, 2->0 -> true
// edges 0->1, 1->2        -> false
```

### 7.6 Topological sort

A topological order lists the vertices of a directed acyclic graph (DAG) so that every edge u to v has u before v. Used for task dependencies, build order, course schedules.

- Kahn's algorithm: compute the in-degree (number of incoming edges) of each vertex; start with all vertices of in-degree 0; repeatedly poll one, add it to the result, and reduce the in-degree of its neighbours, queueing those that reach 0.
- If the result has fewer than V vertices, the graph has a cycle.
- Time O(V + E), space O(V).

```text
Edges: 5->2, 5->0, 4->0, 4->1, 2->3, 3->1

    5 ---> 2 ---> 3
    |             |
    v             v
    0 <--- 4 ---> 1

in-degree: 0:2  1:2  2:1  3:1  4:0  5:0
queue starts with the in-degree 0 vertices: [4, 5]

poll 4 -> result [4]            0: 2->1, 1: 2->1             queue [5]
poll 5 -> result [4,5]          2: 1->0 queue, 0: 1->0 queue queue [2, 0]
poll 2 -> result [4,5,2]        3: 1->0 queue                queue [0, 3]
poll 0 -> result [4,5,2,0]      no outgoing edges            queue [3]
poll 3 -> result [4,5,2,0,3]    1: 1->0 queue                queue [1]
poll 1 -> result [4,5,2,0,3,1]

Topological order: 4 5 2 0 3 1
```

```java
static List<Integer> topoSort(int n, int[][] edges) {
    List<List<Integer>> adj = new ArrayList<>();
    for (int i = 0; i < n; i++) {
        adj.add(new ArrayList<>());
    }
    int[] indegree = new int[n];
    for (int[] e : edges) {
        adj.get(e[0]).add(e[1]);
        indegree[e[1]]++;
    }
    Queue<Integer> queue = new ArrayDeque<>();
    for (int i = 0; i < n; i++) {
        if (indegree[i] == 0) {
            queue.offer(i);
        }
    }
    List<Integer> result = new ArrayList<>();
    while (!queue.isEmpty()) {
        int node = queue.poll();
        result.add(node);
        for (int next : adj.get(node)) {
            indegree[next]--;
            if (indegree[next] == 0) {
                queue.offer(next);
            }
        }
    }
    return result;
}
// topoSort(6, {{5,2},{5,0},{4,0},{4,1},{2,3},{3,1}}) -> [4, 5, 2, 0, 3, 1]
// if result.size() < n there is a cycle
```

### 7.7 Shortest path in unweighted graph

When every edge costs the same, BFS reaches each vertex through the fewest edges the first time it sees it. Store the distance when you enqueue a vertex.

- Time O(V + E), space O(V).
- Use BFS, not DFS, for shortest paths in unweighted graphs.

```text
Sample graph, source 0:

 0 --- 1        dist: 0 -> 0
 |     |              1 -> 1
 2 --- 3 --- 4        2 -> 1
                      3 -> 2   (0-1-3 or 0-2-3)
                      4 -> 3   (0-1-3-4)
```

```java
static int[] shortestDistances(List<List<Integer>> adj, int source) {
    int[] dist = new int[adj.size()];
    Arrays.fill(dist, -1);
    Queue<Integer> queue = new ArrayDeque<>();
    dist[source] = 0;
    queue.offer(source);
    while (!queue.isEmpty()) {
        int node = queue.poll();
        for (int next : adj.get(node)) {
            if (dist[next] == -1) {
                dist[next] = dist[node] + 1;
                queue.offer(next);
            }
        }
    }
    return dist;
}
// shortestDistances(adj, 0) -> [0, 1, 1, 2, 3]
// -1 would mean unreachable
```

### 7.8 Dijkstra

Dijkstra finds the cheapest path from one source to every vertex in a graph with non-negative edge weights. Always process the unfinished vertex with the smallest known distance (a min-heap gives it), and try to improve its neighbours ("relaxing" an edge).

- Time O((V + E) log V) with a PriorityQueue, space O(V + E).
- Does not work with negative weights.
- A vertex can be queued several times with different distances; skip stale entries that are larger than the best known distance.

```text
Undirected weighted graph, source 0:

   0 ---4--- 1
   |       / |
   1     2   1
   |   /     |
   2 ---5--- 3

edges: 0-1 (4), 0-2 (1), 2-1 (2), 1-3 (1), 2-3 (5)

pop (0, d=0): 1 -> 4, 2 -> 1                         heap: (2,1) (1,4)
pop (2, d=1): 1 -> 1+2=3 better than 4, 3 -> 1+5=6   heap: (1,3) (1,4) (3,6)
pop (1, d=3): 3 -> 3+1=4 better than 6               heap: (3,4) (1,4) (3,6)
pop (3, d=4): neighbours cannot improve              heap: (1,4) (3,6)   (remaining are stale, skipped)

dist = [0, 3, 1, 4]
```

```java
// adj.get(u) holds int[] {v, weight}. Time O((V + E) log V), space O(V + E).
static int[] dijkstra(List<List<int[]>> adj, int source) {
    int n = adj.size();
    int[] dist = new int[n];
    Arrays.fill(dist, Integer.MAX_VALUE);
    dist[source] = 0;
    PriorityQueue<int[]> heap = new PriorityQueue<>((a, b) -> Integer.compare(a[1], b[1]));
    heap.offer(new int[] {source, 0});
    while (!heap.isEmpty()) {
        int[] top = heap.poll();
        int node = top[0];
        int d = top[1];
        if (d > dist[node]) {
            continue;
        }
        for (int[] edge : adj.get(node)) {
            int next = edge[0];
            int weight = edge[1];
            if (d + weight < dist[next]) {
                dist[next] = d + weight;
                heap.offer(new int[] {next, dist[next]});
            }
        }
    }
    return dist;
}
// graph above (undirected, so add each edge both ways) -> dist = [0, 3, 1, 4]
```

### 7.9 Grid problems

A 2D grid is a graph where each cell is a vertex connected to its 4 neighbours (up, down, left, right). Run DFS or BFS over cells, checking bounds first.

- Use a `directions` array: `{{1,0},{-1,0},{0,1},{0,-1}}`.
- Mark visited by changing the cell (if allowed) or with a separate `boolean[][]`.
- Number of islands: for every unvisited land cell, count one island and flood-fill (sink) all connected land.
- Time O(rows x cols), space O(rows x cols) worst case for the recursion stack.

```text
grid:                 islands (same letter = same island):
1 1 0 0               A A . .
1 0 0 1               A . . B
0 0 1 1               . . B B

Number of islands = 2
```

```java
static int numIslands(int[][] grid) {
    int count = 0;
    for (int r = 0; r < grid.length; r++) {
        for (int c = 0; c < grid[0].length; c++) {
            if (grid[r][c] == 1) {
                count++;
                sink(grid, r, c);
            }
        }
    }
    return count;
}

static void sink(int[][] grid, int r, int c) {
    if (r < 0 || r >= grid.length || c < 0 || c >= grid[0].length) {
        return;
    }
    if (grid[r][c] != 1) {
        return;
    }
    grid[r][c] = 0;
    sink(grid, r + 1, c);
    sink(grid, r - 1, c);
    sink(grid, r, c + 1);
    sink(grid, r, c - 1);
}
// numIslands({{1,1,0,0},{1,0,0,1},{0,0,1,1}}) -> 2
// note: it changes the input grid; copy it first if you must keep it
```

---

## 8. Searching and Sorting

### 8.1 Linear and binary search

Linear search checks every element: O(n), works on any data. Binary search repeatedly halves a SORTED range: O(log n) time, O(1) space.

- Binary search needs sorted data (or a yes/no condition that flips once, "search on answer").
- Overflow fix: write `mid = lo + (hi - lo) / 2`, not `(lo + hi) / 2`, because `lo + hi` can exceed `Integer.MAX_VALUE`.
- Pitfalls: loop condition `lo <= hi` when both ends are inclusive; always move with `mid + 1` or `mid - 1` (using `mid` can loop forever); `hi` starts at `length - 1` for an inclusive range.
- For duplicates ("first occurrence"), when you find the target remember it and keep searching the left half.

```text
a = [1, 3, 5, 7, 9, 11], target = 7
index: 0  1  2  3  4  5
lo=0 hi=5 mid=2 (5)  5 < 7 -> lo = 3
lo=3 hi=5 mid=4 (9)  9 > 7 -> hi = 3
lo=3 hi=3 mid=3 (7)  found -> return 3

target = 4:  mid=2 (5) -> hi=1;  mid=0 (1) -> lo=1;  mid=1 (3) -> lo=2;  lo > hi -> return -1
```

```java
// Time O(log n), space O(1).
static int binarySearch(int[] a, int target) {
    int lo = 0;
    int hi = a.length - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (a[mid] == target) {
            return mid;
        }
        if (a[mid] < target) {
            lo = mid + 1;
        } else {
            hi = mid - 1;
        }
    }
    return -1;
}
// binarySearch({1, 3, 5, 7, 9, 11}, 7) -> 3
// binarySearch({1, 3, 5, 7, 9, 11}, 4) -> -1
```

```java
// First occurrence of target in a sorted array with duplicates.
static int firstIndex(int[] a, int target) {
    int lo = 0;
    int hi = a.length - 1;
    int ans = -1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (a[mid] == target) {
            ans = mid;
            hi = mid - 1;
        } else if (a[mid] < target) {
            lo = mid + 1;
        } else {
            hi = mid - 1;
        }
    }
    return ans;
}
// firstIndex({1, 2, 2, 2, 3}, 2) -> 1
```

Java already has `Arrays.binarySearch(a, key)` and `Collections.binarySearch(list, key)`; if the key is missing they return `-(insertionPoint) - 1`.

### 8.2 Merge sort

Merge sort splits the array in half, sorts each half recursively, then merges the two sorted halves. Divide and conquer.

- Time O(n log n) in best, average and worst case. Space O(n) for the merge buffer.
- Stable: equal elements keep their original order (take from the left half when equal).
- Good for linked lists and for external (on disk) sorting.

```text
Split down                          Merge up
[38 27 43 3 9 82 10]
[38 27 43 3]   [9 82 10]                    [3 9 10 27 38 43 82]
[38 27] [43 3]  [9] [82 10]           [3 27 38 43]     [9 10 82]
[38][27] [43][3]  [9] [82][10]        [27 38] [3 43]   [9] [10 82]
                                      (merging two sorted halves at each level)

log n levels of splitting, n work per level -> n log n
```

```java
static void mergeSort(int[] a, int lo, int hi) {
    if (lo >= hi) {
        return;
    }
    int mid = lo + (hi - lo) / 2;
    mergeSort(a, lo, mid);
    mergeSort(a, mid + 1, hi);
    merge(a, lo, mid, hi);
}

static void merge(int[] a, int lo, int mid, int hi) {
    int[] left = Arrays.copyOfRange(a, lo, mid + 1);
    int[] right = Arrays.copyOfRange(a, mid + 1, hi + 1);
    int i = 0;
    int j = 0;
    int k = lo;
    while (i < left.length && j < right.length) {
        if (left[i] <= right[j]) {
            a[k] = left[i];
            i++;
        } else {
            a[k] = right[j];
            j++;
        }
        k++;
    }
    while (i < left.length) {
        a[k] = left[i];
        i++;
        k++;
    }
    while (j < right.length) {
        a[k] = right[j];
        j++;
        k++;
    }
}
// int[] a = {38, 27, 43, 3, 9, 82, 10}; mergeSort(a, 0, a.length - 1);
// a -> [3, 9, 10, 27, 38, 43, 82]
```

### 8.3 Quick sort

Quick sort picks a pivot, rearranges the array so smaller elements are left of the pivot and larger ones right (partitioning), then sorts both sides recursively. The pivot ends in its final position.

- Average time O(n log n). Worst case O(n^2) when the pivot is always the smallest or largest (for example sorted input with the last element as pivot). A random pivot makes this very unlikely.
- Space O(log n) for recursion on average (O(n) worst). Sorts in place.
- Not stable.
- Usually faster than merge sort in practice (cache friendly, no buffer).

```text
Lomuto partition, pivot = last element (4). i marks the end of the "<= pivot" zone.

[7, 2, 1, 6, 8, 5, 3, 4]
 7 > 4 skip
 2 <= 4 -> i=0 swap a[0],a[1]   [2, 7, 1, 6, 8, 5, 3, 4]
 1 <= 4 -> i=1 swap a[1],a[2]   [2, 1, 7, 6, 8, 5, 3, 4]
 6, 8, 5 > 4 skip
 3 <= 4 -> i=2 swap a[2],a[6]   [2, 1, 3, 6, 8, 5, 7, 4]
 finally swap pivot with a[i+1] [2, 1, 3, 4, 8, 5, 7, 6]
                                 ^ left  ^  ^ right
                               pivot 4 is in its final place (index 3)
Then sort [2, 1, 3] and [8, 5, 7, 6] the same way.
```

```java
static void quickSort(int[] a, int lo, int hi) {
    if (lo >= hi) {
        return;
    }
    int p = partition(a, lo, hi);
    quickSort(a, lo, p - 1);
    quickSort(a, p + 1, hi);
}

static int partition(int[] a, int lo, int hi) {
    int pivot = a[hi];
    int i = lo - 1;
    for (int j = lo; j < hi; j++) {
        if (a[j] <= pivot) {
            i++;
            int tmp = a[i];
            a[i] = a[j];
            a[j] = tmp;
        }
    }
    int tmp = a[i + 1];
    a[i + 1] = a[hi];
    a[hi] = tmp;
    return i + 1;
}
// int[] a = {7, 2, 1, 6, 8, 5, 3, 4}; quickSort(a, 0, a.length - 1);
// a -> [1, 2, 3, 4, 5, 6, 7, 8]
```

| Sort | Best | Average | Worst | Space | Stable |
|---|---|---|---|---|---|
| Merge sort | n log n | n log n | n log n | O(n) | yes |
| Quick sort | n log n | n log n | n^2 | O(log n) | no |
| Heap sort | n log n | n log n | n log n | O(1) | no |
| Insertion sort | n | n^2 | n^2 | O(1) | yes |

### 8.4 Sorting in Java

Use the built-in sorts; do not write your own unless asked.

- `Arrays.sort(int[])` (primitives): dual-pivot quicksort. Average O(n log n), not stable (stability does not matter for primitives).
- `Arrays.sort(Object[])`, `Collections.sort(list)`, `list.sort(cmp)`: TimSort, a merge sort and insertion sort hybrid. O(n log n) worst, O(n) on nearly sorted data, stable, O(n) extra space.
- Objects need an order: implement `Comparable<T>` (natural order, `compareTo`) or pass a `Comparator<T>` (custom order, `compare`).
- Comparator returns negative if a goes first, zero if equal, positive if b goes first.
- Do not write `a - b` in a comparator: it can overflow. Use `Integer.compare(a, b)`.
- `Comparator.comparing(...)`, `.thenComparing(...)` and `.reversed()` build comparators readably.

```java
String[] words = {"pear", "fig", "apple", "kiwi", "date"};
Arrays.sort(words, Comparator
    .comparingInt(String::length)
    .thenComparing(Comparator.naturalOrder()));
System.out.println(Arrays.toString(words));
// [fig, date, kiwi, pear, apple]
// shorter first, ties alphabetical

List<Integer> nums = new ArrayList<>(List.of(5, 2, 9, 1));
Collections.sort(nums);
// [1, 2, 5, 9]
nums.sort(Comparator.reverseOrder());
// [9, 5, 2, 1]

int[][] intervals = {{5, 8}, {1, 3}, {2, 6}};
Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
// [[1, 3], [2, 6], [5, 8]]  sorted by start

int[] prim = {4, 1, 3};
Arrays.sort(prim);
// [1, 3, 4]
// Arrays.sort(prim, Comparator.reverseOrder()) does NOT compile for int[]; use Integer[] for custom order
```

---

## 9. Recursion, Backtracking and Dynamic Programming

### 9.1 Recursion basics

Recursion means a method calls itself on a smaller version of the problem. Every recursive method needs a base case (stops the recursion) and a recursive step that moves toward the base case.

- Each call uses a stack frame, so depth n costs O(n) space and very deep recursion throws `StackOverflowError`.
- Steps to write one: define what the method returns for the smaller problem, trust it, combine the result.
- If the same inputs repeat, add memoization (9.3).

```text
factorial(4)
 = 4 * factorial(3)
 = 4 * (3 * factorial(2))
 = 4 * (3 * (2 * factorial(1)))     base case: factorial(1) = 1
 = 4 * 3 * 2 * 1 = 24
```

```java
// Time O(n), space O(n) for the stack.
static long factorial(int n) {
    if (n <= 1) {
        return 1;
    }
    return n * factorial(n - 1);
}
// factorial(4) -> 24
```

### 9.2 Backtracking

Backtracking builds a solution one choice at a time. After exploring a choice it undoes it (removes it from the current path) and tries the next one. It is a DFS over a tree of choices.

- Template: choose, explore (recurse), un-choose.
- Add a COPY of the path to the results (`new ArrayList<>(path)`), because the same list is modified later.
- Prune (stop early) when a partial path can no longer lead to a valid answer.
- Subsets: O(n * 2^n) time. Permutations: O(n * n!) time. Space O(n) for the path plus the output.

```text
Subsets of [1, 2, 3]: at each element either take it or skip it.
Decision tree (path after each step):

[]
├─ [1]
│   ├─ [1,2]
│   │   └─ [1,2,3]
│   └─ [1,3]
├─ [2]
│   └─ [2,3]
└─ [3]

Every node is a valid subset. Collected in order:
[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]
```

```java
static List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> result = new ArrayList<>();
    backtrack(nums, 0, new ArrayList<>(), result);
    return result;
}

static void backtrack(int[] nums, int start, List<Integer> path, List<List<Integer>> result) {
    result.add(new ArrayList<>(path));
    for (int i = start; i < nums.length; i++) {
        path.add(nums[i]);
        backtrack(nums, i + 1, path, result);
        path.remove(path.size() - 1);
    }
}
// subsets({1, 2, 3})
// -> [[], [1], [1, 2], [1, 2, 3], [1, 3], [2], [2, 3], [3]]
```

```text
Permutations of [1, 2, 3]: at each position pick any number not yet used.

[]
├─ [1] ─ [1,2] ─ [1,2,3]
│      └ [1,3] ─ [1,3,2]
├─ [2] ─ [2,1] ─ [2,1,3]
│      └ [2,3] ─ [2,3,1]
└─ [3] ─ [3,1] ─ [3,1,2]
       └ [3,2] ─ [3,2,1]
```

```java
static List<List<Integer>> permutations(int[] nums) {
    List<List<Integer>> result = new ArrayList<>();
    permute(nums, new boolean[nums.length], new ArrayList<>(), result);
    return result;
}

static void permute(int[] nums, boolean[] used, List<Integer> path, List<List<Integer>> result) {
    if (path.size() == nums.length) {
        result.add(new ArrayList<>(path));
        return;
    }
    for (int i = 0; i < nums.length; i++) {
        if (used[i]) {
            continue;
        }
        used[i] = true;
        path.add(nums[i]);
        permute(nums, used, path, result);
        path.remove(path.size() - 1);
        used[i] = false;
    }
}
// permutations({1, 2, 3})
// -> [[1, 2, 3], [1, 3, 2], [2, 1, 3], [2, 3, 1], [3, 1, 2], [3, 2, 1]]
```

### 9.3 Dynamic programming idea

Dynamic programming (DP) solves a problem by combining answers to smaller subproblems, and stores each answer so it is computed only once. It applies when the problem has overlapping subproblems (the same smaller problem appears many times) and optimal substructure (the best answer is built from best answers of smaller parts).

- Memoization (top-down): plain recursion plus a cache (array or map). Easy to write from the recursive idea.
- Tabulation (bottom-up): fill a table from the smallest case up with loops. No recursion depth problem, often allows reducing space.
- Recipe: (1) define what `dp[i]` means, (2) write the recurrence (how `dp[i]` uses earlier values), (3) set base cases, (4) decide the fill order, (5) read the answer.

```text
Naive fib(5) repeats work:
                fib(5)
              /        \
          fib(4)       fib(3)        fib(3) is computed twice,
         /     \       /    \        fib(2) three times ...
     fib(3)  fib(2) fib(2) fib(1)    -> O(2^n)
     /   \
 fib(2) fib(1)

With a cache each fib(k) is computed once -> O(n)
```

```java
// Memoization (top-down). Time O(n), space O(n).
static long fibMemo(int n, long[] memo) {
    if (n <= 1) {
        return n;
    }
    if (memo[n] != 0) {
        return memo[n];
    }
    memo[n] = fibMemo(n - 1, memo) + fibMemo(n - 2, memo);
    return memo[n];
}
// fibMemo(10, new long[11]) -> 55

// Tabulation (bottom-up) with O(1) space. Time O(n).
static long fibTab(int n) {
    if (n <= 1) {
        return n;
    }
    long prev = 0;
    long curr = 1;
    for (int i = 2; i <= n; i++) {
        long next = prev + curr;
        prev = curr;
        curr = next;
    }
    return curr;
}
// fibTab(10) -> 55
```

### 9.4 Classic DP problems

Four problems that cover most interview DP.

**Climbing stairs.** You climb 1 or 2 steps at a time; how many ways to reach step n? `ways[i] = ways[i-1] + ways[i-2]` (last move was 1 step or 2 steps). Time O(n), space O(1).

```text
n:     1  2  3  4  5
ways:  1  2  3  5  8      ways[5] = ways[4] + ways[3] = 5 + 3 = 8
```

```java
static int climbStairs(int n) {
    if (n <= 2) {
        return n;
    }
    int prev = 1;
    int curr = 2;
    for (int i = 3; i <= n; i++) {
        int next = prev + curr;
        prev = curr;
        curr = next;
    }
    return curr;
}
// climbStairs(5) -> 8
```

**Coin change (minimum coins).** Fewest coins to make an amount, unlimited coins of each kind. `dp[a] = min(dp[a - coin] + 1)` over all coins. Time O(amount x coins), space O(amount).

```text
coins = [1, 2, 5], amount = 11
amount: 0  1  2  3  4  5  6  7  8  9  10 11
dp:     0  1  1  2  2  1  2  2  3  3  2  3
dp[11] = min(dp[10]+1, dp[9]+1, dp[6]+1) = min(3, 4, 3) = 3   (5 + 5 + 1)
```

```java
static int coinChange(int[] coins, int amount) {
    int[] dp = new int[amount + 1];
    Arrays.fill(dp, amount + 1);
    dp[0] = 0;
    for (int a = 1; a <= amount; a++) {
        for (int coin : coins) {
            if (coin <= a) {
                dp[a] = Math.min(dp[a], dp[a - coin] + 1);
            }
        }
    }
    if (dp[amount] > amount) {
        return -1;
    }
    return dp[amount];
}
// coinChange({1, 2, 5}, 11) -> 3
// coinChange({2}, 3) -> -1
```

**House robber.** Maximum money from houses in a row, without robbing two neighbours. For each house: skip it (keep the best so far) or rob it (its value plus the best up to two houses back). Time O(n), space O(1).

```text
nums = [2, 7, 9, 3, 1]
best through house:  2   7   11  11  12
house 3 (value 9): max(7, 2 + 9) = 11
house 5 (value 1): max(11, 11 + 1) = 12     (rob houses with 2, 9, 1)
```

```java
static int rob(int[] nums) {
    int prev2 = 0;
    int prev1 = 0;
    for (int num : nums) {
        int cur = Math.max(prev1, prev2 + num);
        prev2 = prev1;
        prev1 = cur;
    }
    return prev1;
}
// rob({2, 7, 9, 3, 1}) -> 12
```

**Longest common subsequence (LCS).** Length of the longest sequence appearing in both strings in the same order (not necessarily adjacent). `dp[i][j]` is the LCS of the first i chars of a and first j chars of b. If the chars match: `dp[i-1][j-1] + 1`, else `max(dp[i-1][j], dp[i][j-1])`. Time O(m x n), space O(m x n).

```text
a = "abcde", b = "ace"
        ""  a  c  e
   ""    0  0  0  0
   a     0  1  1  1
   b     0  1  1  1
   c     0  1  2  2
   d     0  1  2  2
   e     0  1  2  3      LCS length = 3 ("ace")
```

```java
static int lcs(String a, String b) {
    int[][] dp = new int[a.length() + 1][b.length() + 1];
    for (int i = 1; i <= a.length(); i++) {
        for (int j = 1; j <= b.length(); j++) {
            if (a.charAt(i - 1) == b.charAt(j - 1)) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
    }
    return dp[a.length()][b.length()];
}
// lcs("abcde", "ace") -> 3
```

---

## 10. Java Collections Mapped to Data Structures

### 10.1 Which class implements what

Each data structure in these notes has a standard Java class. Declare variables by interface (`List`, `Map`, `Deque`), create with the class.

| Data structure | Java class | Internally |
|---|---|---|
| Dynamic array | `ArrayList` | resizable `Object[]` |
| Doubly linked list | `LinkedList` | nodes with prev and next |
| Stack, queue, deque | `ArrayDeque` | circular array |
| Hash table (map) | `HashMap` | array of buckets, list or tree per bucket |
| Hash table, insertion order | `LinkedHashMap` | HashMap plus a linked list of entries |
| Sorted map (BST) | `TreeMap` | Red-Black tree |
| Hash set | `HashSet` | backed by a HashMap |
| Sorted set | `TreeSet` | backed by a TreeMap |
| Heap | `PriorityQueue` | binary min-heap in an array |

Hash-based classes are explained in [JAVA.md](../java/JAVA.md).

### 10.2 Complexity table

`n` is the number of elements. "avg" means average case.

| Class | get / access | add | remove | contains / search | Order |
|---|---|---|---|---|---|
| `ArrayList` | O(1) by index | O(1) amortized at end, O(n) in middle | O(n) (shifts) | O(n) | insertion |
| `LinkedList` | O(n) by index | O(1) at either end | O(1) at ends or via iterator, O(n) to find | O(n) | insertion |
| `ArrayDeque` | O(1) at ends | O(1) at either end | O(1) at either end | O(n) | insertion |
| `HashMap` | O(1) avg | O(1) avg | O(1) avg | O(1) avg (`containsKey`) | none |
| `LinkedHashMap` | O(1) avg | O(1) avg | O(1) avg | O(1) avg | insertion (or access) |
| `TreeMap` | O(log n) | O(log n) | O(log n) | O(log n) | sorted by key |
| `HashSet` | n/a | O(1) avg | O(1) avg | O(1) avg | none |
| `TreeSet` | n/a | O(log n) | O(log n) | O(log n) | sorted |
| `PriorityQueue` | `peek` O(1) | `offer` O(log n) | `poll` O(log n), `remove(Object)` O(n) | O(n) | heap order (only top is guaranteed) |

When to choose:

- `ArrayList`: default list; lots of reads by index, adds at the end. Almost always better than `LinkedList` because of memory locality.
- `LinkedList`: rarely; only when you really remove from the middle through an iterator all the time. Use `ArrayDeque` for queue and stack work.
- `ArrayDeque`: stack, queue, BFS queue, sliding window deque.
- `HashMap` / `HashSet`: fast lookup, counting, grouping, duplicates. No order.
- `LinkedHashMap`: HashMap that remembers insertion order (also the base of an LRU cache with access order).
- `TreeMap` / `TreeSet`: need sorted keys or range queries (`firstKey`, `floorKey`, `ceilingKey`, `headMap`, `subSet`).
- `PriorityQueue`: always need the smallest or largest next (top-K, Dijkstra, merging sorted lists, scheduling).

### 10.3 Comparator for PriorityQueue

`PriorityQueue` orders elements with their natural order (`Comparable`) or with a `Comparator` passed to the constructor. The head is the element that sorts FIRST under the comparator.

- Min-heap: default. Max-heap: `Collections.reverseOrder()` or `(a, b) -> Integer.compare(b, a)`.
- For arrays or objects write a comparator on the field you care about.
- Use `Integer.compare`, not subtraction, to avoid overflow.
- Chain with `thenComparing` for tie-breaks.

```java
// 1. Max-heap of integers
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());

// 2. int[] pairs ordered by the second value (used in Dijkstra): {node, distance}
PriorityQueue<int[]> byDist = new PriorityQueue<>((a, b) -> Integer.compare(a[1], b[1]));
byDist.offer(new int[] {1, 5});
byDist.offer(new int[] {2, 3});
byDist.offer(new int[] {3, 8});
System.out.println(byDist.poll()[0]);
// 2   (smallest distance 3)

// 3. Objects: highest priority first, ties by name
record Task(String name, int priority) { }

PriorityQueue<Task> tasks = new PriorityQueue<>(
    Comparator
        .comparingInt(Task::priority)
        .reversed()
        .thenComparing(Task::name)
);
tasks.offer(new Task("write", 2));
tasks.offer(new Task("test", 5));
tasks.offer(new Task("deploy", 5));
tasks.offer(new Task("rest", 1));
while (!tasks.isEmpty()) {
    System.out.println(tasks.poll().name());
}
// deploy
// test
// write
// rest
```

---

## 11. Problem-Solving Patterns Cheat Sheet

### 11.1 Clue to pattern table

When you read a question, look for the clue and try the matching pattern first. Always state the brute force, then improve it.

| Clue in the question | Try this | Typical complexity |
|---|---|---|
| Sorted array, find a pair or target | Two pointers or binary search | O(n) / O(log n) |
| Pair or triple with a sum, unsorted | HashMap (complement) or sort then two pointers | O(n) / O(n log n) |
| Longest or shortest subarray or substring with a condition | Sliding window | O(n) |
| Fixed size k subarray | Fixed sliding window | O(n) |
| Many range sum queries, subarray sum equals k | Prefix sum (plus HashMap) | O(n) |
| "Have I seen this", duplicates, frequency, anagrams | HashMap or HashSet | O(n) |
| Group items by some property | HashMap with a derived key | O(n) |
| Linked list middle, cycle, k-th from end | Fast and slow pointers | O(n), O(1) space |
| Reverse a list or part of it | Three-pointer reversal | O(n) |
| Matching brackets, undo, nested structure | Stack | O(n) |
| Next greater or smaller element | Monotonic stack | O(n) |
| Level by level, shortest path with equal edges, minimum steps | BFS with a queue | O(V + E) |
| Explore all paths, connected regions, islands, cycles | DFS | O(V + E) |
| Order with dependencies, prerequisites | Topological sort | O(V + E) |
| Cheapest path, non-negative weights | Dijkstra with PriorityQueue | O((V+E) log V) |
| K largest, K smallest, K most frequent, running median | Heap (size k) | O(n log k) |
| Tree questions (height, path, validate, LCA) | Recursion (DFS) returning a value | O(n) |
| BST questions | Use the ordering, or inorder is sorted | O(h) |
| Sorted input, "minimum X such that condition holds" | Binary search (on index or on answer) | O(log n) |
| All subsets, permutations, combinations, N-Queens | Backtracking | exponential |
| Count ways, min or max cost, choices that depend on earlier choices | Dynamic programming | O(n) to O(n^2) |
| Intervals (merge, overlap) | Sort by start, then sweep | O(n log n) |
| Need O(1) extra space on an array | In-place with swaps or read/write pointers | O(n) |

Interview habits:

- Ask about input size, duplicates, negative numbers, empty input and sorted or not, before coding.
- Walk through one small example by hand, then code, then test edge cases (empty, one element, all same).
- Say the time and space complexity at the end without being asked.

### 11.2 One bit trick note

Bit operations work on the binary form of an int. One idea covers many questions: XOR of a number with itself is 0 and with 0 is itself.

- `x & 1` is 1 for odd numbers. `n & (n - 1)` clears the lowest set bit (a power of two has exactly one set bit, so `n > 0 && (n & (n - 1)) == 0`).
- Single number: in an array where every value appears twice except one, XOR all values; pairs cancel and the single one remains.

```java
// Time O(n), space O(1).
static int singleNumber(int[] nums) {
    int result = 0;
    for (int n : nums) {
        result ^= n;
    }
    return result;
}
// singleNumber({4, 1, 2, 1, 2}) -> 4
```
