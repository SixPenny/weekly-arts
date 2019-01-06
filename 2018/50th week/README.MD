## Algorithm

### Description

Initially on a notepad only one character 'A' is present. You can perform two operations on this notepad for each step:

Copy All: You can copy all the characters present on the notepad (partial copy is not allowed).
Paste: You can paste the characters which are copied last time.
Given a number n. You have to get exactly n 'A' on the notepad by performing the minimum number of steps permitted. Output the minimum number of steps to get n 'A'.

Example 1:
```
Input: 3
Output: 3
Explanation:
Intitally, we have one character 'A'.
In step 1, we use Copy All operation.
In step 2, we use Paste operation to get 'AA'.
In step 3, we use Paste operation to get 'AAA'.
```
Note:
The n will be in the range [1, 1000].

### Solution

```java
class Solution {
    int miniStep = 10000;
    public int minSteps(int n) {
        if(n == 1){
            return 0;
        }
        dp(0, 1, 1, 1, n);
        return miniStep;
    }

    private void dp(int preStep, int totalSteps,int clipBoard, int currentN, int n) {
        if (currentN == n) {
            if (totalSteps < miniStep) {
                miniStep = totalSteps;
                return;
            }
        } else if (currentN > n) {
            return;
        }

        if (preStep != 0) {//copy
            dp(0, totalSteps + 1, currentN, currentN, n);
        }
        dp(1, totalSteps + 1, clipBoard, currentN + clipBoard, n);
    }
}
```

## Review
[The Best Programming Advice I Ever Got](http://russolsen.com/articles/2012/08/09/the-best-programming-advice-i-ever-got.html)

Be part of a team that values progress over politics, ideas over territory and initiative over decorum

## Tips

Spring Qualifier 的使用方法
### 摘要

spring 的 qualifier 平常使用一般直接是使用id 来限定，不过spring给我们提供了更强大的功能。

### 使用方法

#### 直接使用

- 直接在`@Qualifier`中限定bean的id
- 在bean上使用`@Qualifier`来指定后续使用的`value`,然后在注入处使用指定的值

Example：
```java
public interface OrderService {
    String createOrder(String name);
}

@Component
@Qualifier(value = "whatever")
public class EmptyOrderService implements OrderService {
    private Logger logger = LoggerFactory.getLogger(EmptyOrderService.class);
    @Override
    public String createOrder(String name) {
        logger.info("empty order service " + name);
        return "empty";
    }
}

```

#### 自定义

`@Qualifier` 除了可以注解在类上，属性上，还可以注解在 annotation上，这样我们可以创造自己的qualifier(在xml里可以使用`<qualifier>`)。

Example：
id 有时候不明确，我们需要按自己的条件来创造qualifier
```java
@Target(value = {ElementType.TYPE, ElementType.METHOD, ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Qualifier
@Documented
public @interface OrderQualifier {
    String orderName() default"default";
    int orderType() default 0;
}
```
然后就可以使用`@OrderQualifier`来指定和注入bean了。

```java
@Component
@OrderQualifier(orderName = "kill", orderType = 2)
public class KillOrderService implements OrderService {
    private Logger logger = LoggerFactory.getLogger(KillOrderService.class);
    @Override
    public String createOrder(String name) {
        logger.info("kill order service " + name);
        return "kill";
    }
}


@Component
@OrderQualifier(orderName = "normal", orderType = 1)
public class NormalOrderService implements OrderService {
    private Logger logger = LoggerFactory.getLogger(NormalOrderService.class);

    @Override
    public String createOrder(String name) {
        logger.info("normal create order " + name);
        return "normal";
    }
}
```

使用时直接注入：
```java
@RestController
@RequestMapping("order")
public class OrderResource {
    @Autowired
    @OrderQualifier(orderName = "kill", orderType = 2)
    OrderService orderService;
    @Autowired
    @OrderQualifier(orderName = "normal", orderType = 1)
    OrderService normalOrderService;
    @Autowired
    @Qualifier("whatever")
    OrderService emptyOrderService;

    @GetMapping("kill")
    public String print(String name) {
        return orderService.createOrder(name);
    }

    @GetMapping("normal")
    public String normal(String name) {
        return normalOrderService.createOrder(name);
    }
    @GetMapping("empty")
    public String empty(String name) {
        return emptyOrderService.createOrder(name);
    }
}

```

测试一下，分别调用回返回了各自的值。


## Share


战争年代出元帅，出战神。每朝的开国太祖身边都有一大批的能战善战之将，而往后再想出一个元帅级别的人物非常难。这是为何？因为一将功能万骨枯。没有仗打，即使是再好的天赋也发挥不出来。

这给了我们一个朴素的提示：实践是决定你能成长到什么地步的关键因素。回到程序员的职业上来，这句话可以转换成：解决过多少bug是你成长到什么地步的关键因素。学习是一件很重要的事情，但是停留在纸面的学习不是真正的学习，只有应用了，遇到问题了，解决掉我们才算有了些许成长。但是也应该看到，自身能践行的部分是有限的，我们学习到的永远只是我们眼睛看到、实践碰到那一部分，这是存在边界的。换言之，以己身之力想要达到牛人的状态是很艰难的，与和平时代的军人想要成长为战神一样艰难。有很多大公司的神级程序员，他们成长到现在的等级，是公司在由小到大的过程中遇到过数不尽的问题，他们也是如此一步步才成长起来的。

一般人没有这种境遇，如果想靠这种环境成长，那我觉得自己永远也成长不起来。但是我们不只有自己，我们有千千万万从事程序员职业的兄弟姐妹，应当看到他们遇到的问题也是我们成长的契机。从他人的经历中吸收经验，从他人的问题中获得自身的成长。我意识到这一点后，就给自己定了一个目标：每天解答一个问题。问题可能很小，但角度可能自己没想到；问题可能很大，自己从来没有考虑过；也可能是自己碰到过的问题，帮助别人也是锻炼自己写作、表达能力的一个机会。

我们有时会抱怨写业务代码重复繁琐，没有成长的空间。人生也是这样，乏味无聊。我们要变着法子折腾，闪转腾挪出空间，公司天地小，那就去广大的互联网世界里寻找。


















