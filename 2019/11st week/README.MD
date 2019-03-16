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

[Rethinking Netflix��s Edge Load Balancing](https://medium.com/netflix-techblog/netflix-edge-load-balancing-695308b5548c)

Netflix ��������һֱ���õģ���ƪ����loader balancer ��˼���ǳ�����Ҳ�������ǰ����������Ӧ�ñ������������ɻ�

## Tips

### ��������

��Ŀԭ��ʹ�õ�ͬ��д����־��ʽ���������Է���qps һ���ȫ��block ��д��־���ˣ����ת��Ϊ�첽��ʽ�����ú��ִ�ӡ������������������������Ϣȫ��������ʺš�


### �������

�첽��ʽ��ӡ��־��async appender ��Ĭ�����ò�����־��ӡ������Ϣ�ģ���Ҫ����һ�����������õ����÷�����Ϣ��

��������

```
<appender name="ASYNC" class="ch.qos.logback.classic.AsyncAppender">
  <appender-ref ref="FILE" />
  <queueSize>512</queueSize>
  <!-- add the following line -->
  <includeCallerData>true</includeCallerData>
</appender>

```

logback ��queue size Ĭ��Ϊ 256���Ƚ�С����������һ�㡣

## Share

���С�鸺������ݱ仯���Ժ�����iOS ��صĹ���������һ�� objective-c ���﷨������о����﷨ֻ��һ�����Եı������Ա����˼�����ͨ�õġ�

objective-c ����c �Ļ����Ϸ�װ�˶����������˼���������������������ߵ��ص㡣���������� C++ һ��������̳У����Ǻ� Java һ��ֻ�������̳С�����������Ʒ��Ҳ��ƫ�� C��Ʃ��˵ʹ��ͷ�ļ���Դ�ļ����ָ�������ʵ�֡�������������ʵ���� Java Ҳ�ܲ�һ������һ���˽�������Ƶĺû��ᡣ


