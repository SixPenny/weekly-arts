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

Spring Qualifier ��ʹ�÷���
### ժҪ

spring �� qualifier ƽ��ʹ��һ��ֱ����ʹ��id ���޶�������spring�������ṩ�˸�ǿ��Ĺ��ܡ�

### ʹ�÷���

#### ֱ��ʹ��

- ֱ����`@Qualifier`���޶�bean��id
- ��bean��ʹ��`@Qualifier`��ָ������ʹ�õ�`value`,Ȼ����ע�봦ʹ��ָ����ֵ

Example��
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

#### �Զ���

`@Qualifier` ���˿���ע�������ϣ������ϣ�������ע���� annotation�ϣ��������ǿ��Դ����Լ���qualifier(��xml�����ʹ��`<qualifier>`)��

Example��
id ��ʱ����ȷ��������Ҫ���Լ�������������qualifier
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
Ȼ��Ϳ���ʹ��`@OrderQualifier`��ָ����ע��bean�ˡ�

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

ʹ��ʱֱ��ע�룺
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

����һ�£��ֱ���ûط����˸��Ե�ֵ��


## Share


ս�������Ԫ˧����ս��ÿ���Ŀ���̫�����߶���һ��������ս��ս֮���������������һ��Ԫ˧���������ǳ��ѡ�����Ϊ�Σ���Ϊһ��������ǿݡ�û���̴򣬼�ʹ���ٺõ��츳Ҳ���Ӳ�������

���������һ�����ص���ʾ��ʵ���Ǿ������ܳɳ���ʲô�ز��Ĺؼ����ء��ص�����Ա��ְҵ��������仰����ת���ɣ����������bug����ɳ���ʲô�ز��Ĺؼ����ء�ѧϰ��һ������Ҫ�����飬����ͣ����ֽ���ѧϰ����������ѧϰ��ֻ��Ӧ���ˣ����������ˣ���������ǲ�������Щ���ɳ�������ҲӦ�ÿ����������ܼ��еĲ��������޵ģ�����ѧϰ������Զֻ�������۾�������ʵ��������һ���֣����Ǵ��ڱ߽�ġ�����֮���Լ���֮����Ҫ�ﵽţ�˵�״̬�Ǻܼ��ѵģ����ƽʱ���ľ�����Ҫ�ɳ�Ϊս��һ�����ѡ��кܶ��˾���񼶳���Ա�����ǳɳ������ڵĵȼ����ǹ�˾����С����Ĺ����������������������⣬����Ҳ�����һ�����ųɳ������ġ�

һ����û�����־���������뿿���ֻ����ɳ������Ҿ����Լ���ԶҲ�ɳ����������������ǲ�ֻ���Լ���������ǧǧ������³���Աְҵ���ֵܽ��ã�Ӧ��������������������Ҳ�����ǳɳ��������������˵ľ��������վ��飬�����˵������л�������ĳɳ�������ʶ����һ��󣬾͸��Լ�����һ��Ŀ�꣺ÿ����һ�����⡣������ܺ�С�����Ƕȿ����Լ�û�뵽��������ܴܺ��Լ�����û�п��ǹ���Ҳ�������Լ������������⣬��������Ҳ�Ƕ����Լ�д��������������һ�����ᡣ

������ʱ�ᱧԹдҵ������ظ�������û�гɳ��Ŀռ䡣����Ҳ����������ζ���ġ�����Ҫ���ŷ������ڣ���ת��Ų���ռ䣬��˾���С���Ǿ�ȥ���Ļ�����������Ѱ�ҡ�

















