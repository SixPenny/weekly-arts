## Algorithm

### Zigzag Conversion

```
The string "PAYPALISHIRING" is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)

P   A   H   N
A P L S I I G
Y   I   R
And then read line by line: "PAHNAPLSIIGYIR"

Write the code that will take a string and make this conversion given a number of rows:

string convert(string s, int numRows);
Example 1:

Input: s = "PAYPALISHIRING", numRows = 3
Output: "PAHNAPLSIIGYIR"


Example 2:

Input: s = "PAYPALISHIRING", numRows = 4
Output: "PINALSIGYAHRPI"
Explanation:

P     I    N
A   L S  I G
Y A   H R
P     I
```

### Solution

```
package com.dylan.leetcode;

import org.junit.Assert;
import org.junit.Test;

/**
 * Created by liufengquan on 2018/7/4.
 */
public class ZigZagConversion {
    public String convert(String s, int numRows) {
        if (numRows <= 1 || s.length() <= numRows) {
            return s;
        }
        int split = numRows + numRows - 2;
        int group = s.length() / split;
        group = s.length() % split == 0 ? group : group + 1;
        StringBuilder stringBuilder = new StringBuilder();
        for (int i = 0; i < split / 2 + 1; i++) {//走到中间
            for (int j = 0; j < group; j++) {
                int begin = j * split + i;
                if (begin < s.length()) {
                    stringBuilder.append(s.charAt(begin));
                }
                int mirror = begin + (numRows - 1 - i) * 2;
                if (mirror != (j + 1) * split//不越界group
                        && mirror != begin//不重复
                        && mirror < s.length()) {//不越界
                    stringBuilder.append(s.charAt(mirror));
                }
            }
        }
        return stringBuilder.toString();
    }

    @Test
    public void test() {
        Assert.assertTrue("PAHNAPLSIIGYIR".equals(convert("PAYPALISHIRING", 3)));
        Assert.assertTrue("PINALSIGYAHRPI".equals(convert("PAYPALISHIRING", 4)));
    }
}


```

### mind path

We can notice that it has a pattern. Every zigzag pattern is composed by a list of `L` structures.
As of example 2, 
```
P                       
A   L  is one `L` structure
Y A                     
P    

I                                
S  I  is another `L` structure
H R
I

N
G is an uncompeleted `L` structure.


```
Let's write the zigzag position down.Assume numRows = 4

```
string  : P A H N A P L S I I G Y I R
position: 0 1 2 3 4 5 6 7 8 9 a b c d
zig pos:  0 1 2 3 2 1 0 1 2 3 2 1 0 1
```

Every `L` structure has `split = numRows + numRows - 2` elements. Total `group = s.length()/split; group = s.length() % split == 0 ? group : group + 1;` groups. Then we extract all chars at pos 0, all chars at pos 1, all chars at pos 2 etc. We get the result string.


## Review

[technical debt](http://blog.ieeesoftware.org/2018/05/why-should-start-ups-care-about.html)

This post reminds us a series of technical debt. In my opinion, not only have start-ups have technical debt, but also big enterprise. It shows us 5 types of debt.

- code debt. poorly written code. duplicate code and unnecessary code complexity, long functions and bad style reducing readability
- documentation debt. shortcomings in distributing knowledge on how to envolve, operate and maintain product.
- architecture debt. Some technical decisions effect the maintainability and adaptability of software.
- testing debt. lack of test automation.
- environmental debt. hardware, supporting applications, processes etc.

Methods to reduce debt
- Be aware of good practices as a engineer
- On team level, run retrospective and learn how to remove  friction from collaboration.
- On organizational level, anticipate when to leverage on technical debt to speed up certain goal and when to slow down and refactor.


review: Technical debt is a life-long debt. I encountered all the debt listed above. You have to know when to ignore it, when to resolve it. Good pratices such as XP, TDD may ease some aspects of debt.


## Technique 

### Haskell's function

#### Curried functions

> In haskell, functions are curried by default. 
> You can call function with one parameter each time. 
> It'll return a partial applied function. 
> Finally,  you get the orignal function result. 

It's same:
```
max 3 4
(max 3) 4
```

> Putting a space between two things is simply function application. The space is sort of like an operator and it has the highest precedence. Let's examine the type of max. It's max :: (Ord a) => a -> a -> a. That can also be written as max :: (Ord a) => a -> (a -> a)

Infix function can also be partially applied by sections. Remember we can use a backtick to turn a prefix function into a infix function, we can use this trick to apply a latter parameter.

```
plusFive = (+5)
compareTen = (`compare` 10)
```

You can't use `(compare 10)`. When you apply another parameter `(compare 10) 5`, it becomes `compare 10 5`.

Let's see another understandable example:

```
isUpperCase = (`elem` ['A'..'Z'])
```

#### Some usual higher-order functions

##### map,filter

> map take a funciton and a list and applies that function on every element in list producing a new list. filter take a function whose return values is bool(called predicate) and a list and applies the function on every element producing a new list containing all the elements on which function return value is true


##### lambda

> haskell use \ to declare a anonymous function because it look a lot like λ


```
filter (\x -> x > 3) [1..10]
```


##### foldl foldr

> foldl: it folds the list up from the left side. It takes 3 parameters- a binary function, a seed and a list to fold

Let's implement sum

```
sum :: (Num a) => a -> a
sum xs = foldl (\acc x -> acc + x) 0 xs
```

reverse a list

```
reverseList xs = foldl (flip (:)) [] xs
```

> foldr folds the list up from the right side

[a discussion on stackoverflow about foldl and foldr behavior with infinite lists](https://stackoverflow.com/questions/3082324/foldl-versus-foldr-behavior-with-infinite-lists)

> How folds differ seems to be a frequent source of confusion, so here's a more general overview:
>
> Consider folding a list of n values [x1, x2, x3, x4 ... xn ] with some function f and seed z.
> 
> foldl is:
> 
> Left associative: f ( ... (f (f (f (f z x1) > x2) x3) x4) ...) xn
> Tail recursive: It iterates through the > list, producing the value afterwards
> Lazy: Nothing is evaluated until the result > is needed
> Backwards: foldl (flip (:)) [] reverses a > list.
> foldr is:
> 
> Right associative: f x1 (f x2 (f x3 (f x4 > ... (f xn z) ... )))
> Recursive into an argument: Each iteration > applies f to the next value and the result > of folding the rest of the list.
> Lazy: Nothing is evaluated until the result > is needed
> Forwards: foldr (:) [] returns a list > unchanged.
> There's a slightly subtle point here that > trips people up sometimes: Because foldl is > backwards each application of f is added to > the outside of the result; and because it is > lazy, nothing is evaluated until the result > is required. This means that to compute any > part of the result, Haskell first iterates > through the entire list constructing an > expression of nested function applications, > then evaluates the outermost function, > evaluating its arguments as needed. If f > always uses its first argument, this means > Haskell has to recurse all the way down to > the innermost term, then work backwards > computing each application of f.
> 
> This is obviously a far cry from the > efficient tail-recursion most functional > programmers know and love!
> 
> In fact, even though foldl is technically > tail-recursive, because the entire result > expression is built before evaluating > anything, foldl can cause a stack overflow!
> 
> On the other hand, consider foldr. It's also > lazy, but because it runs forwards, each > application of f is added to the inside of > the result. So, to compute the result, > Haskell constructs a single function > application, the second argument of which is > the rest of the folded list. If f is lazy in > its second argument--a data constructor, for > instance--the result will be incrementally > lazy, with each step of the fold computed > only when some part of the result that needs > it is evaluated.
> 
> So we can see why foldr sometimes works on > infinite lists when foldl doesn't: The > former can lazily convert an infinite list > into another lazy infinite data structure, > whereas the latter must inspect the entire > list to generate any part of the result. On > the other hand, foldr with a function that > needs both arguments immediately, such as > (+), works (or rather, doesn't work) much > like foldl, building a huge expression > before evaluating it.
> 
> So the two important points to note are > these:
> 
> foldr can transform one lazy recursive data > structure into another.
> Otherwise, lazy folds will crash with a > stack overflow on large or infinite lists.
> You may have noticed that it sounds like > foldr can do everything foldl can, plus > more. This is true! In fact, foldl is nearly > useless!
> 
> But what if we want to produce a non-lazy > result by folding over a large (but not > infinite) list? For this, we want a strict > fold, which the standard libraries > thoughfully provide:
> 
> foldl' is:
> 
> Left associative: f ( ... (f (f (f (f z x1) > x2) x3) x4) ...) xn
> Tail recursive: It iterates through the > list, producing the value afterwards
> Strict: Each function application is > evaluated along the way
> Backwards: foldl' (flip (:)) [] reverses a > list.
> Because foldl' is strict, to compute the > result Haskell will evaluate f at each step, > instead of letting the left argument > accumulate a huge, unevaluated expression. > This gives us the usual, efficient tail > recursion we want! In other words:
> 
> foldl' can fold large lists efficiently.
> foldl' will hang in an infinite loop (not > cause a stack overflow) on an infinite list.
> The Haskell wiki has a page discussing this, > as well.


#### function application $ and function composition .

> $ has the lowest precedence.So $ is right associative

```
($) :: (a -> b) -> a -> b  
f $ x = f x  
```

$ can remove a lot of parentheses

```
sum (filter (> 10) (map (*2) [2..10]))

sum $ filter (> 10) $ map (*2) [2..10]
```

> In math (f · g)(x) = f(g(x)). Haskell use dot to represent function composition to produce a new function

```
fn x = ceiling (negate (tan (cos (max 50 x))))  

fn = ceiling . negate . tan . cos . max 50  
```

Because . has a higher precedence on $ and has a lower precedence about blank space. When we compose functions and finally apply it, we can compose . and $

```
oddSquareSum :: Integer  
oddSquareSum = sum . takeWhile (<10000) . filter odd . map (^2) $ [1..]  


```

## Share

This week I refactored a long method. Here are some tips you have to get in mind.

- split method into small functions
- identify business domain use SRP 
- use polymorphism to reduce if else
- use guard-programing to reduce embeded level
- If your business model is too complicated, thinking how to seperate your business logic and control logic(still thinking about it).
- read some refactor book to get some inspire