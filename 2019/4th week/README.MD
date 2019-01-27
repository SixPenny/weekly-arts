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

Stack ��ʽ��O(N) time O(N) space
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

����ʵ���Һ���Ĵ�������ƽ��д����ʱ����������ͨ�õ�һ�״����߼���ͻȻĳһ����Ҫ��ĳһ���ֶλ�ĳһ�������������һ�����⴦�������Ǵ��Ҳ�ͼ�һ�� if �������ˣ������߿��������Ǵ��뻵ζ���Ŀ�ʼ����ִ����������˵��һ�������ж�һ�� if �Ͷ�һ��ִ��·������Ӧ�� test �ͷ�һ�������ǲ�����bug �ĵط��������ƽ���������������дһ�� if����˵��������˼���ϵ�͵�������������ݱ�ĳ��������������⣬����Ҳ�����ݱ�Ĳ���ά���ˡ�

������⣬�Ҿ�����Ҫ���Ǽ���
1. ��Դ��ظ�����һ��ȥ�Դ������߼�����һ�β��ù������ڶ��β��ù�����������ʱ����Ҫ�����ع��ˣ�
2. �Թ���������һЩ˼������β��ܽ����еĹ���������һ��ͨ�õ���ϵ�С�
3. �˽������ڵ�ҵ�񡣺ܶ�����ǲ�Ʒ����ҵ��������ģ���һ������չ�Կ��ǲ�������Ҫ�����˽�������ҵ�������Ӧ�Ľ���취��

## Tips

��һ���̵���������һ̨�������

��ѧ��ѧ�����ԭ������Щ��������˷ǳ�ͷ��Ҫ˵�ж�ô�ѿ��ܲ������ã��ҵĸ��������������š����š�����������������ʲô���ģ��������������Ϊ0���������ô�����1�������ˡ����롷�Ȿ��֮�������ֻ�Ȼ����ĸо���ԭ��һ�ж�����ô˳��������������ϰ�����������������ǽ̲ġ�����һ�����˾�ɥ����ʵ��



## Share

ƽ̨�����

������˵���뿪ƽ̨��ʲô�����ǡ���Ϊһ����û���κ�IT������ʩ����Ĵ�ҵ���ţ��Ǵ�ҵ��ҵ���������ˣ���ǰ����仰���е���֮�Աǵġ������ֻ����£�����˵ʲô����Ҫ���Լ�ȥ�ɣ�������Ҫ�Լ�������װ����Ҫ�Ĺ����Լ�д�������ʼҲ���Լ����ش���ϴ�������

��һ��û��ƽ̨�����ĵط��Ǻ��Ѷ�ƽ̨����������ʲô����ģ�������˵ȥ�һ��ƽ̨�ˡ����˼���֮�������һ�һ����Ĺ�˾���ָ��˼��꣬��ͻȻ�о��Լ��е�����ƽ̨������ˡ�

������ԭʼɭ�ֽ�����һ��С����ԭʼɭ�ֵ����ʽ�����Բɼ���һ�еĻ�ƶ���Ҫ�Լ��Ŷ��������Լ���ɣ���������ʽ�Ƿֹ�������������ȡ�������衣������Ҳ��Ϊʲô˵һ�����뿪��ƽ̨��ͻȻ�������ʴӵ�ԭ�����Լ����ֻ�������Լ���һ���ּ��ܡ�ƽ̨����̬����Ϊ�����ṩ�˸�רҵ���Ŀ�����ʽ����������Ҫʹ��ĳ�ֻ�������ʱ��ƽ̨�е�ĳ���Ŷӻ�Ϊ�����ṩ�ֳɵĽ��������������Ҫ�Լ�ȥ���ĵ���װ������һ�������ץ�������ˡ����Ǳ������ữ�������е�һ����˿�����뿪���������ˮ�������������Ķ�ƽ̨������������

ƽ̨���㾫���Լ��ĳ���ҵ�񣬳�Ϊһ����ר�ҡ�����ͬʱ�����ǵ�˼ά������һ����ʧȥ��ȫ����Ұ����Ը��˳ɳ��������ģ�������ѹ�����������Ȼ���������ջ��Ϊһ�����������˿����ֻ�ʺ����λ�������˾��û������Ų����ء�

������Ҫ��������ȥ����ƽ̨�Ľ������������˼���������ġ�˵�ļ�һ�㣬���ǻ���۹�۽��ڽ���˾�����Ϊһ��ƽ̨�ϣ������ǳ�Ϊһ��СС����˿�����������ǲ���Ҳ�����������ǣ���ν����������Ϊһ��ƽ̨��

Ҫ�ش�������⣬������Ҫ�ʵ�һ�������ǣ�ƽ̨�ı�����ʲô�����ҿ�����ƽ̨����������������ӣ���ƽ̨�ڸ�������ְ�ܵ�רҵ���Ŷ��������������һ�������ṩ����ƽ̨����������������������أ��ҵĿ���Ҳ�ǵ������ı�����ȥ���ܶ༼����������ͬ�������䱾��������һ���ģ�������Ǿ�����API���������﷨��������Ҳ�ͱ���������һ�ּ�����෴�������������������ּ����ı����������֣������൱��������һ�׵ļ������﷨API��Щ�ǿ��Բ鼼���ֲ�Ϳ���֪����֪ʶ��������ȴ�������ǳɳ��ġ��ǻۡ���

����������ƽ̨֮��ʱ������ƽ̨�����ı���������Ҫ��ʧ�Լ�����Զ�ǵ��Լ������������Լ������Լ��ĵ�����