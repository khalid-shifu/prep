# DSA Interview Questions and Answers

Target: mid to senior Java / Spring Boot backend roles (about 3 years of experience). These are the data structure and algorithm questions that come up most often, mostly easy and medium level.

Each coding question has the problem with a small example, a short idea (brute force first, then the better approach), a step-by-step trace or diagram, Java code, time and space complexity, and follow-up questions with brief answers. Concept notes with more visuals are in `DSA.md`. The Java code and sample outputs were compiled and run on JDK 17 and checked against brute-force versions.

Complexity terms: `n` is the input size, `h` is the height of a tree, `V` and `E` are the number of nodes and edges of a graph.

---

## Index

- [1. Arrays and Strings](#1-arrays-and-strings)
  - [Basic](#11-basic)
    - [Q1. How do you approach a coding interview question?](#q1-how-do-you-approach-a-coding-interview-question-basic)
    - [Q2. Which technique fits which problem clue? (pattern table)](#q2-which-technique-fits-which-problem-clue-pattern-table-basic)
    - [Q3. How do you reverse an array or string, and check a palindrome?](#q3-how-do-you-reverse-an-array-or-string-and-check-a-palindrome-basic)
    - [Q4. How do you check whether two strings are anagrams?](#q4-how-do-you-check-whether-two-strings-are-anagrams-basic)
    - [Q5. How do you remove duplicates from a sorted array in place?](#q5-how-do-you-remove-duplicates-from-a-sorted-array-in-place-basic)
    - [Q6. How do you move all zeroes to the end while keeping order?](#q6-how-do-you-move-all-zeroes-to-the-end-while-keeping-order-basic)
    - [Q7. How do you find the first non-repeating character?](#q7-how-do-you-find-the-first-non-repeating-character-basic)
  - [Intermediate](#12-intermediate)
    - [Q8. What is the maximum sum of a contiguous subarray? (Kadane)](#q8-what-is-the-maximum-sum-of-a-contiguous-subarray-kadane-intermediate)
    - [Q9. How do you rotate an array to the right by k steps?](#q9-how-do-you-rotate-an-array-to-the-right-by-k-steps-intermediate)
    - [Q10. What is the longest substring without repeating characters?](#q10-what-is-the-longest-substring-without-repeating-characters-intermediate)
    - [Q11. How do you compress a string by counting repeated characters?](#q11-how-do-you-compress-a-string-by-counting-repeated-characters-intermediate)
    - [Q12. How do you merge overlapping intervals?](#q12-how-do-you-merge-overlapping-intervals-intermediate)
  - [Advanced](#13-advanced)
    - [Q13. How do you compute the product of the array except self, without division?](#q13-how-do-you-compute-the-product-of-the-array-except-self-without-division-advanced)
- [2. Hashing and Counting](#2-hashing-and-counting)
  - [Basic](#21-basic)
    - [Q14. Two sum](#q14-two-sum-basic)
    - [Q15. How do you count frequencies and find duplicates?](#q15-how-do-you-count-frequencies-and-find-duplicates-basic)
  - [Intermediate](#22-intermediate)
    - [Q16. How do you group anagrams?](#q16-how-do-you-group-anagrams-intermediate)
    - [Q17. How do you find the top K frequent elements?](#q17-how-do-you-find-the-top-k-frequent-elements-intermediate)
    - [Q18. How do you count subarrays with sum equal to K?](#q18-how-do-you-count-subarrays-with-sum-equal-to-k-intermediate)
    - [Q19. What is the longest consecutive sequence in an unsorted array?](#q19-what-is-the-longest-consecutive-sequence-in-an-unsorted-array-intermediate)
  - [Advanced](#23-advanced)
    - [Q20. What are the common two sum variants (sorted two pointers, 3Sum, design)?](#q20-what-are-the-common-two-sum-variants-sorted-two-pointers-3sum-design-advanced)
- [3. Linked Lists, Stacks and Queues](#3-linked-lists-stacks-and-queues)
  - [Basic](#31-basic)
    - [Q21. How do you reverse a linked list?](#q21-how-do-you-reverse-a-linked-list-basic)
    - [Q22. How do you check for valid parentheses?](#q22-how-do-you-check-for-valid-parentheses-basic)
    - [Q23. How do you merge two sorted linked lists?](#q23-how-do-you-merge-two-sorted-linked-lists-basic)
  - [Intermediate](#32-intermediate)
    - [Q24. How do you find the middle of a list and detect a cycle? (fast and slow pointers)](#q24-how-do-you-find-the-middle-of-a-list-and-detect-a-cycle-fast-and-slow-pointers-intermediate)
    - [Q25. How do you remove the N-th node from the end of a list?](#q25-how-do-you-remove-the-n-th-node-from-the-end-of-a-list-intermediate)
    - [Q26. How do you design a stack that returns the minimum in O(1)?](#q26-how-do-you-design-a-stack-that-returns-the-minimum-in-o1-intermediate)
    - [Q27. How do you implement a queue using stacks?](#q27-how-do-you-implement-a-queue-using-stacks-intermediate)
    - [Q28. How do you find the next greater element for each item? (monotonic stack)](#q28-how-do-you-find-the-next-greater-element-for-each-item-monotonic-stack-intermediate)
  - [Advanced](#33-advanced)
    - [Q29. How do you build an LRU cache with LinkedHashMap?](#q29-how-do-you-build-an-lru-cache-with-linkedhashmap-advanced)
    - [Q30. How do you build an LRU cache manually with a doubly linked list and a HashMap?](#q30-how-do-you-build-an-lru-cache-manually-with-a-doubly-linked-list-and-a-hashmap-advanced)
- [4. Trees](#4-trees)
  - [Basic](#41-basic)
    - [Q31. What are the inorder, preorder and postorder traversals?](#q31-what-are-the-inorder-preorder-and-postorder-traversals-basic)
    - [Q32. How do you find the maximum depth and check if a tree is balanced?](#q32-how-do-you-find-the-maximum-depth-and-check-if-a-tree-is-balanced-basic)
    - [Q33. How do you invert a binary tree and check if it is symmetric?](#q33-how-do-you-invert-a-binary-tree-and-check-if-it-is-symmetric-basic)
  - [Intermediate](#42-intermediate)
    - [Q34. How do you do a level order traversal (BFS)?](#q34-how-do-you-do-a-level-order-traversal-bfs-intermediate)
    - [Q35. How do you do inorder, preorder and postorder iteratively?](#q35-how-do-you-do-inorder-preorder-and-postorder-iteratively-intermediate)
    - [Q36. How do you check whether a root-to-leaf path has a given sum?](#q36-how-do-you-check-whether-a-root-to-leaf-path-has-a-given-sum-intermediate)
    - [Q37. How do you validate a binary search tree?](#q37-how-do-you-validate-a-binary-search-tree-intermediate)
    - [Q38. How do you find the K-th smallest element in a BST?](#q38-how-do-you-find-the-k-th-smallest-element-in-a-bst-intermediate)
  - [Advanced](#43-advanced)
    - [Q39. How do you find the lowest common ancestor (LCA) of two nodes?](#q39-how-do-you-find-the-lowest-common-ancestor-lca-of-two-nodes-advanced)
    - [Q40. How do you serialize and deserialize a binary tree?](#q40-how-do-you-serialize-and-deserialize-a-binary-tree-advanced)
- [5. Graphs and Grids](#5-graphs-and-grids)
  - [Basic](#51-basic)
    - [Q41. How do BFS and DFS work on a graph?](#q41-how-do-bfs-and-dfs-work-on-a-graph-basic)
    - [Q42. How do you count the number of islands in a grid?](#q42-how-do-you-count-the-number-of-islands-in-a-grid-basic)
  - [Intermediate](#52-intermediate)
    - [Q43. How do you find the shortest path in an unweighted graph?](#q43-how-do-you-find-the-shortest-path-in-an-unweighted-graph-intermediate)
    - [Q44. How do you detect a cycle in an undirected graph?](#q44-how-do-you-detect-a-cycle-in-an-undirected-graph-intermediate)
    - [Q45. How do you clone a graph?](#q45-how-do-you-clone-a-graph-intermediate)
  - [Advanced](#53-advanced)
    - [Q46. How do you solve Course Schedule? (cycle detection in a directed graph, topological sort)](#q46-how-do-you-solve-course-schedule-cycle-detection-in-a-directed-graph-topological-sort-advanced)
    - [Q47. How does Dijkstra's algorithm find the shortest path in a weighted graph?](#q47-how-does-dijkstras-algorithm-find-the-shortest-path-in-a-weighted-graph-advanced)
- [6. Searching, Sorting, Recursion and DP Basics](#6-searching-sorting-recursion-and-dp-basics)
  - [Basic](#61-basic)
    - [Q48. How does binary search work?](#q48-how-does-binary-search-work-basic)
    - [Q49. How do you solve Fibonacci and Climbing Stairs with memoization?](#q49-how-do-you-solve-fibonacci-and-climbing-stairs-with-memoization-basic)
  - [Intermediate](#62-intermediate)
    - [Q50. How do you find the first and last position of a value in a sorted array?](#q50-how-do-you-find-the-first-and-last-position-of-a-value-in-a-sorted-array-intermediate)
    - [Q51. How do you search in a rotated sorted array?](#q51-how-do-you-search-in-a-rotated-sorted-array-intermediate)
    - [Q52. How do merge sort and quick sort work?](#q52-how-do-merge-sort-and-quick-sort-work-intermediate)
    - [Q53. How do you find the K-th largest element and top K items? (heap vs quickselect)](#q53-how-do-you-find-the-k-th-largest-element-and-top-k-items-heap-vs-quickselect-intermediate)
    - [Q54. How do you generate all subsets and permutations? (backtracking)](#q54-how-do-you-generate-all-subsets-and-permutations-backtracking-intermediate)
    - [Q55. How do you solve House Robber?](#q55-how-do-you-solve-house-robber-intermediate)
  - [Advanced](#63-advanced)
    - [Q56. How do you solve Coin Change?](#q56-how-do-you-solve-coin-change-advanced)
    - [Q57. What is the longest common subsequence (LCS)?](#q57-what-is-the-longest-common-subsequence-lcs-advanced)
- [7. Java Collections and Data Structure Questions](#7-java-collections-and-data-structure-questions)
  - [Basic](#71-basic)
    - [Q58. ArrayList vs LinkedList vs array: which is faster and when?](#q58-arraylist-vs-linkedlist-vs-array-which-is-faster-and-when-basic)
    - [Q59. HashMap vs TreeMap vs LinkedHashMap, and how does HashSet keep elements unique?](#q59-hashmap-vs-treemap-vs-linkedhashmap-and-how-does-hashset-keep-elements-unique-basic)
    - [Q60. Why use ArrayDeque instead of Stack?](#q60-why-use-arraydeque-instead-of-stack-basic)
    - [Q61. Comparable vs Comparator, and how do you sort objects by multiple fields?](#q61-comparable-vs-comparator-and-how-do-you-sort-objects-by-multiple-fields-basic)
    - [Q62. What is the time complexity of common Java collection operations?](#q62-what-is-the-time-complexity-of-common-java-collection-operations-basic)
  - [Intermediate](#72-intermediate)
    - [Q63. How does HashMap work, and why do hashCode and equals matter for keys?](#q63-how-does-hashmap-work-and-why-do-hashcode-and-equals-matter-for-keys-intermediate)
    - [Q64. How does PriorityQueue work, and why is its iteration order not sorted?](#q64-how-does-priorityqueue-work-and-why-is-its-iteration-order-not-sorted-intermediate)
    - [Q65. How do Iterator and ConcurrentModificationException work?](#q65-how-do-iterator-and-concurrentmodificationexception-work-intermediate)
    - [Q66. What are immutable collections in Java?](#q66-what-are-immutable-collections-in-java-intermediate)
  - [Advanced](#73-advanced)
    - [Q67. How would you implement your own stack, queue and HashMap?](#q67-how-would-you-implement-your-own-stack-queue-and-hashmap-advanced)

---

# 1. Arrays and Strings

## 1.1 Basic

### Q1. How do you approach a coding interview question? [Basic]

**Short answer:** Clarify the problem, write an example, state the brute force, improve it, then code, test with examples and state the complexity. Interviewers grade the process as much as the final code.

**Steps:**
1. **Clarify.** Ask about input size, duplicates, negative numbers, sorted or not, empty input, and what to return when there is no answer.
2. **Example.** Write one small input and its expected output. Add one edge case (empty, one element, all equal).
3. **Brute force.** Say the simple solution and its complexity out loud, even if you will not code it. It proves you understand the problem.
4. **Optimize.** Look for the wasted work. Typical fixes: a HashMap to remove an inner loop, sorting, two pointers, a stack, a heap, recursion with memoization.
5. **Code.** Use clear names. One idea per helper method.
6. **Test.** Walk through your example line by line. Then run the edge cases.
7. **Complexity.** State time and extra space. **Big-O** describes how the work grows with input size `n`. Example: O(n) grows in line with n, O(n^2) grows with the square of n.

```text
Example: "find two numbers that add up to target"

brute force : two nested loops           -> O(n^2) time, O(1) space
better      : HashMap of seen numbers    -> O(n) time,   O(n) space
trade-off   : spend memory to save time
```

**Follow-up questions:**
- **What if you are stuck?** Say what you know, solve a smaller input by hand, and look for a pattern. Silence is worse than a partial idea.
- **What if you find a bug while testing?** Trace it with the example, fix it, and say what you changed.
- **How much time to spend on brute force?** One minute to state it. Do not code it unless the optimized idea does not come.

### Q2. Which technique fits which problem clue? (pattern table) [Basic]

**Short answer:** Most interview problems reuse a small set of techniques. Match the clue in the question to the technique.

| Clue in the problem | Technique | Typical question |
|---|---|---|
| Sorted array, pair or triple with a target | Two pointers (one at each end) | Two sum II, 3Sum, palindrome |
| Remove or move items in place | Read/write pointers | Remove duplicates, move zeroes |
| Longest/shortest contiguous subarray or substring | Sliding window | Longest substring without repeats |
| "Seen before", counts, duplicates, pairs | HashMap / HashSet | Two sum, group anagrams |
| Subarray sum, range sum | Prefix sum (+ HashMap) | Subarray sum equals K |
| Matching brackets, "next greater", undo | Stack (monotonic stack) | Valid parentheses, next greater element |
| Cycle, middle of a linked list | Fast and slow pointers | Detect cycle |
| Sorted data, "find in O(log n)" | Binary search | First/last position |
| Top K, K-th largest, running median | Heap (`PriorityQueue`) | Kth largest |
| Level by level, shortest path with equal costs | BFS (queue) | Level order, shortest path |
| All paths, connected parts, tree recursion | DFS (recursion or stack) | Number of islands, path sum |
| Dependencies, ordering | Topological sort | Course schedule |
| All combinations / permutations | Backtracking | Subsets, permutations |
| Best value, count of ways, overlapping subproblems | Dynamic programming (DP) | Climbing stairs, coin change |

**Follow-up questions:**
- **What is a monotonic stack?** A stack whose values stay sorted (always increasing or always decreasing). Elements that break the order are popped. See Q28.
- **How do you recognize DP?** The answer is built from answers of smaller inputs, and a plain recursion repeats the same calls. See Q49.

### Q3. How do you reverse an array or string, and check a palindrome? [Basic]

**Problem:** Reverse `{1, 2, 3, 4, 5}` to `{5, 4, 3, 2, 1}`. Check whether `"A man, a plan, a canal: Panama"` is a palindrome (read the same forward and backward) when only letters and digits count and case is ignored.

**Idea:** Use **two pointers**: one at the start, one at the end. Swap or compare, then move both inward until they meet. Time O(n), extra space O(1).

**Trace (reverse):**

```text
{1, 2, 3, 4, 5}      left=0 right=4  swap -> {5, 2, 3, 4, 1}
{5, 2, 3, 4, 1}      left=1 right=3  swap -> {5, 4, 3, 2, 1}
left=2 right=2       stop (left < right is false)
```

```java
public static void reverse(int[] nums) {
    int left = 0;
    int right = nums.length - 1;
    while (left < right) {
        int temp = nums[left];
        nums[left] = nums[right];
        nums[right] = temp;
        left++;
        right--;
    }
}

public static boolean isPalindrome(String s) {
    int left = 0;
    int right = s.length() - 1;
    while (left < right) {
        while (left < right && !Character.isLetterOrDigit(s.charAt(left))) {
            left++;
        }
        while (left < right && !Character.isLetterOrDigit(s.charAt(right))) {
            right--;
        }
        char a = Character.toLowerCase(s.charAt(left));
        char b = Character.toLowerCase(s.charAt(right));
        if (a != b) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
```

```java
isPalindrome("A man, a plan, a canal: Panama");
// true
isPalindrome("race a car");
// false  (compares 'e' with 'a')
```

**Complexity:** time O(n), extra space O(1).

**Follow-up questions:**
- **How do you reverse a String in Java?** `new StringBuilder(s).reverse().toString()`. `String` is immutable, so you cannot swap characters in place. Use `toCharArray()` for a char array you can swap.
- **Why not `s.replaceAll(...)` and compare with the reverse?** It works, but it allocates two extra strings (O(n) space). Interviewers usually want the two-pointer version.
- **Longest palindromic substring?** Expand around every center (2n-1 centers), O(n^2) time, O(1) space.
- **Can the palindrome check be done recursively?** Yes, but the depth is n/2, so very long strings can overflow the stack.

### Q4. How do you check whether two strings are anagrams? [Basic]

**Problem:** Two strings are **anagrams** if they contain the same characters with the same counts. `"listen"` and `"silent"` -> `true`. `"rat"` and `"car"` -> `false`.

**Idea:** Brute force: sort both and compare, O(n log n). Better: count characters. Add one for every character of `s` and subtract one for every character of `t`. If all counts end at zero, they are anagrams. O(n) time, O(1) space (26 slots for lowercase letters).

```java
public static boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) {
        return false;
    }
    int[] counts = new int[26];
    for (int i = 0; i < s.length(); i++) {
        counts[s.charAt(i) - 'a']++;
        counts[t.charAt(i) - 'a']--;
    }
    for (int count : counts) {
        if (count != 0) {
            return false;
        }
    }
    return true;
}
```

```java
isAnagram("listen", "silent");
// true
isAnagram("rat", "car");
// false  (final counts: 'c' is -1 and 't' is +1, the rest are 0)
```

`s.charAt(i) - 'a'` turns `'a'..'z'` into `0..25`.

**Complexity:** time O(n), space O(1) (fixed 26 slots).

**Follow-up questions:**
- **What about Unicode or uppercase?** Use `HashMap<Character, Integer>` instead of the 26-slot array. Space becomes O(k), k = distinct characters.
- **What if the check is called many times?** Precompute a key per word (sorted letters or count array). See group anagrams in Q16.
- **Does `s.equals(t)` ignore order?** No. Anagrams need counting or sorting.

### Q5. How do you remove duplicates from a sorted array in place? [Basic]

**Problem:** Given a sorted array, keep one copy of each value at the front and return how many unique values there are. `{0,0,1,1,1,2,2,3,3,4}` -> returns `5`, and the first five slots are `0,1,2,3,4`.

**Idea:** Brute force: copy into a `LinkedHashSet`, O(n) extra space. Better: **read/write pointers**. `read` scans every element. `write` marks where the next unique value goes. Because the array is sorted, a new value is simply different from the previous one.

**Trace on `{1, 1, 2, 2, 3}`:**

```text
write=1
read=1: 1 == 1 (previous)  skip
read=2: 2 != 1             nums[1] = 2, write=2   -> {1, 2, 2, 2, 3}
read=3: 2 == 2             skip
read=4: 3 != 2             nums[2] = 3, write=3   -> {1, 2, 3, 2, 3}
return 3   (first 3 slots: 1, 2, 3)
```

```java
public static int removeDuplicates(int[] nums) {
    if (nums.length == 0) {
        return 0;
    }
    int write = 1;
    for (int read = 1; read < nums.length; read++) {
        if (nums[read] != nums[read - 1]) {
            nums[write] = nums[read];
            write++;
        }
    }
    return write;
}
```

**Complexity:** time O(n), extra space O(1).

**Follow-up questions:**
- **Allow each value at most twice?** Compare with `nums[write - 2]` instead: `if (write < 2 || nums[read] != nums[write - 2])`.
- **Unsorted array?** Use a `HashSet` to track seen values (O(n) space), or sort first (O(n log n)).
- **Why return a count instead of a new array?** The array length cannot change, so the caller reads only the first `k` slots.

### Q6. How do you move all zeroes to the end while keeping order? [Basic]

**Problem:** `{0, 1, 0, 3, 12}` -> `{1, 3, 12, 0, 0}`. Do it in place.

**Idea:** Brute force: build a new array, O(n) space. Better: copy each non-zero value to the `write` position, then fill the rest with zeroes. Order of non-zero values is preserved.

**Trace:**

```text
{0, 1, 0, 3, 12}  write=0
i=0: 0   skip
i=1: 1   nums[0]=1  write=1
i=2: 0   skip
i=3: 3   nums[1]=3  write=2
i=4: 12  nums[2]=12 write=3
fill indexes 3..4 with 0 -> {1, 3, 12, 0, 0}
```

```java
public static void moveZeroes(int[] nums) {
    int write = 0;
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] != 0) {
            nums[write] = nums[i];
            write++;
        }
    }
    while (write < nums.length) {
        nums[write] = 0;
        write++;
    }
}
```

**Complexity:** time O(n), extra space O(1).

**Follow-up questions:**
- **Fewer writes when zeroes are rare?** Swap `nums[i]` with `nums[write]` only when `i != write`.
- **Same idea for "remove element"?** Yes. Copy values that are not equal to the target, return `write`.
- **Move zeroes to the front?** Run the same loop from the right.

### Q7. How do you find the first non-repeating character? [Basic]

**Problem:** Return the index of the first character that appears exactly once, or `-1`. `"leetcode"` -> `0`. `"loveleetcode"` -> `2`. `"aabb"` -> `-1`.

**Idea:** Brute force: for each character scan the whole string, O(n^2). Better: two passes. Pass 1 counts every character. Pass 2 returns the first index whose count is 1.

```java
public static int firstUniqueChar(String s) {
    int[] counts = new int[26];
    for (int i = 0; i < s.length(); i++) {
        counts[s.charAt(i) - 'a']++;
    }
    for (int i = 0; i < s.length(); i++) {
        if (counts[s.charAt(i) - 'a'] == 1) {
            return i;
        }
    }
    return -1;
}
```

```java
firstUniqueChar("loveleetcode");
// 2   ('l' appears twice, 'o' twice, 'v' once)
```

**Complexity:** time O(n), space O(1) (26 slots).

**Follow-up questions:**
- **Characters arrive as a stream?** Keep a `LinkedHashMap<Character, Integer>` of counts. The first entry with count 1 is the answer. Insertion order is the arrival order.
- **Any Unicode?** Use `HashMap<Integer, Integer>` keyed by code point.
- **Can it be one pass?** Store first index per character, plus a "duplicate" marker, then pick the smallest valid index.

---

## 1.2 Intermediate

### Q8. What is the maximum sum of a contiguous subarray? (Kadane) [Intermediate]

**Problem:** `{-2, 1, -3, 4, -1, 2, 1, -5, 4}` -> `6`, from the subarray `[4, -1, 2, 1]`.

**Idea:** Brute force: try every start and end, O(n^2) (O(n^3) if you re-sum). **Kadane's algorithm**: at each element decide whether to extend the running subarray or start a new one at this element. `current = max(num, current + num)`. Track the best `current` seen.

**Trace:**

```text
num   current = max(num, current+num)   best
-2    -2                                -2
 1     1   (start new, -2+1 = -1 < 1)    1
-3    -2                                 1
 4     4   (start new)                   4
-1     3                                 4
 2     5                                 5
 1     6                                 6
-5     1                                 6
 4     5                                 6
answer 6
```

```java
public static int maxSubArray(int[] nums) {
    int current = nums[0];
    int best = nums[0];
    for (int i = 1; i < nums.length; i++) {
        current = Math.max(nums[i], current + nums[i]);
        best = Math.max(best, current);
    }
    return best;
}
```

**Complexity:** time O(n), extra space O(1).

**Follow-up questions:**
- **All numbers negative?** Starting from `nums[0]` (not 0) makes the answer the largest single element. Starting `best` at 0 would wrongly return 0.
- **Return the subarray itself?** Track a `start` index that resets when you start a new subarray, and record `bestStart`/`bestEnd` when `best` improves.
- **Maximum product subarray?** Track both max and min product ending here, because two negatives make a positive.
- **Circular array?** Answer is max(Kadane max, total sum - Kadane min), with a guard for all-negative input.

### Q9. How do you rotate an array to the right by k steps? [Intermediate]

**Problem:** `{1,2,3,4,5,6,7}`, `k = 3` -> `{5,6,7,1,2,3,4}`. Do it in place.

**Idea:** Brute force: shift by one, k times, O(n*k). Better: **three reversals**. Reverse the whole array, then reverse the first `k` elements, then reverse the rest. First do `k = k % n` so that `k` larger than `n` still works.

**Trace:**

```text
start            1 2 3 4 5 6 7
reverse all      7 6 5 4 3 2 1
reverse first 3  5 6 7 4 3 2 1
reverse rest     5 6 7 1 2 3 4
```

```java
public static void rotate(int[] nums, int k) {
    int n = nums.length;
    k = k % n;
    reverse(nums, 0, n - 1);
    reverse(nums, 0, k - 1);
    reverse(nums, k, n - 1);
}

private static void reverse(int[] nums, int left, int right) {
    while (left < right) {
        int temp = nums[left];
        nums[left] = nums[right];
        nums[right] = temp;
        left++;
        right--;
    }
}
```

**Complexity:** time O(n), extra space O(1).

**Follow-up questions:**
- **Simpler version with extra space?** `result[(i + k) % n] = nums[i]`, then copy back. O(n) space.
- **Rotate left by k?** Rotate right by `n - k`.
- **What if `k` is 0 or a multiple of `n`?** `k % n` is 0. `reverse(nums, 0, -1)` does nothing, so the code stays correct.
- **Empty array?** Check `n == 0` first, otherwise `k % n` divides by zero.

### Q10. What is the longest substring without repeating characters? [Intermediate]

**Problem:** `"abcabcbb"` -> `3` (`"abc"`). `"bbbbb"` -> `1`. `"pwwkew"` -> `3` (`"wke"`).

**Idea:** Brute force: check every substring, O(n^3) or O(n^2). Better: **sliding window**, a range `[left, right]` of the string that is kept valid. Move `right` forward one character at a time. If that character was already seen inside the window, jump `left` to just after its last position. Store the last index of each character in a map.

**Trace on `"abcabcbb"`:**

```text
right char  last index (before)  left  window   length
0     a     -                    0     a        1
1     b     -                    0     ab       2
2     c     -                    0     abc      3
3     a     0 (>= left)          1     bca      3
4     b     1 (>= left)          2     cab      3
5     c     2 (>= left)          3     abc      3
6     b     4 (>= left)          5     cb       2
7     b     6 (>= left)          7     b        1
best = 3
```

```java
public static int lengthOfLongestSubstring(String s) {
    Map<Character, Integer> lastIndex = new HashMap<>();
    int left = 0;
    int best = 0;
    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        Integer previous = lastIndex.get(c);
        if (previous != null && previous >= left) {
            left = previous + 1;
        }
        lastIndex.put(c, right);
        best = Math.max(best, right - left + 1);
    }
    return best;
}
```

The check `previous >= left` matters. A character seen before the window started must not move `left` backward.

**Complexity:** time O(n), space O(k) where k is the number of distinct characters (at most 128 for ASCII).

**Follow-up questions:**
- **Return the substring?** Remember `bestLeft` when `best` improves, then use `substring`.
- **Why not reset the map when a repeat is found?** Stale entries are harmless because of the `previous >= left` check, and not clearing keeps it O(n).
- **ASCII only?** Replace the map with `int[128]` for speed.
- **At most K distinct characters?** Keep a count map and shrink `left` while the map size exceeds K.

### Q11. How do you compress a string by counting repeated characters? [Intermediate]

**Problem:** `"aabcccccaaa"` -> `"a2b1c5a3"`. If the compressed string is not shorter than the original, return the original. `"abc"` -> `"abc"`, because `"a1b1c1"` is longer.

**Idea:** Walk through the string with a run pointer. For each run of equal characters, append the character and the run length. Build the result with a `StringBuilder` (repeated `+=` on a String is O(n^2) because each concatenation copies).

```java
public static String compress(String s) {
    StringBuilder sb = new StringBuilder();
    int i = 0;
    while (i < s.length()) {
        char c = s.charAt(i);
        int j = i;
        while (j < s.length() && s.charAt(j) == c) {
            j++;
        }
        sb.append(c);
        sb.append(j - i);
        i = j;
    }
    if (sb.length() < s.length()) {
        return sb.toString();
    }
    return s;
}
```

```java
compress("aabcccccaaa");
// "a2b1c5a3"  (length 8 < 11)
```

**Complexity:** time O(n), space O(n) for the result.

**Follow-up questions:**
- **In-place on a `char[]`, writing the count only when greater than 1?** Use read/write pointers like Q5. Write the character, then the digits of the count. Counts above 9 need several characters, so convert the count to a string.
- **Why `StringBuilder` and not `+=`?** Strings are immutable. Each `+=` creates a new string and copies, so n concatenations cost O(n^2).
- **How do you decompress `"a2b1"`?** Read a letter, then read digits until the next letter, append the letter that many times.

### Q12. How do you merge overlapping intervals? [Intermediate]

**Problem:** `[[1,3],[2,6],[8,10],[15,18]]` -> `[[1,6],[8,10],[15,18]]`. `[1,3]` and `[2,6]` overlap, so they merge into `[1,6]`.

**Idea:** Brute force: compare every pair repeatedly, O(n^2) or worse. Better: **sort by start**. Then overlapping intervals are neighbors. Keep a result list. If the current interval starts at or before the end of the last result interval, extend that end. Otherwise append the current interval.

**Trace:**

```text
sorted: [1,3] [2,6] [8,10] [15,18]
result = [[1,3]]
[2,6]   2 <= 3  overlap  -> last = [1, max(3,6)] = [1,6]
[8,10]  8 >  6  no       -> add  -> [[1,6],[8,10]]
[15,18] 15 > 10 no       -> add  -> [[1,6],[8,10],[15,18]]
```

```java
public static int[][] merge(int[][] intervals) {
    Arrays.sort(intervals, (a, b) -> Integer.compare(a[0], b[0]));
    List<int[]> merged = new ArrayList<>();
    for (int[] current : intervals) {
        if (merged.isEmpty()) {
            merged.add(current);
            continue;
        }
        int[] last = merged.get(merged.size() - 1);
        if (current[0] <= last[1]) {
            last[1] = Math.max(last[1], current[1]);
        } else {
            merged.add(current);
        }
    }
    return merged.toArray(new int[0][]);
}
```

**Complexity:** time O(n log n) for the sort, extra space O(n) for the result.

**Follow-up questions:**
- **Do `[1,4]` and `[4,5]` merge?** With `<=` yes, giving `[1,5]`. Use `<` if touching intervals must stay separate. Ask the interviewer.
- **Why `Integer.compare` and not `a[0] - b[0]`?** Subtraction can overflow for large values.
- **Insert a new interval into a sorted list?** One pass: add all intervals ending before it, merge all overlapping ones, add the rest. O(n).
- **Minimum meeting rooms?** Sort starts and ends separately, or use a min-heap of end times (see Q53).
- **This modifies the input arrays.** The code reuses the input arrays in the result. Copy them first if the caller needs the original.

---

## 1.3 Advanced

### Q13. How do you compute the product of the array except self, without division? [Advanced]

**Problem:** `{1, 2, 3, 4}` -> `{24, 12, 8, 6}`. `result[i]` is the product of every element except `nums[i]`. Do not use division, and aim for O(n) time.

**Idea:** Brute force: for each index multiply all others, O(n^2). Division fails when the array has zeroes. Better: `result[i] = (product of everything left of i) * (product of everything right of i)`. Build the left products in a first pass, then multiply by a running right product in a second pass from the end. The output array does not count as extra space.

**Trace:**

```text
nums               1  2  3  4
left pass result   1  1  2  6      (result[i] = product of nums[0..i-1])

right pass (right starts at 1):
i=3: result[3] = 6 * 1  = 6    right = 1 * 4  = 4
i=2: result[2] = 2 * 4  = 8    right = 4 * 3  = 12
i=1: result[1] = 1 * 12 = 12   right = 12 * 2 = 24
i=0: result[0] = 1 * 24 = 24
result             24 12  8  6
```

```java
public static int[] productExceptSelf(int[] nums) {
    int n = nums.length;
    int[] result = new int[n];
    result[0] = 1;
    for (int i = 1; i < n; i++) {
        result[i] = result[i - 1] * nums[i - 1];
    }
    int right = 1;
    for (int i = n - 1; i >= 0; i--) {
        result[i] = result[i] * right;
        right = right * nums[i];
    }
    return result;
}
```

**Complexity:** time O(n), extra space O(1) not counting the output.

**Follow-up questions:**
- **How would division work, and where does it break?** Total product divided by `nums[i]`. It fails if any element is 0. With one zero only that position is non-zero. With two or more zeroes every result is 0.
- **Overflow?** Products grow fast. Use `long`, or `BigInteger` if the interviewer asks for exact results.
- **Why is the left pass `result[i-1] * nums[i-1]`?** `result[i-1]` already holds the product of everything left of `i-1`. Multiplying by `nums[i-1]` extends it to everything left of `i`.

---

# 2. Hashing and Counting

## 2.1 Basic

### Q14. Two sum [Basic]

**Problem:** Given an array and a target, return the indexes of the two numbers that add up to the target. Exactly one answer exists and you may not use the same element twice. `{2, 7, 11, 15}`, target `9` -> `[0, 1]`.

**Idea:** Brute force: two nested loops, O(n^2) time. Better: one pass with a `HashMap` from value to index. For each number, the partner is `target - number`. If the partner is already in the map, you are done. Otherwise store the current number. A lookup in a HashMap is O(1) on average.

**Trace on `{2, 7, 11, 15}`, target 9:**

```text
i=0 num=2  need=7  map={}          not found -> map={2:0}
i=1 num=7  need=2  map={2:0}       found at index 0 -> return [0, 1]
```

```java
public static int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> seen = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int need = target - nums[i];
        if (seen.containsKey(need)) {
            return new int[] {seen.get(need), i};
        }
        seen.put(nums[i], i);
    }
    throw new IllegalArgumentException("No two sum solution");
}
```

**Complexity:** time O(n), extra space O(n).

**Follow-up questions:**
- **Duplicates like `{3, 3}`, target 6?** Works. The lookup happens before the insert, so the first 3 is found by the second 3, and an element is never paired with itself.
- **Array is sorted?** Use two pointers, O(n) time and O(1) space (see Q20).
- **Return the values, not indexes?** Sort and use two pointers, or use a `HashSet`.
- **No solution?** Return an empty array or throw, whichever the problem states. Ask.

### Q15. How do you count frequencies and find duplicates? [Basic]

**Problem:** Count how many times each number appears. For `{4, 5, 4, 6, 5, 4}` the counts are `4 -> 3`, `5 -> 2`, `6 -> 1`. Also check whether any value repeats, and find all duplicates in an array of values `1..n` without extra memory.

**Idea:** A `HashMap<value, count>` gives counts in O(n). `map.merge(x, 1, Integer::sum)` adds one, creating the entry if missing. For "any duplicate", a `HashSet`: `add` returns `false` when the value is already there.

```java
public static Map<Integer, Integer> frequency(int[] nums) {
    Map<Integer, Integer> counts = new HashMap<>();
    for (int num : nums) {
        counts.merge(num, 1, Integer::sum);
    }
    return counts;
}

public static boolean hasDuplicate(int[] nums) {
    Set<Integer> seen = new HashSet<>();
    for (int num : nums) {
        if (!seen.add(num)) {
            return true;
        }
    }
    return false;
}
```

```java
frequency(new int[] {4, 5, 4, 6, 5, 4});
// {4=3, 5=2, 6=1}  (HashMap order is not guaranteed in general)
hasDuplicate(new int[] {1, 2, 3, 1});
// true
```

**Duplicates in O(1) extra space.** When all values are in `1..n` and the array length is `n`, use the array itself as a marker. Value `v` maps to index `v - 1`. Make `nums[v - 1]` negative the first time. If it is already negative, `v` is a duplicate.

```text
nums = {4, 3, 2, 7, 8, 2, 3, 1}
i=0 v=4 index 3: 7  -> -7
i=1 v=3 index 2: 2  -> -2
i=2 v=2 index 1: 3  -> -3
i=3 v=7 index 6: 3  -> -3
i=4 v=8 index 7: 1  -> -1
i=5 v=2 index 1: already -3 -> duplicate 2
i=6 v=3 index 2: already -2 -> duplicate 3
i=7 v=1 index 0: 4  -> -4
result [2, 3]
```

```java
public static List<Integer> findDuplicates(int[] nums) {
    List<Integer> result = new ArrayList<>();
    for (int i = 0; i < nums.length; i++) {
        int value = Math.abs(nums[i]);
        int index = value - 1;
        if (nums[index] < 0) {
            result.add(value);
        } else {
            nums[index] = -nums[index];
        }
    }
    return result;
}
```

**Complexity:** counting and `hasDuplicate`: O(n) time, O(n) space. `findDuplicates`: O(n) time, O(1) extra space (the input is modified; restore it with `Math.abs` if needed).

**Follow-up questions:**
- **Why `Math.abs` when reading?** An earlier step may already have negated that slot.
- **Sort instead of a set?** O(n log n) time, O(1) extra space, but it reorders the array.
- **Most frequent element?** Track the best count while iterating the map. For a majority element (more than n/2 times), Boyer-Moore voting does it in O(1) space.
- **`merge` vs `getOrDefault`+`put`?** Same result. `merge` is shorter and does one lookup less.

---

## 2.2 Intermediate

### Q16. How do you group anagrams? [Intermediate]

**Problem:** `["eat", "tea", "tan", "ate", "nat", "bat"]` -> `[["eat","tea","ate"], ["tan","nat"], ["bat"]]` (group order may differ).

**Idea:** Brute force: compare every pair with the anagram check from Q4, O(n^2 * k). Better: give every word a **key** that is identical for anagrams, and group by key in a map. Sorting the letters works: `"eat"`, `"tea"`, `"ate"` all become `"aet"`.

```text
"eat" -> "aet"  \
"tea" -> "aet"   >  group "aet" : [eat, tea, ate]
"ate" -> "aet"  /
"tan" -> "ant"  \   group "ant" : [tan, nat]
"nat" -> "ant"  /
"bat" -> "abt"      group "abt" : [bat]
```

```java
public static List<List<String>> groupAnagrams(String[] words) {
    Map<String, List<String>> groups = new HashMap<>();
    for (String word : words) {
        char[] chars = word.toCharArray();
        Arrays.sort(chars);
        String key = new String(chars);
        groups.computeIfAbsent(key, k -> new ArrayList<>()).add(word);
    }
    return new ArrayList<>(groups.values());
}
```

**Complexity:** time O(n * k log k) for n words of length k, space O(n * k).

**Follow-up questions:**
- **Faster than sorting each word?** Use the 26-letter count as the key, for example `"1#0#0#...#"`. That is O(k) per word, so O(n * k) total.
- **Why a `String` key and not `char[]`?** Arrays use identity `equals`/`hashCode`, so two equal arrays would be different keys. `String` compares by content.
- **Why is the output order unspecified?** `HashMap` has no defined order. Use `LinkedHashMap` for first-seen order.

### Q17. How do you find the top K frequent elements? [Intermediate]

**Problem:** `{1, 1, 1, 2, 2, 3}`, `k = 2` -> `[1, 2]`.

**Idea:** Count with a HashMap first (O(n)). Then pick the K largest counts. Options: sort the entries, O(m log m) where m is the number of distinct values; a min-heap of size K, O(m log k); or **bucket sort**, O(n). Bucket sort uses the fact that a count is at most `n`. Make `n + 1` buckets, where bucket `c` holds the numbers that appear `c` times. Read buckets from the highest count downward.

**Trace:**

```text
counts:  1 -> 3,  2 -> 2,  3 -> 1
buckets: [0]=[]  [1]=[3]  [2]=[2]  [3]=[1]  [4]=[]  [5]=[]  [6]=[]
read from bucket 6 down: first non-empty is [3]=[1] -> take 1
                                         then [2]=[2] -> take 2
size is 2 = k, stop -> [1, 2]
```

```java
public static int[] topKFrequent(int[] nums, int k) {
    Map<Integer, Integer> counts = new HashMap<>();
    for (int num : nums) {
        counts.merge(num, 1, Integer::sum);
    }
    List<List<Integer>> buckets = new ArrayList<>();
    for (int i = 0; i <= nums.length; i++) {
        buckets.add(new ArrayList<>());
    }
    for (Map.Entry<Integer, Integer> entry : counts.entrySet()) {
        buckets.get(entry.getValue()).add(entry.getKey());
    }
    int[] result = new int[k];
    int index = 0;
    for (int count = nums.length; count >= 1 && index < k; count--) {
        for (int value : buckets.get(count)) {
            if (index < k) {
                result[index] = value;
                index++;
            }
        }
    }
    return result;
}
```

**Complexity:** time O(n), space O(n).

**Follow-up questions:**
- **Heap version?** `PriorityQueue<Map.Entry<Integer,Integer>>` ordered by count (min-heap). Add each entry, and poll when the size exceeds K. O(m log k). See Q53.
- **Which is better in an interview?** The heap is the usual expected answer. Bucket sort is the "can you do O(n)" follow-up.
- **Ties at the K boundary?** Ask the interviewer. The code above takes any of the tied values.
- **Data too big for memory / streaming?** Count-Min Sketch plus a heap for approximate counts, or partition by hash and merge partial counts.

### Q18. How do you count subarrays with sum equal to K? [Intermediate]

**Problem:** `{1, 2, 3}`, `k = 3` -> `2`, for subarrays `[1, 2]` and `[3]`. The array may contain negative numbers.

**Idea:** Brute force: try all start and end pairs, O(n^2). Better: a **prefix sum** is the sum of elements from the start up to the current index. A subarray `(i, j]` has sum `prefix[j] - prefix[i]`. So at index `j` you need the number of earlier prefixes equal to `prefix[j] - k`. Keep a map from prefix value to how many times it occurred. Start with `{0: 1}` so subarrays that begin at index 0 are counted.

**Trace on `{1, 2, 3}`, k = 3:**

```text
map = {0:1}, sum = 0, count = 0
num=1  sum=1  need=1-3=-2  not in map          map={0:1, 1:1}
num=2  sum=3  need=3-3=0   map[0]=1 -> count=1 map={0:1, 1:1, 3:1}
num=3  sum=6  need=6-3=3   map[3]=1 -> count=2 map={0:1, 1:1, 3:1, 6:1}
answer 2
```

```java
public static int subarraySum(int[] nums, int k) {
    Map<Integer, Integer> prefixCounts = new HashMap<>();
    prefixCounts.put(0, 1);
    int sum = 0;
    int count = 0;
    for (int num : nums) {
        sum += num;
        count += prefixCounts.getOrDefault(sum - k, 0);
        prefixCounts.merge(sum, 1, Integer::sum);
    }
    return count;
}
```

```java
subarraySum(new int[] {1, -1, 0}, 0);
// 3   ([1,-1], [0], [1,-1,0])
```

**Complexity:** time O(n), space O(n).

**Follow-up questions:**
- **Why not a sliding window?** Windows work only for non-negative numbers, because then the sum only grows as the window grows. With negatives, extending the window can lower the sum.
- **Why `{0: 1}` first?** A prefix of 0 means "empty prefix". Without it, a subarray starting at index 0 would be missed.
- **Longest subarray with sum K?** Store the first index of each prefix, not the count. Length = `j - firstIndex[sum - k]`.
- **Binary array with equal 0s and 1s?** Treat 0 as -1 and find the longest subarray with sum 0.

### Q19. What is the longest consecutive sequence in an unsorted array? [Intermediate]

**Problem:** `{100, 4, 200, 1, 3, 2}` -> `4`, for `1, 2, 3, 4`. Required time is O(n), so sorting (O(n log n)) is not allowed.

**Idea:** Put all numbers in a `HashSet`. A number is the **start** of a sequence only if `num - 1` is not in the set. From each start, count upward while `num + 1` exists. Every number is walked at most once as part of one sequence, so the total is O(n).

**Trace:**

```text
set = {100, 4, 200, 1, 3, 2}
100: 99 absent  -> start, 101 absent                 length 1
4:   3 present  -> skip
200: 199 absent -> start, 201 absent                 length 1
1:   0 absent   -> start, 2, 3, 4 present, 5 absent  length 4
3:   2 present  -> skip
2:   1 present  -> skip
best 4
```

```java
public static int longestConsecutive(int[] nums) {
    Set<Integer> set = new HashSet<>();
    for (int num : nums) {
        set.add(num);
    }
    int best = 0;
    for (int num : set) {
        if (set.contains(num - 1)) {
            continue;
        }
        int current = num;
        int length = 1;
        while (set.contains(current + 1)) {
            current++;
            length++;
        }
        best = Math.max(best, length);
    }
    return best;
}
```

**Complexity:** time O(n), space O(n).

**Follow-up questions:**
- **Why is it not O(n^2)?** The inner loop runs only from sequence starts, so each element is visited by exactly one inner loop.
- **Duplicates in the input?** The set removes them.
- **Sort-based alternative?** Sort, then count runs while skipping equal values. O(n log n), O(1) extra space if sorting in place.

---

## 2.3 Advanced

### Q20. What are the common two sum variants (sorted two pointers, 3Sum, design)? [Advanced]

**Problem:** (a) Two sum on a **sorted** array. (b) **3Sum**: all unique triplets that add up to 0. `{-1, 0, 1, 2, -1, -4}` -> `[[-1,-1,2], [-1,0,1]]`.

**Idea (a):** Two pointers, one at each end. If the sum is too small, move `left` right. If too big, move `right` left. O(n) time, O(1) space.

**Idea (b):** Brute force: three loops, O(n^3). Better: sort, fix one number `nums[i]`, then solve two sum on the rest with two pointers. Skip equal neighbors so each triplet appears once. O(n^2).

**Trace of 3Sum on sorted `[-4, -1, -1, 0, 1, 2]`:**

```text
i=0 (-4): left=1 right=5: -4-1+2=-3 <0 left++ ... all sums < 0 until left meets right -> none
i=1 (-1): left=2 (-1) right=5 (2): sum=0 -> add [-1,-1,2], left=3, right=4
          left=3 (0)  right=4 (1): sum=0 -> add [-1,0,1], left=4, right=3 -> stop
i=2 (-1): same as previous value -> skip
i=3 (0):  left=4 (1) right=5 (2): sum=3 >0 right-- -> stop
result [[-1,-1,2], [-1,0,1]]
```

```java
public static int[] twoSumSorted(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    while (left < right) {
        int sum = nums[left] + nums[right];
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

public static List<List<Integer>> threeSum(int[] nums) {
    Arrays.sort(nums);
    List<List<Integer>> result = new ArrayList<>();
    for (int i = 0; i < nums.length - 2; i++) {
        if (i > 0 && nums[i] == nums[i - 1]) {
            continue;
        }
        int left = i + 1;
        int right = nums.length - 1;
        while (left < right) {
            int sum = nums[i] + nums[left] + nums[right];
            if (sum == 0) {
                result.add(Arrays.asList(nums[i], nums[left], nums[right]));
                left++;
                right--;
                while (left < right && nums[left] == nums[left - 1]) {
                    left++;
                }
                while (left < right && nums[right] == nums[right + 1]) {
                    right--;
                }
            } else if (sum < 0) {
                left++;
            } else {
                right--;
            }
        }
    }
    return result;
}
```

**Complexity:** `twoSumSorted` O(n) time, O(1) space. `threeSum` O(n^2) time, O(1) extra space besides the sort and output.

**Follow-up questions:**
- **Why sort for 3Sum?** Sorting lets the two-pointer step work and makes equal values adjacent so duplicates are easy to skip.
- **3Sum closest?** Same loop, track the sum with the smallest `|sum - target|`.
- **4Sum?** Add one more outer loop, O(n^3).
- **Design: two sum where numbers are added over time?** Keep a `HashMap<Integer,Integer>` of counts. `add` is O(1). `find(target)` loops over the keys and looks up `target - key` in O(1), so O(n). The count matters for `target = 2 * key`, which needs the key twice.
- **Two sum on a BST?** Inorder traversal into a sorted list, then two pointers. Or traverse with a `HashSet`.

---

# 3. Linked Lists, Stacks and Queues

The linked list questions use this node class.

```java
public class ListNode {

    int val;
    ListNode next;

    ListNode(int val) {
        this.val = val;
    }
}
```

## 3.1 Basic

### Q21. How do you reverse a linked list? [Basic]

**Problem:** `1 -> 2 -> 3 -> null` becomes `3 -> 2 -> 1 -> null`.

**Idea:** Walk the list and flip each `next` pointer to point backward. You need three references: `prev` (already reversed part), `curr` (node being flipped) and `next` (saved so you do not lose the rest of the list).

**Trace:**

```text
start:        null   1 -> 2 -> 3 -> null
              prev   curr

step 1: next=2, 1.next=null, prev=1, curr=2
              null <- 1    2 -> 3 -> null
                    prev  curr

step 2: next=3, 2.next=1, prev=2, curr=3
              null <- 1 <- 2    3 -> null
                          prev  curr

step 3: next=null, 3.next=2, prev=3, curr=null
              null <- 1 <- 2 <- 3
                                prev      curr=null -> stop, return prev
```

```java
public static ListNode reverse(ListNode head) {
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

public static ListNode reverseRecursive(ListNode head) {
    if (head == null || head.next == null) {
        return head;
    }
    ListNode newHead = reverseRecursive(head.next);
    head.next.next = head;
    head.next = null;
    return newHead;
}
```

**Complexity:** iterative: time O(n), space O(1). Recursive: time O(n), space O(n) for the call stack.

**Follow-up questions:**
- **Reverse only positions m to n?** Walk to node `m - 1`, then reverse the next `n - m + 1` nodes and reconnect both ends. Use a dummy node for the `m = 1` case.
- **Reverse in groups of k?** Reverse each block of k, link blocks together, leave a final short block as is.
- **Is a list a palindrome?** Find the middle (Q24), reverse the second half, compare, and optionally reverse back. O(n) time, O(1) space.
- **Which version do you prefer?** Iterative. The recursive version can overflow the stack on a very long list.

### Q22. How do you check for valid parentheses? [Basic]

**Problem:** A string of `()[]{}` is valid if every opening bracket is closed by the same type in the right order. `"()[]{}"` -> true. `"{[]}"` -> true. `"(]"` -> false. `"([)]"` -> false.

**Idea:** Use a **stack** (last in, first out). On an opening bracket, push the closing bracket you expect. On a closing bracket, pop and compare. At the end the stack must be empty.

**Trace for `"{[]}"` and `"([)]"`:**

```text
"{[]}"                          "([)]"
'{' push '}'   stack: }         '(' push ')'   stack: )
'[' push ']'   stack: } ]       '[' push ']'   stack: ) ]
']' pop ']' ok stack: }         ')' pop ']'  != ')' -> false
'}' pop '}' ok stack: empty
end: empty -> true
```

```java
public static boolean isValid(String s) {
    Deque<Character> stack = new ArrayDeque<>();
    for (char c : s.toCharArray()) {
        if (c == '(') {
            stack.push(')');
        } else if (c == '[') {
            stack.push(']');
        } else if (c == '{') {
            stack.push('}');
        } else if (stack.isEmpty() || stack.pop() != c) {
            return false;
        }
    }
    return stack.isEmpty();
}
```

**Complexity:** time O(n), space O(n).

**Follow-up questions:**
- **Why check `stack.isEmpty()` before `pop`?** A string like `")"` would otherwise throw `NoSuchElementException`.
- **Why `ArrayDeque` and not `Stack`?** See Q60.
- **Minimum characters to add to make it valid?** Count unmatched closers (stack empty on a closer) plus the leftover openers.
- **Longest valid parentheses substring?** A stack of indexes, or a DP array.
- **Quick rejection?** An odd length can never be valid.

### Q23. How do you merge two sorted linked lists? [Basic]

**Problem:** `1 -> 2 -> 4` and `1 -> 3 -> 4` merge into `1 -> 1 -> 2 -> 3 -> 4 -> 4`.

**Idea:** Use a **dummy** head node so you never special-case the first node. Keep a `tail` pointer. Repeatedly take the smaller head of the two lists, attach it to `tail`, and advance. When one list ends, attach the rest of the other.

**Trace:**

```text
a: 1 -> 2 -> 4      b: 1 -> 3 -> 4
dummy -> 1(a)       (a.val 1 <= b.val 1, take a)      a = 2 -> 4
dummy -> 1 -> 1(b)  (b 1 < a 2)                       b = 3 -> 4
dummy -> 1 -> 1 -> 2(a)                               a = 4
dummy -> 1 -> 1 -> 2 -> 3(b)                          b = 4
dummy -> ... -> 4(a)                                  a = null
a is empty: attach rest of b (4)
result: 1 -> 1 -> 2 -> 3 -> 4 -> 4
```

```java
public static ListNode mergeTwoLists(ListNode a, ListNode b) {
    ListNode dummy = new ListNode(0);
    ListNode tail = dummy;
    while (a != null && b != null) {
        if (a.val <= b.val) {
            tail.next = a;
            a = a.next;
        } else {
            tail.next = b;
            b = b.next;
        }
        tail = tail.next;
    }
    if (a != null) {
        tail.next = a;
    } else {
        tail.next = b;
    }
    return dummy.next;
}
```

**Complexity:** time O(n + m), extra space O(1) (nodes are reused).

**Follow-up questions:**
- **Merge K sorted lists?** Put the K heads in a min-heap (`PriorityQueue`), poll the smallest, push its next. O(N log K).
- **Why `<=`?** It keeps the merge stable: equal values keep the order of list `a` first.
- **Sort a linked list?** Merge sort: split at the middle (Q24), sort both halves, merge. O(n log n), no random access needed.

---

## 3.2 Intermediate

### Q24. How do you find the middle of a list and detect a cycle? (fast and slow pointers) [Intermediate]

**Problem:** (a) Return the middle node of `1 -> 2 -> 3 -> 4 -> 5` (the node `3`). For an even length return the second middle. (b) Detect whether a list has a cycle (a node whose `next` points back to an earlier node), and find where the cycle starts.

**Idea:** Move two pointers: `slow` by one node, `fast` by two nodes per step. (a) When `fast` reaches the end, `slow` is at the middle. (b) In a cycle `fast` laps `slow` and they meet. Without a cycle `fast` reaches `null`. The brute force alternative is a `HashSet` of visited nodes, O(n) space.

**Cycle trace.** List `3 -> 2 -> 0 -> -4`, and the tail `-4` points back to `2`:

```text
 3 -> 2 -> 0 -> -4
      ^          |
      +----------+

start      slow=3   fast=3
step 1     slow=2   fast=0
step 2     slow=0   fast=2     (0 -> -4 -> 2)
step 3     slow=-4  fast=-4    (2 -> 0 -> -4)   they meet: cycle exists

find the start: put p1 at head (3), keep p2 at the meeting node (-4), move both one step
           p1=2     p2=2       (-4 -> 2)        they meet at 2 = start of the cycle
```

```java
public static ListNode middle(ListNode head) {
    ListNode slow = head;
    ListNode fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }
    return slow;
}

public static boolean hasCycle(ListNode head) {
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

public static ListNode cycleStart(ListNode head) {
    ListNode slow = head;
    ListNode fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) {
            ListNode p1 = head;
            ListNode p2 = slow;
            while (p1 != p2) {
                p1 = p1.next;
                p2 = p2.next;
            }
            return p1;
        }
    }
    return null;
}
```

**Complexity:** time O(n), space O(1).

**Follow-up questions:**
- **Why do the pointers always meet in a cycle?** Inside the cycle `fast` gains one node on `slow` each step, so the gap shrinks by 1 until it is 0.
- **Why does the reset-to-head trick find the start?** The distance from head to the cycle start equals the distance from the meeting point to the cycle start (going around). This follows from `fast` having walked twice as far as `slow`.
- **Length of the cycle?** After they meet, walk one pointer around until it returns to the meeting node, counting steps.
- **Why compare nodes with `==`?** Cycle detection is about the same node object, not equal values.

### Q25. How do you remove the N-th node from the end of a list? [Intermediate]

**Problem:** `1 -> 2 -> 3 -> 4 -> 5`, `n = 2` -> `1 -> 2 -> 3 -> 5`.

**Idea:** Brute force: count the length, then walk `length - n` steps (two passes). One pass: move `fast` n steps ahead, then move `fast` and `slow` together. When `fast` is on the last node, `slow` is just before the node to delete. A **dummy** node in front handles removing the head.

**Trace:**

```text
dummy -> 1 -> 2 -> 3 -> 4 -> 5 -> null       n = 2
fast moves 2 steps from dummy: fast = 2, slow = dummy
fast=2 slow=dummy
fast=3 slow=1
fast=4 slow=2
fast=5 slow=3      fast.next is null, stop
slow.next (4) is removed: 3 -> 5
result 1 -> 2 -> 3 -> 5
```

```java
public static ListNode removeNthFromEnd(ListNode head, int n) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode fast = dummy;
    ListNode slow = dummy;
    for (int i = 0; i < n; i++) {
        fast = fast.next;
    }
    while (fast.next != null) {
        fast = fast.next;
        slow = slow.next;
    }
    slow.next = slow.next.next;
    return dummy.next;
}
```

**Complexity:** time O(n), space O(1).

**Follow-up questions:**
- **Why the dummy node?** If `n` equals the length, the head must go. With a dummy, `slow` stops on the dummy and `dummy.next` becomes the new head.
- **`n` larger than the length?** The `fast` loop would hit `null`. Validate `n` or stop early.
- **Find the N-th node from the end (without removing)?** Same two pointers.

### Q26. How do you design a stack that returns the minimum in O(1)? [Intermediate]

**Problem:** Support `push`, `pop`, `top` and `getMin`, all in O(1). After `push(-2)`, `push(0)`, `push(-3)`: `getMin()` is `-3`. After `pop()`: `top()` is `0` and `getMin()` is `-2`.

**Idea:** Along with each value, store the minimum of the stack at the moment it was pushed. The top entry always holds the current minimum, and popping restores the previous minimum automatically.

```text
push -2   stack (value,min): (-2,-2)
push  0   stack: (-2,-2) (0,-2)
push -3   stack: (-2,-2) (0,-2) (-3,-3)    getMin = -3
pop       stack: (-2,-2) (0,-2)            getMin = -2
```

```java
public class MinStack {

    private final Deque<int[]> stack = new ArrayDeque<>();

    public void push(int value) {
        int min = value;
        if (!stack.isEmpty()) {
            min = Math.min(value, stack.peek()[1]);
        }
        stack.push(new int[] {value, min});
    }

    public void pop() {
        stack.pop();
    }

    public int top() {
        return stack.peek()[0];
    }

    public int getMin() {
        return stack.peek()[1];
    }
}
```

**Complexity:** every operation O(1) time. Space O(n).

**Follow-up questions:**
- **Use less space?** Keep a second stack that gets a value only when it is less than or equal to the current minimum. Pop from it when the popped value equals its top.
- **Why `<=` (not `<`) in that variant?** Duplicate minimums must each be remembered, or popping one would wrongly drop the minimum.
- **Min and max?** Store both in each entry.

### Q27. How do you implement a queue using stacks? [Intermediate]

**Problem:** Build a FIFO queue (first in, first out) with only stack operations.

**Idea:** Use two stacks. `in` receives pushes. `out` serves pops and peeks. When `out` is empty, move everything from `in` to `out`, which reverses the order, so the oldest element ends up on top. Each element is moved at most once, so `pop` is **amortized** O(1) (the average over many operations is O(1), even though one call may cost O(n)).

**Trace:**

```text
push(1) push(2) push(3)      in = [1,2,3] (top is 3)   out = []
peek()  out is empty -> move: pop 3, pop 2, pop 1 and push each onto out
                             in = []   out = [3,2,1] (top is 1)   returns 1
push(4)                      in = [4]  out = [3,2,1]
pop()                        returns 1 from out        out = [3,2]
pop()                        returns 2
pop()                        returns 3
pop()                        out empty -> move 4 -> returns 4
```

```java
public class QueueUsingStacks {

    private final Deque<Integer> in = new ArrayDeque<>();
    private final Deque<Integer> out = new ArrayDeque<>();

    public void push(int value) {
        in.push(value);
    }

    public int pop() {
        shiftIfNeeded();
        return out.pop();
    }

    public int peek() {
        shiftIfNeeded();
        return out.peek();
    }

    public boolean isEmpty() {
        return in.isEmpty() && out.isEmpty();
    }

    private void shiftIfNeeded() {
        if (out.isEmpty()) {
            while (!in.isEmpty()) {
                out.push(in.pop());
            }
        }
    }
}
```

**Complexity:** push O(1). pop and peek amortized O(1), worst case O(n). Space O(n).

**Follow-up questions:**
- **Why not move elements back to `in` after each pop?** That would make every operation O(n).
- **Stack using queues?** One queue: after pushing, rotate the queue `size - 1` times so the new element is at the front. Push is O(n), pop O(1).
- **Why not `LinkedList` as a queue?** It works. `ArrayDeque` is usually faster and has no per-node object.

### Q28. How do you find the next greater element for each item? (monotonic stack) [Intermediate]

**Problem:** For each element find the first larger element to its right, or `-1`. `{2, 1, 2, 4, 3}` -> `[4, 2, 4, -1, -1]`.

**Idea:** Brute force: for each element scan right, O(n^2). Better: a **monotonic stack** of indexes whose values are waiting for a greater element, kept in decreasing order. When the current value is larger than the value on top, the current value is the answer for that top. Pop and record it, repeat, then push the current index.

**Trace:**

```text
nums = {2, 1, 2, 4, 3}   result starts as [-1,-1,-1,-1,-1], stack holds indexes
i=0 (2): stack empty                               push 0        stack [0]
i=1 (1): nums[0]=2 < 1? no                         push 1        stack [0,1]
i=2 (2): nums[1]=1 < 2 yes -> result[1]=2, pop
         nums[0]=2 < 2? no                         push 2        stack [0,2]
i=3 (4): nums[2]=2 < 4 -> result[2]=4, pop
         nums[0]=2 < 4 -> result[0]=4, pop         push 3        stack [3]
i=4 (3): nums[3]=4 < 3? no                         push 4        stack [3,4]
left in stack keep -1
result [4, 2, 4, -1, -1]
```

```java
public static int[] nextGreater(int[] nums) {
    int n = nums.length;
    int[] result = new int[n];
    Arrays.fill(result, -1);
    Deque<Integer> stack = new ArrayDeque<>();
    for (int i = 0; i < n; i++) {
        while (!stack.isEmpty() && nums[stack.peek()] < nums[i]) {
            int index = stack.pop();
            result[index] = nums[i];
        }
        stack.push(i);
    }
    return result;
}
```

**Complexity:** time O(n) (each index is pushed and popped once), space O(n).

**Follow-up questions:**
- **Circular array?** Loop `i` from 0 to `2n - 1` and use `nums[i % n]`. Push indexes only for `i < n`.
- **Daily temperatures (days until a warmer day)?** Same stack, store `i - index` instead of the value.
- **Largest rectangle in a histogram?** Monotonic increasing stack. This is a known hard variant.
- **Why store indexes and not values?** You need positions to write results and to compute distances.

---

## 3.3 Advanced

### Q29. How do you build an LRU cache with LinkedHashMap? [Advanced]

**Problem:** A cache with a fixed capacity. `get(key)` returns the value. `put(key, value)` inserts. When the capacity is exceeded, evict the **least recently used** entry (the one not read or written for the longest time).

**Idea:** `LinkedHashMap` keeps a doubly linked list through its entries. With `accessOrder = true` every `get` and `put` moves the entry to the end, so the eldest entry is the least recently used. Override `removeEldestEntry` to evict when the size passes the capacity.

```java
public class LruCache<K, V> extends LinkedHashMap<K, V> {

    private final int capacity;

    public LruCache(int capacity) {
        super(capacity, 0.75f, true);
        this.capacity = capacity;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > capacity;
    }
}
```

**Trace with capacity 2:**

```text
put(1,"A")   order (old -> new): 1
put(2,"B")   order: 1, 2
get(1)       returns "A"   order: 2, 1        (1 became most recent)
put(3,"C")   order: 2, 1, 3 -> size 3 > 2, eldest 2 is removed -> 1, 3
get(2)       returns null
```

**Complexity:** `get` and `put` O(1) average. Space O(capacity).

**Follow-up questions:**
- **Is it thread safe?** No. Wrap with `Collections.synchronizedMap`, or use a cache library (Caffeine) in real projects.
- **Why does `get` change the structure?** In access order mode a read relinks the entry. That is also why concurrent reads are unsafe.
- **Production alternative?** Caffeine or Guava Cache, with size limits, TTL and statistics. See the LRU question in `D:\prep\java\INTERVIEW-QUESTIONS.md` (Q33).
- **LRU vs LFU?** LRU evicts the oldest-used entry. LFU (least frequently used) evicts the entry with the fewest accesses.

### Q30. How do you build an LRU cache manually with a doubly linked list and a HashMap? [Advanced]

**Problem:** Same as Q29, but without `LinkedHashMap`. `get` and `put` must be O(1).

**Idea:** A `HashMap<key, node>` finds a node in O(1). A **doubly linked list** (each node knows `prev` and `next`) lets you remove a node and move it to the front in O(1), because you do not need to search for its neighbors. Most recently used is near the head, least recently used is near the tail. Two **sentinel** (dummy) nodes, `head` and `tail`, remove null checks.

```text
head <-> [most recent] <-> ... <-> [least recent] <-> tail
          ^ add and move here                          ^ evict from here (tail.prev)
```

**Trace with capacity 2:**

```text
put(1,1)   head <-> 1 <-> tail
put(2,2)   head <-> 2 <-> 1 <-> tail
get(1)     returns 1, move to front:  head <-> 1 <-> 2 <-> tail
put(3,3)   add 3 at front: head <-> 3 <-> 1 <-> 2 <-> tail   size 3 > 2
           remove tail.prev (2) and its map entry:  head <-> 3 <-> 1 <-> tail
get(2)     returns -1
```

```java
public class LruCache {

    private static class Node {

        int key;
        int value;
        Node prev;
        Node next;

        Node(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    private final int capacity;
    private final Map<Integer, Node> map = new HashMap<>();
    private final Node head = new Node(0, 0);
    private final Node tail = new Node(0, 0);

    public LruCache(int capacity) {
        this.capacity = capacity;
        head.next = tail;
        tail.prev = head;
    }

    public int get(int key) {
        Node node = map.get(key);
        if (node == null) {
            return -1;
        }
        remove(node);
        addToFront(node);
        return node.value;
    }

    public void put(int key, int value) {
        Node node = map.get(key);
        if (node != null) {
            node.value = value;
            remove(node);
            addToFront(node);
            return;
        }
        Node created = new Node(key, value);
        map.put(key, created);
        addToFront(created);
        if (map.size() > capacity) {
            Node eldest = tail.prev;
            remove(eldest);
            map.remove(eldest.key);
        }
    }

    private void remove(Node node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    private void addToFront(Node node) {
        node.next = head.next;
        node.prev = head;
        head.next.prev = node;
        head.next = node;
    }
}
```

**Complexity:** `get` and `put` O(1). Space O(capacity).

**Follow-up questions:**
- **Why must the node store its key?** When you evict `tail.prev`, you need its key to delete it from the map.
- **Why a doubly linked list, not a singly linked one?** To unlink a node in O(1) you need its predecessor. A singly linked list would need a scan.
- **Why not an `ArrayList` for the order?** Removing from the middle is O(n).
- **Make it thread safe?** Lock the whole cache (`synchronized` or `ReentrantLock`). Even `get` writes, so a read-write lock does not help much.
- **Add TTL (expiry)?** Store an expiry time in each node and check it in `get`. Evict expired entries lazily, or with a background cleaner.

---

# 4. Trees

A **binary tree** is a structure where each node has at most two children, called left and right. The **root** is the top node. A **leaf** has no children. The **height** of a tree is the number of levels (this file counts nodes, so a single node has height 1). A **binary search tree (BST)** keeps every value in the left subtree smaller than the node and every value in the right subtree larger.

Most tree questions below use this BST and this node class.

```text
        4
       / \
      2   6
     / \   \
    1   3   7
```

```java
public class TreeNode {

    int val;
    TreeNode left;
    TreeNode right;

    TreeNode(int val) {
        this.val = val;
    }
}
```

## 4.1 Basic

### Q31. What are the inorder, preorder and postorder traversals? [Basic]

**Problem:** Visit every node of the tree and list the values. The three orders differ in when the node itself is visited relative to its subtrees.

| Order | Visit order | Output for the tree above | Common use |
|---|---|---|---|
| Preorder | node, left, right | `4 2 1 3 6 7` | Copy a tree, serialize |
| Inorder | left, node, right | `1 2 3 4 6 7` | On a BST gives sorted order |
| Postorder | left, right, node | `1 3 2 7 6 4` | Delete a tree, compute sizes from children |

**Idea:** Each traversal is a recursive method. The base case is a `null` node. The only difference is where the `add` line sits.

**Trace of inorder (recursive calls on the tree above):**

```text
inorder(4)
  inorder(2)
    inorder(1)  -> left null, add 1, right null
    add 2
    inorder(3)  -> add 3
  add 4
  inorder(6)
    left null
    add 6
    inorder(7)  -> add 7
result 1 2 3 4 6 7
```

```java
public static void inorder(TreeNode node, List<Integer> result) {
    if (node == null) {
        return;
    }
    inorder(node.left, result);
    result.add(node.val);
    inorder(node.right, result);
}

public static void preorder(TreeNode node, List<Integer> result) {
    if (node == null) {
        return;
    }
    result.add(node.val);
    preorder(node.left, result);
    preorder(node.right, result);
}

public static void postorder(TreeNode node, List<Integer> result) {
    if (node == null) {
        return;
    }
    postorder(node.left, result);
    postorder(node.right, result);
    result.add(node.val);
}
```

**Complexity:** time O(n). Space O(h) for the call stack, where h is the tree height (O(log n) if balanced, O(n) if the tree is a chain).

**Follow-up questions:**
- **Can a tree be rebuilt from one traversal?** Not from inorder alone. Preorder + inorder (or postorder + inorder) identify a binary tree uniquely when values are distinct.
- **Which traversal gives sorted output for a BST?** Inorder.
- **What are the risks of recursion?** A very deep (skewed) tree can cause `StackOverflowError`. Use the iterative version (Q35).
- **Is level order one of these?** No. It is breadth-first (Q34). The three above are depth-first.

### Q32. How do you find the maximum depth and check if a tree is balanced? [Basic]

**Problem:** Depth of the sample tree is `3` (longest root-to-leaf path has 3 nodes). A tree is **height-balanced** if for every node the heights of its left and right subtrees differ by at most 1.

**Idea:** Depth of a node = `1 + max(depth(left), depth(right))`, with depth of `null` = 0. For balance, a naive solution calls `height` on every node, O(n^2) in the worst case. Better: compute the height and the balance in one pass. Return `-1` as a signal for "unbalanced below", and stop early.

```text
Balanced (depth 3)              Not balanced
        4                         1
       / \                         \
      2   6                         2
     / \   \                         \
    1   3   7                         3
```

For the right tree, at node 1: left height 0, right height 2, difference 2 > 1, so it is unbalanced.

```java
public static int maxDepth(TreeNode node) {
    if (node == null) {
        return 0;
    }
    int left = maxDepth(node.left);
    int right = maxDepth(node.right);
    return 1 + Math.max(left, right);
}

public static boolean isBalanced(TreeNode root) {
    return checkHeight(root) != -1;
}

private static int checkHeight(TreeNode node) {
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
```

**Complexity:** both O(n) time, O(h) space.

**Follow-up questions:**
- **Minimum depth?** Careful: a node with only one child is not a leaf. If one child is `null`, use the other child's depth. Or use BFS and return at the first leaf.
- **Diameter of a tree (longest path between any two nodes)?** At each node, `left height + right height`. Track the maximum while computing heights.
- **Iterative depth?** Level order BFS, count levels (Q34).
- **Why do balanced trees matter?** Search, insert and delete stay O(log n). Java's `TreeMap` uses a red-black tree to stay balanced.

### Q33. How do you invert a binary tree and check if it is symmetric? [Basic]

**Problem:** (a) Invert (mirror) the tree: swap the left and right child of every node. (b) A tree is symmetric if its left half is the mirror of its right half.

**Idea:** (a) Recursively invert both subtrees and swap them. (b) Compare two subtrees `a` and `b` at the same time. They mirror each other if the values are equal, `a.left` mirrors `b.right`, and `a.right` mirrors `b.left`.

```text
Before             After (inverted)
      4                 4
     / \               / \
    2   7             7   2
   / \ / \           / \ / \
  1  3 6  9         9  6 3  1

Symmetric (true)        Not symmetric (false)
      1                       1
     / \                     / \
    2   2                   2   2
   / \ / \                   \   \
  3  4 4  3                   3   3
```

```java
public static TreeNode invert(TreeNode node) {
    if (node == null) {
        return null;
    }
    TreeNode left = invert(node.left);
    TreeNode right = invert(node.right);
    node.left = right;
    node.right = left;
    return node;
}

public static boolean isSymmetric(TreeNode root) {
    if (root == null) {
        return true;
    }
    return isMirror(root.left, root.right);
}

private static boolean isMirror(TreeNode a, TreeNode b) {
    if (a == null && b == null) {
        return true;
    }
    if (a == null || b == null) {
        return false;
    }
    return a.val == b.val
        && isMirror(a.left, b.right)
        && isMirror(a.right, b.left);
}
```

**Complexity:** time O(n), space O(h).

**Follow-up questions:**
- **Are two trees identical?** Same recursion as `isMirror`, but compare `a.left` with `b.left` and `a.right` with `b.right`.
- **Iterative invert?** BFS with a queue: for each node swap children and enqueue them.
- **Is one tree a subtree of another?** For each node of the big tree, run the "identical" check. O(n * m).

---

## 4.2 Intermediate

### Q34. How do you do a level order traversal (BFS)? [Intermediate]

**Problem:** Return the values level by level. For the sample tree: `[[4], [2, 6], [1, 3, 7]]`.

**Idea:** **Breadth-first search (BFS)** visits nodes closest to the root first. Use a queue. Before processing a level, record the queue size. That many nodes belong to the current level. Take them out and add their children to the queue for the next level.

**Trace:**

```text
queue [4]                 level size 1 -> take 4, add 2, 6      level = [4]
queue [2, 6]              level size 2 -> take 2 (add 1, 3), take 6 (add 7)   level = [2, 6]
queue [1, 3, 7]           level size 3 -> take 1, 3, 7          level = [1, 3, 7]
queue []                  stop
result [[4], [2, 6], [1, 3, 7]]
```

```java
public static List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) {
        return result;
    }
    Queue<TreeNode> queue = new ArrayDeque<>();
    queue.offer(root);
    while (!queue.isEmpty()) {
        int levelSize = queue.size();
        List<Integer> level = new ArrayList<>();
        for (int i = 0; i < levelSize; i++) {
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
```

**Complexity:** time O(n), space O(w) where w is the widest level (up to n/2).

**Follow-up questions:**
- **Right side view?** The last node of each level.
- **Zigzag order?** Reverse every second level (or use a `Deque` and add at the front on those levels).
- **Why capture `queue.size()` first?** The queue grows while you process the level, so the live size would mix levels.
- **Minimum depth?** BFS and stop at the first leaf.

### Q35. How do you do inorder, preorder and postorder iteratively? [Intermediate]

**Problem:** Produce the same traversals as Q31 without recursion, using an explicit stack. This avoids `StackOverflowError` on very deep trees.

**Idea:**
- **Inorder:** go as far left as possible pushing nodes. Pop one, visit it, then go to its right child.
- **Preorder:** push the root. Pop, visit, push right child then left child (left is processed first).
- **Postorder:** do a modified preorder (node, right, left), then reverse the result. Reverse of `node, right, left` is `left, right, node`.

**Trace of iterative inorder on the sample tree:**

```text
curr=4: push 4, curr=2: push 2, curr=1: push 1, curr=null     stack (top first): 1 2 4
pop 1 -> visit 1, curr = 1.right = null
pop 2 -> visit 2, curr = 3
curr=3: push 3, curr=null (3.left)                            stack: 3 4
pop 3 -> visit 3, curr = null
pop 4 -> visit 4, curr = 6
curr=6: push 6, curr=null                                     stack: 6
pop 6 -> visit 6, curr = 7
curr=7: push 7, curr=null
pop 7 -> visit 7, curr = null, stack empty -> done
output 1 2 3 4 6 7
```

```java
public static List<Integer> inorderIterative(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    Deque<TreeNode> stack = new ArrayDeque<>();
    TreeNode curr = root;
    while (curr != null || !stack.isEmpty()) {
        while (curr != null) {
            stack.push(curr);
            curr = curr.left;
        }
        curr = stack.pop();
        result.add(curr.val);
        curr = curr.right;
    }
    return result;
}

public static List<Integer> preorderIterative(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    if (root == null) {
        return result;
    }
    Deque<TreeNode> stack = new ArrayDeque<>();
    stack.push(root);
    while (!stack.isEmpty()) {
        TreeNode node = stack.pop();
        result.add(node.val);
        if (node.right != null) {
            stack.push(node.right);
        }
        if (node.left != null) {
            stack.push(node.left);
        }
    }
    return result;
}

public static List<Integer> postorderIterative(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    if (root == null) {
        return result;
    }
    Deque<TreeNode> stack = new ArrayDeque<>();
    stack.push(root);
    while (!stack.isEmpty()) {
        TreeNode node = stack.pop();
        result.add(node.val);
        if (node.left != null) {
            stack.push(node.left);
        }
        if (node.right != null) {
            stack.push(node.right);
        }
    }
    Collections.reverse(result);
    return result;
}
```

```java
preorderIterative(root);
// [4, 2, 1, 3, 6, 7]
postorderIterative(root);
// [1, 3, 2, 7, 6, 4]
```

The postorder loop collects `4 6 7 2 3 1` (node, right, left), and the reverse is `1 3 2 7 6 4`.

**Complexity:** time O(n), space O(h) for the stack (O(n) for the postorder list).

**Follow-up questions:**
- **Traversal in O(1) extra space?** Morris traversal temporarily links a node's inorder predecessor back to it. It is a known trick, rarely required.
- **Why push right before left in preorder?** A stack is last in, first out, so the left child pops first.
- **Where is this used?** Iterative inorder is the base of "kth smallest in a BST" (Q38) and the BST iterator.

### Q36. How do you check whether a root-to-leaf path has a given sum? [Intermediate]

**Problem:** Does any root-to-leaf path add up to the target? In the tree below, target `22` -> `true`, because `5 + 4 + 11 + 2 = 22`.

```text
        5
       / \
      4   8
     /   / \
    11  13  4
   /  \      \
  7    2      1
```

**Idea:** Brute force: list all paths and sum each. Better: subtract the node value from the target while going down. At a **leaf**, the path works if the remaining target equals the leaf value.

**Trace of the successful path:**

```text
node 5   remaining 22 -> 17
node 4   remaining 17 -> 13
node 11  remaining 13 -> 2
node 2   leaf, remaining 2 == 2 -> true
```

```java
public static boolean hasPathSum(TreeNode node, int target) {
    if (node == null) {
        return false;
    }
    if (node.left == null && node.right == null) {
        return node.val == target;
    }
    int remaining = target - node.val;
    return hasPathSum(node.left, remaining)
        || hasPathSum(node.right, remaining);
}
```

**Complexity:** time O(n), space O(h).

**Follow-up questions:**
- **Return all paths with that sum?** Backtracking: keep a `List<Integer> path`, add the node on entry, remove it on exit, and copy the path when a leaf matches.
- **Why must the check be at a leaf?** The path must end at a leaf. Without the leaf check, a prefix like `5 + 4 + 11 = 20` could match another target wrongly.
- **Path can start and end anywhere going downward (target sum III)?** Use the prefix sum map from Q18 along the root-to-node path. O(n).
- **Negative values?** The code is still correct. It never prunes on "remaining < 0".

### Q37. How do you validate a binary search tree? [Intermediate]

**Problem:** Check that every node is greater than all values in its left subtree and less than all in its right subtree.

```text
Valid BST            Invalid BST
    2                    5
   / \                  / \
  1   3                3   7
                          / \
                         4   9
```

In the invalid tree, 4 is less than its parent 7 (looks fine locally), but it is in the right subtree of 5, so it must be greater than 5.

**Idea:** A common mistake is to compare only a node with its direct children. Instead pass down an allowed range `(low, high)`. Going left, the node's value becomes the new `high`. Going right, it becomes the new `low`. Every node must be strictly inside its range.

**Trace on the invalid tree:**

```text
5   range (-inf, +inf)   ok
3   range (-inf, 5)      ok
7   range (5, +inf)      ok
4   range (5, 7)         4 <= 5 -> false
```

```java
public static boolean isValidBst(TreeNode root) {
    return validate(root, null, null);
}

private static boolean validate(TreeNode node, Integer low, Integer high) {
    if (node == null) {
        return true;
    }
    if (low != null && node.val <= low) {
        return false;
    }
    if (high != null && node.val >= high) {
        return false;
    }
    return validate(node.left, low, node.val)
        && validate(node.right, node.val, high);
}
```

**Complexity:** time O(n), space O(h).

**Follow-up questions:**
- **Alternative approach?** Inorder traversal must give a strictly increasing sequence. Keep the previous value and compare.
- **Why `Integer` (boxed) bounds?** Values can be `Integer.MIN_VALUE` or `MAX_VALUE`, so no `int` sentinel is safe. `null` means "no bound".
- **Are duplicates allowed?** The code treats equal values as invalid. Some definitions allow them on one side. Ask.

### Q38. How do you find the K-th smallest element in a BST? [Intermediate]

**Problem:** For the sample tree, `k = 3` -> `3`. The inorder sequence is `1 2 3 4 6 7`, so the third value is `3`.

**Idea:** Inorder traversal of a BST visits values in sorted order. Run the iterative inorder (Q35) and stop at the K-th visit. This is O(h + k), without visiting the whole tree.

```java
public static int kthSmallest(TreeNode root, int k) {
    Deque<TreeNode> stack = new ArrayDeque<>();
    TreeNode curr = root;
    while (curr != null || !stack.isEmpty()) {
        while (curr != null) {
            stack.push(curr);
            curr = curr.left;
        }
        curr = stack.pop();
        k--;
        if (k == 0) {
            return curr.val;
        }
        curr = curr.right;
    }
    throw new IllegalArgumentException("k is larger than the tree size");
}
```

**Trace (k = 3):** pop 1 (k becomes 2), pop 2 (k becomes 1), pop 3 (k becomes 0) -> return 3.

**Complexity:** time O(h + k), space O(h).

**Follow-up questions:**
- **The tree is modified often and K-th smallest is queried often?** Store in each node the size of its left subtree. Then each query walks down one path in O(h). Or use an order-statistic tree.
- **K-th largest?** Reverse inorder (right, node, left).
- **Why is this better than collecting everything into a list?** It stops early and uses O(h) space.

---

## 4.3 Advanced

### Q39. How do you find the lowest common ancestor (LCA) of two nodes? [Advanced]

**Problem:** The **lowest common ancestor** of nodes `p` and `q` is the deepest node that has both as descendants (a node counts as its own descendant). In the sample tree: LCA(1, 3) = 2, LCA(1, 7) = 4, LCA(2, 3) = 2.

**Idea:** Recursive search in a general binary tree. If the current node is `null`, `p` or `q`, return it. Otherwise search both subtrees. If both sides return a node, then `p` and `q` are on different sides, so the current node is the LCA. If only one side returns a node, pass that result up.

**Trace for LCA(1, 3):**

```text
lca(4)
  left  = lca(2)
            left  = lca(1) -> node is p -> returns 1
            right = lca(3) -> node is q -> returns 3
            both non-null -> returns 2
  right = lca(6) -> searches 7, finds nothing -> null
  right is null -> returns left = 2
answer 2
```

```java
public static TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || root == p || root == q) {
        return root;
    }
    TreeNode left = lowestCommonAncestor(root.left, p, q);
    TreeNode right = lowestCommonAncestor(root.right, p, q);
    if (left != null && right != null) {
        return root;
    }
    if (left != null) {
        return left;
    }
    return right;
}

public static TreeNode lcaBst(TreeNode root, TreeNode p, TreeNode q) {
    TreeNode node = root;
    while (node != null) {
        if (p.val < node.val && q.val < node.val) {
            node = node.left;
        } else if (p.val > node.val && q.val > node.val) {
            node = node.right;
        } else {
            return node;
        }
    }
    return null;
}
```

**Complexity:** general tree: time O(n), space O(h). BST version: time O(h), space O(1).

**Follow-up questions:**
- **What if `p` or `q` might not be in the tree?** The code above assumes both exist. To handle a missing node, count how many of them were found.
- **Nodes have parent pointers?** Walk up from both nodes (like finding the intersection of two linked lists).
- **Many LCA queries on a fixed tree?** Preprocess with binary lifting or Euler tour + sparse table for O(log n) or O(1) per query.
- **Why compare with `==`?** Nodes are compared by identity, not by value, in the general tree version.

### Q40. How do you serialize and deserialize a binary tree? [Advanced]

**Problem:** Convert a tree to a string, and rebuild the same tree from the string. Used for storing or sending a tree.

**Idea:** Preorder traversal, writing a marker (`#`) for each `null`. The markers make the shape unambiguous, so one traversal is enough to rebuild. Deserialize by reading tokens in the same order: a token is the node value, then recursively build left, then right.

```text
    1
   / \
  2   3
     / \
    4   5

serialized preorder with null markers:
"1,2,#,#,3,4,#,#,5,#,#"
 1  2 L R  3  4 L R  5 L R     (L and R are the null children)
```

```java
public static String serialize(TreeNode root) {
    StringBuilder sb = new StringBuilder();
    write(root, sb);
    return sb.toString();
}

private static void write(TreeNode node, StringBuilder sb) {
    if (node == null) {
        sb.append("#,");
        return;
    }
    sb.append(node.val);
    sb.append(',');
    write(node.left, sb);
    write(node.right, sb);
}

public static TreeNode deserialize(String data) {
    Queue<String> tokens = new ArrayDeque<>(Arrays.asList(data.split(",")));
    return read(tokens);
}

private static TreeNode read(Queue<String> tokens) {
    String token = tokens.poll();
    if (token.equals("#")) {
        return null;
    }
    TreeNode node = new TreeNode(Integer.parseInt(token));
    node.left = read(tokens);
    node.right = read(tokens);
    return node;
}
```

The serialized form from this code ends with a trailing comma (`"1,2,#,#,3,4,#,#,5,#,#,"`). `split(",")` drops the trailing empty string, so it round-trips.

**Complexity:** time O(n), space O(n).

**Follow-up questions:**
- **Why not store only inorder?** Different tree shapes can have the same inorder. The null markers or a second traversal are needed.
- **Compact form for a BST?** Preorder values only. Insert them one by one, or rebuild with min/max bounds. No null markers needed.
- **Level order format?** LeetCode shows trees that way (`[1,2,3,null,null,4,5]`). Needs a queue.
- **Values that contain commas?** Use a length-prefixed format or JSON.

---

# 5. Graphs and Grids

A **graph** is a set of **nodes** (vertices) connected by **edges**. In an **undirected** graph an edge works both ways. In a **directed** graph it has a direction. A graph is usually stored as an **adjacency list**: for each node, the list of its neighbors. A **grid** (2D array) is a graph where each cell is a node and neighboring cells are connected.

## 5.1 Basic

### Q41. How do BFS and DFS work on a graph? [Basic]

**Problem:** Visit every node reachable from node 0. Use this undirected graph.

```text
    0 --- 1
    |     |
    2 --- 3 --- 4

adjacency list:
0: [1, 2]
1: [0, 3]
2: [0, 3]
3: [1, 2, 4]
4: [3]
```

**Idea:**
- **BFS (breadth-first search):** visit all neighbors first, then their neighbors. Uses a **queue**. Finds the fewest-edges path.
- **DFS (depth-first search):** follow one path as deep as possible, then backtrack. Uses recursion or a **stack**.
- Both need a `visited` set, otherwise a cycle makes them run forever.

**BFS trace from 0 (mark visited when adding to the queue):**

```text
queue [0]                 visit 0, add 1, 2         visited {0,1,2}
queue [1, 2]              visit 1, add 3            (0 already visited)
queue [2, 3]              visit 2                   (0 and 3 already visited)
queue [3]                 visit 3, add 4
queue [4]                 visit 4
BFS order: 0 1 2 3 4
```

**DFS trace from 0:**

```text
dfs(0) -> dfs(1) -> dfs(3) -> neighbor 1 visited, neighbor 2: dfs(2) -> neighbors 0, 3 visited, back
                           -> neighbor 4: dfs(4)
DFS order: 0 1 3 2 4
```

```java
public static List<Integer> bfs(List<List<Integer>> graph, int start) {
    List<Integer> order = new ArrayList<>();
    boolean[] visited = new boolean[graph.size()];
    Queue<Integer> queue = new ArrayDeque<>();
    queue.offer(start);
    visited[start] = true;
    while (!queue.isEmpty()) {
        int node = queue.poll();
        order.add(node);
        for (int next : graph.get(node)) {
            if (!visited[next]) {
                visited[next] = true;
                queue.offer(next);
            }
        }
    }
    return order;
}

public static void dfs(List<List<Integer>> graph, int node, boolean[] visited, List<Integer> order) {
    visited[node] = true;
    order.add(node);
    for (int next : graph.get(node)) {
        if (!visited[next]) {
            dfs(graph, next, visited, order);
        }
    }
}
```

```java
bfs(graph, 0);
// [0, 1, 2, 3, 4]
```

**Complexity:** time O(V + E) (V nodes, E edges), space O(V).

**Follow-up questions:**
- **Why mark visited when enqueuing in BFS?** Otherwise the same node can be added several times before it is processed.
- **When to use which?** BFS for shortest path in an unweighted graph and level by level work. DFS for exploring all paths, connected components, cycle detection and topological sort.
- **Disconnected graph?** Loop over all nodes and start a search from each unvisited one. The number of starts is the number of components.
- **Adjacency list vs matrix?** The list uses O(V + E) space and is best for sparse graphs. A matrix uses O(V^2) and gives O(1) edge lookup.

### Q42. How do you count the number of islands in a grid? [Basic]

**Problem:** In a grid of `'1'` (land) and `'0'` (water), count the groups of connected land cells (connected up, down, left, right).

```text
1 1 0 0 0
1 1 0 0 0
0 0 1 0 0
0 0 0 1 1

island 1: the four cells at the top left
island 2: the single cell at row 2, column 2
island 3: the two cells at row 3, columns 3 and 4      answer 3
```

**Idea:** Scan every cell. When you find land, count one island and run a DFS that turns every connected land cell into water (`'0'`) so it is not counted again. This "sinking" modifies the input. Use a `visited` array instead if the grid must stay unchanged.

**Trace:**

```text
(0,0) is '1' -> count=1, sink (0,0),(0,1),(1,0),(1,1)
cells (0,1) (1,0) (1,1) are now '0', skipped
(2,2) is '1' -> count=2, sink (2,2)
(3,3) is '1' -> count=3, sink (3,3),(3,4)
result 3
```

```java
public static int numIslands(char[][] grid) {
    int count = 0;
    for (int row = 0; row < grid.length; row++) {
        for (int col = 0; col < grid[0].length; col++) {
            if (grid[row][col] == '1') {
                count++;
                sink(grid, row, col);
            }
        }
    }
    return count;
}

private static void sink(char[][] grid, int row, int col) {
    boolean outside = row < 0 || col < 0 || row >= grid.length || col >= grid[0].length;
    if (outside || grid[row][col] != '1') {
        return;
    }
    grid[row][col] = '0';
    sink(grid, row + 1, col);
    sink(grid, row - 1, col);
    sink(grid, row, col + 1);
    sink(grid, row, col - 1);
}
```

**Complexity:** time O(rows * cols), space O(rows * cols) for the recursion in the worst case (a grid full of land).

**Follow-up questions:**
- **Huge grid, stack overflow risk?** Use BFS with a queue, or an explicit stack.
- **Largest island area?** Make the DFS return `1 + sum of the four calls` and track the maximum.
- **Count islands when land cells arrive one by one?** Union-Find (disjoint set) keeps the count in near O(1) per update.
- **Diagonal connections?** Add the four diagonal directions to the neighbor list.

---

## 5.2 Intermediate

### Q43. How do you find the shortest path in an unweighted graph? [Intermediate]

**Problem:** Fewest edges from node 0 to node 4 in the graph of Q41. Answer: `3` (`0 -> 1 -> 3 -> 4`).

**Idea:** BFS explores nodes in order of their distance. The first time BFS reaches a node is by a shortest path. Store `dist[node]` (initialized to `-1` meaning unvisited) and `parent[node]` so you can rebuild the path.

**Trace:**

```text
dist: start 0:0
from 0: 1 -> 1, 2 -> 1       parent[1]=0, parent[2]=0
from 1: 3 -> 2               parent[3]=1
from 2: 3 already set
from 3: 4 -> 3               parent[4]=3
path for 4: 4 <- 3 <- 1 <- 0 , reversed: 0 1 3 4
```

```java
public static List<Integer> shortestPath(List<List<Integer>> graph, int start, int target) {
    int n = graph.size();
    int[] dist = new int[n];
    int[] parent = new int[n];
    Arrays.fill(dist, -1);
    Arrays.fill(parent, -1);
    Queue<Integer> queue = new ArrayDeque<>();
    queue.offer(start);
    dist[start] = 0;
    while (!queue.isEmpty()) {
        int node = queue.poll();
        for (int next : graph.get(node)) {
            if (dist[next] == -1) {
                dist[next] = dist[node] + 1;
                parent[next] = node;
                queue.offer(next);
            }
        }
    }
    List<Integer> path = new ArrayList<>();
    if (dist[target] == -1) {
        return path;
    }
    for (int node = target; node != -1; node = parent[node]) {
        path.add(node);
    }
    Collections.reverse(path);
    return path;
}
```

```java
shortestPath(graph, 0, 4);
// [0, 1, 3, 4]
```

**Complexity:** time O(V + E), space O(V).

**Follow-up questions:**
- **Shortest path in a grid (maze)?** Same BFS, where the neighbors are the four adjacent cells and walls are skipped.
- **Edges have different weights?** BFS is no longer correct. Use Dijkstra (Q47). If weights are only 0 or 1, use 0-1 BFS with a deque.
- **Why not DFS?** DFS reaches a node by some path, not necessarily the shortest one.
- **Word ladder?** Words are nodes, and words that differ by one letter are connected. BFS gives the shortest transformation.

### Q44. How do you detect a cycle in an undirected graph? [Intermediate]

**Problem:** Does the graph contain a cycle (a path that returns to its starting node)?

```text
Has a cycle                 No cycle (tree)
  0 --- 1                     0 --- 1
   \   /                       \
     2 --- 3                     2 --- 3
edges 0-1, 1-2, 2-0, 2-3     edges 0-1, 0-2, 2-3
```

**Idea:** DFS and remember the node you came from (`parent`). Walking from a node, if you meet a neighbor that is already visited and it is **not** the parent, you found a second route to it, so there is a cycle. The parent is excluded because every undirected edge points back to the node you just left.

**Trace on the left graph (adjacency 0:[1,2], 1:[0,2], 2:[1,0,3], 3:[2]):**

```text
dfs(0, parent -1): visit 0, neighbor 1 not visited
  dfs(1, parent 0): visit 1, neighbor 0 is the parent -> skip
                    neighbor 2 not visited
    dfs(2, parent 1): visit 2, neighbor 1 is the parent -> skip
                      neighbor 0 is visited and not the parent -> cycle found, return true
```

```java
public static boolean hasCycle(List<List<Integer>> graph) {
    boolean[] visited = new boolean[graph.size()];
    for (int node = 0; node < graph.size(); node++) {
        if (!visited[node] && dfs(graph, node, -1, visited)) {
            return true;
        }
    }
    return false;
}

private static boolean dfs(List<List<Integer>> graph, int node, int parent, boolean[] visited) {
    visited[node] = true;
    for (int next : graph.get(node)) {
        if (!visited[next]) {
            if (dfs(graph, next, node, visited)) {
                return true;
            }
        } else if (next != parent) {
            return true;
        }
    }
    return false;
}
```

**Complexity:** time O(V + E), space O(V).

**Follow-up questions:**
- **Why the outer loop?** The graph may be disconnected, and a cycle can be in any component.
- **Directed graph?** "Visited and not the parent" is wrong there. Use the "currently on the recursion path" state, or topological sort (Q46).
- **Parallel edges (two edges between the same nodes)?** That is a cycle of length 2, but the parent check would miss it. Track edge ids instead of parent nodes.
- **Union-Find alternative?** Process edges one by one. If both ends already belong to the same set, the edge closes a cycle.
- **Is a connected graph a tree?** It is a tree if it has exactly `V - 1` edges and no cycle.

### Q45. How do you clone a graph? [Intermediate]

**Problem:** Make a deep copy of a connected undirected graph. Each node has a value and a list of neighbors.

```text
1 --- 2
|     |
4 --- 3
```

**Idea:** Copy nodes with DFS. Keep a map from original node to its copy. When the DFS meets a node already in the map, reuse the copy instead of creating another, which also stops infinite loops on cycles.

**Trace starting at node 1:**

```text
clone(1): create 1', map {1:1'}
  neighbor 2: clone(2): create 2', map {1:1',2:2'}
    neighbor 1: in map -> use 1'
    neighbor 3: clone(3): create 3'
      neighbor 2: in map -> use 2'
      neighbor 4: clone(4): create 4'
        neighbor 1: in map -> use 1'
        neighbor 3: in map -> use 3'
  neighbor 4: in map -> use 4'
```

```java
public class Node {

    int val;
    List<Node> neighbors = new ArrayList<>();

    Node(int val) {
        this.val = val;
    }
}

public static Node cloneGraph(Node node) {
    if (node == null) {
        return null;
    }
    return clone(node, new HashMap<>());
}

private static Node clone(Node node, Map<Node, Node> copies) {
    if (copies.containsKey(node)) {
        return copies.get(node);
    }
    Node copy = new Node(node.val);
    copies.put(node, copy);
    for (Node neighbor : node.neighbors) {
        copy.neighbors.add(clone(neighbor, copies));
    }
    return copy;
}
```

**Complexity:** time O(V + E), space O(V).

**Follow-up questions:**
- **Why put the copy in the map before visiting neighbors?** Otherwise a cycle would recurse forever.
- **BFS version?** Same map. Queue the original nodes and create copies as you discover them.
- **Same idea elsewhere?** Copying a linked list with random pointers uses the same original-to-copy map.

---

## 5.3 Advanced

### Q46. How do you solve Course Schedule? (cycle detection in a directed graph, topological sort) [Advanced]

**Problem:** There are `n` courses. A pair `[a, b]` means course `b` must be taken before `a`. Can all courses be finished? Return an order if possible. Example: `n = 4`, `[[1,0],[2,0],[3,1],[3,2]]` -> yes, order `0, 1, 2, 3`. For `[[1,0],[0,1]]` the answer is no, because the courses wait on each other (a cycle).

**Idea:** A **topological order** lists nodes of a directed graph so every edge goes from an earlier node to a later one. It exists only if the graph has no cycle. **Kahn's algorithm:** count the **in-degree** (number of incoming edges) of each node. Start with the nodes of in-degree 0. Remove one, lower the in-degree of its neighbors, and add any neighbor that reaches 0. If all nodes get processed there is no cycle.

```text
0 --> 1 --> 3
|           ^
+---> 2 ----+

in-degree: 0:0  1:1  2:1  3:2
```

**Trace:**

```text
queue [0]
take 0 -> order [0]        1: 1->0 add, 2: 1->0 add        queue [1, 2]
take 1 -> order [0,1]      3: 2->1                         queue [2]
take 2 -> order [0,1,2]    3: 1->0 add                     queue [3]
take 3 -> order [0,1,2,3]  all 4 processed -> possible

Cycle case [[1,0],[0,1]]: in-degrees 0:1, 1:1, queue is empty at the start,
processed 0 < 2 -> not possible
```

```java
public static int[] findOrder(int numCourses, int[][] prerequisites) {
    List<List<Integer>> graph = new ArrayList<>();
    for (int i = 0; i < numCourses; i++) {
        graph.add(new ArrayList<>());
    }
    int[] inDegree = new int[numCourses];
    for (int[] pair : prerequisites) {
        int course = pair[0];
        int before = pair[1];
        graph.get(before).add(course);
        inDegree[course]++;
    }
    Queue<Integer> queue = new ArrayDeque<>();
    for (int i = 0; i < numCourses; i++) {
        if (inDegree[i] == 0) {
            queue.offer(i);
        }
    }
    int[] order = new int[numCourses];
    int count = 0;
    while (!queue.isEmpty()) {
        int node = queue.poll();
        order[count] = node;
        count++;
        for (int next : graph.get(node)) {
            inDegree[next]--;
            if (inDegree[next] == 0) {
                queue.offer(next);
            }
        }
    }
    if (count == numCourses) {
        return order;
    }
    return new int[0];
}
```

```java
findOrder(4, new int[][] {{1, 0}, {2, 0}, {3, 1}, {3, 2}});
// [0, 1, 2, 3]
```

**Complexity:** time O(V + E), space O(V + E).

**Follow-up questions:**
- **Only "can finish" (true/false)?** Return `count == numCourses`.
- **DFS alternative?** Colour nodes: unvisited, in the current path, done. Reaching an in-path node means a cycle. Reverse the finish order to get a topological order.
- **Is the order unique?** No. If two nodes are in the queue at once, either order is valid.
- **Real uses?** Build systems, task schedulers, package dependency resolution, Spring bean initialization order.

### Q47. How does Dijkstra's algorithm find the shortest path in a weighted graph? [Advanced]

**Problem:** Find the minimum total weight from a start node to every other node, when edge weights are not negative.

```text
A --4-- B --1-- D
 \     /       /
  1   2       5
   \ /       /
    C ------+
```

Edges: A-B 4, A-C 1, C-B 2, B-D 1, C-D 5. Nodes are numbered `A=0, B=1, C=2, D=3`.

**Idea:** Keep `dist[]` (best known distance, starting at infinity). Use a min-heap (`PriorityQueue`) ordered by distance. Repeatedly take the closest unfinished node. For each neighbor, if going through this node is shorter, update the neighbor's distance and push it. A heap entry that is older than `dist[]` is stale and skipped. This is a **greedy** approach: once the closest node is popped, its distance is final, because all weights are non-negative.

**Trace from A:**

```text
dist = [A:0, B:inf, C:inf, D:inf]     heap: (A,0)
pop (A,0): B = 0+4 = 4, C = 0+1 = 1   heap: (C,1) (B,4)
pop (C,1): B = 1+2 = 3 < 4 -> 3, D = 1+5 = 6   heap: (B,3) (B,4) (D,6)
pop (B,3): D = 3+1 = 4 < 6 -> 4       heap: (B,4) (D,4) (D,6)
pop (B,4): 4 > dist[B]=3 -> stale, skip
pop (D,4): neighbors give nothing better
pop (D,6): stale, skip
final dist = [0, 3, 1, 4]
```

```java
public static int[] dijkstra(List<List<int[]>> graph, int start) {
    int n = graph.size();
    int[] dist = new int[n];
    Arrays.fill(dist, Integer.MAX_VALUE);
    dist[start] = 0;
    PriorityQueue<int[]> heap = new PriorityQueue<>((a, b) -> Integer.compare(a[1], b[1]));
    heap.offer(new int[] {start, 0});
    while (!heap.isEmpty()) {
        int[] top = heap.poll();
        int node = top[0];
        int distance = top[1];
        if (distance > dist[node]) {
            continue;
        }
        for (int[] edge : graph.get(node)) {
            int next = edge[0];
            int weight = edge[1];
            if (distance + weight < dist[next]) {
                dist[next] = distance + weight;
                heap.offer(new int[] {next, dist[next]});
            }
        }
    }
    return dist;
}
```

Each edge in `graph.get(u)` is `{neighbor, weight}`. For the graph above (undirected), add each edge in both directions.

**Complexity:** time O((V + E) log V) with a binary heap, space O(V + E).

**Follow-up questions:**
- **Negative edge weights?** Dijkstra can be wrong. Use Bellman-Ford, O(V * E), which also detects negative cycles.
- **Why skip stale entries?** `PriorityQueue` has no cheap "decrease key", so you push a new entry and ignore old ones when popped.
- **Need the path, not only the distance?** Store a `parent[]` array and update it when `dist[next]` improves.
- **Weights are all 1?** Plain BFS (Q43) is simpler and faster.
- **Real uses?** Routing, maps, network latency.

---

# 6. Searching, Sorting, Recursion and DP Basics

## 6.1 Basic

### Q48. How does binary search work? [Basic]

**Problem:** Find `7` in the sorted array `{1, 3, 5, 7, 9, 11}`. Answer: index `3`. Return `-1` if absent.

**Idea:** Brute force: scan every element, O(n). Better: because the array is sorted, compare the target with the middle element and throw away the half that cannot contain it. Each step halves the search range, so it takes O(log n) steps.

**Trace (target 7):**

```text
index:  0  1  2  3  4   5
value:  1  3  5  7  9  11

left=0 right=5 mid=2 value 5  < 7 -> left = 3
left=3 right=5 mid=4 value 9  > 7 -> right = 3
left=3 right=3 mid=3 value 7 == 7 -> return 3
```

```java
public static int binarySearch(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return mid;
        }
        if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return -1;
}
```

**Complexity:** time O(log n), space O(1).

**Follow-up questions:**
- **Why `left + (right - left) / 2` and not `(left + right) / 2`?** The sum `left + right` can overflow `int` for large indexes.
- **Where would the target be inserted?** After the loop, `left` is the insertion position.
- **What does `Arrays.binarySearch` return when not found?** `-(insertionPoint) - 1`, a negative number. For `{1,3,5}` and target `4`, the insertion point is 2, so it returns `-3`.
- **Does it need sorted input?** Yes. On unsorted data the result is undefined.
- **Binary search on the answer?** When you can test "is value X enough?" and the test flips from false to true once (for example the minimum speed to finish on time), binary search over X.

### Q49. How do you solve Fibonacci and Climbing Stairs with memoization? [Basic]

**Problem:** Fibonacci: `fib(0) = 0`, `fib(1) = 1`, `fib(n) = fib(n-1) + fib(n-2)`. So `fib(5) = 5`. Climbing stairs: you climb 1 or 2 steps at a time. How many ways to reach step `n`? For `n = 5` the answer is `8`.

**Idea:** The plain recursion recomputes the same values again and again, which is O(2^n).

```text
fib(5)
├── fib(4)
│   ├── fib(3)
│   │   ├── fib(2) ...
│   │   └── fib(1)
│   └── fib(2) ...         <- fib(2) and fib(3) are computed several times
└── fib(3) ...
```

**Dynamic programming (DP)** means solving each smaller problem once and reusing the result. Two styles: **memoization** (top down recursion that caches results) and **tabulation** (bottom up loop). Climbing stairs is Fibonacci in disguise: `ways(n) = ways(n-1) + ways(n-2)`, because the last move was either 1 step or 2 steps.

**Trace for climbing stairs (`ways(1) = 1`, `ways(2) = 2`):**

```text
ways(3) = ways(2) + ways(1) = 2 + 1 = 3
ways(4) = ways(3) + ways(2) = 3 + 2 = 5
ways(5) = ways(4) + ways(3) = 5 + 3 = 8
```

```java
public static long fibMemo(int n, long[] memo) {
    if (n <= 1) {
        return n;
    }
    if (memo[n] != 0) {
        return memo[n];
    }
    memo[n] = fibMemo(n - 1, memo) + fibMemo(n - 2, memo);
    return memo[n];
}

public static int climbStairs(int n) {
    if (n <= 2) {
        return n;
    }
    int previous = 1;
    int current = 2;
    for (int step = 3; step <= n; step++) {
        int next = previous + current;
        previous = current;
        current = next;
    }
    return current;
}
```

```java
fibMemo(5, new long[6]);
// 5
climbStairs(5);
// 8
```

**Complexity:** `fibMemo`: time O(n), space O(n). `climbStairs` (tabulation with two variables): time O(n), space O(1).

**Follow-up questions:**
- **How do you spot a DP problem?** The problem asks for a count or best value, the answer for `n` depends on smaller answers, and the plain recursion repeats subproblems.
- **Memoization vs tabulation?** Memoization is easier to write from the recursion and computes only needed states. Tabulation avoids recursion depth limits and is easier to shrink to O(1) space.
- **Overflow?** `fib(93)` no longer fits in a `long`. Use `BigInteger` or `% 1_000_000_007` if the question asks for a remainder.
- **Steps of 1, 2 or 3?** `ways(n) = ways(n-1) + ways(n-2) + ways(n-3)`.
- **Faster than O(n)?** Matrix exponentiation gives O(log n). Rarely asked.

---

## 6.2 Intermediate

### Q50. How do you find the first and last position of a value in a sorted array? [Intermediate]

**Problem:** `{5, 7, 7, 8, 8, 10}`, target `8` -> `[3, 4]`. Target `6` -> `[-1, -1]`. Required time is O(log n).

**Idea:** Run binary search twice. A **lower bound** search finds the first index with value `>= target`. An **upper bound** search finds the first index with value `> target`. The first position is the lower bound (if that element equals the target). The last position is the upper bound minus one. Both searches use a half-open range `[left, right)`.

**Trace for target 8:**

```text
index: 0 1 2 3 4 5
value: 5 7 7 8 8 10

lowerBound (move right when nums[mid] >= target):
left=0 right=6 mid=3 (8)   8 < 8? no -> right=3
left=0 right=3 mid=1 (7)   7 < 8 -> left=2
left=2 right=3 mid=2 (7)   7 < 8 -> left=3   -> first = 3

upperBound (move left when nums[mid] <= target):
left=0 right=6 mid=3 (8)   8 <= 8 -> left=4
left=4 right=6 mid=5 (10)  10 <= 8? no -> right=5
left=4 right=5 mid=4 (8)   8 <= 8 -> left=5  -> upper = 5, last = 5 - 1 = 4
```

```java
public static int[] searchRange(int[] nums, int target) {
    int first = lowerBound(nums, target);
    if (first == nums.length || nums[first] != target) {
        return new int[] {-1, -1};
    }
    int last = upperBound(nums, target) - 1;
    return new int[] {first, last};
}

private static int lowerBound(int[] nums, int target) {
    int left = 0;
    int right = nums.length;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] < target) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }
    return left;
}

private static int upperBound(int[] nums, int target) {
    int left = 0;
    int right = nums.length;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] <= target) {
            left = mid + 1;
        } else {
            right = mid;
        }
    }
    return left;
}
```

**Complexity:** time O(log n), space O(1).

**Follow-up questions:**
- **How many times does the target occur?** `upperBound - lowerBound`.
- **Why not find one match and expand outward?** If every element equals the target, expanding is O(n).
- **Insert position?** `lowerBound(nums, target)`.
- **Why `left < right` here but `left <= right` in Q48?** This version keeps `right` as an excluded end. Both are fine. Pick one style and be consistent.

### Q51. How do you search in a rotated sorted array? [Intermediate]

**Problem:** A sorted array was rotated at an unknown point: `{4, 5, 6, 7, 0, 1, 2}`. Find `0` -> index `4`. All values are distinct. Required time is O(log n).

**Idea:** After picking `mid`, at least one half is still sorted. Find the sorted half by comparing `nums[left]` with `nums[mid]`. If the target lies inside the sorted half's range, search that half. Otherwise search the other half.

**Trace (target 0):**

```text
index: 0 1 2 3 4 5 6
value: 4 5 6 7 0 1 2

left=0 right=6 mid=3 (7): left half 4..7 is sorted (4 <= 7)
                          is 0 in [4, 7)? no -> left = 4
left=4 right=6 mid=5 (1): left half 0..1 is sorted (0 <= 1)
                          is 0 in [0, 1)? yes -> right = 4
left=4 right=4 mid=4 (0): found -> 4
```

```java
public static int search(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] == target) {
            return mid;
        }
        if (nums[left] <= nums[mid]) {
            if (nums[left] <= target && target < nums[mid]) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        } else {
            if (nums[mid] < target && target <= nums[right]) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
    }
    return -1;
}
```

**Complexity:** time O(log n), space O(1).

**Follow-up questions:**
- **Find the minimum of the rotated array?** Compare `nums[mid]` with `nums[right]`. If `nums[mid] > nums[right]` the minimum is on the right, otherwise on the left including `mid`.
- **Duplicates allowed?** When `nums[left] == nums[mid] == nums[right]` you cannot decide a side, so shrink both ends by one. The worst case becomes O(n).
- **Why `<=` in `nums[left] <= nums[mid]`?** When `left == mid`, the left half is one element and counts as sorted.

### Q52. How do merge sort and quick sort work? [Intermediate]

**Problem:** Explain and implement the two classic O(n log n) sorts.

**Idea:**
- **Merge sort:** split the array in halves, sort each half recursively, then **merge** the two sorted halves. Always O(n log n). Needs O(n) extra space. It is **stable** (equal elements keep their original order).
- **Quick sort:** choose a **pivot**, **partition** so smaller elements are left of it and larger right of it (the pivot is now in its final place), then sort both sides recursively. Average O(n log n), worst case O(n^2) (for example an already sorted array with a bad pivot choice). In place. Not stable.

| | Merge sort | Quick sort |
|---|---|---|
| Time (average / worst) | O(n log n) / O(n log n) | O(n log n) / O(n^2) |
| Extra space | O(n) | O(log n) stack |
| Stable | Yes | No |
| Good for | Linked lists, external (disk) sorting, stable need | Arrays in memory |

**Merge sort trace on `{5, 2, 4, 1}`:**

```text
         [5, 2, 4, 1]
         /          \
     [5, 2]        [4, 1]
     /    \        /    \
   [5]    [2]    [4]    [1]
     \    /        \    /
     [2, 5]        [1, 4]       merge each pair
         \          /
        [1, 2, 4, 5]            merge the halves
```

**Quick sort partition trace on `{4, 2, 7, 1, 5}` with the last element (5) as pivot:**

```text
i = -1 (end of the "<= pivot" zone)
j=0: 4 <= 5 -> i=0, swap with itself      {4, 2, 7, 1, 5}
j=1: 2 <= 5 -> i=1                        {4, 2, 7, 1, 5}
j=2: 7 <= 5? no
j=3: 1 <= 5 -> i=2, swap a[2] and a[3]    {4, 2, 1, 7, 5}
finally swap pivot with a[i+1]=a[3]       {4, 2, 1, 5, 7}   pivot 5 is at index 3
```

```java
public static void mergeSort(int[] nums, int left, int right) {
    if (left >= right) {
        return;
    }
    int mid = left + (right - left) / 2;
    mergeSort(nums, left, mid);
    mergeSort(nums, mid + 1, right);
    merge(nums, left, mid, right);
}

private static void merge(int[] nums, int left, int mid, int right) {
    int[] temp = new int[right - left + 1];
    int i = left;
    int j = mid + 1;
    int k = 0;
    while (i <= mid && j <= right) {
        if (nums[i] <= nums[j]) {
            temp[k] = nums[i];
            i++;
        } else {
            temp[k] = nums[j];
            j++;
        }
        k++;
    }
    while (i <= mid) {
        temp[k] = nums[i];
        i++;
        k++;
    }
    while (j <= right) {
        temp[k] = nums[j];
        j++;
        k++;
    }
    System.arraycopy(temp, 0, nums, left, temp.length);
}

public static void quickSort(int[] nums, int left, int right) {
    if (left >= right) {
        return;
    }
    int pivotIndex = partition(nums, left, right);
    quickSort(nums, left, pivotIndex - 1);
    quickSort(nums, pivotIndex + 1, right);
}

private static int partition(int[] nums, int left, int right) {
    int pivot = nums[right];
    int i = left - 1;
    for (int j = left; j < right; j++) {
        if (nums[j] <= pivot) {
            i++;
            swap(nums, i, j);
        }
    }
    swap(nums, i + 1, right);
    return i + 1;
}

private static void swap(int[] nums, int a, int b) {
    int temp = nums[a];
    nums[a] = nums[b];
    nums[b] = temp;
}
```

```java
int[] nums = {5, 2, 4, 1};
mergeSort(nums, 0, nums.length - 1);
// nums is {1, 2, 4, 5}
```

**Follow-up questions:**
- **What does `Arrays.sort` use?** For primitives, a dual-pivot quicksort. For objects, **TimSort** (a merge sort hybrid that is stable and fast on partly sorted data). `Collections.sort` uses the same TimSort.
- **How do you avoid quick sort's worst case?** Pick a random pivot or the median of three values (first, middle, last).
- **Why is merge sort preferred for linked lists?** It needs no random access, and merging lists needs no extra space.
- **Can any comparison sort beat O(n log n)?** No. The lower bound is O(n log n). Counting sort or radix sort beat it only for limited value ranges.
- **Sort a nearly sorted array fast?** Insertion sort is O(n) there, and TimSort uses this fact.

### Q53. How do you find the K-th largest element and top K items? (heap vs quickselect) [Intermediate]

**Problem:** `{3, 2, 1, 5, 6, 4}`, `k = 2` -> `5`. The sorted array is `1 2 3 4 5 6`, so the second largest is 5.

**Idea:** Brute force: sort, then take index `n - k`, O(n log n). Two better ways:
- **Min-heap of size K:** keep the K largest values seen so far. The heap's top (the smallest of those K) is the answer. O(n log k), O(k) space. Works on streams.
- **Quickselect:** use the partition from Q52, but recurse only into the side that holds the target index `n - k`. Average O(n), worst case O(n^2), modifies the array.

A **heap** (priority queue) always gives quick access to the smallest (min-heap) or largest (max-heap) element. Java's `PriorityQueue` is a min-heap by default.

**Heap trace (k = 2):**

```text
add 3 -> [3]
add 2 -> [2, 3]
add 1 -> [1, 2, 3] size 3 > 2 -> remove smallest (1) -> [2, 3]
add 5 -> [2, 3, 5]             remove smallest (2)     -> [3, 5]
add 6 -> [3, 5, 6]             remove smallest (3)     -> [5, 6]
add 4 -> [4, 5, 6]             remove smallest (4)     -> [5, 6]
heap top = 5
```

```java
public static int findKthLargest(int[] nums, int k) {
    PriorityQueue<Integer> heap = new PriorityQueue<>();
    for (int num : nums) {
        heap.offer(num);
        if (heap.size() > k) {
            heap.poll();
        }
    }
    return heap.peek();
}

public static int findKthLargestQuickSelect(int[] nums, int k) {
    int target = nums.length - k;
    int left = 0;
    int right = nums.length - 1;
    while (left <= right) {
        int pivotIndex = partition(nums, left, right);
        if (pivotIndex == target) {
            return nums[pivotIndex];
        }
        if (pivotIndex < target) {
            left = pivotIndex + 1;
        } else {
            right = pivotIndex - 1;
        }
    }
    throw new IllegalArgumentException("k is out of range");
}

private static int partition(int[] nums, int left, int right) {
    int pivot = nums[right];
    int i = left - 1;
    for (int j = left; j < right; j++) {
        if (nums[j] <= pivot) {
            i++;
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
        }
    }
    int temp = nums[i + 1];
    nums[i + 1] = nums[right];
    nums[right] = temp;
    return i + 1;
}
```

**Top K pattern for other data.** To get the K points closest to the origin, keep a **max-heap** of size K ordered by distance, and remove the largest when the size passes K. The pattern is: to keep the K smallest use a max-heap, to keep the K largest use a min-heap.

```java
public static int[][] kClosest(int[][] points, int k) {
    PriorityQueue<int[]> heap = new PriorityQueue<>(
        (a, b) -> Integer.compare(squared(b), squared(a)));
    for (int[] point : points) {
        heap.offer(point);
        if (heap.size() > k) {
            heap.poll();
        }
    }
    return heap.toArray(new int[0][]);
}

private static int squared(int[] point) {
    return point[0] * point[0] + point[1] * point[1];
}
```

**Complexity:** heap O(n log k) time, O(k) space. Quickselect O(n) average, O(1) extra space.

**Follow-up questions:**
- **Which to choose?** Heap when data streams in or must not be modified. Quickselect when you want the best average speed on an in-memory array.
- **Why a min-heap for the K largest?** The smallest of the K largest sits on top, so it is the one to throw away when a bigger value arrives.
- **Why compare squared distances?** Skipping the square root keeps it in integers and the order is the same.
- **Merge K sorted lists, or minimum meeting rooms?** Also min-heap problems. The heap holds the next candidate from each list, or the end times of the rooms in use.
- **Median of a data stream?** Two heaps: a max-heap for the lower half and a min-heap for the upper half.

### Q54. How do you generate all subsets and permutations? (backtracking) [Intermediate]

**Problem:** Subsets of `{1, 2, 3}`: all 8 combinations including the empty one. Permutations of `{1, 2, 3}`: all 6 orderings.

**Idea:** **Backtracking** builds a result step by step. Make a choice, recurse, then **undo** the choice and try the next one. Brute force would be to enumerate by hand, so backtracking is the standard way to organize it.

- **Subsets:** at each call, record the current list. Then, for each later element, add it, recurse from the next index, and remove it.
- **Permutations:** at each call, try every element that is not yet used in the current list. When the list has `n` elements, record it.

**Subsets decision tree (each node records its list):**

```text
[]
├── [1]
│   ├── [1,2]
│   │   └── [1,2,3]
│   └── [1,3]
├── [2]
│   └── [2,3]
└── [3]

recorded order: [] [1] [1,2] [1,2,3] [1,3] [2] [2,3] [3]
```

**Permutations (recorded order):**

```text
[1,2,3] [1,3,2] [2,1,3] [2,3,1] [3,1,2] [3,2,1]
```

```java
public static List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> result = new ArrayList<>();
    backtrackSubsets(nums, 0, new ArrayList<>(), result);
    return result;
}

private static void backtrackSubsets(int[] nums, int start, List<Integer> current,
                                     List<List<Integer>> result) {
    result.add(new ArrayList<>(current));
    for (int i = start; i < nums.length; i++) {
        current.add(nums[i]);
        backtrackSubsets(nums, i + 1, current, result);
        current.remove(current.size() - 1);
    }
}

public static List<List<Integer>> permutations(int[] nums) {
    List<List<Integer>> result = new ArrayList<>();
    backtrackPermutations(nums, new boolean[nums.length], new ArrayList<>(), result);
    return result;
}

private static void backtrackPermutations(int[] nums, boolean[] used, List<Integer> current,
                                          List<List<Integer>> result) {
    if (current.size() == nums.length) {
        result.add(new ArrayList<>(current));
        return;
    }
    for (int i = 0; i < nums.length; i++) {
        if (used[i]) {
            continue;
        }
        used[i] = true;
        current.add(nums[i]);
        backtrackPermutations(nums, used, current, result);
        current.remove(current.size() - 1);
        used[i] = false;
    }
}
```

**Complexity:** subsets: O(n * 2^n) time (2^n subsets, each copied in O(n)), space O(n) recursion plus output. Permutations: O(n * n!) time.

**Follow-up questions:**
- **Why `new ArrayList<>(current)`?** `current` is reused and changed later. Without a copy, every stored result would end up pointing at the same list.
- **Why `current.remove(current.size() - 1)` and not `remove(Integer)`?** The index version removes the last element. `remove(int)` vs `remove(Object)` overloads are a classic bug.
- **Input has duplicates?** Sort first, then skip an element equal to the previous one at the same recursion level (`if (i > start && nums[i] == nums[i - 1]) continue;` for subsets).
- **Combination sum, N-Queens, word search?** Same template: choose, recurse, undo, with pruning to cut hopeless branches.

### Q55. How do you solve House Robber? [Intermediate]

**Problem:** Houses in a row hold money. You cannot rob two adjacent houses. Maximize the total. `{2, 7, 9, 3, 1}` -> `12` (rob 2, 9 and 1).

**Idea:** Brute force: try every valid selection, exponential. DP: at house `i` you either skip it (best so far stays) or rob it (best from two houses back plus this house). `best(i) = max(best(i-1), best(i-2) + nums[i])`. Only the last two values are needed, so use two variables.

**Trace (`prevPrev` = best up to i-2, `prev` = best up to i-1):**

```text
start        prevPrev=0 prev=0
house 2:   current = max(0, 0+2) = 2     -> prevPrev=0 prev=2
house 7:   current = max(2, 0+7) = 7     -> prevPrev=2 prev=7
house 9:   current = max(7, 2+9) = 11    -> prevPrev=7 prev=11
house 3:   current = max(11, 7+3) = 11   -> prevPrev=11 prev=11
house 1:   current = max(11, 11+1) = 12  -> prev=12
answer 12
```

```java
public static int rob(int[] nums) {
    int prevPrev = 0;
    int prev = 0;
    for (int num : nums) {
        int current = Math.max(prev, prevPrev + num);
        prevPrev = prev;
        prev = current;
    }
    return prev;
}
```

**Complexity:** time O(n), space O(1).

**Follow-up questions:**
- **Houses in a circle (first and last are neighbors)?** Answer is the larger of `rob(nums[0..n-2])` and `rob(nums[1..n-1])`.
- **Which houses did we rob?** Keep the full `dp[]` array and walk back from the end, or store choices.
- **Tree of houses?** Tree DP: each node returns two values, best with the node robbed and best without.

---

## 6.3 Advanced

### Q56. How do you solve Coin Change? [Advanced]

**Problem:** Given coin values and an amount, find the fewest coins that make the amount, or `-1` if impossible. `coins = {1, 2, 5}`, `amount = 11` -> `3` (5 + 5 + 1).

**Idea:** Greedy (always take the largest coin) fails: for `{1, 3, 4}` and `6`, greedy picks 4 + 1 + 1 (3 coins) but 3 + 3 is 2 coins. DP: `dp[a]` is the fewest coins for amount `a`. For each amount try every coin: `dp[a] = min(dp[a - coin] + 1)`. `dp[0] = 0`. Fill the rest with a large "impossible" value.

**DP table for `{1, 2, 5}`:**

```text
amount: 0 1 2 3 4 5 6 7 8 9 10 11
dp:     0 1 1 2 2 1 2 2 3 3  2  3

dp[11] = min( dp[10]+1 = 3, dp[9]+1 = 4, dp[6]+1 = 3 ) = 3
```

```java
public static int coinChange(int[] coins, int amount) {
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
```

`amount + 1` works as "infinity" because no valid answer needs more than `amount` coins (all coins are at least 1).

**Complexity:** time O(amount * number of coins), space O(amount).

**Follow-up questions:**
- **Count the number of ways (combinations)?** `ways[0] = 1`. Loop the coins in the outer loop and amounts in the inner loop: `ways[a] += ways[a - coin]`. Coin order in the outer loop avoids counting `1+2` and `2+1` separately.
- **Why not plain recursion?** It repeats subproblems and is exponential. Memoize on the remaining amount to get the same O(amount * coins).
- **Print which coins?** Store the last coin used for each amount and walk back from `amount`.
- **Why does greedy work for some coin systems?** Standard currencies are designed so greedy is optimal. Arbitrary coin sets are not.

### Q57. What is the longest common subsequence (LCS)? [Advanced]

**Problem:** A **subsequence** keeps the order but may skip characters (not necessarily adjacent). Find the length of the longest subsequence common to two strings. `"abcde"` and `"ace"` -> `3` (`"ace"`).

**Idea:** Brute force: try all subsequences of one string, O(2^n). DP: `dp[i][j]` is the LCS length of the first `i` characters of `a` and the first `j` characters of `b`. If the characters `a[i-1]` and `b[j-1]` are equal, `dp[i][j] = dp[i-1][j-1] + 1`. Otherwise `dp[i][j] = max(dp[i-1][j], dp[i][j-1])`.

**DP table (rows: characters of `abcde`, columns: characters of `ace`):**

```text
        ""  a  c  e
   ""    0  0  0  0
   a     0  1  1  1
   b     0  1  1  1
   c     0  1  2  2
   d     0  1  2  2
   e     0  1  2  3

dp[3][2]: 'c' == 'c' -> dp[2][1] + 1 = 2
dp[5][3]: 'e' == 'e' -> dp[4][2] + 1 = 3      answer 3
```

```java
public static int lcs(String a, String b) {
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
```

**Complexity:** time O(n * m), space O(n * m). Space can drop to O(min(n, m)) by keeping only two rows.

**Follow-up questions:**
- **How to print the subsequence?** Walk back from `dp[n][m]`. If characters match, take it and go diagonally. Otherwise move toward the larger neighbor.
- **Longest common substring (contiguous)?** If characters differ set `dp[i][j] = 0`, and track the maximum cell.
- **Edit distance (Levenshtein)?** Same table shape: `min(insert, delete, replace) + 1` when characters differ.
- **Where is this used?** Diff tools, version control, DNA comparison, plagiarism checks.

---

# 7. Java Collections and Data Structure Questions

Interviewers often ask these to Java developers right after a coding question. Collections are also covered in `D:\prep\java\INTERVIEW-QUESTIONS.md` (Q26 to Q33). This section is a short DSA-oriented version.

## 7.1 Basic

### Q58. ArrayList vs LinkedList vs array: which is faster and when? [Basic]

**Short answer:** `ArrayList` for almost everything. It stores elements in one array, so index access is O(1) and iteration is cache friendly. `LinkedList` is faster only for adding or removing at the ends or at an iterator position, and even then `ArrayDeque` is usually better.

| Operation | `ArrayList` | `LinkedList` | `int[]` / `T[]` |
|---|---|---|---|
| `get(i)` | O(1) | O(n) | O(1) |
| `add` at end | O(1) amortized | O(1) | fixed size |
| `add(0, x)` / insert in the middle | O(n) (shifts elements) | O(n) to find the spot, O(1) to link | O(n) shift |
| `remove(i)` | O(n) | O(n) to find, O(1) to unlink | O(n) shift |
| `contains` | O(n) | O(n) | O(n) |
| Memory per element | reference only | reference + two node pointers + object header | primitives stored directly |

- **Amortized O(1)** for `ArrayList.add`: it occasionally grows the array (about 1.5 times, with a copy), but the average over many adds is O(1).
- `LinkedList` insertion is O(1) only if you already hold the node or iterator. Finding the position by index is O(n), so total cost is still O(n).
- An **array** has a fixed length and can hold primitives. A `List` is resizable but holds objects (primitives are boxed, for example `int` becomes `Integer`).

```java
List<Integer> fixed = Arrays.asList(1, 2, 3);
fixed.set(0, 10);
// allowed: fixed is now [10, 2, 3]
fixed.add(4);
// throws UnsupportedOperationException (size is fixed, backed by the array)
```

**Follow-up questions:**
- **Why is `LinkedList` slow in practice even for inserts?** Each node is a separate heap object, so traversal jumps around memory (poor CPU cache use), and each element costs more memory.
- **How to pre-size an `ArrayList`?** `new ArrayList<>(expectedSize)` avoids repeated resizing.
- **Primitive array to `List<Integer>`?** `Arrays.stream(arr).boxed().toList()` (Java 16+). `Arrays.asList(arr)` on an `int[]` gives a `List<int[]>` with one element, a common mistake.
- **`Vector` vs `ArrayList`?** `Vector` is legacy and synchronized on every method. Prefer `ArrayList`, and `CopyOnWriteArrayList` or locks for concurrency.

### Q59. HashMap vs TreeMap vs LinkedHashMap, and how does HashSet keep elements unique? [Basic]

**Short answer:** All three implement `Map`. They differ in ordering and cost: `HashMap` is unordered and O(1), `LinkedHashMap` keeps insertion (or access) order and is O(1), `TreeMap` keeps keys sorted and is O(log n).

| | `HashMap` | `LinkedHashMap` | `TreeMap` |
|---|---|---|---|
| Iteration order | None guaranteed | Insertion order (or access order) | Sorted by key |
| `get` / `put` | O(1) average | O(1) average | O(log n) |
| Null key | One allowed | One allowed | Not allowed (natural ordering) |
| Backed by | Hash table | Hash table + linked list | Red-black tree (balanced BST) |
| Extra methods | - | `removeEldestEntry` (LRU, Q29) | `firstKey`, `floorKey`, `ceilingKey`, `subMap`, `headMap` |
| Keys need | `equals` + `hashCode` | `equals` + `hashCode` | `Comparable` or a `Comparator` |

```java
Map<String, Integer> sorted = new TreeMap<>();
sorted.put("banana", 2);
sorted.put("apple", 1);
sorted.put("cherry", 3);
// iteration: apple=1, banana=2, cherry=3

Map<String, Integer> insertion = new LinkedHashMap<>();
insertion.put("banana", 2);
insertion.put("apple", 1);
insertion.put("cherry", 3);
// iteration: banana=2, apple=1, cherry=3
```

**HashSet uniqueness.** A `HashSet` is a `HashMap` where your element is the key and the value is a shared dummy object. `add(x)` calls `map.put(x, PRESENT)`. It returns `false` if the key already exists, which is why `set.add(x)` doubles as a "seen before?" check. Uniqueness is decided by `hashCode()` and `equals()`. `TreeSet` decides by `compareTo`/`compare` instead (two values that compare as 0 are duplicates), and `LinkedHashSet` keeps insertion order.

**Follow-up questions:**
- **When to choose `TreeMap`?** When you need sorted keys or range queries, for example "the closest smaller key" (`floorKey`), or the first and last key.
- **Why does `TreeMap` reject null keys?** It must compare keys, and `compareTo(null)` fails.
- **Is HashMap order really random?** It is deterministic for a given JVM and data but not defined. Do not depend on it.
- **Thread safety?** None of the three. Use `ConcurrentHashMap` (and `ConcurrentSkipListMap` for a sorted concurrent map).

### Q60. Why use ArrayDeque instead of Stack? [Basic]

**Short answer:** `Stack` is a legacy class that extends `Vector`, so every method is synchronized (extra cost) and it exposes list methods that break the stack idea (`get(i)`, `add(i, x)`). Java's own docs recommend `Deque` implementations such as `ArrayDeque`, for both stack and queue use.

```java
Deque<Integer> stack = new ArrayDeque<>();
stack.push(1);
stack.push(2);
stack.push(3);
System.out.println(stack);
// [3, 2, 1]   (push adds at the front, so the top is first)
stack.pop();
// 3
stack.peek();
// 2

Queue<Integer> queue = new ArrayDeque<>();
queue.offer(1);
queue.offer(2);
queue.poll();
// 1
```

| Need | Method on `Deque` | Fails on empty |
|---|---|---|
| Stack push / pop / look | `push` / `pop` / `peek` | `pop` throws `NoSuchElementException`, `peek` returns `null` |
| Queue add / remove / look | `offer` / `poll` / `peek` | `poll` and `peek` return `null` |

**Follow-up questions:**
- **`ArrayDeque` vs `LinkedList` as a queue?** `ArrayDeque` is a resizable circular array: faster, less memory. `LinkedList` allows `null` elements, `ArrayDeque` does not.
- **Is `ArrayDeque` thread safe?** No. Use `ConcurrentLinkedDeque` or `LinkedBlockingDeque` across threads.
- **Why does the printed order look reversed?** A stack's iteration order starts at the top. That is expected.
- **`Stack.search`?** Returns a 1-based distance from the top. Rarely used.

### Q61. Comparable vs Comparator, and how do you sort objects by multiple fields? [Basic]

**Short answer:** `Comparable` gives a class one **natural order** by implementing `compareTo(other)` inside the class. `Comparator` is a separate object that defines any other order, such as `compare(a, b)`. Use `Comparator` for several orders or for classes you cannot change.

Both return a negative number if the first is smaller, zero if equal, and a positive number if larger.

```java
public record Employee(String name, String dept, int salary) {
}

List<Employee> employees = new ArrayList<>();
employees.add(new Employee("Asha", "IT", 90));
employees.add(new Employee("Ravi", "HR", 70));
employees.add(new Employee("Meera", "IT", 90));
employees.add(new Employee("Karan", "IT", 80));

Comparator<Employee> order = Comparator
    .comparing(Employee::dept)
    .thenComparing(Employee::salary, Comparator.reverseOrder())
    .thenComparing(Employee::name);

employees.sort(order);
// Ravi(HR,70), Asha(IT,90), Meera(IT,90), Karan(IT,80)
```

The order is: department ascending, then salary descending, then name ascending. Asha and Meera have the same department and salary, so name decides.

Natural order with `Comparable`:

```java
public class Version implements Comparable<Version> {

    private final int major;
    private final int minor;

    public Version(int major, int minor) {
        this.major = major;
        this.minor = minor;
    }

    @Override
    public int compareTo(Version other) {
        int byMajor = Integer.compare(major, other.major);
        if (byMajor != 0) {
            return byMajor;
        }
        return Integer.compare(minor, other.minor);
    }
}
```

**Follow-up questions:**
- **Why not `return a - b` in a comparator?** Subtraction can overflow for large or negative values and give the wrong sign. Use `Integer.compare(a, b)`.
- **Nulls in the data?** `Comparator.nullsFirst(...)` and `nullsLast(...)` wrap another comparator.
- **Should `compareTo` match `equals`?** It should, or sorted sets and maps behave surprisingly: `TreeSet` treats `compareTo == 0` as a duplicate even if `equals` says false.
- **Is `List.sort` stable?** Yes (TimSort). Equal elements keep their original relative order, which makes chained sorting possible.
- **Reverse the whole order?** `order.reversed()`.

### Q62. What is the time complexity of common Java collection operations? [Basic]

**Short answer:** Know this table. Average case unless stated.

| Collection | Add | Remove | Get / contains | Notes |
|---|---|---|---|---|
| `ArrayList` | O(1) amortized at end, O(n) in the middle | O(n) | `get(i)` O(1), `contains` O(n) | Best default list |
| `LinkedList` | O(1) at ends | O(1) at ends, O(n) by index/value | `get(i)` O(n), `contains` O(n) | Rarely the best choice |
| `ArrayDeque` | O(1) at both ends | O(1) at both ends | `contains` O(n) | Stack and queue |
| `HashMap` / `HashSet` | O(1) | O(1) | O(1) | Worst case O(log n) in Java 8+ (tree buckets), if keys have bad hashes |
| `LinkedHashMap` / `LinkedHashSet` | O(1) | O(1) | O(1) | Keeps order |
| `TreeMap` / `TreeSet` | O(log n) | O(log n) | O(log n) | Sorted, has floor/ceiling |
| `PriorityQueue` | `offer` O(log n) | `poll` O(log n), `remove(Object)` O(n) | `peek` O(1), `contains` O(n) | Binary heap |
| `Arrays.sort` / `Collections.sort` | - | - | - | O(n log n) |

- Space for all of these is O(n).
- `HashMap` operations stay O(1) only while `hashCode` spreads keys well. See Q63.
- **Iteration** over any collection is O(n). Over a `HashMap` with a huge capacity but few entries it is O(capacity + n), so do not over-size.

**Follow-up questions:**
- **Fastest way to check "is x in this large collection"?** A `HashSet`. Not a `List` (O(n)).
- **Fastest queue and stack?** `ArrayDeque`.
- **Sorted data with insertions?** `TreeMap` or `TreeSet`. A sorted `ArrayList` makes insertion O(n).
- **Why is `ArrayList.add` called amortized?** Occasional O(n) copies when the array grows are spread across many cheap O(1) adds.

---

## 7.2 Intermediate

### Q63. How does HashMap work, and why do hashCode and equals matter for keys? [Intermediate]

**Short answer:** A `HashMap` has an array of **buckets**. The key's `hashCode()` picks a bucket (index = hash & (length - 1)). Inside the bucket, `equals()` finds the exact key. Keys with the same hash share a bucket (a **collision**), kept in a linked list, converted to a balanced tree when a bucket gets long (8 or more entries and the table has at least 64 buckets, Java 8+). The table doubles when it is 75 percent full (load factor 0.75). For internals, see Q28, Q29 and Q32 in `D:\prep\java\INTERVIEW-QUESTIONS.md`.

```text
buckets:  [0] -> null
          [1] -> (keyA) -> (keyC)      keyA and keyC collide in bucket 1
          [2] -> null
          [3] -> (keyB)

get(keyC): hash -> bucket 1 -> compare with keyA (equals false) -> compare with keyC (equals true)
```

**The contract:** if `a.equals(b)` then `a.hashCode() == b.hashCode()`. Breaking it loses entries:

```java
public class BadKey {

    private final int id;

    public BadKey(int id) {
        this.id = id;
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof BadKey other && other.id == id;
    }
}

Map<BadKey, String> map = new HashMap<>();
map.put(new BadKey(1), "one");
map.get(new BadKey(1));
// null  (equal keys, different default hashCodes, so a different bucket is searched)
```

The fixed class overrides both:

```java
@Override
public boolean equals(Object o) {
    return o instanceof GoodKey other && other.id == id;
}

@Override
public int hashCode() {
    return Integer.hashCode(id);
}
```

For several fields use `Objects.hash(a, b, c)`. A `record` generates both methods automatically.

**Follow-up questions:**
- **Can I use a mutable object as a key?** Avoid it. If a field used in `hashCode` changes after insertion, the entry sits in the wrong bucket and `get` fails. Use immutable keys such as `String`, `Integer`, records.
- **What if `hashCode` always returns the same value?** Everything lands in one bucket. Lookups degrade to O(n), or O(log n) after treeification if keys are `Comparable`.
- **What happens on resize?** A new array twice as large is created and entries are redistributed. That is O(n), which is why you pre-size when you know the count.
- **Equal hash, different keys?** A collision, resolved with `equals`.

### Q64. How does PriorityQueue work, and why is its iteration order not sorted? [Intermediate]

**Short answer:** `PriorityQueue` is a **binary heap** stored in an array. A **min-heap** guarantees only that every parent is less than or equal to its children, so the smallest element is always at the front. The other elements are only partially ordered, so printing or iterating the queue does not give sorted output. Only repeated `poll()` does.

```text
offer 5, 1, 4, 2, 3 one by one gives the heap tree:

        1
       / \
      2   4
     / \
    5   3

array form: [1, 2, 4, 5, 3]      (children of index i are at 2i+1 and 2i+2)
```

```java
PriorityQueue<Integer> heap = new PriorityQueue<>();
heap.offer(5);
heap.offer(1);
heap.offer(4);
heap.offer(2);
heap.offer(3);
System.out.println(heap);
// [1, 2, 4, 5, 3]   (not sorted)
heap.poll();
// 1
heap.poll();
// 2
heap.poll();
// 3
```

**Max-heap and custom order:**

```java
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Comparator.reverseOrder());
maxHeap.offer(3);
maxHeap.offer(9);
maxHeap.offer(5);
maxHeap.poll();
// 9

PriorityQueue<int[]> byDistance = new PriorityQueue<>((a, b) -> Integer.compare(a[1], b[1]));
```

| Operation | Cost |
|---|---|
| `peek` | O(1) |
| `offer`, `poll` | O(log n) (sift the element up or down the tree) |
| `remove(Object)`, `contains` | O(n) |
| Build from a collection | O(n) (heapify) |

**Follow-up questions:**
- **Is it thread safe?** No. Use `PriorityBlockingQueue`.
- **Does it keep insertion order for equal priorities?** No, it is not stable. Add a sequence number to the comparator if you need FIFO among ties.
- **Does it allow `null`?** No.
- **How do I get sorted order from it?** Poll repeatedly. That is heap sort, O(n log n).
- **What goes wrong if elements are not `Comparable` and no comparator is given?** `ClassCastException` on the first `offer`.

### Q65. How do Iterator and ConcurrentModificationException work? [Intermediate]

**Short answer:** An `Iterator` walks a collection one element at a time (`hasNext`, `next`, and optionally `remove`). Most Java collections are **fail-fast**: they keep a modification counter (`modCount`). If the collection is structurally changed during iteration by anything other than the iterator itself, the next call to `next()` throws `ConcurrentModificationException`.

```java
List<Integer> numbers = new ArrayList<>(List.of(1, 2, 3, 4));
for (Integer n : numbers) {
    if (n == 2) {
        numbers.remove(n);
    }
}
// throws ConcurrentModificationException at the next iteration
```

The for-each loop uses an iterator behind the scenes. Safe ways to remove while looping:

```java
Iterator<Integer> iterator = numbers.iterator();
while (iterator.hasNext()) {
    if (iterator.next() % 2 == 0) {
        iterator.remove();
    }
}
// numbers is [1, 3]

numbers.removeIf(n -> n % 2 == 0);
// same result, shortest form
```

**Follow-up questions:**
- **A famous quirk?** Removing the second-to-last element in a for-each over an `ArrayList` does not throw, because `hasNext()` returns false before `next()` checks `modCount`. Do not rely on it.
- **Is fail-fast guaranteed?** No. It is best effort to catch bugs, not a thread safety mechanism.
- **Concurrent alternatives?** `CopyOnWriteArrayList` (iterates over a snapshot) and `ConcurrentHashMap` (weakly consistent iterators, never throws it).
- **Why `iterator.remove()` is safe?** It updates the iterator's expected modCount together with the list's.
- **`for (int i...)` loop and remove?** No exception, but indexes shift, so go backward or adjust `i`.

### Q66. What are immutable collections in Java? [Intermediate]

**Short answer:** An **immutable** collection cannot change after creation. `List.of`, `Set.of`, `Map.of` (Java 9+) and `List.copyOf` create truly immutable collections. `Collections.unmodifiableList` only gives a read-only **view** of a list that others may still change.

```java
List<String> fixed = List.of("a", "b");
fixed.add("c");
// throws UnsupportedOperationException

List<String> original = new ArrayList<>(List.of("a", "b"));
List<String> view = Collections.unmodifiableList(original);
original.add("c");
view.size();
// 3  (the view sees the change)

List<String> copy = List.copyOf(original);
original.add("d");
copy.size();
// 3  (a real snapshot, unaffected by "d")
```

| Creation | Mutable? | Nulls | Notes |
|---|---|---|---|
| `List.of(...)`, `Set.of(...)`, `Map.of(...)` | No | Not allowed (NPE) | `Set.of` and `Map.of` have unspecified iteration order |
| `List.copyOf(collection)` | No | Not allowed | Real copy |
| `Collections.unmodifiableList(list)` | Not through the view | Allowed | Reflects changes to the original |
| `Arrays.asList(...)` | `set` yes, `add`/`remove` no | Allowed | Backed by the array |
| `stream.toList()` (Java 16+) | No | Allowed | Unmodifiable list |
| `stream.collect(Collectors.toList())` | Unspecified (currently `ArrayList`) | Allowed | Do not assume mutability |

Note that immutability is shallow: the list cannot change, but the objects inside can if they are mutable.

**Follow-up questions:**
- **Why use immutable collections?** Thread safety without locks, safe to share and return from methods, and no defensive copies when stored.
- **`Set.of("a", "a")`?** Throws `IllegalArgumentException` for duplicates.
- **`Map.of` supports how many pairs?** Up to 10. Use `Map.ofEntries(...)` for more.
- **How do you make a class immutable?** `final` class, `private final` fields, no setters, defensive copies of mutable inputs and outputs. See Q41 in `D:\prep\java\INTERVIEW-QUESTIONS.md`.

---

## 7.3 Advanced

### Q67. How would you implement your own stack, queue and HashMap? [Advanced]

**Short answer:** A stack and queue are thin wrappers over an array or linked nodes. A HashMap is an array of buckets, where each bucket holds a linked list of entries (separate chaining).

**Array stack** (grows by doubling, so `push` is amortized O(1)):

```java
public class ArrayStack {

    private int[] data = new int[4];
    private int size = 0;

    public void push(int value) {
        if (size == data.length) {
            data = Arrays.copyOf(data, data.length * 2);
        }
        data[size] = value;
        size++;
    }

    public int pop() {
        if (size == 0) {
            throw new NoSuchElementException("stack is empty");
        }
        size--;
        return data[size];
    }

    public boolean isEmpty() {
        return size == 0;
    }
}
```

**Queue** options: a linked list with `head` (remove here) and `tail` (add here) pointers, both O(1). Or a **circular array** with a `head` index and a `size`: add at `(head + size) % capacity`, remove at `head` then `head = (head + 1) % capacity`. Resize by copying in order when full. This is how `ArrayDeque` works.

**HashMap with chaining:**

```text
buckets (array of 4)
[0] -> null
[1] -> (5,"e") -> (9,"i")      keys 5 and 9 both map to bucket 1 (hash % 4)
[2] -> null
[3] -> (3,"c")
```

```java
public class SimpleHashMap<K, V> {

    private static class Entry<K, V> {

        final K key;
        V value;
        Entry<K, V> next;

        Entry(K key, V value, Entry<K, V> next) {
            this.key = key;
            this.value = value;
            this.next = next;
        }
    }

    private Entry<K, V>[] buckets = newTable(8);
    private int size = 0;

    @SuppressWarnings("unchecked")
    private static <K, V> Entry<K, V>[] newTable(int capacity) {
        return (Entry<K, V>[]) new Entry[capacity];
    }

    private int indexFor(Object key, int capacity) {
        int hash = key == null ? 0 : key.hashCode();
        return (hash & 0x7fffffff) % capacity;
    }

    public V get(K key) {
        int index = indexFor(key, buckets.length);
        for (Entry<K, V> entry = buckets[index]; entry != null; entry = entry.next) {
            if (Objects.equals(entry.key, key)) {
                return entry.value;
            }
        }
        return null;
    }

    public void put(K key, V value) {
        int index = indexFor(key, buckets.length);
        for (Entry<K, V> entry = buckets[index]; entry != null; entry = entry.next) {
            if (Objects.equals(entry.key, key)) {
                entry.value = value;
                return;
            }
        }
        buckets[index] = new Entry<>(key, value, buckets[index]);
        size++;
        if (size > buckets.length * 0.75) {
            resize();
        }
    }

    private void resize() {
        Entry<K, V>[] bigger = newTable(buckets.length * 2);
        for (Entry<K, V> head : buckets) {
            for (Entry<K, V> entry = head; entry != null; entry = entry.next) {
                int index = indexFor(entry.key, bigger.length);
                bigger[index] = new Entry<>(entry.key, entry.value, bigger[index]);
            }
        }
        buckets = bigger;
    }
}
```

`& 0x7fffffff` clears the sign bit so the index is never negative.

**Complexity:** stack `push`/`pop` O(1) amortized. HashMap `get`/`put` O(1) average, O(n) worst case when all keys collide.

**Follow-up questions:**
- **How would you add `remove`?** Find the entry in its bucket and unlink it from the chain, keeping a `prev` pointer. Decrease `size`.
- **Other collision strategy?** **Open addressing**: on a collision, probe the next free slot in the array (linear probing). Faster for small keys but needs careful deletion and a lower load factor. Java's `HashMap` uses chaining.
- **Why resize at 0.75?** It balances memory use and collision rate. Higher means more collisions, lower wastes memory.
- **Why are bucket counts powers of two in the JDK?** Index is `hash & (n - 1)` instead of the slower modulo. This is why `HashMap` also spreads the hash bits first.
- **Thread safe stack or queue?** Wrap operations in `synchronized` blocks, or use `ConcurrentLinkedQueue` / `LinkedBlockingQueue` from the JDK.
