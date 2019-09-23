## Algorithm

[116. Populating Next Right Pointers in Each Node](https://github.com/SixPenny/leetcode/blob/master/problems/116.%20Populating%20Next%20Right%20Pointers%20in%20Each%20Node.md)

[1195. Fizz Buzz Multithreaded](https://github.com/SixPenny/leetcode/blob/master/problems/1195.%20Fizz%20Buzz%20Multithreaded.md)


## Review

[How Does Exercise Improve Work Productivity?](https://www.livestrong.com/article/422836-how-does-exercise-improve-work-productivity/)

- 规律运动可以让你充满能量和提高警觉
- 运动可以增加大脑分泌血清素
- 运动增加血液流动，增加流入脑中的血液量
- 运动可以减轻疾病风险，包括肥胖，糖尿病，心脏病等。


[Why You Should Mentor a Junior Developer](https://medium.com/hackernoon/why-you-should-mentor-a-junior-developer-d6c51ab95c75)

做一名初学者的导师，不仅是教，更是学。


## Tips

最近Android 发现使用 sqlite 数据库保存数据有丢失的情况，但是丢失呢只是丢失某一张表里的数据，而不是全部丢失。这张表是一张 key-value 表，里面保存的是项目相关的各种配置。没有找到具体原因，于是我们将数据库配置的形式改换为文件方法，使用 `SharePreference` 来实现。

代码如下：
```java
SharedPreferences sp = context.getSharedPreferences(key, Context.MODE_PRIVATE);

// 写入
SharedPreferences.Editor editor = sp.edit();
editor.putString(key, value);
editor.apply();

// 读取
String result = sp.getString(key, "");
```

## Share

本周看了很多李永乐老师的视频，都很有意思。有一期讲为什么双十一的优惠规则设置的那么复杂[网购的优惠规则为啥这么复杂](https://www.youtube.com/watch?v=CDORrtQu3fE)

原因是价格歧视。以前卖东西只设置一个固定价格，心理价位比定价高的就买了，心理价位比定价低的就不买，这就造成了商家少赚了心理价位高的钱，没有赚到心理价位低的人的钱。如何才能利益最大化呢？商家就通过优惠券、复杂的优惠规则、动态定价等方式来最大化利益。

原理是这样，网站定一个比较高的价，心理价位高的人看到网站的这个商品，虽然价比较高，但是在他的心理价位之下，那么嫌麻烦就直接买了；心理价位低的人看到觉得价格很高，但是通过研究复杂的优惠规则之后，可以优惠到自己的心理价位，他就以比较低的价格买了。这样商家就可以两部分人同时兼顾，实现更高的利润。

以前亚马逊爆出过不同的人看到的商品价格不一样，也是价格歧视的一种，只是更赤裸裸罢了。
