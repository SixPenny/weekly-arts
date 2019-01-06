## Problem description
```
Suppose an array sorted in ascending order is rotated at some pivot unknown to you beforehand.

(i.e., [0,1,2,4,5,6,7] might become [4,5,6,7,0,1,2]).

You are given a target value to search. If found in the array return its index, otherwise return -1.

You may assume no duplicate exists in the array.

Your algorithm's runtime complexity must be in the order of O(log n).

Example 1:

Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
Example 2:

Input: nums = [4,5,6,7,0,1,2], target = 3
Output: -1

```

## Solution Self

看到这个题目，头脑中第一个蹦出来的就是找到rotated的元素，然后两边分别做binary search就可以了。

第一版代码
```java
class Solution {
    public int search(int[] nums, int target) {
        if (nums.length == 0) {
            return -1;
        }
        if (nums.length == 1){
            return nums[0] == target ? 0 : -1;
        }
        int pivot = binarySearchPivot(nums, 0, nums.length);
        if (pivot == -1){
            return searchIndex(nums, 0, nums.length, target);
        }
        int index = searchIndex(nums, 0, pivot, target);
        if (index != -1) {
            return index;
        }

        return searchIndex(nums, pivot, nums.length, target);
    }
    private int searchIndex(int[] nums, int low, int high, int target) {
        if (high <= low) {
            return -1;
        }
        int middle = (low + high) /2;
        if (nums[middle] == target) {
            return middle;
        }
        if (nums[middle] < target) {
            return searchIndex(nums, middle + 1, high, target);
        }
        return searchIndex(nums, low, middle, target);
    }
    private int binarySearchPivot(int[] nums, int low, int high) {
        if (high <= low) {
            return -1;
        }

        int middle = (low + high) / 2;
        if (middle > 0
                && nums[middle] < nums[middle - 1]) {
            return middle;
        }
        int leftSearch = binarySearchPivot(nums, low, middle);
        if (leftSearch != -1) {
            return leftSearch;
        }
        return binarySearchPivot(nums, middle + 1, high);
    }
}
```

分两步：
1：找到pivot，就是rotated的那个点，
2：左右两侧分别使用binarySearch来寻找元素，找到返回index，未找到返回-1

提交到LeetCode，Accepted！

不过上面这个程序有一个问题，问题描述中要求算法的复杂度需为log(n)，在搜索pivot的过程中，一直是先搜索左侧，再搜索右侧，这导致当pivot在最右侧的时候，这个`binarySearchpivot`算法的复杂度为O(n),很明显LeetCode没有强大到计算你程序的复杂度。


改进一版：

```java

package com.dylan.leetcode;

import org.junit.Assert;
import org.junit.Test;

/**
 * Created by liufengquan on 2018/6/20.
 */
public class SearchInRotatedSortedArray {
    public int search(int[] nums, int target) {
        if (nums.length == 0) {
            return -1;
        }
        if (nums.length == 1){
            return nums[0] == target ? 0 : -1;
        }
        int pivot = binarySearchPivot(nums, 0, nums.length);
        if (pivot == -1){
            return searchIndex(nums, 0, nums.length, target);
        }
        int index = searchIndex(nums, 0, pivot, target);
        if (index != -1) {
            return index;
        }

        return searchIndex(nums, pivot, nums.length, target);
    }

    private int searchIndex(int[] nums, int low, int high, int target) {
        if (high <= low) {
            return -1;
        }
        int middle = (low + high) /2;
        if (nums[middle] == target) {
            return middle;
        }
        if (nums[middle] < target) {
            return searchIndex(nums, middle + 1, high, target);
        }
        return searchIndex(nums, low, middle, target);
    }
    private int binarySearchPivot(int[] nums, int low, int high) {
        if (high <= low) {
            return -1;
        }

        int middle = (low + high) / 2;
        if (middle > 0
                && nums[middle] < nums[middle - 1]) {
            return middle;
        }
        if (nums[middle] <= nums[high - 1]) {
            return binarySearchPivot(nums, low, middle);
        }
        return binarySearchPivot(nums, middle + 1, high);
    }

    @Test
    public void test() {
        SearchInRotatedSortedArray searchInRotatedSortedArray = new SearchInRotatedSortedArray();
        int[] nums = new int[]{4, 5, 6, 7, 0, 1, 2};
        Assert.assertTrue( searchInRotatedSortedArray.search(nums, 0) == 4);
        Assert.assertTrue(searchInRotatedSortedArray.search(nums, 3) == -1);
        nums = new int[]{5, 4};
        org.junit.Assert.assertTrue(searchInRotatedSortedArray.binarySearchPivot(nums, 0, 2) == 1);
        org.junit.Assert.assertTrue(searchInRotatedSortedArray.search(nums, 4) == 1);
        org.junit.Assert.assertTrue(searchInRotatedSortedArray.search(nums, 5) == 0);

        nums = new int[]{1, 3};
        Assert.assertTrue(searchInRotatedSortedArray.binarySearchPivot(nums, 0, 2) == -1);
        Assert.assertTrue(searchInRotatedSortedArray.search(nums, 0) == -1);

        nums = new int[]{3, 4, 5, 6, 1, 2};
        System.out.println(searchInRotatedSortedArray.binarySearchPivot(nums, 0, nums.length));
        Assert.assertTrue(searchInRotatedSortedArray.binarySearchPivot(nums, 0, nums.length) == 4);
        Assert.assertTrue(searchInRotatedSortedArray.search(nums, 2) == nums.length -1);
    }
}


```

基于pivot元素的左右两侧都比它大的原则，以及pivot左右都是升序的题设，检查了一下，对于检测middle左侧还是右侧有一个判定，这样程序的执行时间复杂度就是O(log(n))了。


## Solution Others

其实在寻找pivot的过程中我们已经看到了，对于搜索左侧还是右侧我们是可以根据middle跟high的元素大小来判定出来的，因此这个pivot其实没有必要搜索出来，直接根据target的值做二分搜索就可以了。看了一下Discuss里面的网友上传解决方法，一般都是用这种。

在这贴一个他人的解决方法：

```java
class Solution {
    public int search(int[] nums, int target) {
        int start = 0;
        int end = nums.length - 1;
        while (start <= end) {
            int mid = (start + end) / 2;
            if (nums[mid] == target) return mid;
            if (nums[mid] > target) {
                if (nums[start] <= nums[mid] && nums[end] <= nums[start] && nums[start] > target)
                    start = mid + 1;
                else end = mid - 1;
            }
            else {
                if (nums[mid] <= nums[end] && nums[end] <= nums[start] && nums[end] < target)
                    end = mid - 1;
                else start = mid + 1;
            }
        }
        return -1;
    }
}


```


## DONE

考察要点：二分搜索