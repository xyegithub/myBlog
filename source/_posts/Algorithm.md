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

# String

## Minimum Changes to Make Alternating Binary String

You are given a string `s` consisting only of the characters `0` and `1`. In one
operation, you can change any `0` to `1` or vice versa.

The string is called alternating if no two adjacent characters are equal. Return
the minimum number of operations needed to make `s` alternating.

```python
class Solution:
    def minOperations(self, s: str) -> int:
        n = len(s)
        # The ans is the operation needed to turn s into `010101`
        ans = 0
        for i in range(n):
            if i % 2 and s[i] != "1":
              ans += 1
            if not i % 2 and s[i] != "0":
                ans += 1
        return min(ans, n - ans)
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

## Rank Teams by Votes

In a special ranking system, each voter gives a rank from highest to lowest to
all teams participated in the competition, e.g., one gives `ABC`, one gives
`BCA`.

The ordering of teams is decided by who received the most position-one votes. If
`A` received the most 1-st place Then `A` is the first. If `B` received the same
first place as `A` then compare the number of second place they received. If `A`
and `B` received all the places the same number then ordered them by their
letter, i.e., `A` is in front of `B`.

```python
class Solution:
    def rankTeams(self, votes: List[str]) -> str:
        n = len(votes[0])
        # initialization of a hash map, the default value is list [0, 0, ..., 0]
        # NOTE: How to set defaultdict by lambda without input, it is indeed a function factory.
        ranking = collections.defaultdict(lambda: [0] * n)
         # count the vote situation for each team, vid is the name of teams.
        for vote in votes:
            for i, vid in enumerate(vote):
                ranking[vid][i] += 1

        # result is a list, e.g., [('A', [5, 0, 0]), ('B', [0, 2, 3]), ('C', [0, 3, 2])]
        result = list(ranking.items())
        # sort the order of letter -ord(x[0]) is regard as the last criterion
        result.sort(key=lambda x: (x[1], -ord(x[0])), reverse=True)
        # return the ordered vid
        return "".join([vid for vid, rank in result])
```

## Number Game

Give a list of numbers. You can `+ 1` or `- 1` at position `i` which is regarded
as one operator at position `i`. Please return the minimum operators for
position `i` to make the list of numbers satisfying
`nums[a] + 1 == nums[a + 1], 0 <= a <= i`. The original topic is
[here](https://leetcode.cn/problems/5TxKeK/).

### Analysis

To satisfying `nums[a] + 1 == nums[a + 1]`, we do not know which number it is
for position `i` which makes the operators minimum.

Thus we can transform this problem, we make `nums[a] = nums[a] - a`. Then we
only need to make each position of the `nums` all equal to the same number. Then
the minimum way is to set the number as the median number of `nums`.

<font color=red>NOTE:</font> it is the median number, not the mean value. If the
length of the list is even then the median is the mean of the middle two
numbers.

It is easy to prove that the mean value can not bring the minimum operators. For
examples, for list `[1, 3, 8]` the mean value is `4` and it needs
`3 + 1 + 4 = 8` operators to make the list into `[4, 4, 4]`. The median is `3`
and it needs only `2 + 0 + 5 = 7` operators to make the list into `[3, 3, 3]`.

<font color=blue>The reason for why it is the median.</font>

Let `s` be the value that each position will reach and `m` be the median. If `s`
is large than `m`, then `#(nums[i] > s) < #(nums[i] < s)`. If set `s = s - 1`,
`nums[i]` will add `1` operator if `nums[i] > s` and `nums[i]` will decrease `1`
operator if `nums[i] < s`. For `#(nums[i] > s) < #(nums[i] < s)`, the total
number of operators will decrease. It will always decrease until `s == m`, i.e.,
`#(nums[i] > s) = #(nums[i] < s)`.

```python
from typing import List
from heapq import heappush, heappushpop


class MedianFinder:
    def __init__(self):
        self.small = []  # 大顶堆
        self.large = []  # 小顶堆
        self.leftSum = 0
        self.rightSum = 0

    def add(self, num: int) -> None:
        # if #small == #large then put the num in large
        # How to push it in large.
        # NOTE: not put it in large directly, find the largest value then put it in to the large.
        # This ensure that large is always larger than small.
        # #small always smaller than #large and only larger than 1
        if len(self.small) == len(self.large):
            leftMax = -heappushpop(self.small, -num)
            heappush(self.large, leftMax)
            self.leftSum += num - leftMax
            self.rightSum += leftMax
        elif len(self.small) < len(self.large):
            rightMin = heappushpop(self.large, num)
            heappush(self.small, -rightMin)
            self.leftSum += rightMin
            self.rightSum += num - rightMin

    def getMedian(self) -> float:
     # self.large[0] is the smallest value in large
     # -self.small[0] is the largest value in small
     # get the median if the length is odd, else the mean of the two middle numbers
        if len(self.small) == len(self.large):
            return (self.large[0] - self.small[0]) / 2
        else:
            return self.large[0]

    def getDiffSum(self) -> float:
        median = self.getMedian()
        return self.rightSum - len(self.large) * median + len(self.small) * median - self.leftSum


class Solution:
    def numsGame(self, nums: List[int]) -> List[int]:
        MOD = int(1e9 + 7)
        res = []
        medianFinder = MedianFinder()
        for i in range(len(nums)):
            normalized = nums[i] - i
            medianFinder.add(normalized)
            res.append(int(medianFinder.getDiffSum() % MOD))
        return res
```

# Brute Force Search

## Coordinate with Maximum Network Quality <font color=magenta>[2022-05-10]</font>

An array of network towers `towers` is given, where
$towers[i] = [x_i, y_i, q_i]$. $x_i$ and $y_i$ are the integral coordinates of
the network tower location and $q_i$ is the network quality factor. The network
quality of `towers[i]` at $(x, y)$ is $q_i / (1 + d)$ where
$d = \sqrt{(x - x_i)^2 + (y - y_i)^2}$.

You are also given an integer `radius`. All the tower is not reachable if and
only if `d > radius`.

The network quality of a location is the sum of network qualities from all
towers. Return the location with the maximum network quality.

If there are multiple coordinates with the same network quality, return the
lexicographically minimum non-negative coordinate.

Constraints:

1. 1 <= towers.length <= 50
2. towers[i].length == 3
3. 0 <= $x_i$, $y_i$, $q_i$ <=50
4. 1 <= radius <= 50

### Analysis

For 0 <= $x_i$, $y_i$, $q_i$ <=50, if x or y is large than 51, it can not be the
answer. All the network quality will be stronger when x or y is smaller. Thus,
we do only need to search within [0, 51].

```python
import math
class Solution:
    def bestCoordinate(self, towers: List[List[int]], radius: int) -> List[int]:
        max_signal, x, y = 0, 0, 0
        # search from small coordinate to large then we will get the small coordinate first
        for i in range(51):
            for j in range(51):
                signal_ij = 0
                for tower in towers:
                    delta_x, delta_y = tower[0] - i, tower[1] - j
                    d = math.sqrt(delta_x * delta_x + delta_y * delta_y)
                    # if > radius no quality
                    if d > radius:
                        continue
                      # use floor
                    signal_ij += math.floor(tower[2] / (1 + d))
                if signal_ij > max_signal:
                    max_signal, x, y = signal_ij, i, j
        return [x, y]
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

## Maximum Number of Tasks You Can Assign <font color=magenta>[2022-05-11]</font>

The topic is
[here](https://leetcode.cn/problems/maximum-number-of-tasks-you-can-assign/).

### Analysis

1. The task need small strength is preferred.
2. The worker with large strength is preferred

A wrong code

```python
class Solution:
    def maxTaskAssign(self, tasks: List[int], workers: List[int], pills: int, strength: int) -> int:
        tasks.sort()
        workers.sort()
        n =  len(tasks)
        m =  len(workers)
        ans = 0
        j = 0
        for i in range(m):
            if j < n:
                # A new worker waiting for task
                if workers[i] >= tasks[j]:
                    ans += 1
                    # the task has been assigned, consider the next task
                    j += 1
                else:
                    # if there is pills avaliable
                    if pills and workers[i] + strength >= tasks[j]:
                        # use this pill
                        pills -= 1
                        ans += 1
                        j += 1
        return ans
```

This code prefers to use small strength workers, which is wrong. When the
workers can do `k` tasks, it must be the top `k` strength workers taking the
bottom `k` tasks.

The final answer.

```python
from sortedcontainers import SortedList


class Solution:
    def maxTaskAssign(self, tasks, workers, pills, strength):
        n, m = len(tasks), len(workers)
        tasks.sort()
        workers.sort()

        def check(mid: int) -> bool:
            p = pills
            #  the workers needed to be check
            ws = SortedList(workers[m - mid:])
            # tasks from large to small
            for i in range(mid - 1, -1, -1):
                if ws[-1] >= tasks[i]:
                    # tasks[i] is assigned to ws[-1]
                    ws.pop()
                else:
                    if p == 0:
                        # if does not have pills anymore
                        return False
                    # has pills
                    # Note: find the smallest worker can take the task by pill
                    rep = ws.bisect_left(tasks[i] - strength)
                    if rep == len(ws):
                        return False
                    p -= 1
                    ws.pop(rep)
            return True

        # The scope of k needed to be checked if 1 checked false, answer is 0
        # if right = min(m, n) checked true, answer is right
        left, right, ans = 1, min(m, n), 0
        while left <= right:
            mid = (left + right) // 2
            if check(mid):
                # if mid is true, then ans is larger than mid
                # find it in [mid + 1, right]
                ans = mid
                left = mid + 1
            else:
                # if mid is false, then ans is smaller than mid
                # find it in [left, mid - 1]
                right = mid - 1

        return ans
```
