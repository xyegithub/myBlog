---
title: Algorithm
top: false
cover: false
toc: true
mathjax: true
date: 2021-12-17 16:26:10
password:
summary:
description: 遇到过精巧的算法设计
categories:
  - Programming
tags:
  - Algorithm
  - Programming
---

# Depth First Search

## Longest Increasing Path in a Matrix

Given an $m \times n$ integers matrix, return the length of the longest
increasing path in matrix.

Move direction: left, right, up, or down.

```python
class Solution:

    DIRS = [(-1, 0), (1, 0), (0, -1), (0, 1)]

    def longestIncreasingPath(self, matrix: List[List[int]]) -> int:
        if not matrix:
            return 0

        @lru_cache(None) # save  the storage
        def find_max(i, j):
        # find the next value and try the four directions
        # return the longest direction.
            max_ = 1
            for dr in Solution.DIRS:
                try_i = i + dr[0]
                try_j = j + dr[1]
                # the direction will be discarded if do not meet the condition
                # and a direction meet the condition will be searched for the next
                # value first.
                if 0 <= try_i and try_i < n and 0<= try_j and try_j < m and \
                matrix[i][j] < matrix[try_i][try_j]:
                    max_ = max(max_, find_max(try_i, try_j) + 1)
                    # The value is increasing, so by setting `matrix[i][j] \
                    # < matrix[try_i][try_j]`, a searched position will never
                    # be searched again.
            return max_

        n, m = len(matrix), len(matrix[0])
        res = 0
        for i in range(n):
            for j in  range(m):
                res = max(res, find_max(i, j))
        return res

```
