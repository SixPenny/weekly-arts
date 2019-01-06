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

���ܿ��ˡ�Test Driven - Practical TDD And Acceptance TDD for Java Developers�� �����н�����ִ��test driven����Ҫ�Ĳ��裬ʱ��ִ٣�ֻ�����ǰ�����¡�

���������Ƴ����`test-code-refactor`������ģʽ��ͨ����д��������֤������ȷ���ع����ġ���ʵ���ҿ�����Ӧ�����Ĳ�`design-test-code-refactor`���ڱ�дtest֮ǰ��������Ҫ��Ū���Ҫ������Api��Ȼ��������API��дtest��code��

ʹ�õ�һ����Ҫ������`test-list`,��������˿ɲ��Ե�����������ʹ��������Ȼ������ȵķ�ʽ������������test�����������е�test������ˣ�����Ҳ������ˡ�


## Share

���һֱ��˼�������������ߴ����������ӿ���뽻�������˲�ͬ�ļ�������ʵ�����Ҫ�����ŶӶ��淶�˲����������������Լ�Ҳ�и����С��Ŀ��������ʵ��һ�¡����쿴����һƪ�����ٶȵ����£�д�ĺܰ������Լ�����������뷨����������¼�����档

[������һ���з��Ŷӵġ������ٶȡ���](https://mp.weixin.qq.com/s/NEs8zO14ZgLTWwyUIwMyqQ)
Code Velocity�Ķ����ǣ�һ�δ���������git���commit time�������������������У��м侭���˶���ʱ�䡣���仰˵�������д�꿪ʼ������ܵ�������������

���code velocity��Ҫ����ĸ��ؼ�����

1.��Ƶ���ذѴ���ϻ�master
2.�ǳ�ǿ����ܻع������
3.һ��������������ֵ�ط���������
4.�Ѵ���Ŀ���С��Ŀ��������

����������ǰѴ���Ŀ���С��Ŀ��ʹ�ò��������ȷ�ʽ��ߴ��������Ӷ����Է��ĵĽ�����ϻ��master������һ����������ֵ�ط�������Ƿȱ����Ҫһ��һ���ظ��ƴ��룬���������ʩ��