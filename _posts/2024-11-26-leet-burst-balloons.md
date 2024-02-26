---
layout: post
title: "Burst Balloons: Dynamic Programming Explained"
date: 2024-11-26 19:00:00
description: A deep dive into solving the Burst Balloons problem with dynamic programming, including code examples and visualizations.
tags: algorithms, dynamic-programming, competitive-programming
categories: dynamic-programming
---

## Problem Description

The **Burst Balloons** problem is a challenging dynamic programming question. Given an array of balloons `nums`, you must burst them in an order that maximizes the coins collected. Bursting a balloon \(i\) gives you coins equal to:

\[
\text{nums}[i-1] \times \text{nums}[i] \times \text{nums}[i+1]
\]

The neighbors of \(i\) are updated as balloons are burst. If a balloon index goes out of bounds, treat it as having a value of \(1\). Your goal is to calculate the **maximum coins** you can collect.

---

### Example 1:
**Input:**
```plaintext
nums = [3, 1, 5, 8]
```

**Output:**
```plaintext
167
```

**Explanation:**
The optimal bursting order is:
```
[3, 1, 5, 8] -> [3, 5, 8] -> [3, 8] -> [8] -> []
```

Coins collected:
```
3 * 1 * 5 + 3 * 5 * 8 + 1 * 3 * 8 + 1 * 8 * 1 = 167
```

---

## Key Observations

- The order of bursting balloons significantly affects the result.
- The problem cannot be solved greedily because the "local best" decision (bursting the largest balloon first, for example) does not necessarily lead to the global optimum.
- This problem has **overlapping subproblems** and an **optimal substructure**, making it ideal for **dynamic programming**.

---

## Dynamic Programming Approach

### Idea:
Define a DP table `dp[l][r]` where:
\[
dp[l][r] = \text{maximum coins you can collect by bursting balloons in the range } [l, r]
\]

### Transition Formula:
If you burst balloon \(k\) last in the range \([l, r]\), the coins collected are:
\[
\text{coins} = \text{nums}[l-1] \times \text{nums}[k] \times \text{nums}[r+1]
\]
And the subproblem becomes:
\[
dp[l][r] = dp[l][k-1] + dp[k+1][r] + (\text{nums}[l-1] \times \text{nums}[k] \times \text{nums}[r+1])
\]

### Base Case:
If \(l > r\) (invalid range), set:
\[
dp[l][r] = 0
\]

---

### Steps to Solve

1. **Pad the Input Array**:
   Add \(1\) at both ends of the array to handle boundary conditions easily. For example:
   ```
   nums = [3, 1, 5, 8]
   becomes:
   nums = [1, 3, 1, 5, 8, 1]
   ```

2. **Iterate Over Subproblem Sizes**:
   Solve smaller ranges (e.g., size 1) before larger ranges (e.g., size 2, size 3, etc.).

3. **Compute DP Transitions**:
   For each range \([l, r]\), consider all balloons \(k\) as the last balloon to burst, and use the transition formula to calculate \(dp[l][r]\).

4. **Final Answer**:
   The answer is stored in \(dp[1][n]\), where \(n\) is the original size of the array.

---

## Code Implementation (C++)

Here’s the full solution in **C++**:

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int maxCoins(vector<int>& nums) {
        int n = nums.size();
        // Pad the array with 1s at both ends
        nums.insert(nums.begin(), 1);
        nums.push_back(1);
        
        // Create a DP table
        vector<vector<int>> dp(n + 2, vector<int>(n + 2, 0));
        
        // Iterate over the length of the range
        for (int length = 1; length <= n; length++) {
            for (int left = 1; left <= n - length + 1; left++) {
                int right = left + length - 1;
                
                // Iterate over all possible 'last burst' balloons
                for (int k = left; k <= right; k++) {
                    dp[left][right] = max(dp[left][right],
                        dp[left][k-1] + dp[k+1][right] +
                        nums[left-1] * nums[k] * nums[right+1]);
                }
            }
        }
        
        return dp[1][n];
    }
};
```

---

### Dry Run

#### Input:
```plaintext
nums = [3, 1, 5, 8]
```

#### Steps:
1. Pad the array:
   ```plaintext
   nums = [1, 3, 1, 5, 8, 1]
   ```

2. Solve subproblems for ranges of increasing size:
   - **Size 1:** Calculate \(dp[i][i]\).
   - **Size 2:** Calculate \(dp[i][i+1]\).
   - Continue until the full range \([1, n]\).

3. Final DP Table:
   ```
   dp[1][4] = 167
   ```

4. Return \(dp[1][4]\) as the result.

---

## Complexity Analysis

1. **Time Complexity**:
   - Three nested loops:
     - Length of the range (\(O(n)\)),
     - Left boundary (\(O(n)\)),
     - Last balloon \(k\) (\(O(n)\)).
   - Total: \(O(n^3)\).

2. **Space Complexity**:
   - DP table of size \(O(n^2)\).

---

## Visual Example with DP Transitions

Here’s an ASCII visualization of the transitions for bursting balloons in a range \([l, r]\).

### Example Range: [2, 4] in [1, 3, 1, 5, 8, 1]
1. If \(k = 2\) is the last balloon:
   \[
   dp[2][4] = dp[2][1] + dp[3][4] + (nums[1] \times nums[2] \times nums[5])
   \]

2. If \(k = 3\) is the last balloon:
   \[
   dp[2][4] = dp[2][2] + dp[4][4] + (nums[1] \times nums[3] \times nums[5])
   \]

3. If \(k = 4\) is the last balloon:
   \[
   dp[2][4] = dp[2][3] + dp[5][4] + (nums[1] \times nums[4] \times nums[5])
   \]

Take the maximum of these values for \(dp[2][4]\).

---

## Key Takeaways

- The **Burst Balloons** problem demonstrates the power of dynamic programming to tackle complex problems involving choices and dependencies.
- Using **range-based subproblems** and considering the "last action" simplifies the problem.
- While the time complexity is \(O(n^3)\), this approach is efficient for the given constraints (\(n \leq 300\)).

Feel free to try this approach on other range-based DP problems!

---

**Let me know your thoughts on this problem or if you have questions!**