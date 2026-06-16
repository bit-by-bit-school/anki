---
name: leetcode
---

# Valid Palindrome

<details>
<summary>Show problem statement and constraints</summary>

A phrase is a palindrome if, after converting all uppercase letters into lowercase letters and removing all non-alphanumeric characters, it reads the same forward and backward. Alphanumeric characters include letters and numbers.
Given a string s, return true if it is a palindrome, or false otherwise.

Constraints:

- $1 <= s.length <= 2 * 105$
- s consists only of printable ASCII characters.

</details>

. . .

Pattern: Two Pointers

Solution: Filter out non-alphanumeric characters, then compare inward. A mismatch at any point means the input is not a palindrome
