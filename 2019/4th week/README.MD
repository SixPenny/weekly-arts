## Algorithm

### Description

Given two strings S and T, return if they are equal when both are typed into empty text editors. # means a backspace character.

Example 1:
```
Input: S = "ab#c", T = "ad#c"
Output: true
Explanation: Both S and T become "ac".
```
Example 2:
```
Input: S = "ab##", T = "c#d#"
Output: true
Explanation: Both S and T become "".
```
Example 3:
```
Input: S = "a##c", T = "#a#c"
Output: true
Explanation: Both S and T become "c".
```
Example 4:
```
Input: S = "a#c", T = "b"
Output: false
Explanation: S becomes "c" while T becomes "b".
```
Note:

1 <= S.length <= 200
1 <= T.length <= 200
S and T only contain lowercase letters and '#' characters.

### Solution

Stack 方式：O(N) time O(N) space
```java
class Solution {
    public boolean backspaceCompare(String S, String T) {
        Stack<Character> stackS = purify(S);
        Stack<Character> stackT = purify(T);
        if (stackS.size() != stackT.size()) {
            return false;
        }
        while (!stackS.empty()) {
            if (!stackS.pop().equals(stackT.pop())) {
                return false;
            }
        }
        return true;
    }

    private Stack<Character> purify(String s) {
        Stack<Character> stack = new Stack<>();
        for (char b : s.toCharArray()) {
            if (b == '#') {
                if (!stack.empty()) {
                    stack.pop();
                }
            } else {
                stack.push(b);
            }
        }
        return stack;
    }
}
```


Two pointers O(N) time O(1) space

```java
class Solution {
    public boolean backspaceCompare(String S, String T) {
        int ss = S.length(), ts = T.length();
        while (true) {
            ss = previousChar(S, ss);
            ts = previousChar(T, ts);
            if (ss == -1 && ts == -1) {
                return true;
            }
            if (ss == -1 || ts == -1) {
                return false;
            }
            if (S.charAt(ss) != T.charAt(ts)) {
                return false;
            }
        }
    }

    private int previousChar(String s, int end) {
        if (end < 0 || s == null || "".equals(s)) {
            return -1;
        }
        int sharpNum = 0;
        for (int i = end - 1; i >= 0; i--) {
            if (s.charAt(i) == '#') {
                sharpNum++;
            } else if (sharpNum == 0) {
                return i;
            } else {
                sharpNum--;
            }
        }

        return -1;
    }
}
```

## Review

[Special Cases Are a Code Smell](https://blog.conjur.org/special-cases-are-a-code-smell/)

这其实给我很深的触动，在平常写代码时，经常会有通用的一套处理逻辑，突然某一天需要对某一个字段或某一个条件的情况做一个特殊处理。我们大家也就加一个 if 就完事了，在作者看来，这是代码坏味道的开始。从执行流程上来说，一个函数中多一个 if 就多一倍执行路径，相应的 test 就翻一倍。这是藏污纳bug 的地方。如果你平常遇到这种情况就写一个 if，正说明了你在思考上的偷懒，函数最终演变的超长且无人能理解，代码也最终演变的不可维护了。

这个问题，我觉得需要考虑几点
1. 像对待重复代码一样去对待特殊逻辑（第一次不用管它，第二次不用管它，第三次时就需要考虑重构了）
2. 对工作场景多一些思考，如何才能将所有的规则容纳在一个通用的体系中。
3. 了解你所在的业务。很多规则是产品或是业务想出来的，对一般性扩展性考虑不够，需要我们了解自身的业务提出相应的解决办法。

## Tips

从一个继电器制作出一台计算机。

大学里学计算机原理，那些与非门令人非常头大，要说有多么难可能并不见得，我的根本困惑在于与门、非门、或门在物理世界是什么样的？非门如何在输入为0的情况下怎么输出了1？。读了《编码》这本书之后我有种恍然大悟的感觉，原来一切都是这么顺畅。让你困惑的障碍不是你的智力，而是教材。真是一个令人沮丧的事实。



## Share

平台与个人

常听人说：离开平台你什么都不是。作为一个从没有任何IT基础设施建设的创业部门（非创业企业）出来的人，以前对这句话是有点嗤之以鼻的。在那种环境下，可以说什么都需要你自己去干，软件需要自己摸索着装，需要的功能自己写，上线最开始也是自己本地打包上传启动。

在一个没有平台环境的地方是很难对平台这两个字有什么理解的，更不用说去搭建一个平台了。干了几年之后进入了一家还算大的公司，又干了几年，才突然感觉自己有点理解平台这个词了。

这就像从原始森林进入了一个小城镇。原始森林的生活方式是狩猎采集，一切的活计都需要自己团队甚至是自己完成；城镇的生活方式是分工合作，交换获取互相所需。我想这也是为什么说一个人离开了平台就突然间无所适从的原因：你自己会的只是养活自己的一部分技能。平台的生态环境为我们提供了更专业化的开发方式，当我们需要使用某种基础服务时，平台中的某个团队会为我们提供现成的解决方案，不再需要自己去看文档安装，遇到一大堆问题抓耳挠腮了。我们变成了社会化大生产中的一颗螺丝钉，离开了这里你会水土不服，慢慢的对平台产生了依赖。

平台让你精研自己的场景业务，成为一个“专家”，但同时将我们的思维禁锢在一处，失去了全局视野。这对个人成长是致命的，它将你压在那里，如果你恍然不觉，最终会成为一颗特殊规格的螺丝钉，只适合这个位置这个公司，没有了腾挪的余地。

我们需要跳出来，去看看平台的建设者们是如何思考这个问题的。说的简单一点，他们会把眼光聚焦在将公司打造成为一个平台上，而不是成为一颗小小的螺丝钉。那我们是不是也可以这样考虑：如何将自身打造成为一个平台？

要回答这个问题，我们先要问的一个问题是：平台的本质是什么？在我看来，平台的最本质特征就是连接，将平台内各个不能职能的专业化团队连接起来组成了一个对外提供服务平台。个人如何培养这种能力呢？我的看法也是到技术的本质中去，很多技术看起来不同，但究其本质内在是一样的，如果我们纠结与API，纠结于语法，那我们也就被限制在这一种技术里，相反如果我们深入内里，将这种技术的本质掌握在手，我们相当于掌握了一套的技术。语法API这些是可以查技术手册就可以知道的知识，而内在却是让我们成长的“智慧”。

当我们身处平台之中时，享受平台带来的便利，但不要迷失自己，永远记得自己的能力才是自己所能自己的底气。
