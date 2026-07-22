---
name: leetcode medium
---

# Longest Substring Without Repeating Characters (3)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
Given a string s, find the length of the longest substring without duplicate characters.

**Constraints:**

- `0 <= s.length <= 5 * 104`
- `s consists of English letters, digits, symbols and spaces.`

</details>

. . .

Pattern: Sliding Window + Hashmap of Indices

Solution: Maintain a hashmap of characters against their indices and a running current length. If the character at the right pointer is not in the hashmap, or its stored index is before the left pointer, record its index, advance the right pointer, increase the current length by 1, and update the maximum. Otherwise, move the left pointer to the index after the repeat and reset the current length to right minus left, without advancing the right pointer — it gets processed as a fresh character on the next pass.

Complexity: O(n) time, O(min(n, charset size)) space.

# Three Sum (15)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
Given an integer array nums, return all the triplets [nums[i], nums[j], nums[k]] such that i != j, i != k, and j != k, and nums[i] + nums[j] + nums[k] == 0.

Notice that the solution set must not contain duplicate triplets.

**Constraints:**

- `3 <= nums.length <= 3000`
- `105 <= nums[i] <= 105`

</details>

. . .

Pattern: Sorting + Two Pointers

Solution: Sort the input. Then for each element, initialize two pointers at the start and end of the array. If the sum is less than 0, advance the left pointer, otherwise if it is more move back the right pointer.

Complexity: O(n^2) time, O(1) space.

# Linked List Cycle II (142)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
Given the head of a linked list, return the node where the cycle begins. If there is no cycle, return null.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the next pointer. Internally, pos is used to denote the index of the node that tail's next pointer is connected to (0-indexed). It is -1 if there is no cycle. Note that pos is not passed as a parameter.

Do not modify the linked list.

**Constraints:**

- `The number of the nodes in the list is in the range [0, 104].`
- `105 <= Node.val <= 105`
- `pos is -1 or a valid index in the linked-list.`

</details>

. . .

Pattern: Two Pointers

Solution: Determine if there's a cycle using two pointers, one fast and one slow. If the two pointers meet, there's a cycle. When they meet, create another pointer at the head of the list. Now move both the new pointer and earlier slow pointer one step at a time until they meet. This is the starting node of the cycle.

Complexity: O(n) time, O(1) space.

# House Robber (198)

<details>
<summary>Problem Statement</summary>

You are a robber planning to rob houses along a street, where each house has some amount of money stashed. You cannot rob two adjacent houses (robbing both triggers an alarm). Given an array of non-negative integers representing the money in each house, return the maximum amount you can rob without robbing two adjacent houses.

Constraints:

- 1 <= nums.length <= 100
- 0 <= nums[i] <= 400

</details>

. . .

**Pattern**

Dynamic Programming 1D — top-down memoized recursion via closure

**Solution**

For a given starting index, the best achievable amount is either: rob the house at that index and add the best amount achievable from two indices ahead, or skip it and take the best amount achievable from one index ahead — whichever is larger. Results are cached by index in a dictionary that lives outside the recursive calls, so each index is only computed once. The last one or two indices are base cases, returning the maximum of the remaining houses directly. The answer is the result for index 0.

# Binary Tree Right Side View (199)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
Given the root of a binary tree, imagine yourself standing on the right side of it, return the values of the nodes you can see ordered from top to bottom.

**Constraints:**

- `The number of nodes in the tree is in the range [0, 100].`
- `100 <= Node.val <= 100`

</details>

. . .

Pattern: Breadth First Search

Solution: When adding a node to the queue, also add its level. Write the node seen against the level in a hashmap. Process the left subtree before the right so that the final values will be the rightmost ones.

Complexity: O(n) time, O(height) space.

# Minimum Size Subarray Sum (209)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
Given an array of positive integers nums and a positive integer target, return the minimal length of a subarray whose sum is greater than or equal to target. If there is no such subarray, return 0 instead.

**Constraints:**

- `1 <= target <= 109`
- `1 <= nums.length <= 105`
- `1 <= nums[i] <= 104`

</details>

. . .

Pattern: Sliding Window

Solution: Increase the right pointer when the current sum is at or below the target, and the left pointer when we exceed the target, while tracking the minimum length found so far.

Complexity: O(n) time, O(1) space.

# Product of Array Except Self (238)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
Given an integer array nums, return an array answer such that answer[i] is equal to the product of all the elements of nums except nums[i]without using the division operation.

**Constraints:**

- `2 <= nums.length <= 105`
- `-30 <= nums[i] <= 30`
- `The input is generated such that answer[i] is guaranteed to fit in a 32-bit integer.`

</details>

. . .

Pattern: Prefix Product Array

Solution: Calculate a prefix product array and a suffix product array. Then each entry is the product of the left hand side prefix and the right hand side suffix.

Memory usage can be further reduced by avoiding constructing a suffix array and instead processing in reverse. In that case, the ongoing product calculation can be used as the suffixes at each step.

Complexity: O(n) time, O(1) space other than output.

# Binary Tree Longest Consecutive Sequence (298)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
You are given the root of a binary tree. Your task is to find the length of the longest consecutive sequence path in the tree.

A consecutive sequence path is defined as a path where each node's value is exactly one more than its parent node's value in the path. For example, a path with values 1 → 2 → 3 is a consecutive sequence.

**Constraints:**

- `The path can start at any node in the tree (not necessarily the root)`
- `The path must follow parent-to-child connections only (you cannot traverse from a child back to its parent)`
- `The values must increase by exactly 1 at each step along the path`

</details>

. . .

Pattern: Post Order Depth First Search

Solution: Keep a global track of the maximum length found thus far. Starting from the root, recursively compute the longest sequences starting from the left and right children and add 1 to each. Then, for each child, check if it continues the sequence (i.e. child.val - current.val = 1?). If it doesn't, then that child's sequence value is set to 1. Take the maximum of the two sequence values, and compare it versus the global maximum.

Complexity: O(n) time and O(height of tree) space.

# Insert Delete Get Random (380)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
Implement the RandomizedSet class:

- RandomizedSet() Initializes the RandomizedSet object.
- bool insert(int val) Inserts an item val into the set if not present. Returns true if the item was not present, false otherwise.
- bool remove(int val) Removes an item val from the set if present. Returns true if the item was present, false otherwise.
- int getRandom() Returns a random element from the current set of elements (it's guaranteed that at least one element exists when this method is called). Each element must have the same probability of being returned.

**Constraints:**

- `-231 <= val <= 231 - 1`
- `At most 2 * 105 calls will be made to insert, remove, and getRandom.`
- `There will be at least one element in the data structure when getRandom is called.`

</details>

. . .

Pattern: Hashmap and Array

Solution: Stores all the values in an array. The hashmap stores a value as key against its index in the array. On delete, the map is checked, and then the value is removed from both. Insert proceeds similarly. The array is used for getting a random element.

Complexity: O(1) time (amortized), O(n) space.

# Daily Temperatures (739)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
Given an array of integers temperatures represents the daily temperatures, return an array answer such that answer[i] is the number of days you have to wait after the ith day to get a warmer temperature. If there is no future day for which this is possible, keep answer[i] == 0 instead.

**Constraints:**

- `1 <= temperatures.length <= 105`
- `30 <= temperatures[i] <= 100`

</details>

. . .

Pattern: Monotonic Stack / Process in Reverse

Monotonic Stack Solution:
Iterate from left to right, pushing onto the stack whenever encountering a temperature that is equal to or less than the current top of the stack. When a temperature that is higher is encountered, pop the stack and calculate the index difference to store it as a result. Then compare the higher temperature to the top of the stack again and repeat or proceed accordingly.

Complexity: O(n) time and space; works on streamed input.

Process in Reverse Solution:
The rightmost number is assigned 0, and then every number thereafter is assigned by repeatedly comparing and looking up in the output array. The idea is to skip comparisons with numbers we already have determined won't be larger.

So for any number, we compare it to its rightmost neighbor, and then if the neighbor is larger, we assign the difference between the indices of our number and the compared number (in this case 1). Otherwise, we check the neighbor's already computed output value, and if 0, we assign 0. Otherwise, we jump the output value number of spaces to the right, and redo this comparison until termination.

Complexity: O(n) time and O(1) space.

# Product of Last K Numbers (1352)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
Design an algorithm that accepts a stream of integers and retrieves the product of the last k integers of the stream.

Implement the ProductOfNumbers class:

- ProductOfNumbers() Initializes the object with an empty stream.
- void add(int num) Appends the integer num to the stream.
- int getProduct(int k) Returns the product of the last k numbers in the current list. You can assume that always the current list has at least k numbers.

**Constraints:**

- `0 <= num <= 100`
- `1 <= k <= 4 * 104`
- `At most 4 * 104 calls will be made to add and getProduct.`
- `The product of the stream at any point in time will fit in a 32-bit integer.`

</details>

. . .

Pattern: Prefix Sum Array

Solution: Modified prefix sum array, which instead contains products. We reset this array every time we insert a zero. The product for any k is determined simply by dividing the most recent prefix product by the prefix product at k indices before.

Complexity: O(1) time for add and getProduct. O(n) space.
