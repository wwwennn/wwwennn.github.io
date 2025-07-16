---
title: "LC 450. Delete Node in a BST"
date: 2025-07-16 16:35:00 +0800
categories: [LeetCode]
tags: [leetcode, recursion, binary search tree]
---

Link: https://leetcode.com/problems/delete-node-in-a-bst

For tree problems, recursion usually comes into play. At the high level, the idea as follows:
```
if root.val < key:
  root.right = delete(root.right, key)
elif root.val > key:
  root.left = delete(root.left, key)
else:
  # delete root
```

To delete `root`, we need to get a new root. To maintain the binary search tree property, the new root should either be the largest value smaller than `root.val` or the smallest value larger than `root.val`. Here we will choose to use the latter one.

![example](assets/lc-450.png)

(a small tip about creating a complicated example to help us work on the problem: sometimes we only need to draw a structure. Like in the above example, we don't care the exact values of the tree nodes.)

So we need to:
1. Get the smallest value larger than `root.val`
2. Modify the tree

To get the smallest value larger than `root.val`, the code is:
```
cur = root.right
while cur.left:
  cur = cur.left
```

To modify the tree, we need to do:
1. fix the subtree rooted at `cur` - In the above example, `A` node is the `cur`. Since we promote `A` to root, we need to remove `A` from the subtree, i.e., `B.left = delete(A, A.val)`
2. make `cur` the new root - i.e., `cur.left, cur.right = root.left, root.right`.

To put all together, we get the following code:
```
class Solution:
    def deleteNode(self, root: Optional[TreeNode], key: int) -> Optional[TreeNode]:
        if not root:
            return root
          
        if root.val == key:
            # Find the largest left node and make it new root
            if not root.right:
                return root.left
            cur = root.right
            prev = None
            while cur.left:
                prev = cur
                cur = cur.left
            if prev == None:
                root.right.left = root.left
                return root.right
            else:
                prev.left = self.deleteNode(cur, cur.val)
                cur.left = root.left
                cur.right = root.right
                return cur
        elif root.val < key:
            root.right = self.deleteNode(root.right, key)
        else:
            root.left = self.deleteNode(root.left, key)
        return root
```

Note, we add some corner cases handling in the above implementation(`if not root.right`, `if prev == None`). Just ask yourself questions like `what if this is null?` and you'll get it during implementation. The most important is the high level idea.
