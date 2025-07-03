---
title: "LC 334. Increasing Triplet Subsequence"
date: 2025-07-03 11:06:00 +0800
categories: [LeetCode]
tags: [leetcode, array]
---

Link: https://leetcode.com/problems/increasing-triplet-subsequence

## Description

Given an integer array `nums`, return `true` if there exists a triple of indices `(i, j, k)` such that `i < j < k` and `nums[i] < nums[j] < nums[k]`. If no such indices exists, return `false`.

**Example**

Input: nums = [1,2,3,4,5] \
Output: true \
Explanation: Any triplet where i < j < k is valid. 

## Write-up

To find a triple `nums[i] < nums[j] < nums[k]` where `i < j < k`, the main point is to find the second smallest element. For each number in the array, if it's larger than the current second smallest element, say x, it means there must exist an element y smaller than x. Then y, x and the current element consist of an increasing triplet.

Code to find the second smallest element is:
```
first = second = float('inf')
for n in nums:
  if n <= first:
    first = n
  elif n <= second:
    second = n
```

Given second smallest element at each position, code to find an increasing triplet is:
```
for n in nums:
  if n > second:
    return True
return False
```
By reading the 2 code snippets, we can see that they can be combined:
```
first = second = float('inf')
for n in nums:
  if n <= first:
    first = n
  elif n <= second:
    second = n
  else:
    # n > second
    return True
return False
```
