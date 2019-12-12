# Markdown Cheatsheet
https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet

# Void Type in Java
https://www.baeldung.com/java-void-type

# Comparing two integers
https://stackoverflow.com/questions/1514910/how-to-properly-compare-two-integers-in-java
if (x.intValue() == y.intValue())
if (x.equals(y))

# Can non-static methods modify static variables ?
From [Java Tutorials](https://docs.oracle.com/javase/tutorial/java/javaOO/classvars.html)
*Instance methods* can access *instance variables* and *instance methods* directly.
*Instance methods* can access *class variables* and *class methods* directly.
*Class methods* can access *class variables* and *class methods* directly.
*Class methods* CANNOT access *instance variables* or *instance methods* directly—they must use an __object reference__. 
Also, class methods CANNOT use the __this__ keyword as there is no instance for this to refer to.

The only way this would be false is if the non-static context did not have access to the static member (ex. the static member is **private** to a class and the non-static code is not in that class).

# Double v.s. BigDecimal?
https://stackoverflow.com/questions/3413448/double-vs-bigdecimal

The disadvantage of BigDecimal is that it's slower, and it's a bit more difficult to program algorithms that way (due to + - * and / not being overloaded).

There are two main differences from double:
- Arbitrary precision, similarly to BigInteger they can contain number of arbitrary precision and size
- Doubles store values as binary and therefore might introduce an error solely by converting a decimal number to a binary number, without even doing any arithmetic （1/3, 1/7, 1/10）.
- Base 10 instead of Base 2, a BigDecimal is n*10^scale where n is an arbitrary large signed integer and scale can be thought of as the number of digits to move the decimal point left or right

The reason you should use BigDecimal for monetary calculations is not that it can represent any number, but that it can represent all numbers that can be represented in decimal notion(numbers that have a finite number of decimal digits) and that include virtually all numbers in the monetary world (you never transfer 1/3 $ to someone).

P.S. decimal这里指十进制

# How to call a C program from Java?
Java native interface (JNI) is a framework provided by java that enables java programs to call native code and vice-versa.
Using JNI a java program has the capability to call the native C code. But we lose the core objective of java which is platform independence. So calling a C program from java should be used judiciously.
https://javapapers.com/core-java/how-to-call-a-c-program-from-java/