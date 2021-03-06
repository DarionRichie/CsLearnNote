---
export_on_save:
 html: true
---

@import "/blog_head.md"

# 493. Reverse Pairs

> <https://leetcode.com/problems/reverse-pairs/description/>

## Description


Given an array `nums`, we call `(i, j)` an important reverse pair if `i < j` and `nums[i] > 2*nums[j]`.

You need to return the number of important reverse pairs in the given array.

**Example1**:
**Input**: `[1,3,2,3,1]`
**Output**: `2`

**Example2**:
**Input**: `[2,4,3,5,1]`
**Output**: `3`

**Note**:
- The length of the given array will not exceed `50,000`.
- All the numbers in the input array are in the range of 32-bit integer.

## Solution

```python {class=line-numbers}
class Solution(object):
    def reversePairs(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        import bisect, math
        ret = 0
        record = []
        for n in nums:
            v = n*2
            ret += len(record)-bisect.bisect_right(record, v)
            bisect.insort(record, n)
        return ret
```
