---
name: leetcode easy
---

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

Pattern: Single pass tracking the running minimum

Solution: Track `min_price` and `max_profit`. For each `price`, update `min_price = min(min_price, price)`, then `max_profit = max(max_profit, price - min_price)`. Return `max_profit`.

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
