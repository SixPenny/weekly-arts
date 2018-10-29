## Algorithm
#### Description

Difficulty: Medium

In an array A of 0s and 1s, how many non-empty subarrays have sum S?

 

Example 1:

Input: A = [1,0,1,0,1], S = 2  
Output: 4  
Explanation:  
The 4 subarrays are bolded below:

[`1,0,1`,0,1]  
[`1,0,1,0`,1]  
[1,`0,1,0,1`]  
[1,0,`1,0,1`]  


#### Solution

看到这道题我首先想到的是使用滑动窗口，从左向右两个指针找出所有答案。解决方法很不优雅。

```go
func numSubarraysWithSum(A []int, S int) int {
    if len(A) == 0 || S < 0 {
        return 0
    }
    
    if S == 0 {
        return zero(A)
    } else {
        return notZero(A, S)
    }
}

func notZero(A []int, S int) int {
    left,right,total, sum := 0, 0, 0, 0
    for true {
        //find first window
        for ;right < len(A) && sum < S;right++ {
            sum += A[right]
        }
        if sum == S {
            total++
        }
        temp := right
        for ; right < len(A) && A[right] ==0;right++{
            total++
        }
        for left <=right && sum == S {
            if A[left] == 0 {
                total += right - temp + 1
            } else {
                sum -= 1
            }
            left++
        }
        if right >= len(A){
            return total
        }
    }
    return 0
}

func zero(A []int) int {
    left,right,total := -1, 0, 0
    for true {
        //first zero
        for ; right < len(A) && A[right] != 0;right++ {
            
        }
        left = right 
        // find sequential zero
        for ; right < len(A) && A[right] == 0; right++ {
            
        }
        total += addon(right - left)
        if right == len(A) {
            return total
        }
    }
    return 0
}

func addon(n int) int{
    total := 0
    for n > 0 {
        total += n
        n--
    }
    return total
}
```

看到网友的[讨论](https://leetcode.com/problems/binary-subarrays-with-sum/discuss/186647/Java-Clean-Solution-2-Sum-+-Prefix-Sum-Caching) ，原来这道题可以如此简单，这才是算法的魅力啊

## Review

[How to Configure Your iPhone to Work for You, Not Against You](https://betterhumans.coach.me/how-to-set-up-your-iphone-for-productivity-focus-and-your-own-longevity-bb27a68cc3d8)

文章列举了一系列的手段来帮助你摆脱手机吞噬时间黑洞，而是让手机变成一个生产力工具，文章太长，可以收藏下来慢慢看。

## Technique

以前一直不理解 `Virtual Memory`,看过很多概念很快就忘了，这两天在看`Modern Operating System`, 里面一句话就说明白了，在此摘抄给大家。

```
Virtual memory makes it possible to run programs larger than physical memory by placing them on the disk and using main memory as a kind of cache for the most heavily executed parts.
```

虚拟内存的本质就是将内存当做了磁盘的缓存，虽然实现手段和 CPU 中的 L1 L2 Cache 不同，但是本质是不变的。

## Share

今日分享一下面试经历

面试不仅仅是找工作，也可以检验自己的能力。周二去面试了一家外资公司，复盘一下，为以后的学习与工作提供经验。

先说一下面试概况。此次面试原定是在一下午完成，有5轮，由于有一个manager 出差，因此变成了4轮。而且职位是以 Go 语言为开发语言，因此面试主要关注算法，简单的java 知识和其他的能力。

第一位来聊了两句二话没说，先做一个算法。给定行列数 row 和column， 生成一个矩阵，使之符合如下形式：
```
1   2   3   4   5  
14  15  16  17  6  
13  20  19  18  7  
12  11  10  9   8  
```

也就是一个由外向内的回型矩阵。 这道题在我看来就是考你的编程基本功，不涉及什么 DP，Two Pointers 等的编程技巧。我的解答方法是四个边界(rowMax, columnMax, rowMin, ColumnMin) 和一个方向，因为它符合一个模式: 先i++,j++,再i--,j--，然后往复，转换的边界就是上面说的四个。在写的过程中才发现问题有几个，一是边界变量太多，导致判断很复杂，二是只有一个方向，需要和边界、当前位置一起决定怎么走，写起来很啰嗦。面试官提示更好的方法可以用一个边界矩阵，把已经走过的位置记录下来，并且使用4个方向，写起来会相对清晰。后面就聊了点建立上写的当前做的工作，就一笔带过吧。

第二位来做了两个算法。一是创建一个 `MaxStack` 类，使之支持 stack 的相关方法(push,pop,top)，并且还可以支持`popMax` 弹出最大值,`peekMax` 查看最大值两个方法。提示用 Java 现有的Stack类来实现。这个题看到说实话有点蒙没什么思路，感觉应该用数组一类的再存一份，但就如何实现上面的要求没有头绪。于是做了一个简单的二叉搜索树来搜寻最大值，并且支持查找删除操作，时间复杂度 O(lgN)。 面试官看了觉得实现没啥问题，就是只是因为这个题特意做了一个BST有点小题大做了，提示用一个数组来存每次push的元素的最大值，这个我后来想想 如果一直 `popMax` 的话，要一直记录pop出的最大数，后面pop 的时候还是要去比对，最差情况会到O(N), 各有优劣了只能说。二是一个森林的题，给一个二维数组，判断有多少颗互不相交的树，使用 DFS ,BFS 都可以。

第三位没有出算法，要求设计一个分布式队列，第一次碰到这样的题。主要设计方面有  

1.API设计  
2.集群管理  
3.容错设计

我按照业界的分布式方案以自己的理解给了一个简单的方案，Master-Task模式，Master 存放meta信息（Task节点，consumer，producer等记录），多台集群使用raft算法选举master。Task节点每个有一个Slave节点做备份，并且存储Queue消息的一部分，client 先从 Master节点获取queue对应的Task节点，然后长连接所有的Task节点生产、消费消息。这个对自己的思维是很大的挑战，相当于从看别人的设计转变为以现有知识自己从零设计，是一种相当有趣的思维锻炼方式，以后可以自己有意识使用。

第四位是研发总监，关注点在业务，组织结构层面，可惜我对组织结构之类的不感兴趣，从大面上说了说被说我跟龚宇说的一样[facepalmed]，然后出了一道给定一个数组和一个sum，找出所有的数组对使得和为sum。面了一下午心态有点膨胀，觉得怎么出这么简单的题，结果使用了一个O(2n)复杂度的算法又被鄙视一通，想想确实一次遍历就够了，还是太轻视算法题了，不能拘泥于一种解法，要精益求精啊。

回头看这次面试，算法的锻炼是很有益处的，让你面对问题时工具库里有更多趁手的工具使用，但也明白了很多不足，很多时候还是太饭来张口了，没有经过深度思考。要对一个问题形成自己的理解最好的方法就是自己去实践、解决它，然后和业内方案作对比才能明白自己弱在哪，再做针对性的提升，循环往复才能成长啊。