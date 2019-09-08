## Algorithm

LeetCode 新增了并发题，总共 4 道，地址是：https://leetcode.com/problemset/concurrency/

[1114. Print in Order](https://github.com/SixPenny/leetcode/blob/master/problems/1114.%20Print%20in%20Order.md)  

[1115. Print FooBar Alternately](https://github.com/SixPenny/leetcode/blob/master/problems/1115.%20Print%20FooBar%20Alternately.md)  

[1116. Print Zero Even Odd](https://github.com/SixPenny/leetcode/blob/master/problems/1116.%20Print%20Zero%20Even%20Odd.md)  

[1117. Buildhttps://github.com/SixPenny/leetcode/blob/master/problems/1116.%20Print%20Zero%20Even%20Odd.mding H2O](https://github.com/SixPenny/leetcode/blob/master/problems/1117.%20Building%20H2O.md)  

## Review

1. 
[How code reviews work at Microsoft](https://medium.com/free-code-camp/how-code-reviews-work-at-microsoft-4ebdea0cd0c0)

[Proven Code Review Best Practices from Microsoft](michaelagreiler.com/code-review-best-practices/)

Code Review 必须要有工具支持，以前在团队里也实践过，文章作者写了很多最佳实践，不过最根本的没有写出来，那就是好的代码 taste。不管流程怎么规范，怎么客气，如果没有好的代码 taste 是无法给出有价值的 review 意见的。

2. [Syslog : The Complete System Administrator Guide](https://devconnected.com/syslog-the-complete-system-administrator-guide/)

本文介绍了 Syslog 协议，和现在的分布式日志处理有相似之处，可作借鉴。

## Tips

Android 中使用了 GridLayoutManager ，如何判断用户是否在布局的底部。

```java
private boolean isBottom() {
    GridLayoutManager gridLayoutManager = (GridLayoutManager) mRvMsg.getLayoutManager();
    if (gridLayoutManager == null) {
        return true;
    }
    int lastPos = gridLayoutManager.findLastVisibleItemPosition();

    int size = mPresenter.getData().size();

    return size - lastPos <= 5;
}

```

## Share

终于读完了《基业长青》下面是读书笔记

[个人架构：使命、愿景、核心价值观](https://cloud.tencent.com/developer/article/1500299)
