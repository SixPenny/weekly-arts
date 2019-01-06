## Algorithm

### description

Given a string containing digits from 2-9 inclusive, return all possible letter combinations that the number could represent.

A mapping of digit to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.

![phone image](http://upload.wikimedia.org/wikipedia/commons/thumb/7/73/Telephone-keypad2.svg/200px-Telephone-keypad2.svg.png)

Example:

Input: "23"
Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
Note:

Although the above answer is in lexicographical order, your answer could be in any order you want.


### solution

```
package com.dylan.leetcode;

import java.util.*;

import org.junit.Assert;
import org.junit.Test;

/**
 * Created by liufengquan on 2018/7/30.
 */
public class LetterCombinationsOfAPhoneNumber {
    private Map<Integer, List<String>> numberAlphaMap = new HashMap<>();

    {
        numberAlphaMap.put(2, Arrays.asList("a","b","c"));
        numberAlphaMap.put(3, Arrays.asList("d","e","f"));
        numberAlphaMap.put(4, Arrays.asList("g","h","i"));
        numberAlphaMap.put(5, Arrays.asList("j","k","l"));
        numberAlphaMap.put(6, Arrays.asList("m","n","o"));
        numberAlphaMap.put(7, Arrays.asList("p","q","r", "s"));
        numberAlphaMap.put(8, Arrays.asList("t","u","v"));
        numberAlphaMap.put(9, Arrays.asList("w","x","y", "z"));
    }
    public List<String> letterCombinations(String digits) {
        List<Integer> integers = new ArrayList<>();
        for (int i = 0; i < digits.length(); i++) {
            integers.add(Character.digit(digits.charAt(i), 10));
        }

        return integers.stream().map(integer -> numberAlphaMap.get(integer))
                .reduce(new LinkedList<>(), this::zip);
    }

    private List<String> zip(List<String> left, List<String> right) {
        if (left.size() == 0) {
            return right;
        }
        if (right.size() == 0) {
            return left;
        }
        List<String> result = new LinkedList<>();
        for (String s : left) {
            for (String s1 : right) {
                result.add(s + s1);
            }
        }
        return result;
    }

    @Test
    public void testLetterCombination() {
        List<String> result = letterCombinations("23");
        Assert.assertArrayEquals(result.toArray(), Arrays.asList("ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf").toArray());

    }
}

```

From left to right, zip each other.

## Review

This week's article is [How to Split JUnit Tests in a Continuous Integration Environment](https://semaphoreci.com/community/tutorials/how-to-split-junit-tests-in-a-continuous-integration-environment)

I didn't aware of this for a long time. Sometimes I remove the `@Test` annotation manually in case that it doesn't block the test running. Apparently it is low and ineffienct.

This article describes 3 ways to split it.

- Using Surefire Exclusions to Split Fast Tests from Slow Tests
- Splitting JUnit Tests According to Execution Environment
- Splitting JUnit Tests Into Logical Subsets
   - use `@Category` annotation
  
I checked the Junit website and find another useful annotation `@Ignore`. For some network tests, you can ignore it. When you need to run a specific ignored test, run it manually.


## Technique

本周看了《Test Driven - Practical TDD And Acceptance TDD for Java Developers》 ，书中介绍了执行test driven所需要的步骤，时间仓促，只浏览了前面两章。

测试驱动推崇的是`test-code-refactor`三步走模式，通过先写测试来保证功能正确，重构安心。其实在我看来，应该是四步`design-test-code-refactor`，在编写test之前，我们需要先弄清楚要如何设计Api，然后才能针对API编写test和code。

使用的一个重要工具是`test-list`,里面包含了可测试的用例，可以使用深度优先或广度优先的方式来向其中添加test用例，等所有的test都完成了，任务也就完成了。


## Share

最近一直在思考、查找如何提高代码质量，加快代码交付，看了不同的技术。其实这个需要整个团队都规范了才能跑起来，不过自己也有负责的小项目，可以先实践一下。今天看到了一篇代码速度的文章，写的很棒，比自己零零碎碎的想法更完整，记录在下面。

[如何提高一个研发团队的“代码速度”？](https://mp.weixin.qq.com/s/NEs8zO14ZgLTWwyUIwMyqQ)
Code Velocity的定义是：一段代码变更，从git里的commit time，到在生产环境里运行，中间经过了多少时间。换句话说，代码从写完开始，多快能到达生产环境。

提高code velocity需要提高四个关键能力

1.能频繁地把代码合回master
2.非常强大的跑回归的能力
3.一键部署乃至无人值守发布的能力
4.把大项目拆成小项目做的能力

最近在做的是把大项目拆成小项目，使用测试驱动等方式提高代码质量从而可以放心的将代码合会回master。现在一键部署、无人值守发布能力欠缺，需要一步一步地改善代码，建设基础设施。