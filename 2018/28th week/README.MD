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


��˾ͨ���ռ�������Ϣ����AIѵ��������棬ÿ�꼸ǧ�ڵĹ�ģ������û�п��ܽ�������ϢҲ��Ϊһ����Ʒ�������أ�����̽�������ֿ����ԣ��������ҿ��������Ƿǳ����ѵġ�Free for free. ������ǽ����ǵ���Ϣ��Ϊһ����Ʒ��������ô��������ʹ�õĸ�����ѷ���Ҳ���Ʊ��շѻ����Ȿ������һ�ֽ��ס�Ҫ��ʵ��ƽ���ǲ�̫���ܵģ��Ͼ���ƽ�������ǵ����ԡ�



## Technique


���ܿ���һ�¾���ġ�C���Խ̡̳�����Ȼ�ڴ�ѧʱ�����ʹ��C����ţ�������ô�����ȥ���Ժ����ǵ���������ʧ�����ˡ��ù���Java���ٿ�C�ĺ����������Ǵ����ǿӡ�

### ��ʽ����

���������ں��������������������ں������ж��塣���������������ʹ�õķ�ʽʹ�ã����Һ���������������ĺ�������ͬһ��Դ�ļ��У�����������Ͳ�һ�£���ô���������⵽�ô�����������ǵ�������ģ���ƥ��Ĵ�����޷�������(first hole)��

�����ǰû����������һ�����ֳ�����ĳ������ʽ�У�����������һ����Բ���ţ���ô�����ľͻ���Ϊ��������һ���������֣����ұ���ʽ����(second hole)��


### �ⲿ����

C���Գ�����Կ�����һϵ���ⲿ���󹹳ɣ������Ǳ���������internal�������������ں����ڲ��Ĳ������������ⲿ���������ں���֮�⣬�����ڶ��������ʹ�á������������ⲿ�ġ�

�ⲿ�����뺯�����ʣ�ͨ��ͬһ�����ֶ��ⲿ�������������ã���ʹ�ǵ�������Ĳ�ͬ��������ʵ���϶������õ�ͬһ������

�ⲿ����������ȫ�ַ�Χ���ʣ����Ϊ����֮�����ݽ����ṩ��һ�ֿ���������������ͷ���ֵ�ķ�ʽ(hole)��������Ҫ�ر�С�ģ���ò�Ҫ��������

�ⲿ�����������ڴ��������ĵط���ʼ���������ļ���ĩβ���������Ҫ���ⲿ�����Ķ���֮ǰʹ�øñ��������ⲿ�����Ķ����������ʹ�ò���ͬһ��Դ�ļ��У����������Ӧ�ı���������ǿ��ʹ�ùؼ���`extern`��extern��������ָ������ĳ��ȡ�


��������������˵�����������ԣ��������������֮�⻹������洢���ķ���(���Բ�ͬ�ط����������õ���ͬһ���洢��ַ)��


�� `static`�޶��ⲿ�����뺯�������Խ�������������޶�Ϊ������Դ�ļ���ʣ�ಿ�֡����Լ���ͬһ�����������ļ�����ͬ���ֵĳ�ͻ������java�еİ�������staticҲ���������ڲ�������������һֱ���ڣ���һ��ֻ�����ض�������ʹ�õ�һֱռ�ݴ洢�ռ�ı���(`Ǳ���ڴ�й©`)



## Share


������̸̸׷�����׵ľ������������򣬺�����Խ��Խ��С��Խ�����ӡ����ϼҹ�˾�������ǲ��Ի������ϳ������⣬����һ�仰���Ͻ��Ȱ����������������֮��Դ�ϲ��û������ȥ׷�������������ĸ�Դ��ʲô����α�֤�´β��ٷ�ͬ���Ĵ���(������ͷ��Ҫ���Ʒ���̱�ǿ����ְ��ͬѧ)��

��ҹ�˾��С�鳤�����˲�ͬ�ĸ��ܡ�ÿ�����ϳ�����(�����漰�ŶӶ࣬���̳������Ͼ��������һЩ����)������绰����ظ����ˣ��������������֮�����ǻ���`�Ҿ�����֪������������ԭ����ʲô`��������ĵ��绰�����˵ľ��ȡ���������ʱ������⣬������������ڣ����ǻᱻ�ʵ��ƿ����ԡ�

ϣ����Ҷ��������ִ���ɰ���ʵ��׵ľ��񣬸�Ҫ�ҵ��Լ�������ĵط��������´�������ͬһ�����