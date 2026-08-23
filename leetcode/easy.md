---
name: leetcode easy
---

# Two Sum (1)

<details>
<summary>Show problem statement and constraints</summary>

Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

You can return the answer in any order.

Constraints:

- `1 <= nums.length <= 10^5`
- `-10^9 <= nums[i] <= 10^9`
- `-10^9 <= target <= 10^9`

</details>

. . .

**Pattern:** Hashing

**Solution:** Create a hashmap to store `number` and its `index` as key-value pairs. For each number in the input array, calculate `complement = target - number`. If the complement exists in the hashmap, return its index and the current index. Otherwise, add the current number and its index to the hashmap.

**Complexity:** O(n) time and O(n) space

# Remove Duplicates from Sorted Array (26)

<details>
<summary>Show problem statement and constraints</summary>

Given an integer array `nums` sorted in non-decreasing order, remove the duplicates in-place such that each unique element appears only once. The relative order of the elements should be kept the same. Return `k`, the number of unique elements, after placing them at the front of `nums`.

**Constraints:**

- `1 <= nums.length <= 3 * 10^4`
- `-100 <= nums[i] <= 100`
- `nums is sorted in non-decreasing order`

</details>

. . .

Pattern: Two pointers

Solution: Anchor pointer at last confirmed-unique element (starts at index 0), scan pointer starts one ahead. Advance scanner continuously; on a differing value, advance anchor and copy scanner's value into it, incrementing count.

Complexity: O(n) time and O(1) space

# Merge Sorted Array (88)

<details>
<summary>Show problem statement and constraints</summary>

You are given two integer arrays nums1 and nums2, sorted in
non-decreasing order, and two integers m and n, representing the
number of elements in nums1 and nums2 respectively.

Merge nums1 and nums2 into a single array sorted in non-decreasing
order.

The final sorted array should not be returned by the function, but
instead be stored inside the array nums1. To accommodate this,
nums1 has a length of m + n, where the first m elements denote the
elements that should be merged, and the last n elements are set to
0 and should be ignored. nums2 has a length of n.

Constraints:

- `nums1.length = m + n`
- `nums2.length = n`
- `0 <= m, n <= 200`
- `1 <= m + n <= 200`
- `-10^9 <= nums1[i], nums2[j] <= 10^9`

</details>

. . .

**Pattern:** Two Pointers

**Solution:** Place a pointer at index m-1 in nums1 and another at index n-1 in nums2. Place a third pointer
at index m+n-1 in nums1. Compare the first two pointers' values and place the greater at the third pointer's
position, decrementing whichever of the first two supplied it. If the first pointer has moved past
index 0 (all of nums1's original elements placed), skip the comparison and place the second pointer's
value directly. The third pointer decreases with every comparison. Once the second pointer has placed
all its values, the remaining elements in nums1 are already in their correct positions.

Complexity: O(m+n) time and O(1) space

# Best Time to Buy and Sell Stock (121)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
Given an array `prices` where `prices[i]` is the price of a given stock on day `i`, return the maximum profit you can achieve from buying on one day and selling on a later day. If no profit is possible, return 0.

**Constraints:**

- `1 <= prices.length <= 10^5`
- `0 <= prices[i] <= 10^4`

</details>

. . .

Pattern: Single pass tracking the running minimum, sliding window

Solution:
Tracking the running minimum: Track `min_price` and `max_profit`. For each `price`, update `min_price = min(min_price, price)`, then `max_profit = max(max_profit, price - min_price)`. Return `max_profit`.

Sliding Window: Anchor the left pointer at index `0`, scan the right pointer from left to right starting at
index `1`. Calculate `current_profit` through difference of left and right pointers, when it is `< 0` move
the left pointer to the right pointer and keep updating the `max_profit` when `current_profit > max_profit`
as the right pointer scans the array.

Complexity: O(n) time and O(1) space

# Valid Palindrome (125)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
A phrase is a palindrome if, after converting all uppercase letters into lowercase letters and removing all non-alphanumeric characters, it reads the same forward and backward. Alphanumeric characters include letters and numbers.
Given a string s, return true if it is a palindrome, or false otherwise.

**Constraints:**

- `1 <= s.length <= 2 * 105`
- s consists only of printable ASCII characters.

</details>

. . .

Pattern: Two Pointers

Solution: Filter out non-alphanumeric characters, then compare inward. A mismatch at any point means the input is not a palindrome

Complexity: O(n) time and O(n) space

# Contains Duplicates (217)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
Given an integer array nums, return true if any value appears at least twice in the array, and return false if every element is distinct.

**Constraints:**

- `1 <= nums.length <= 105`
- `109 <= nums[i] <= 109`

</details>

. . .

Pattern: Hashing

Solution: Iterate through the input array and create a hashmap simultaneously, mapping number to its frequency. At any point if the number already exists in the hashmap then return True. At the end of the iteration, return False.

Complexity: O(n) time and O(n) space

# Contains Duplicates 2 (219)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
Given an integer array nums and an integer k, return true if there are two distinct indices i and j in the array such that `nums[i] == nums[j]` and `abs(i - j) <= k`.

**Constraints:**

- `1 <= nums.length <= 105`
- `-109 <= nums[i] <= 109`
- `0 <= k <= 105`

</details>

. . .

Pattern: Sliding Window

Solution: Maintain a sliding window of the last k elements using a hash set. For each `nums[i]`, check whether it is already in the set. If yes, return True. Otherwise add it to the set. If the window grows beyond k, remove the element that falls out. Return False if no duplicate is found.

Complexity: O(n) time and O(min(n,k)) space

# Valid Anagram (242)

<details>
<summary>Show problem statement and constraints</summary>

Statement:

Given two strings s and t, return true if t is an anagram of s, and false otherwise.

Constraints:

- `1 <= s.length, t.length <= 5 * 104`
- s and t consist of lowercase English letters.

</details>

...

Pattern: Hashing

Solution: Create a hashmap with one string, mapping distinct letter to its frequency in the string. Iterate through the other string checking if each letter exists in the hashmap. After the iteration completes, for valid anagrams, the values in the hashmap should all be zero.

Complexity: O(n) time and O(n) space

# K-diff Pairs in an Array (532)

<details>
<summary>Show problem statement and constraints</summary>

Given an array of integers `nums` and an integer `k`, return the number of unique k-diff pairs in the array.

A k-diff pair is an integer pair `(nums[i], nums[j])` where:
- `0 <= i, j < nums.length`
- `i != j`
- `|nums[i] - nums[j]| == k`

Constraints:

- `1 <= nums.length <= 10^4`
- `-10^7 <= nums[i] <= 10^7`
- `0 <= k <= 10^7`

</details>

. . .

**Pattern:** Hashing

**Solution:** Maintain a hashmap of frequencies for all numbers in the array. Iterate through the unique keys in the hashmap. If `k > 0`, check if `num + k` exists in the hashmap to form a pair. If `k == 0`, check if the frequency of the current `num` is strictly greater than 1.

**Complexity:** O(n) time and O(n) space


# Min Cost Climbing Stairs (746)

<details>
<summary>Min Cost Climbing Stairs</summary>

You are given an integer array `cost` where `cost[i]` is the cost of the `i`-th step on a staircase. Once you pay the cost, you can either climb one or two steps. You can either start from the step with index 0, or the step with index 1. Return the minimum cost to reach the top of the floor (i.e., one step past the last index in `cost`).

Constraints:

- `2 <= cost.length <= 1000`
- `0 <= cost[i] <= 999`

</summary>
</details>

. . .

**Pattern:** 1D DP — bottom-up tabulation, in-place

**Solution:** Append a 0 to `cost` representing the top floor. Starting from the third-to-last index and moving left, overwrite each `cost[i]` with `cost[i] + min(cost[i+1], cost[i+2])` — the cost of this step plus the cheaper of the two ways to finish from one or two steps ahead. Once every index has been overwritten this way, the answer is the cheaper of `cost[0]` and `cost[1]`, since you can start from either.

Complexity: O(n) time and O(1) space

# Concatenation of an Array (1929)

<details>
<summary>Show problem statement and constraints</summary>

Statement:
Given an integer array nums of length n, you want to create an array ans of length 2n where `ans[i] == nums[i]` and `ans[i + n] == nums[i]` for `0 <= i < n` (0-indexed).

Specifically, ans is the concatenation of two nums arrays.

Return the array ans.

Constraints:

- `n == nums.length`
- `1 <= n <= 1000`
- `1 <= nums[i] <= 1000`

</details>

...

Pattern: Simulation

Solution: Initialize an array of length twice the input array and set all its values to 0. Every element with index i and i + n, n being the length of the input array, are equal.

Complexity: O(n) time and O(n) space

# Count Number of Pairs With Absolute Difference K (2006)

<details>
<summary>Show problem statement and constraints</summary>

Given an integer array `nums` and an integer `k`, return the number of pairs `(i, j)` where `i < j` such that `|nums[i] - nums[j]| == k`.

The value of `|x|` is the absolute value of `x`.

Constraints:

- `1 <= nums.length <= 200`
- `1 <= nums[i] <= 100`
- `1 <= k <= 99`

</details>

. . .

**Pattern:** Hashing

**Solution:** Create a hashmap to store `number` and its `frequency` as key-value pairs. For each number in the input array, check if `num + k` or `num - k` exists in the hashmap. If they do, add their frequencies to the total pairs count. Afterward, increment the current number's frequency in the hashmap.

**Complexity:** O(n) time and O(n) space
