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

## Keyboard

A keyboard only have 26 keys, `a~z`. Each key can be only typed `k` times at
most.

How many possible content there is, when the keyboard is typed `n` times?

### Method 1: dfs

```python
 class Solution:
    # depth first search: from the first letter to the last letter
    # fill the n positions.
    def keyboard(self, k: int, n: int) -> int:
        MOD = 1000000007

        # c is the number of remain letters
        # n is the number of letters needed to type in
        # k is the max number that each letter can type
        @lru_cache(None)
        def dfs(c, n, k):
            if n == 0:
                # no letters is needed any more
                # only 1 way: type nothing
                # pick 0 letters
                return 1
            if c <= 0:
                # no letter remained but n is not 0
                # this is a failed type scheme
                return 0
            res = 0
            for i in range(0, min(n, k) + 1):
            # there are math.comb(n, i) ways to put i letters
            # into n position.
                res += math.comb(n, i) * dfs(c-1, n - i, k) % MOD
            return res % MOD
        ans = dfs(26, n, k)
        return ans % MOD
```

### Method 2: dynamic programming

```python
class Solution:
    def keyboard(self, k: int, n: int) -> int:
        MOD = 1000000007
        res = [[0 for _ in range(27)] for _ in range(n + 1)]
        # res[i][j] is the number of possibilities when first j letters
        # is used to fill i positions.
        # when the position is 0, no matter how many letters are used.
        # there is only one possibility, i.e., filling nothing.
        # thus, res[0][i] are all 1.
        res[0][0] = 1
        for i in range(27):
            res[0][i] = 1

        # res[i][j] can be divided into several possibilities.
        # When 0 letter is filled for the $j^{th}$ letter, res[i][j-1]
        # when 1 letter is filled for the j-th letter, res[i-1][j-1]
        # * C_i^1
        # when 2 letters are filled for the j-th letter, res[i-2][j-1]
        # * C_i^2
        # C_i^m represent how many possibilities there are filling m of
        # the same letters into i positions.
        # m can be 0, but can not be larger than k or i.
        for i in range(1, n + 1):
            for j in range(1, 27):
                for m in range(min(i + 1, k + 1)):
                    res[i][j] += res[i - m][j - 1] * math.comb(i, m)
                    res[i][j] %= MOD

        return res[-1][-1] % MOD
```

# Dynamic Programming

## The number of palindromes

Given a string return the number of the palindromes. The substrings are regarded
as different strings if they are start and end at different position.

```python
class Solution:
    def countSubstrings(self, s: str) -> int:
        n = len(s)
        res = [[False for _ in range(n)] for _ in range(n)]
        # if n == 0, there are 0 palindromes.
        # if n == 1, there are 1 palindromes.
        if not n or n == 1:
            return n
        ans = n

        for i in range(n):
            res[0][i] = True

        for i in range(n - 1):
            res[1][i] = s[i] == s[i + 1]
            if res[1][i]:
                ans += 1
# if n == 2 it will not enter the following circle.
# if n > 2 it will contnue to caculate palindromes whose length is larger than 2
        for l in range(2, n):
            for st in range(n - l):
                res[l][st] = res[l - 2][st + 1] and s[st] == s[st + l]
                if res[l][st]:
                    ans += 1
        return ans
```

## Knight Dialer

The topic is [here](https://leetcode-cn.com/problems/knight-dialer/).

```python
class Solution(object):
    def knightDialer(self, N):
        MOD = 10**9 + 7
        # From 0 can reach 4 and 6
        # From 1 can reach 6 and 8
        moves = [[4,6],[6,8],[7,9],[4,8],[3,9,0],[],
                     [1,7,0],[2,6],[1,3],[2,4]]

        # There is dp[i]==1 ways to reach i by one step
        dp = [1] * 10
        for hops in range(N-1):
            dp2 = [0] * 10
            for node, count in enumerate(dp):
              # There are count way to reach node by hops + 1 steps
                for nei in moves[node]:
                    dp2[nei] += count
                    dp2[nei] %= MOD
              # There are dp2[nei] way to reach nei by hops + 2 steps
            dp = dp2
        return sum(dp) % MOD
```

# Linked List

## Palindrome Linked List

Given the `head` of a singly linked list, return `true` if it is a Palindrome.

```python
class Solution:
    def isPalindrome(self, head: ListNode) -> bool:
        res = []
        if not head:
            return True
        num = 0
        first = head
        while first.next:
            res.append(first.val)
            num += 1 # count the length of the linked list
            first = first.next
        res.append(first.val) # store all the node value
        for i in range(num // 2 + 1): # does not need to check all the nodes
            if res[i] != res[num - i]:
                return False
        return True
```

If one want only use O(n) time and O(1) storage, must reverse the last half of
the linked list.

```python
class Solution:
    def isPalindrome(self, head: ListNode) -> bool:
        if not head or not head.next:
            return True
        def find_the_middle(node):
            slow = node
            fast = node
            while fast.next and fast.next.next:
                slow = slow.next
                fast = fast.next.next
            second_start = slow.next
            slow.next = None
            return second_start
        second_start = find_the_middle(head)
        def reverse(node):
            start = node
            pre = node.next
            start.next = None
            while pre:
                cur = pre
                pre = pre.next
                cur.next = start
                start = cur
            return start
        new_start = reverse(second_start)
        while head and new_start:
            if head.val != new_start.val:
                return False
            head = head.next
            new_start = new_start.next
        return True
```

# Sort

## Wiggle Sort 2

Given an integer array `nums`, recorder it such that
`nums[0] < nums[1] > nums[2] < nums[3] > ...`. You may assume the input array
always has a valid answer.

```python
class Solution:
    def wiggleSort(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        res = []
        for i in nums:
            res.append(i)
        res.sort()
        n = len(nums)
        # Because the smaller number is always filled first,
        # let len(smaller numbers) - len(larger numbers) == 1
        # if the len(nums) is odd.
        # if n is even, the small half from 0 to n // 2 - 1
        # the large half from n // 2 to n - 1
        # if n is odd, small number from 0 to n // 2
        # large number from n // 2 + 1 to n - 1,
        # Thus, no matter n is odd or even, small number from 0 to
        # (n + 1) // 2 - 1, large number from (n + 1) // 2 to n - 1
        half = (n + 1) // 2
        # to avoid that the last small number is equal to the first large
        # number, fill the small number from *last* to first,
        # also, fill the large number from last to *first*
        # ** means that the number should try to be apart.

        # if n is even, half * 2 == n
        # if n is odd, half * 2 == n + 1
        for i in range(half):
            nums[2 * i] = res[half - 1 -i]
            # len(large number) is smaller than len(small number),
            # thus, the when i = half -1, maybe no large number anymore
            try:
                nums[2 * i + 1] = res[n - 1 - i]
            except:
                pass
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

Consider this, if y is the first station that the car can not arrive from x.
That is to say $\sum_{i = y}^x res[i] < 0$. For a station z between x and y. It
can arrive z from x, which means the sum $> 0$. Then from z to y, the sum is
definitely $< 0$. It can not arrive y from z.

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
