`When you convert a boxed primitive type to string, always use toString() method rather than use + ""  `


There is a lot of code like `a + ""` to convert a Long, Integer and other boxed primitive type to string in my project. I used frequently this form before while I realized this was a bad practice  recently.

Let's take a look at how java specs says about the concatenation operator + from [String Concatenation Operator +](https://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.18.1)

```
If only one operand expression is of type String, then string conversion (¡ì5.1.11) is performed on the other operand to produce a string at run time.

The result of string concatenation is a reference to a String object that is the concatenation of the two operand strings. The characters of the left-hand operand precede the characters of the right-hand operand in the newly created string.

The String object is newly created (¡ì12.5) unless the expression is a constant expression (¡ì15.28).

An implementation may choose to perform conversion and concatenation in one step to avoid creating and then discarding an intermediate String object. To increase the performance of repeated string concatenation, a Java compiler may use the StringBuffer class or a similar technique to reduce the number of intermediate String objects that are created by evaluation of an expression.

For primitive types, an implementation may also optimize away the creation of a wrapper object by converting directly from a primitive type to a string.

```

check 5.1.11 which it refers

```
Any type may be converted to type String by string conversion.

A value x of primitive type T is first converted to a reference value as if by giving it as an argument to an appropriate class instance creation expression (¡ì15.9):

If T is boolean, then use new Boolean(x).

If T is char, then use new Character(x).

If T is byte, short, or int, then use new Integer(x).

If T is long, then use new Long(x).

If T is float, then use new Float(x).

If T is double, then use new Double(x).

This reference value is then converted to type String by string conversion.

Now only reference values need to be considered:

If the reference is null, it is converted to the string "null" (four ASCII characters n, u, l, l).

Otherwise, the conversion is performed as if by an invocation of the toString method of the referenced object with no arguments; but if the result of invoking the toString method is null, then the string "null" is used instead.

The toString method is defined by the primordial class Object (¡ì4.3.2). Many classes override it, notably Boolean, Character, Integer, Long, Float, Double, and String.
```

We learned that when you do `t = l + ""`(l is Long type), java translated it this way:
```
StringBuffer s = new StringBuffer();
s.add(l);
s.add("");
t = s.toString();
```

It is not a big deal that java creates extra object. What matters is that it transforms l to string "null" when l is null. It causes big problems as you pass your value to others. I encountered this problem before. And I realized that there are hidden booms in project because people tend to use this form rather than `toString` method.

It forces you to think what you should do while l is null when you use `toString` method. And It looks elegant while `+ ""` form looks ugly. 