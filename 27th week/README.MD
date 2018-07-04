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