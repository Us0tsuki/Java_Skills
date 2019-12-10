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
