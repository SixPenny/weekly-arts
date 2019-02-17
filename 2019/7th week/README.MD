## Algorithm

### Description

In this problem, a tree is an undirected graph that is connected and has no cycles.

The given input is a graph that started as a tree with N nodes (with distinct values 1, 2, ..., N), with one additional edge added. The added edge has two different vertices chosen from 1 to N, and was not an edge that already existed.

The resulting graph is given as a 2D-array of edges. Each element of edges is a pair [u, v] with u < v, that represents an undirected edge connecting nodes u and v.

Return an edge that can be removed so that the resulting graph is a tree of N nodes. If there are multiple answers, return the answer that occurs last in the given 2D-array. The answer edge [u, v] should be in the same format, with u < v.

Example 1:
```
Input: [[1,2], [1,3], [2,3]]
Output: [2,3]
Explanation: The given undirected graph will be like this:
  1
 / \
2 - 3
```
Example 2:
```
Input: [[1,2], [2,3], [3,4], [1,4], [1,5]]
Output: [1,4]
Explanation: The given undirected graph will be like this:
5 - 1 - 2
    |   |
    4 - 3
```
Note:  
The size of the input 2D-array will be between 3 and 1000.
Every integer represented in the 2D-array will be between 1 and N, where N is the size of the input array.

### Solution

```java
class Solution {
     public int[] findRedundantConnection(int[][] edges) {
        int[] uf = new int[edges.length + 1];
        for (int i = 0; i < uf.length; i++) {
            uf[i] = i;
        }

        for (int[] edge : edges) {
            int p1 = findParent(uf, edge[0]);
            int p2 = findParent(uf, edge[1]);
            if (p1 == p2) {
                return edge;
            }
            uf[p2] = p1;
        }

        return null;
    }

    int findParent(int[] uf, int i) {
        while (uf[i] != i) {
            i = uf[i];
        }
        return i;
    }
}

```
Union-find algorithm 已经忘了，这个是在查看了Wikipedia 之后才做出来的。

## Review 

[What Neil Gaiman and My Secret Agent Grandmother Taught Me](https://medium.com/s/greatescape/how-stories-help-us-escape-491f24b233c8)
讲述了表达的重要性，我们应该考虑成为一个有思想的故事讲述者。


## Tips

- 去掉Windows中的回车符
> tr -d '\r'< shortvideo.txt> shortvideo.txt.1

- 循环需要sleep

> cat shortvideo.txt.1 | xargs -I% sh -c '{ echo % ; sleep 0.5 ; curl http://a.com?id=%; }'

## Share


我们为什么要留在北京，而不是回去小城市里？

放开胸怀
信息，资源，人脉
考虑社会的问题，而不是个人的问题
精气神，雄性的激情
人无好坏之分，所有人都要交往，
目的和手段必不可分

解放思想，思想是前提
奋斗就是敢付诸实施
善于批判



















