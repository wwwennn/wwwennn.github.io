---
title: "LC 392. Is Subsequence"
date: 2025-07-12 11:38:00 +0800
categories: [LeetCode]
tags: [leetcode, two pointers]
---


Link: https://leetcode.com/problems/is-subsequence

The original problem is easy to solve with two pointers.
```
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        j = 0
        for i, c in enumerate(s):
            k = j
            while j < len(t) and t[j] != s[i]:
                j += 1
            if j >= len(t):
                return False
            j += 1
        return True
```

There are multiple ways to write the two pointers algorithm. The above solution loops through each character in `s` and tries to find a matching character in `t`. You can flip it around, i.e., looping over `t` and looking for a matching character in `s`. Each way works but you'll see why we use the above implementation next. No matter how you code, the time complexity is `O(T)`, where T is the length of `t` and the space complexity is `O(1)`.

The interesting thing about this problem is its follow-up:

Suppose there are lots of incoming `s`, say `s1, s2, ..., sk` where `k >= 10^9`, and you want to check one by one to see if `t` has its subsequence. In this scenario, how would you change your code?

If for each incoming `s`, we run the above code for it, the total time complexity will be `O(kT)`. Can we do better? We cannot reduce `k` part, then we need to focus on the `O(T)` part. There are 2 potential directions. 
- `O(T)` is linear. One normal optimization is to improve it to `O(logT)`. It's not obvious to do that in this case.
- Another observation is that the length of `s` is shorter than that of `t`. Improving `O(T)` to `O(S)` sounds feasible. Let's see how to do that next.

What we do in the above code is to that for each character in `s`, find its matching character in `t`. Is it possible to get the matching character in `t` in `O(1)`?

One idea is to build a map over `t` with characters as keys and their indices as values like `{c1: [i1, i2], c2: [i3, i4]}`. Then for each character in `s`, we can do a binary search over its indices. 
```
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        dic = defaultdict(list)
        for i, c in enumerate(t):
            dic[c].append(i)

        prev = 0
        for i, c in enumerate(s):
            if c not in dic:
                return False
            indice_list = dic[c]
            idx = bisect.bisect_left(indice_list, prev)
            if idx != len(indice_list):
                prev = indice_list[idx] + 1
            else:
                return False
        return True
```
The preprocessing of `t` will be done only once. The total time complexity will be `O(T) + kO(SLogT)`. This is not better than the original algorithm.

Note, for each character in `t`, we don't need all their indice. What we need is for `c1` at index `i` and the character to find `c2`, the next closest index of `c2` after `c1` at index `i`.

To put it in another word, say for a `c1` in `s`, we find its matching char in `t` is `c1` at index `j`. Then we're at next char `c2` in `s`, is there a way to get from `t[j]`, which is `c1`, to `t[x]`, which is `c2` in O(1)? Yes, if we know the closest index of `c2` after the index of `c1` in `t`.

For example:
```
t = 'ahbgdc'

0 1 2 3 4 5
a h b g d c
```

We know that:
- The next `h` after `a` is at index `1`. 
- The next `b` after `a` is at index `2`. 
- The next `g` after `a` is at index `3`.
- The next `d` after `a` is at index `4`.
- The next `c` after `a` is at index `5`.

Same for `h`:
- The next `b` after `h` is at index `2`. 
- The next `g` after `h` is at index `3`.
- The next `d` after `h` is at index `4`.
- The next `c` after `h` is at index `5`.

Say `s = 'abc'`, we start from `t[0]`. 
- The next `a` after `t[0]` is `t[0]`, which is `t[0]` itself. 
- Then we match `b`. The next `b` after `t[0]:'a'` is `t[2]`. 
- Then we match `c`. The next `c` after `t[2]` is `t[5]`.

With the above info, when we check each `s`, we do the following:
```
start = 0
for c in s:
    if start >= len(t):
        return False
    # We're at position start, we need to get the next closest char `c`
    next = position[start][ord(c) - ord('a')]
    if next < 0:
        # There's no way to get from the previous matching character to the currect character
        return False
    elif next > 0:
        # Found a matching character at position next. Increment by 1 to get the next starting point.
        start = next + 1
    else:
        # The char at index start is the matching character. Increment by 1 to get the next starting point.
        start += 1
return True
```

The time complexity is `O(S)`.

How to get `position`?
```
position = [[-1 for _ in range(26)] for _ in range(len(t))]
for i in range(len(t) - 1, -1, -1):
    position[i][ord(t[i]) - ord('a')] = 0
    j = i - 1
    while j >= 0 and t[j] != t[i]:
        position[j][ord(t[i]) - ord('a')] = i
        j -= 1
```
`position[i][j]` means the closest position of `chr(ord('a') + j)` from index `i`. If it's `-1`, it means there's no way to get to the character from index `i`. Learn the above code carefully to see how we get the **closest position**.

To put it together:
```
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        position = [[-1 for _ in range(26)] for _ in range(len(t))]
        for i in range(len(t) - 1, -1, -1):
            position[i][ord(t[i]) - ord('a')] = 0
            j = i - 1
            while j >= 0 and t[j] != t[i]:
                position[j][ord(t[i]) - ord('a')] = i
                j -= 1
        
        start = 0
        for c in s:
            if start >= len(t):
                return False
            next = position[start][ord(c) - ord('a')]
            if next < 0:
                # There's no way to get from the previous matching character to the currect character
                return False
            elif next > 0:
                # Found a matching character at position next. Increment by 1 to get the next starting point.
                start = next + 1
            else:
                # The char at index start is the matching character. Increment by 1 to get the next starting point.
                start += 1
        return True
```

The time complexity is `O(T^2) + kO(S)`.
