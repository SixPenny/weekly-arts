## Algorithm

### Description


In LeetCode Store, there are some kinds of items to sell. Each item has a price.

However, there are some special offers, and a special offer consists of one or more different kinds of items with a sale price.

You are given the each item's price, a set of special offers, and the number we need to buy for each item. The job is to output the lowest price you have to pay for exactly certain items as given, where you could make optimal use of the special offers.

Each special offer is represented in the form of an array, the last number represents the price you need to pay for this special offer, other numbers represents how many specific items you could get if you buy this offer.

You could use any of special offers as many times as you want.

Example 1:
```
Input: [2,5], [[3,0,5],[1,2,10]], [3,2]
Output: 14
Explanation: 
There are two kinds of items, A and B. Their prices are $2 and $5 respectively. 
In special offer 1, you can pay $5 for 3A and 0B
In special offer 2, you can pay $10 for 1A and 2B. 
You need to buy 3A and 2B, so you may pay $10 for 1A and 2B (special offer #2), and $4 for 2A.
```
Example 2:
```
Input: [2,3,4], [[1,1,0,4],[2,2,1,9]], [1,2,1]
Output: 11
Explanation: 
The price of A is $2, and $3 for B, $4 for C. 
You may pay $4 for 1A and 1B, and $9 for 2A ,2B and 1C. 
You need to buy 1A ,2B and 1C, so you may pay $4 for 1A and 1B (special offer #1), and $3 for 1B, $4 for 1C. 
You cannot add more items, though only $9 for 2A ,2B and 1C.
```
Note:
There are at most 6 kinds of items, 100 special offers.
For each item, you need to buy at most 6 of them.
You are not allowed to buy more items than you want, even if that would lower the overall price.




### Solution

DP

```java
class Solution {
    private Map<String, Integer> costMap = new HashMap<>();
    public int shoppingOffers(List<Integer> price, List<List<Integer>> specials, List<Integer> needs) {
        String key = needs.stream().map(String::valueOf).collect(Collectors.joining(","));
        if (costMap.containsKey(key)) {
            return costMap.get(key);
        }
        Integer min = null;
        for (int i = 0; i < specials.size(); i++) {
            List<Integer> special = specials.get(i);
            boolean hasSpecial = true;
            for (int j = 0; j < needs.size(); j++) {
                if (special.get(j) > needs.get(j)) {
                    hasSpecial = false;
                    break;
                }
            }
            if (hasSpecial) {
                List<Integer> tempNeeds = new ArrayList<>();
                for (int j = 0; j < needs.size(); j++) {
                    tempNeeds.add(needs.get(j) - special.get(j));
                }
                int result = special.get(special.size() - 1) + shoppingOffers(price, specials, tempNeeds);
                if (min == null || result < min) {
                    min = result;
                }
            }
        }
        // no special offer used
        int withoutSpecial = 0;
        for (int i = 0; i < needs.size(); i++) {
            withoutSpecial += needs.get(i) * price.get(i);
        }
        if (min == null || withoutSpecial < min) {
            min = withoutSpecial;
        }
        costMap.put(key, min);
        return min;
    }
}
```


Problem that code have:
1. The second needs for loop is redundent. It can be moved above.
2. Integer and int boxing and deboxing

But removing it from code, it doesn's make any progress.
```java
class Solution {
    private Map<String, Integer> costMap = new HashMap<>();
    public int shoppingOffers(List<Integer> price, List<List<Integer>> specials, List<Integer> needs) {
        String key = needs.stream().map(String::valueOf).collect(Collectors.joining(","));
        if (costMap.containsKey(key)) {
            return costMap.get(key);
        }
        int min = 0;
        for (int i = 0; i < needs.size(); i++) {
            min += needs.get(i) * price.get(i);
        }
        for (int i = 0; i < specials.size(); i++) {
            List<Integer> special = specials.get(i);
            List<Integer> tempNeeds = new ArrayList<>();
            boolean hasSpecial = true;
            for (int j = 0; j < needs.size(); j++) {
                if (special.get(j) > needs.get(j)) {
                    hasSpecial = false;
                    break;
                }
                tempNeeds.add(needs.get(j) - special.get(j));
            }
            if (hasSpecial) {
                int result = special.get(special.size() - 1) + shoppingOffers(price, specials, tempNeeds);
                if (result < min) {
                    min = result;
                }
            }
        }
        costMap.put(key, min);
        return min;
    }
}
```


Remove memoiation. It makes a big progress.

```java
class Solution {
    public int shoppingOffers(List<Integer> price, List<List<Integer>> specials, List<Integer> needs) {
        int min = 0;
        for (int i = 0; i < needs.size(); i++) {
            min += needs.get(i) * price.get(i);
        }
        for (int i = 0; i < specials.size(); i++) {
            List<Integer> special = specials.get(i);
            List<Integer> tempNeeds = new ArrayList<>();
            boolean hasSpecial = true;
            for (int j = 0; j < needs.size(); j++) {
                if (special.get(j) > needs.get(j)) {
                    hasSpecial = false;
                    break;
                }
                tempNeeds.add(needs.get(j) - special.get(j));
            }
            if (hasSpecial) {
                int result = special.get(special.size() - 1) + shoppingOffers(price, specials, tempNeeds);
                if (result < min) {
                    min = result;
                }
            }
        }
        return min;
    }
}

```

## Review 

[Rethinking Netflix’s Edge Load Balancing](https://medium.com/netflix-techblog/netflix-edge-load-balancing-695308b5548c)

Netflix 的网关是一直在用的，这篇对于loader balancer 的思考非常棒，也解答了以前对于新启动应用暴增的流量的疑惑。

## Tips

### 问题描述

项目原本使用的同步写入日志方式，后来测试发现qps 一大就全部block 在写日志上了，因此转换为异步方式，配置后发现打印的类名，方法名和行数等信息全部变成了问号。


### 解决方案

异步方式打印日志，async appender 里默认是拿不到日志打印处的信息的，需要配置一下让它可以拿到调用方的信息。

配置如下

```
<appender name="ASYNC" class="ch.qos.logback.classic.AsyncAppender">
  <appender-ref ref="FILE" />
  <queueSize>512</queueSize>
  <!-- add the following line -->
  <includeCallerData>true</includeCallerData>
</appender>

```

logback 的queue size 默认为 256，比较小，可以增大一点。

## Share

最近小组负责的内容变化，以后会兼做iOS 相关的工作。看了一下 objective-c 的语法，深深感觉到语法只是一门语言的表象，语言背后的思想才是通用的。

objective-c 是在c 的基础上封装了对象操作，因此兼具面向过程与面向对象两者的特点。不过它不像 C++ 一样允许多继承，而是和 Java 一样只允许单继承。它的整体设计风格也更偏向 C，譬如说使用头文件和源文件来分割声明和实现。面向对象的特性实现与 Java 也很不一样，是一个了解其他设计的好机会。



