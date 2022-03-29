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

# Other

## The Gas Station

There are `n` stations along a circular route, where the amount of gas at the
$i^{th}$ station is gas[i].

A car costs cost[i] of gas to travel from the $i^{th}$ station to its next
$(i + 1)^{th}$ station.

Can the car run a circle. If can, return the start station. If not, return -1.
If there exists a solution, it is guaranteed to be the unique.

```python
class Solution:
    def canCompleteCircuit(self, gas: List[int], cost: List[int]) -> int
        n = len(gas)
        res = [0] * n
        # a car pass station i, it can get gas[i] and cost
        # cost[i]. Thus gets gas[i] - cost[i].

        # if in station i, gas[i] - cost[i] >= 0, it can
        # arrive the next station.
        # after arrive the next station, and gets the station
        # if the remain >= 0 it can gets the next next station.
        for i in range(n):
            res[i] = gas[i] - cost[i]
        first = 0
        second = 0
        sum_ = res[0]
        key = False
        # second represents the station the car already getting.
        # first represents the starting station.
        # sum_ represents if the car can get the next staion.
        # if sum_ >= 0, it can, else, it can not.
        while True:
            if sum_ >= 0:
                second += 1
                if second == n:
                    second = 0
                    key = True
                if key and second == first:
                    return first
                sum_ += res[second]
            else:
                sum_ -= res[first]
                first += 1
                # from 0 to n, first do not find an answer, return -1.
                if first == n:
                    return -1
                else:
                    if first > second:
                        second += 1
                        sum_ += res[second
```

Consider this, if a car can not arrive y from x. That is to say
$\sum_{i = y}^x res[i] < 0$. For a station z between x and y. It can arrive z
from x, which means the sum $> 0$. Then from z to y, the sum is definitely
$< 0$. It can not arrive y from z.

Thus, if a car can not arrive y from x, it can not arrive y from any station
between x and y. It is no need to search such stations.

```python
class Solution:
    def canCompleteCircuit(self, gas: List[int], cost: List[int]) -> int:
        n = len(gas)
        res = [0] * n
        for i in range(n):
            res[i] = gas[i] - cost[i]
        first = 0
        second = 0
        sum_ = res[0]
        key = False
        while True:
            if sum_ >= 0:
                second += 1
                if second == n:
                    second = 0
                    key = True
                if key and second == first:
                    return first
                sum_ += res[second]
            else:
            # if can not arrive second + 1, then
            # starting from second + 1
                second += 1
                first = second
                # if the new starting point equal or bigger than
                # n, first move from 0 to n fails to find.
                # if key is True, second + 1 is actually larger than
                # n, first move from 0 to n fails to find.
                if first == n or key:
                    return -1
                sum_ = res[first]
```
