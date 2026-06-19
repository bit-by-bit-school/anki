---
name: leetcode
---

# Valid Palindrome

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

# Contains Duplicates 2

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
