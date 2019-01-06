## Algorighm

### String to integer(atoi)

```
Implement atoi which converts a string to an integer.

The function first discards as many whitespace characters as necessary until the first non-whitespace character is found. Then, starting from this character, takes an optional initial plus or minus sign followed by as many numerical digits as possible, and interprets them as a numerical value.

The string can contain additional characters after those that form the integral number, which are ignored and have no effect on the behavior of this function.

If the first sequence of non-whitespace characters in str is not a valid integral number, or if no such sequence exists because either str is empty or it contains only whitespace characters, no conversion is performed.

If no valid conversion could be performed, a zero value is returned.

Note:

Only the space character ' ' is considered as whitespace character.
Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [?231,  231 ? 1]. If the numerical value is out of the range of representable values, INT_MAX (231 ? 1) or INT_MIN (?231) is returned.
Example 1:

Input: "42"
Output: 42
Example 2:

Input: "   -42"
Output: -42
Explanation: The first non-whitespace character is '-', which is the minus sign.
             Then take as many numerical digits as possible, which gets 42.
Example 3:

Input: "4193 with words"
Output: 4193
Explanation: Conversion stops at digit '3' as the next character is not a numerical digit.
Example 4:

Input: "words and 987"
Output: 0
Explanation: The first non-whitespace character is 'w', which is not a numerical 
             digit or a +/- sign. Therefore no valid conversion could be performed.
Example 5:

Input: "-91283472332"
Output: -2147483648
Explanation: The number "-91283472332" is out of the range of a 32-bit signed integer.
             Thefore INT_MIN (?231) is returned.
```


### My Solution

```java
package com.dylan.leetcode;

import org.junit.Assert;
import org.junit.Test;

/**
 * Created by liufengquan on 2018/7/10.
 */
public class StringToInteger {
    public int myAtoi(String str) {
        int result = 0;

        if (str.length() == 0
                || (str = trimStartingWhiteSpaces(str)).length() == 0) {
            return result;
        }
        int pre;
        int flag = 1;
        for (int i = 0; i < str.length(); i++) {
            if (i == 0 && str.charAt(i) == '-') {
                flag = -1;
                continue;
            }
            if (i == 0 && str.charAt(i) == '+') {
                continue;
            }
            if (Character.isDigit(str.charAt(i))) {
                pre = result;
                if (flag == 1) {
                    result = result * 10 + Character.digit(str.charAt(i), 10);
                } else {
                    result = result * 10 - Character.digit(str.charAt(i), 10);
                }
                if (pre != (result / 10)) {
                    return flag == -1 ? Integer.MIN_VALUE : Integer.MAX_VALUE;
                }

            } else {
                break;
            }
        }

        return result;
    }

    private String trimStartingWhiteSpaces(String string) {
        int index = 0;
        while (index < string.length() && string.charAt(index) == ' ') {
            index++;
        }

        return string.substring(index);
    }


    @Test
    public void test() {
        Assert.assertTrue(42 == myAtoi("42"));
        Assert.assertTrue(0 == myAtoi(" "));
        Assert.assertTrue(43 == myAtoi("    43"));
        Assert.assertTrue(-43 == myAtoi("   -43"));
        Assert.assertTrue(5555 == myAtoi("5555alksdjfk"));
        Assert.assertTrue(0 == myAtoi(" word with 98987"));
        Assert.assertTrue(-2147483648 == myAtoi("-91283472332"));
        Assert.assertTrue(-2147483648 == myAtoi("-6147483648"));
        Assert.assertTrue(Integer.MAX_VALUE == myAtoi("215645657478785547454"));
    }
}

```


### Problems met

- how to test whether result has overflowed? 
    - I use `flat == 1 && pre > 0 && result < 0` at first to test it(also when `flag == -1`). It's wrong because this expression is only valid when do binary operation such as `result = result < 1`. 
    - Secondly, I run `flag == 1 && pre > result` and `flag == -1 && pre < result`. It succeeds in most of cases but `-6147483648`. 
    - Finally I figured it out using `pre != (result / 10)`
    - Somebody using Long to represent result. It's smart but memory-costing
- Failed at `+32`, `whitespace("  ")`


## Review

[what-if-people-were-paid-for-their-data](https://medium.com/@the_economist/what-if-people-were-paid-for-their-data-8df63f021e38)

- data today should be viewd as labour and paid
- people united to protect their data
- how to achieve this? A union is needed and will grow gradully.


大公司通过收集个人信息，做AI训练，卖广告，每年几千亿的规模，那有没有可能将个人信息也作为一种商品来交易呢？作者探讨了这种可能性，不过在我看来，这是非常困难的。Free for free. 如果我们将我们的信息作为一种商品买卖，那么我们现在使用的各项免费服务也都势必收费化，这本来就是一种交易。要想实现平等是不太可能的，毕竟不平等是我们的天性。



## Technique


本周看了一下经典的《C语言教程》，虽然在大学时候就是使用C入的门，但是这么多年过去，脑海中那点记忆早就消失殆尽了。用惯了Java，再看C的函数，那真是处处是坑。

### 隐式声明

函数可以在函数体中声明，但不可在函数体中定义。如果采用先声明后使用的方式使用，并且函数定义与调用它的函数放在同一个源文件中，如果声明类型不一致，那么编译器会检测到该错误。如果函数是单独编译的，不匹配的错误就无法检测出来(first hole)。

如果先前没有声明过的一个名字出现在某个表达式中，并且其后紧跟一个左圆括号，那么上下文就会认为该名字是一个函数名字，并且被隐式声明(second hole)。


### 外部变量

C语言程序可以看成由一系列外部对象构成，可能是变量或函数。internal用于描述定义在函数内部的参数及变量，外部变量定义在函数之外，可以在多个函数中使用。函数本身是外部的。

外部变量与函数性质：通过同一个名字对外部变量的所有引用（即使是单独编译的不同函数），实际上都是引用的同一个对象

外部变量可以在全局范围访问，这就为函数之间数据交换提供了一种可以替代函数参数和返回值的方式(hole)。但必须要特别小心，最好不要这样做。

外部变量的作用于从声明它的地方开始，到所在文件的末尾结束。如果要在外部变量的定义之前使用该变量，或外部变量的定义与变量的使用不再同一个源文件中，则必须在相应的变量声明中强制使用关键字`extern`，extern声明无需指定数组的长度。


变量的声明用于说明变量的属性，而变量定义除此之外还将引起存储器的分配(所以不同地方的声明引用的是同一个存储地址)。


用 `static`限定外部变量与函数，可以将对象的作用域限定为被编译源文件的剩余部分。可以减少同一程序中其他文件中相同名字的冲突。类似java中的包作用域。static也可以声明内部变量，但是它一直存在，是一种只能再特定函数中使用单一直占据存储空间的变量(`潜在内存泄漏`)



## Share


今天想谈谈追根究底的精神。现在做程序，好像是越老越胆小，越好面子。在上家公司，不管是测试还是线上出了问题，都是一句话：赶紧先把问题解决掉。解决完之后皆大欢喜，没有人再去追究这个问题产生的根源是什么，如何保证下次不再犯同样的错误(想起了头条要求产品复盘被强制离职的同学)。

这家公司的小组长给我了不同的感受。每次线上出问题(由于涉及团队多，流程长，线上经常会出现一些问题)，他打电话给相关负责人，在最后问题解决了之后，总是会问`我就是想知道出这个问题的原因是什么`，能想象的到电话对面人的窘迫。在组内有时候出问题，如果你遮遮掩掩，总是会被问的哑口无言。

希望大家都能有这种打破砂锅问到底的精神，更要找到自己出问题的地方，避免下次再跳进同一个坑里。