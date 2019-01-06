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

���������Ŀ��ͷ���е�һ���ĳ����ľ����ҵ�rotated��Ԫ�أ�Ȼ�����߷ֱ���binary search�Ϳ����ˡ�

��һ�����
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

��������
1���ҵ�pivot������rotated���Ǹ��㣬
2����������ֱ�ʹ��binarySearch��Ѱ��Ԫ�أ��ҵ�����index��δ�ҵ�����-1

�ύ��LeetCode��Accepted��

�����������������һ�����⣬����������Ҫ���㷨�ĸ��Ӷ���Ϊlog(n)��������pivot�Ĺ����У�һֱ����������࣬�������Ҳ࣬�⵼�µ�pivot�����Ҳ��ʱ�����`binarySearchpivot`�㷨�ĸ��Ӷ�ΪO(n),������LeetCodeû��ǿ�󵽼��������ĸ��Ӷȡ�


�Ľ�һ�棺

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

����pivotԪ�ص��������඼�������ԭ���Լ�pivot���Ҷ�����������裬�����һ�£����ڼ��middle��໹���Ҳ���һ���ж������������ִ��ʱ�临�ӶȾ���O(log(n))�ˡ�


## Solution Others

��ʵ��Ѱ��pivot�Ĺ����������Ѿ������ˣ�����������໹���Ҳ������ǿ��Ը���middle��high��Ԫ�ش�С���ж������ģ�������pivot��ʵû�б�Ҫ����������ֱ�Ӹ���target��ֵ�����������Ϳ����ˡ�����һ��Discuss����������ϴ����������һ�㶼�������֡�

������һ�����˵Ľ��������

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

����Ҫ�㣺��������