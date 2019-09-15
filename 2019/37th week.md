## Algorithm 

[199. Binary Tree Right Side View](https://github.com/SixPenny/leetcode/blob/master/problems/199.%20Binary%20Tree%20Right%20Side%20View.md)

## Review

[LAMBDAS FOR C — SORT OF](https://hackaday.com/2019/09/11/lambdas-for-c-sort-of/)

这篇文章介绍了如何使用 gcc 的一些 feature 来实现 c 中的 lambda，很有趣

## Tips

中秋之前遇到了一个Android 问题，ListView 在使用复用模式时对于图片头像的展示出现了混乱。

多人头像图片的展示使用了 `CombineBitMap` 框架来组合头像展示，`CombineBitMap` 没有使用 tag 来判断当前ImageView 是否是在可视区域内的，而这个github 上有人提了bug 作者并没有解决，因此我将源代码直接放到了程序中，自行加入了判断，但这只解决了一个问题，就是`CombineBitMap` 覆盖其他 ImageView 的图片的问题。

再次测试过程中，我们发现后面加载的头像元素覆盖了前面的多人头像，缘由在于单个人的头像使用了`com.nostra13.universalimageloader` 组件展示而不是使用相同的 `CombineBitMap` ， 我将图片展示换成统一的 `CombineBitMap` ，就没有问题了。

## Share

[设计模式之观察者模式](https://my.oschina.net/liufq/blog/3104646)
