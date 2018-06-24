## Algorithm

[LeetCode 33-SearchIn Rotated Sorted Array](https://my.oschina.net/liufq/blog/1833370)

### Problem description
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

### Solution Self

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


### Solution Others

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


### DONE

考察要点：二分搜索

## Review

[functor and monad examples in plain java](http://www.nurkiewicz.com/2016/06/functor-and-monad-examples-in-plain-java.html)

This is a good artical for newbie to learn what a functor is. If you never heard about functional programing, you might get interested in it after you read it.

There is two part, as the title tells you, functor part and monad part. Monad part is a little ambigious. I don't understand what monad is. It feels like a special functor. So I googled `diffrence between functor and monad`.

Functor describes a generalisation of functions that perform mapping operations. It abstracts the data types. From view of computer-sience perspect, functor add a layer to data and give it a common `map` method to each data type. So we can use it consistently.

Monad is still ambigious to me. I will dig it continuely.

## Technique

`Jump into the haskell hole`

> note: this is a book note from [learn you a haskell](http://learnyouahaskell.com/)

### Command Collections

- `:?` show help
- `:set prompt "ghci>"`  change the prompt 
- `:set +t` print type infos 
- `:module + Data.Ratio` load a module
- `:module - Data.Ratio` unload the module
- `:m` remove all the modules loaded
- `:unset ` same commands as set
- `:l file-name` load file into ghci


### Start out

- download haskell platform at [downloads](https://downloads.haskell.org/~platform/8.4.3/HaskellPlatform-8.4.3-full-x86_64-setup.exe)

- basic operations
   - `3 + 5` `5 - 6` `3 * 7` `5 / 2` ps: `5/2` result `2.5`
   - comparing `>` `<` `>=` `<=` `==` `/=`
   - `compare a b` function generate `LT` `GT` `EQ` results
   - Bool values `True` `False` support `&&` `||` `not` operations
   - minus(-) is a operator you should pay attention to. `3 * (-4)` rather than `3 * -4`,it will report error
   - haskell is strong typed, `1 + "aaa"` don't make sense
- built-in functions
   -  `succ 7` returns 8
   -  `min` `max` `div`(like `/` operator) 
   -  function calls in pattern `fun param1 param2`. But we can use \`(backtick) to convert it to a infix funciton. For example <code>5 \`div\` 2<code>
   -  function is first class in haskell. we can define it just like variables except it has parameters
   -  `doubleX x = x + x` define a double function
   -  `if then` is an expression which return a value and `else` is mandatory. For example `biggerThanSeven x = if x > 7 then "bigger than 7" else "smaller than seven"`. You may check this url for [expression vs statement](https://stackoverflow.com/questions/4728073/what-is-the-difference-between-an-expression-and-a-statement-in-python)
   - Functions can't begin with uppercase letters
   
- lists operations
   - lists are a homogenous data structure
   - strings are list of chars
   - define a list `list = [1,2,3,4]`
   - `++` to put two list together
   - `:` to put an elem to a list `1:[2]` get `[1,2]` 
   - `!!` to get a elem by index `[1,2,3] !! 2` get `3`(index starts from 0)
   - support `>` `<` `>=` `<=` by comparing each elem in lexicographical order
   - functions 
      - `head` return first element 
      - `tail` sublist except first element
      - `init` sublist except last element
      - `last` return last element
      - ![image](http://s3.amazonaws.com/lyah/listmonster.png)
      - `length` 
      - `null` check a list if it's empty list
      - `reverse` a list
      - `take n list` take n elems from list. It's useful in a infinite list 
      - `drop n list` drop the first n elements
      - `maximum list` biggest elems (not only number only if they are comparable)
      - `minimum list` 
      - `sum list` sum a list of numbers
      - `product list` 
      - `elem` test a element is a member of list or not
    - Enumeration
       - `[1..10]` produce a list from 1 to 10 with step 1
       - `[10..1]` produce empty list because step is default 1
       - `[10,9..1]` you can use this way to set a step
       - `[1,4..10]` you get `[1,4,7,10]` the step is 3
       - `[1,3..]` without upper bound, we can create a infinite list.
    - Infinite List
       - `cycle list` cycle the elements into a infinite list
       - `repeat elem` like cycle with only one element in a list
       - use `take` to retrieve elements that you need.Don't try to display the result!
- List comprehension
    - we do this in math ![Set in math](http://s3.amazonaws.com/lyah/setnotation.png)
    - Haskell also give us the powerful ability to manipulate lists!
    - I wanna take 20 elements from a list generated by `[1,5..]` with each element mod 3 is 1 then double it, we can do this way using comprehension
    - `take 20 [x * 2 | x <- [1,5..], (mod x 3) == 1]`
    - Not only can we have multiple predictes but also we can draw elements from multiple lists.
    - `[x * y | x <- [1..4], y<-[5..9], x /= 3, y > 7]`
    - nested list examples `xxs = [[1,2],[3,4],[3,5]]`
    - `[[x | x<-xs, x /= 3] | xs <- xxs]`
    - What if we wanna flatten the list? Possible way is Recursion.
- Tuple
    - tuple is a bunch of element which can have different types and immuatable.
    - different size tuples are different types.
    - functions 
        - `fst tuple` take the first element of a tuple which only contains two elements
        - `snd tuple` take the second element 
        - `zip list1 list2` turn two lists into a list of tuples which consist of the corresponding element in each list 
        

### Comments 

Haskell has big differences with C-like languages. Sometimes you find that some notations are weird and quirky. The description of list on the above remind me of somebody's saying that don't treat list as an array in a continuous memory but a head element followed by a sublist. It's a recursive view of list that fits well with functional programming. 

Just dig it.


## Share

`When you convert a boxed primitive type to string, always use toString() method rather than use + ""  `


There is a lot of code like `a + ""` to convert a Long, Integer and other boxed primitive type to string in my project. I used frequently this form before while I realized this was a bad practice  recently.

Let's take a look at how java specs says about the concatenation operator + from [String Concatenation Operator +](https://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.18.1)

```
If only one operand expression is of type String, then string conversion (§5.1.11) is performed on the other operand to produce a string at run time.

The result of string concatenation is a reference to a String object that is the concatenation of the two operand strings. The characters of the left-hand operand precede the characters of the right-hand operand in the newly created string.

The String object is newly created (§12.5) unless the expression is a constant expression (§15.28).

An implementation may choose to perform conversion and concatenation in one step to avoid creating and then discarding an intermediate String object. To increase the performance of repeated string concatenation, a Java compiler may use the StringBuffer class or a similar technique to reduce the number of intermediate String objects that are created by evaluation of an expression.

For primitive types, an implementation may also optimize away the creation of a wrapper object by converting directly from a primitive type to a string.

```

check 5.1.11 which it refers

```
Any type may be converted to type String by string conversion.

A value x of primitive type T is first converted to a reference value as if by giving it as an argument to an appropriate class instance creation expression (§15.9):

If T is boolean, then use new Boolean(x).

If T is char, then use new Character(x).

If T is byte, short, or int, then use new Integer(x).

If T is long, then use new Long(x).

If T is float, then use new Float(x).

If T is double, then use new Double(x).

This reference value is then converted to type String by string conversion.

Now only reference values need to be considered:

If the reference is null, it is converted to the string "null" (four ASCII characters n, u, l, l).

Otherwise, the conversion is performed as if by an invocation of the toString method of the referenced object with no arguments; but if the result of invoking the toString method is null, then the string "null" is used instead.

The toString method is defined by the primordial class Object (§4.3.2). Many classes override it, notably Boolean, Character, Integer, Long, Float, Double, and String.
```

We learned that when you do `t = l + ""`(l is Long type), java translated it this way:
```
StringBuffer s = new StringBuffer();
s.add(l);
s.add("");
t = s.toString();
```

It is not a big deal that java creates extra object. What matters is that it transforms l to string "null" when l is null. It causes big problems as you pass your value to others. I encountered this problem before. And I realized that there are hidden booms in project because people tend to use this form rather than `toString` method.

It forces you to think what you should do while l is null when you use `toString` method. And It looks elegant while `+ ""` form looks ugly. 