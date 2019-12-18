# Markdown Cheatsheet
https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet

# Void Type in Java
https://www.baeldung.com/java-void-type

# Comparing two integers
https://stackoverflow.com/questions/1514910/how-to-properly-compare-two-integers-in-java \
if (x.intValue() == y.intValue()) or \
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


# Do I really need to define default constructor in java?
A default (no-argument) constructor is automatically created only when you do not define any constructor yourself.
If you need two constructors, one with arguments and one without, you need to manually define both.

In other words, the moment we define any parameterized constructor, we must also define a no-arg constructor if we want to instantiate the object of that class via a no-arg constructor.

Also in case of inheritance, a sub-class with no constructors; is supplied one default constructor. This default constructor supplied by Java as above calls the super class's no-arg constructor. __If it can't find one, then it will throw an error.__

So yes __it's always a good choice to define a no-arg/default constructor.__

# How do I address unchecked cast warnings?
```
return (List<SiteUpgradePolicyEntity>) this.findBySQLQuery(queryName,queryParams);
findBySQLQuery signature:
public __List<?>__ findBySQLQuery(final String queryName, final Map<String, Object> parameters) throws DataAccessException {}
```
## 1. The obvious answer, of course, is not to do the unchecked cast. 
Actually, you are strongly discouraged to do almost any cast, so you should limit it as much as possible! You lose the benefits of Java's compile-time strongly-typed features.
In any case, Class.cast() should be used mainly when you retrieve the Class token via reflection. It's more idiomatic to write
```MyObject myObject = (MyObject) object```
rather than
```MyObject myObject = MyObject.class.cast(object)```

## 2. How to have Java method return generic list of any type?
```
public <T> List<T> magicalListGetter() {
    return new ArrayList<T>();
}
```
if cast is needed,
```
private Object actuallyT;

public <T> List<T> magicalListGetter(Class<T> klazz) {
    List<T> list = new ArrayList<>();
    list.add(klazz.cast(actuallyT));
    try {
        list.add(klazz.getConstructor().newInstance()); // If default constructor
    } ...
    return list;
}
```

## 3. If it's absolutely necessary, then at least try to __limit the scope__ of the @SuppressWarnings annotation. 
According to its Javadocs, it can go on local variables; this way, it doesn't even affect the entire method.

Example:
```
@SuppressWarnings("unchecked")
Map<String, String> myMap = (Map<String, String>) deserializeMap();
```
There is no way to determine whether the Map really should have the generic parameters <String, String>. You must know beforehand what the parameters should be (or you'll find out when you get a ClassCastException). This is why the code generates a warning, because the compiler can't possibly know whether is safe.

# StringUtils.isBlank() vs StringUtils.isEmpty()
StringUtils.isBlank() checks if a String is whitespace, empty ("") or null.
```
 StringUtils.isBlank(null)      = true
 StringUtils.isBlank("")        = true  
 StringUtils.isBlank(" ")       = true  
 StringUtils.isBlank("bob")     = false  
 StringUtils.isBlank("  bob  ") = false
```
For comparison StringUtils.isEmpty(): 
```
 StringUtils.isEmpty(null)      = true
 StringUtils.isEmpty("")        = true  
 StringUtils.isEmpty(" ")       = false  
 StringUtils.isEmpty("bob")     = false  
 StringUtils.isEmpty("  bob  ") = false
```

> Official Implementation:
```
public static boolean isBlank(CharSequence cs) {
    int strLen;
    if (cs != null && (strLen = cs.length()) != 0) {
        for(int i = 0; i < strLen; ++i) {
            if (!Character.isWhitespace(cs.charAt(i))) {
                return false;
            }
        }

        return true;
    } else {
        return true;
    }
}
```
