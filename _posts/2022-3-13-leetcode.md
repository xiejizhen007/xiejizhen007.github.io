---
title: Leetcode 总结
tags: leetcode
---

## Leetcode 刷题总结

### 33 搜索旋转排序数组

[题目链接](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

有序数组，查找，emmm，那就二分？

对于旋转点 `k` 来说，旋转点的左侧，右侧仍然是有序的，只是整体可能是无序的。
所以在二分的过程中，只需要判断当前时处于 `k` 的左侧还是 `k` 的右侧，判断的方法 `nums[mid] < nums[n-1]`。
确认了在 `k` 的左侧或者时右侧后，然后再判断目标是处于 `[0, mid-1]` 还是 `[mid+1, n-1]`，根据条件修改相应的边界条件即可。

### 215 数组中的第 K 大的元素

[题目链接](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

要求第 `k` 大的元素，可以先对数组进行排序，即可通过下标选到第 `k` 大的数。

参考 LeetCode 的[官方题解](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/solution/shu-zu-zhong-de-di-kge-zui-da-yuan-su-by-leetcode-/)。

要找到第 `k` 大的数，对于长度为 `n` 的数组 `nums`，不需要对数组全都排序，只要 `nums[n-k-1]` 大于等于 `n-k-1` 左边的数，小于等于 `n-k-1` 右边的数即可。

在快速排序算法中，就是将数组划分成小数组排序的过程。
对于数组 `nums`，在快速排序算法中，在 `nums[l,,,r]` 中随机选取一个主元 `q`，将数组划分为 `nums[l,,,q-1]`、`nums[q+1,,,,r]`，
使得 `nums[l,,,q-1]` 的每个元素都小于等于 `nums[q]`，使得 `nums[q+1,,,r]` 的每个元素都大于等于 `nums[q]`。

所以在划分的过程中，如果 `q == n - k - 1`，即可直接返回 `nums[q]`，如果 `q < n - k - 1`，即当前的 `q` 分布在左子区间，所以之后递归右子区间，
同理 `q` 分布在右子区间时，递归左子区间求解即可。

```cpp
int quickSelect(vector<int>& nums, int l, int r, int index) {
    int q = randomPartition(nums, l, r);
    if (q == index) {
        return nums[q];
    } else {
        return q < index ? quickSelect(nums, q + 1, r, index) : quickSelect(nums, l, q - 1, index);
    }
}

int randomPartition(vector<int>& nums, int l, int r) {
    int i = rand() % (r - l + 1) + l;
    swap(nums[i], nums[r]);
    return partition(nums, l, r);
}

int partition(vector<int>& nums, int l, int r) {
    int x = nums[r], i = l;
    for (int j = l; j < r; j++) {
        if (nums[j] < x) {
            swap(nums[j], nums[i++]);
        }
    }

    swap(nums[i], nums[r]);
    return i;
}
```

### 剑指 Offer 36

[题目链接](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)

根据二叉搜索树的特性，中序遍历即可得到有序的数组，再调整各节点的左右指针即可得到题目要求的双向循环链表。

在中序遍历的过程中，访问的节点是有序的，所以可以创建一个 `head`，`pre` 指针，`head` 指针指向最小的节点，`pre` 指针指向当前节点的上一个节点，为前驱节点。
在遍历时，只需要调整正在遍历的节点 `cur` 的前驱节点，同时调整 `pre` 的后继节点，`pre->right = cur`，`cur->left = pre`。
遍历结束之后，`pre` 指向二叉搜索树最大的节点，`head` 指向二叉搜索树最小的节点，调整 `head` 的前驱节点，`pre` 的后继节点即可。

### 剑指 Offer 54

[题目链接](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/)

在二叉搜索树中查找节点，根据二叉搜索树的特性，当前节点的值比左子树的所有值要大，比右子树的所有值要小。

方法一：所以对二叉搜索树进行中序遍历，即可得到一个有序的数组，再从数组中找到第 `k` 大的数。

方法二：也可以反向遍历，即先遍历右子树，在遍历左子树，即可得到递减的数组，再从数组中找到答案。
进一步优化，我们在找到最大的节点开始，即最右端的节点，对 `k -= 1`，当 `k == 0` 时，就找到了我们要找的节点，把找到的节点记录下来，即可。

```cpp
void dfs(node) {
    dfs(node->right);
    
    k-=1;
    if (k == 0) ans = node->val;
    
    dfs(node->left);
}
```
