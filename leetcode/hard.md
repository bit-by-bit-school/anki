---
name: leetcode hard
---

# Largest Rectangle in Histogram (84)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
Given an array of integers heights representing the histogram's bar height where the width of each bar is 1, return the area of the largest rectangle in the histogram.

**Constraints:**

- `1 <= heights.length <= 105`
- `0 <= heights[i] <= 104`

</details>

. . .

Pattern: Monotonic Stack

Solution: Keep a running tally of maximum rectangle size so far. Use an increasing monotonic stack with the first array element added with a run size of 1. Iterate through the array. If the current element is greater than or equal to the top of the stack, add it to the stack with a run size of 1.

Otherwise, initialize a run count, starting at 0. Keep popping off the stack until a smaller number is encountered or the stack is empty. With each pop, increment the run count by the run size, and multiply the popped number with the count and check the value against max rectangle size, updating if needed. Finally, add the current number to the stack, with an existing run size of the count plus 1. Pop everything when you reach the end of the array.

Complexity: O(n) time, O(n) space.

# Bricks Falling When Hit (803)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
You are given an m x n binary grid, where each 1 represents a brick and 0 represents an empty space. A brick is stable if:

It is directly connected to the top of the grid, or
At least one other brick in its four adjacent cells is stable.
You are also given an array hits, which is a sequence of erasures we want to apply. Each time we want to erase the brick at the location hits[i] = (rowi, coli). The brick on that location (if it exists) will disappear. Some other bricks may no longer be stable because of that erasure and will fall. Once a brick falls, it is immediately erased from the grid (i.e., it does not land on other stable bricks).

Return an array result, where each result[i] is the number of bricks that will fall after the ith erasure is applied.

Note that an erasure may refer to a location with no brick, and if it does, no bricks drop.

**Constraints:**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 200`
- `grid[i][j] is 0 or 1.`
- `1 <= hits.length <= 4 * 104`
- `hits[i].length == 2`
- `0 <= xi <= m - 1`
- `0 <= yi <= n - 1`
- `All (xi, yi) are unique.`

</details>

. . .

Pattern: Process in Reverse + Union Find

Solution: Use a disjoint set and include a size operation, that tells us the size of this component. Whenever a component is made to point to a new parent, it's size is also sent to that parent.

An ephemeral source node is created where all nodes on the top edge (with row number 0) are connected to the source node. The variable 'top', is used to track the size of this component, the "roof".

Next, initialize the disjoint union structure as per the final state of the grid after all cuts have been made (nodes are grid squares with a brick; edges between 4-directionally adjacent nodes).

Now each cut is treated as a connection - and the value of top is compared. If it increases, that increase is the number of dropped bricks.

After each cut is processed, reverse the sequence of dropped brick counts to arrive at the final answer.

Complexity: O(n * a(n)) time, where a represents the inverse Ackermann function. O(n) space.

# Shortest Subarray with Sum at Least K (862)

<details>
<summary>Show problem statement and constraints</summary>

**Statement:**
Given an integer array nums and an integer k, return the length of the shortest non-empty subarray of nums with a sum of at least k. If there is no such subarray, return -1.

A subarray is a contiguous part of an array.

**Constraints:**

- `1 <= nums.length <= 105`
- `-105 <= nums[i] <= 105`
- `1 <= k <= 109`

</details>

. . .

Pattern: Prefix Sum Array + Deque

Solution: First construct a prefix sum array for the array. Next, create a deque to hold the indices of the prefix sums that might serve as the starting point for our target subarray. These sums must form a monotonically increasing sequence. This is because if an earlier prefix sum is greater than or equal to a later one, that later index will always give a shorter subarray with an equal or greater sum for any future ending position.

Iterate through each position. First, check if valid subarrays can be found using the indices stored in the deque. This is done by calculating the difference between the current prefix sum and the prefix sum at the front of the deque. If this difference meets or exceeds the target sum, this is a valid subarray. At this point, update shortestSubarrayLength and remove that starting index from the deque, since it won't help find a shorter subarray with any future ending positions.

Second, maintain the monotonicity of the deque. Remove indices from the back of the deque if their prefix sums are greater than or equal to the current prefix sum. This step is crucial because any removed positions would only yield longer subarrays with the same or smaller sums, making them unnecessary.

Finally, add the current index to the back of the deque because it could potentially be the starting point of a valid subarray in the future.

When iteration is completed, the variable shortestSubarrayLength will contain the length of the shortest such subarray.

Complexity: O(n) time, O(n) space.