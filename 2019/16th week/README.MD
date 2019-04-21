## Algorightm 

### Description

Given a non-empty binary tree, find the maximum path sum.

For this problem, a path is defined as any sequence of nodes from some starting node to any node in the tree along the parent-child connections. The path must contain at least one node and does not need to go through the root.

Example 1:
```
Input: [1,2,3]

       1
      / \
     2   3

Output: 6
```
Example 2:
```
Input: [-10,9,20,null,null,15,7]

   -10
   / \
  9  20
    /  \
   15   7

Output: 42
```

### Solution

这题主要是要理解 path 的含义。path 定义为从任意起始点开始，通过父子间的连接的一系列点的集合，并且`同一条连接不能使用多次`。上面第二个例子中，output 对应的path 为 15 -> 20 -> 7（或7 ->20 -> 15）, 而绝不是 20->15->7,因为15与7之间不存在连接，15也不能回到20再到7.

先给一个错误的例子，这个例子实现的是最大子树和，而不仅仅是path
它的思路为：
    1. 返回的数据组中记录的是{包含当前节点的最大子树和, 当前节点的maxPathSum}
    2. 深度遍历，将节点加入后更新返回值
    3. root 节点的maxPathSum 即为解
```
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int maxPathSum(TreeNode root) {
        return maxAndSum(root)[1];
    }

    //0:sum 1:max
    private int[] maxAndSum(TreeNode node) {
        if (node.left == null && node.right == null) {
            return new int[]{node.val, node.val};
        }
        int[] left = new int[]{0, Integer.MIN_VALUE};
        if (node.left != null) {
            left = maxAndSum(node.left);
        }
        int[] right = new int[]{0, Integer.MIN_VALUE};
        if (node.right != null) {
            right = maxAndSum(node.right);
        }
        int[] r1 = new int[]{node.val, node.val};
        int[] r2 = new int[]{add(node.val, left[0]), left[1]};
        int[] r3 = new int[]{add(node.val, right[0]), right[1]};
        int[] r4 = new int[]{add(node.val, left[0]), add(node.val, left[0])};
        int[] r5 = new int[]{add(node.val, right[0]), add(node.val, right[0])};
        int[] r6 = new int[]{add(add(node.val, left[0]), right[0]), add(add(node.val, left[0]), right[0])};
        return Stream.of(r1, r2, r3, r4, r5, r6).max(((o1, o2) -> {
            if (o1[1] == o2[1]) {
                return 0;
            }
            if (o1[1] == Integer.MIN_VALUE) {
                return -1;
            } else if (o2[1] == Integer.MIN_VALUE) {
                return 1;
            }
            return o1[1] - o2[1];
        })).get();
    }


    private int add(int a, int b) {
        if (a == Integer.MIN_VALUE ) {
            return b;
        } else if (b == Integer.MIN_VALUE) {
            return a;
        }
        return a+b;
    }
}
```
这个程序在测试case `[5,4,8,11,null,13,4,7,2,null,null,null,1]` 上失败了，输出了55，实际应是48.

在discuss区很多网友也有这个疑问，不过明白了 path 的含义就能看出我们程序的问题，返回值应该是{包含当前节点的最大path和,当前节点的maxPathSum}，这个 `包含当前节点的最大path和` 由于需要parent 使用，因此它不能既向左又向右，而是要么向左要么向右，要么只有自己三者的最大值，即 max(node,node+left,node+right)。

因此修改后的程序：

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int maxPathSum(TreeNode root) {
        return maxAndSum(root)[1];
    }

    //0:sum 1:max
    private int[] maxAndSum(TreeNode node) {
        if (node.left == null && node.right == null) {
            return new int[]{node.val, node.val};
        }
        int[] left = new int[]{0, Integer.MIN_VALUE};
        if (node.left != null) {
            left = maxAndSum(node.left);
        }
        int[] right = new int[]{0, Integer.MIN_VALUE};
        if (node.right != null) {
            right = maxAndSum(node.right);
        }

        int rl = Math.max(Math.max(node.val, add(node.val, left[0])), add(node.val, right[0]));
        
        int r1 = node.val;
        int r2 = left[1];
        int r3 = right[1];
        int r4 = add(node.val, left[0]);
        int r5 = add(node.val, right[0]);
        int r6 = add(add(node.val, left[0]), right[0]);
        return Stream.of(r1, r2, r3, r4, r5, r6).max(((o1, o2) -> {
            if (o1.equals(o2)) {
                return 0;
            }
            if (o1 == Integer.MIN_VALUE) {
                return -1;
            } else if (o2 == Integer.MIN_VALUE) {
                return 1;
            }
            return o1 - o2;
        })).map(ints -> new int[]{rl, ints}).get();
    }




    private int add(int a, int b) {
        if (a == Integer.MIN_VALUE ) {
            return b;
        } else if (b == Integer.MIN_VALUE) {
            return a;
        }
        return a+b;
    }
}
```

这个accepted 了，但是运行时间惨不忍睹47ms，只打败了 1% 的人。  
第一次觉得可能和 Stream 的使用有关，换成了 array + sort的形式，运行时间减少了10ms，依然惨不忍睹。

再观察一下，rl 其实已经是r1,r4,r5的比较结果，只需要将其与r2,r3,r6比较即可，没有必要使用排序这么重量级的手段。
最终程序如下：
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int maxPathSum(TreeNode root) {
        return maxAndSum(root)[1];
    }

    //0:sum 1:max
    private int[] maxAndSum(TreeNode node) {
        if (node.left == null && node.right == null) {
            return new int[]{node.val, node.val};
        }
        int[] left = new int[]{0, Integer.MIN_VALUE};
        if (node.left != null) {
            left = maxAndSum(node.left);
        }
        int[] right = new int[]{0, Integer.MIN_VALUE};
        if (node.right != null) {
            right = maxAndSum(node.right);
        }

        int rl = Math.max(Math.max(node.val, add(node.val, left[0])), add(node.val, right[0]));

        int rr = Math.max(rl, left[1]);
        rr = Math.max(rr, right[1]);
        rr = Math.max(rr, add(add(node.val, left[0]), right[0]));

        return new int[]{rl, rr};
    }

    private int add(int a, int b) {
        if (a == Integer.MIN_VALUE ) {
            return b;
        } else if (b == Integer.MIN_VALUE) {
            return a;
        }
        return a+b;
    }
}
```
    
运行时间变为 1ms， 打败99.7%。

时间复杂度分析：  
T(n) = 2T(n/2) + const

因此 T(n) = O(n)

## Review

[Goodbye docker and thanks for all fish](https://technodrone.blogspot.com/2019/02/goodbye-docker-and-thanks-for-all-fish.html)

docker 在 kubernates 胜出之后，已经变成了一个组件，而不再是不可或缺的部分了，而且docker 公司也由于没有盈利快要消失，导致docker 容器更新发展缓慢，而其他的容器技术正在飞速发展，因此作者认为docker 被取代只是一个时间问题。

## Tips

Android studio 提示打开 app 需要使用 3.4 版本以上，实际上是因为gradle 的版本太高了导致了。

在 build.gradle 中，将gradle 的版本替换为 3.3.1 即可。

## Share

分享一下王爽汇编语言的读书笔记

[Chapter 1-4](http://note.youdao.com/noteshare?id=e12b53e0fc8c6a12f4c66777f22ffa7f)  
[Chapter 5-8](http://note.youdao.com/noteshare?id=9c8ad8bb10e13446c196306337a90a45)  
[Chapter 9-12](http://note.youdao.com/noteshare?id=37d7723f21596d142262b4c0d415d6a0)  
[Chapter 13-17](http://note.youdao.com/noteshare?id=6cd53a0a2244df1d869b9f20da2fe33b)  

以前读汇编语言相关的书籍云山雾罩，看完之后无法将汇编与高级语言结合起来，王爽的这本从浅入深，注重本质，让人看完之后豁然开朗，读起来就不想停下来，大家对汇编感兴趣的可以使用这本当作入门书。

当然书中使用的技术已经比较落后了，但这并不妨碍本书的优点，而且老技术少了包装在外层的花里胡哨的外壳显得更加本质，易于我们理解。
