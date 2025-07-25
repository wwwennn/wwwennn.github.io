---
title: "Bit Operations"
date: 2025-07-24 19:49:00 +0800
categories: [LeetCode]
tags: [leetcode, bit manipulation]
---

Bit operations

Involved LC problems:
- LC 338: https://leetcode.com/problems/counting-bits
- LC 1318: https://leetcode.com/problems/minimum-flips-to-make-a-or-b-equal-to-c

Let's work on LC 338 first.

For each `i`, `(0 <= i <= n)`, to get the count of `1`s in this binary representation, we will first get its binary representation and count `1`s. To get binary representation, we will keep dividing the number by 2 until it becomes 1 and the remindars are its binary format, like following.
```
2  | 3   1
   |___
     1
```
By going through the process of getting a number's binary, we can see that `binary[i] = binary[i // 2] + (0 if i % 2 == 0 else 1)`. This is a dynamic programming state formula. So the solution of LC 338 is obvious now.

```
class Solution:
    def countBits(self, n: int) -> List[int]:
        res = [0] * (n + 1)
        for i in range(1, n + 1):
            res[i] = res[i // 2] + (0 if i % 2 == 0 else 1)
        return res
```

This is an easy problem, but it helps us review **how to get the binary representation of a number**.

Next, let's see LC 1318. 

The idea is straightforward. We check the bits of `a`, `b` and `c` from the least significant to most significant. If `a_bit[i] | b_bit[i] != c_bit[i]`, we do flips. The solution is as follows:
```
class Solution:
    def minFlips(self, a: int, b: int, c: int) -> int:
        count = 0
        while a or b or c:
            la = (a & 1)
            lb = (b & 1)
            lc = (c & 1)
            if (la | lb) != lc:
                if lc == 1:
                    count += 1
                else:
                    if la == 1:
                        count += 1
                    if lb == 1:
                        count += 1
            a = (a >> 1)
            b = (b >> 1)
            c = (c >> 1)
        return count
```

**2 takeaways for this solution:**
1. To get the least significant bit of a number, we `&` it with `1`. I.e., `la = (a & 1)`.
2. To remove the least significant bit of a number, we `>>` it by `1`. I.e., `a >> 1`.

So, combining the 2, we can get how to loop over all the bits of a number from least significant to most significant.
```
while n:
  ln = (n & 1)
  n = (n >> 1)
```

