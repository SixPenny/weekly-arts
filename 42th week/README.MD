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

一开始的解题思路并不是stack，而是递归，一次将可碰撞的陨石消灭掉，到最后无碰撞就是剩下的陨石，代码如下：

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

每次都需要从左至右判断，因此时间复杂度为O(n^2)，并不高效。


我们拆解一下子问题，假设左侧的陨石系统是稳定的，每次加入一块新的陨石，调整陨石系统至稳定，加至最后一块陨石后剩下的是稳定的陨石系统，就是我们的答案。

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

- 冰山表面只是其一角，更多的在水面下
- 给自己打的标签也只是真实你的很小一部分，自己描述的自己也是一样
- 你是谁不是靠文字来定义，在字里行间空白部分也是你。
- 喧哗是你，沉默是你


## Tips

Why REST api must be stateless?

今天看了 RESTful API 定义为 Representational State Transfer，字面意思是表示的状态转换，既然有状态，为什么平常说的一直必须是无状态的呢?

在 Stack Overflow 上有一个人也有[相同疑惑](https://stackoverflow.com/questions/3105296/if-rest-applications-are-supposed-to-be-stateless-how-do-you-manage-sessions), 答案写的很易懂。REST 中的ST 状态转换说的不是服务器上的状态转换，而是从客户端到服务器的状态转换。服务器不保存一切与session有关的信息，相反session信息存放在客户端上，在每次请求时客户端都需要将状态信息回传，使得服务器的处理一致化，才能容易的横向扩展服务器。

REST 的要求与以前服务器的实现方式差别很大，以前客户端就只是一个sessionId，信息都存放在服务器上，服务器通过sessionId对应的实体判断客户端对应的客户是谁，REST 时代将责任转到了客户端，以前的交互方式已经不适应了，出现了 JWT 的交互方式，减轻服务器的压力。

## Share

随时记录，每天总结。

每当到了写arts 的时候总是词穷。其实在一周里遇到的事情不算少，只是自己当时的灵光一现没有及时记录下来，导致写分享的时候发现自己不知道该写啥了，主题都没有一个，很尴尬。其实现在有手机各方面都很方便，在反思现有状态或者有新的点子时都可以随时记录到手机中。到了晚上将一天的火花总结一下，或者针对某个主题搜索也好，看书也好将其扩充出去，形成一篇自己的文章。

