## Algorithm

#### Description
tags: Stack  
difficulty: medium  

We are given an array asteroids of integers representing asteroids in a row.

For each asteroid, the absolute value represents its size, and the sign represents its direction (positive meaning right, negative meaning left). Each asteroid moves at the same speed.

Find out the state of the asteroids after all collisions. If two asteroids meet, the smaller one will explode. If both are the same size, both will explode. Two asteroids moving in the same direction will never meet.


Example 1:  
Input:  
asteroids = [5, 10, -5]  
Output: [5, 10]  
Explanation: 
The 10 and -5 collide resulting in 10.  The 5 and 10 never collide.

Example 2:  
Input:   
asteroids = [8, -8]  
Output: []  
Explanation:  
The 8 and -8 collide exploding each other.

Example 3:  
Input:  
asteroids = [10, 2, -5]  
Output: [10]  
Explanation:  
The 2 and -5 collide resulting in -5.  The 10 and -5 collide resulting in 10.

Example 4:  
Input:  
asteroids = [-2, -1, 1, 2]  
Output: [-2, -1, 1, 2]  
Explanation:   
The -2 and -1 are moving left, while the 1 and 2 are moving right.  
Asteroids moving the same direction never meet, so no asteroids will meet each other.  

Note:
- The length of asteroids will be at most 10000.
- Each asteroid will be a non-zero integer in the range [-1000, 1000]..


#### Solution

һ��ʼ�Ľ���˼·������stack�����ǵݹ飬һ�ν�����ײ����ʯ����������������ײ����ʣ�µ���ʯ���������£�

```go
func asteroidCollision(asteroids []int) []int {
    if len(asteroids) ==0 {
        return asteroids
    }
    var location int =0
    for ;location < len(asteroids) && asteroids[location] < 0;location++{
        
    }
    
    if location == len(asteroids){
        return asteroids
    }
    curLoc := location
    for ;location < len(asteroids) && asteroids[location] > 0;location++{
        
    }
    if location == len(asteroids){
        return asteroids
    }
    //do one collision
    i:=location-1
    for ;i>=curLoc;i--{
        if abs(asteroids[i]) == abs(asteroids[location]){
            return asteroidCollision(append(asteroids[:i], asteroids[location+1:]...))
        }
        if abs(asteroids[i]) > abs(asteroids[location]) {
            return asteroidCollision(append(asteroids[:i+1], asteroids[location+1:]...))
        }
    }
    return asteroidCollision(append(asteroids[:curLoc], asteroids[location:]...))
}
func abs(a int) int{
    if a > 0{
        return a
    }
    return -a
}
```

ÿ�ζ���Ҫ���������жϣ����ʱ�临�Ӷ�ΪO(n^2)��������Ч��


���ǲ��һ�������⣬����������ʯϵͳ���ȶ��ģ�ÿ�μ���һ���µ���ʯ��������ʯϵͳ���ȶ����������һ����ʯ��ʣ�µ����ȶ�����ʯϵͳ���������ǵĴ𰸡�

```go
func asteroidCollision(asteroids []int) []int {
    if len(asteroids) ==0 {
        return asteroids
    }
    stack := make([]int, 1001)
    top := 0
    stack[top] = -1001 //barrier
    for _,val := range asteroids {
        if val > 0 {
            top++
            stack[top] = val
        } else {
            for i:=top; i>=0;i--{
                if stack[i] < 0 {//push
                    top++
                    stack[top] =val
                    break
                } else if stack[i] < abs(val) {
                    top-- //pop
                } else if stack[i] == abs(val){
                    top-- //pop
                    break
                } else {
                    break
                }
            }
        }
    }
    
    return stack[1:top+1]
}
func abs(a int) int{
    if a > 0{
        return a
    }
    return -a
}
```

## Review

[Why You Are Not Who You Say You Are](https://medium.com/personal-growth/why-you-are-not-who-you-say-you-are-3ecb509cf45a)

- ��ɽ����ֻ����һ�ǣ��������ˮ����
- ���Լ���ı�ǩҲֻ����ʵ��ĺ�Сһ���֣��Լ��������Լ�Ҳ��һ��
- ����˭���ǿ����������壬�������м�հײ���Ҳ���㡣
- �������㣬��Ĭ����


## Tips

Why REST api must be stateless?

���쿴�� RESTful API ����Ϊ Representational State Transfer��������˼�Ǳ�ʾ��״̬ת������Ȼ��״̬��Ϊʲôƽ��˵��һֱ��������״̬����?

�� Stack Overflow ����һ����Ҳ��[��ͬ�ɻ�](https://stackoverflow.com/questions/3105296/if-rest-applications-are-supposed-to-be-stateless-how-do-you-manage-sessions), ��д�ĺ��׶���REST �е�ST ״̬ת��˵�Ĳ��Ƿ������ϵ�״̬ת�������Ǵӿͻ��˵���������״̬ת����������������һ����session�йص���Ϣ���෴session��Ϣ����ڿͻ����ϣ���ÿ������ʱ�ͻ��˶���Ҫ��״̬��Ϣ�ش���ʹ�÷������Ĵ���һ�»����������׵ĺ�����չ��������

REST ��Ҫ������ǰ��������ʵ�ַ�ʽ���ܴ���ǰ�ͻ��˾�ֻ��һ��sessionId����Ϣ������ڷ������ϣ�������ͨ��sessionId��Ӧ��ʵ���жϿͻ��˶�Ӧ�Ŀͻ���˭��REST ʱ��������ת���˿ͻ��ˣ���ǰ�Ľ�����ʽ�Ѿ�����Ӧ�ˣ������� JWT �Ľ�����ʽ�������������ѹ����

## Share

��ʱ��¼��ÿ���ܽᡣ

ÿ������дarts ��ʱ�����Ǵ����ʵ��һ�������������鲻���٣�ֻ���Լ���ʱ�����һ��û�м�ʱ��¼����������д������ʱ�����Լ���֪����дɶ�ˣ����ⶼû��һ���������Ρ���ʵ�������ֻ������涼�ܷ��㣬�ڷ�˼����״̬�������µĵ���ʱ��������ʱ��¼���ֻ��С��������Ͻ�һ��Ļ��ܽ�һ�£��������ĳ����������Ҳ�ã�����Ҳ�ý��������ȥ���γ�һƪ�Լ������¡�
