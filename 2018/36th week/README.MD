## Algorithm

#### Description

name :50. Pow(x, n)

diffcultiy : medium

```
Implement pow(x, n), which calculates x raised to the power n (xn).

Example 1:

Input: 2.00000, 10
Output: 1024.00000
Example 2:

Input: 2.10000, 3
Output: 9.26100
Example 3:

Input: 2.00000, -2
Output: 0.25000
Explanation: 2-2 = 1/22 = 1/4 = 0.25


Note:
-100.0 < x < 100.0
n is a 32-bit signed integer, within the range [?231, 231 ? 1]

```

#### Solution

1. 一次for 循环， O(n) 复杂度，不过会 TLE
2. 二分法

利用 x^2n = x^n * x^n 来解决
```
func myPow(x float64, n int) float64 {
    if n == 0 || x == 1.0{
        return 1
    }
    if n == 1{
        return x
    }
    
    if n > 0 {
        if n % 2 == 0 {
            return myPow(x * x, n / 2)
        }
    
        return x * myPow(x * x, (n - 1) / 2)
    }
    return 1 / myPow(x, -n)
}
```
## Review

[Survival Tips For Women In Tech](https://patricia.no/2018/09/06/survival_tips_for_women_in_tech.html)

虽然女性的社会地位在当今相比古代提升了不止一点，但是女性在工作中还是会受到各种各样的歧视，这篇文章为女性在科技行业工作提供了24个技巧，来帮助女性在社会中“存活” 下来。我觉得里面的技巧不止对女性，对每一个想要在职业上有更好发展的人，都值得一看。

技巧有很多，这里只摘几个我觉得最有用的。

1. 不要试图成为他们中的一员，要把全部精力放在工作上。
2. 文档化自己的工作。
3. 心情糟糕的日子里提醒自己为何进入到这份事业中来的
4. 如果你有很棒的想法，做一个demo出来而不是空口白话
5. 不惧怕退出，不要因为“坏人”牺牲自己的精神健康
6. 学习，学习，学习，知识就是力量
7. 与非科技相关的工作保持距离如果你想做科技相关的事情的话


## Technique

What is a patch in a version control system?

#### 摘要

以前一直用 SVN 图形界面，只会使用页面 update， commit ，branch 等功能，前两天为了打一个docker 镜像，在centos 上安装了SVN ，发现命令中有一个 patch，不明所以，因此记录一下。

##### What

patch 中文意思是补丁，顾名思义在版本管理系统中就是给系统的代码打补丁。公司里面用的比较少，因为大家一个团队使用一个SVN 地址提交代码，但是在全世界程序员参加的开源项目使用这种方式就不行了，需要有专门人来做代码 review 和 merge。每个人 fork 一个分支，开发完之后生成一个 patch 文件给原项目人员，由他们审查之后决定要不要合并到主干代码中(GitHub 里叫 Pull Request)

放一张流程图：

![](https://i.stack.imgur.com/OYVpL.png)

##### How

类 Unix 系统中都会自带 `patch` 和 `diff` 程序，可以使用这两个来演示一下补丁的使用。

我们现在有两个文件（SVN 系统中是本地跟远程文件diff，实际也是两个文件）file1, file2，他们的文件内容分别如下：

```file
$ cat file1
this is file1
nothing more
line 3
$ cat file2
this is file2
nothing more
```

使用 `diff` 比较两个文件：
```
$ diff -u file1 file2
--- file1       2018-09-07 10:57:28.853583937 +0800
+++ file2       2018-09-07 10:57:06.062578590 +0800
@@ -1,3 +1,2 @@
-this is file1
+this is file2
 nothing more
-line 3
```

这就是 patch 文件的格式，可以看到是使用特殊的符号来标识每行内容的增减，修改，最开始是文件的时间戳信息。我们创建一个 `file-diff.patch`文件，然后就可以用`patch` 程序来打上补丁。

```
$ diff -u file1 file2 > file-diff.patch
$ patch file1 file-diff.patch
patching file file1
$ cat file1
this is file2
nothing more
```

如果你同时修改了file1，patch 和file1 内容就可能产生冲突，这回生成`file1.rej`文件，这就需要你自己手动去解决冲突，合并代码了。

## Share

周五团队内部团建，去了一个轰趴馆，周六回来全身疲惫，睡了一下午，今天(09-09)又睡了一上午，才感觉到自己有点缓过来，但是头还是有点疼，后背酸痛，提不起精神来。

回头想一想，这次团建对自身有什么帮助没有？答案其实很明显，除了吃喝玩乐，轰趴对于个人、团队其实并没有什么作用(我觉得所有的团建结果都是这样)，回来之后还要休息很久才能缓过来，这让我反思团建自身可以做什么，而不做什么才是对自己最负责的呢？

首先要明确自身是否要参加团建，作为公司提供的一种活动形式，其实公司并没有强迫个人必须参加，这需要根据每个人的时间规划、团建观自行决定。就我自身而言，平常出去玩的机会比较少，团建这种作为一次玩耍的选择还是不错的，如果没有冲突，我一般会选择参加。

其次是明确团建目标，公司的目标和个人的目标肯定是不一样的，我们只讨论个人目标。个人的团建目标我觉得首先就是放松，其次才是玩乐，玩乐要以放松为前提。适当的释放工作压力是必要的，但就因为这次机会而拼命玩，回来之后几天休息不过来却是有点得不偿失(年轻人忽略)。我感觉大部分人包括我都没有很好的把控玩的度，在我凌晨2点回房间睡觉的时候，有人在k房里唱歌，有人还在游戏机前打街霸。

最后是执行。每个人都有从众心理，当别人叫你去打牌，而时间指针已经划过了12点，这时你需要考虑的不是自己的面皮薄说不出拒绝的话，而要考虑打牌与我当初来定下的团建目标是否冲突？如果你的目标是疯狂两天，那就毫不犹豫的答应；如果你的目标是放松一下，那你应该鼓励自己拒绝然后去睡觉。

对于自身来说，这次团建消耗了太多的精力，当然也是一个警示，以后再参加团建或类似活动时，好好想一想自己为了什么来参加的？