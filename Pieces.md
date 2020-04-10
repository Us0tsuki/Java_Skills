# Markdown Guide
https://markdown-guide.readthedocs.io/en/latest/basics.html#line-return

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

# Maven  GC overhead limit exceeded
While runnning mvn dependency:tree on a big project.
Solution: Just allocate more memory to jvm
https://cwiki.apache.org/confluence/display/MAVEN/OutOfMemoryError

# Avoiding negative conditionals
In Clean Code, Robert Martin writes, “Negatives are just a bit harder to understand than positives. So, when possible, conditionals should be expressed as positives.” (Martin, [G29]). IntelliJ IDEA has three inspections to help you stay positive.
```
public boolean isAcademic(String email) {
  if (email != null && !email.isEmpty()) {
    return email.contains(".edu");
  }
  return false;
}
```
When conditionals are negative, this is a good indication that control flow can be refactored. When refactoring your code, you should apply the intention Invert If Condition. Notice how De Morgan’s law is implicitly used to refactor this clause
```
public boolean isAcademic(String email) {
  if (email == null || email.isEmpty()) {
    return false;
  }

  return email.contains(".edu");
}
```

# Maven dependency tree
## 第一板斧:找到传递依赖的鬼出在哪里？
dependency:tree是把照妖镜，pom.xml用它照照，所有传递性依赖都将无处遁形，并且会以层级树方式展现，非常直观。

```
[INFO] --- maven-dependency-plugin:2.1:tree (default-cli) @ euler-foundation ---
[INFO] com.hsit:euler-foundation:jar:0.9.0.1-SNAPSHOT
[INFO] +- com.rop:rop:jar:1.0.1:compile
[INFO] |  +- org.slf4j:slf4j-api:jar:1.7.5:compile
[INFO] |  +- org.slf4j:slf4j-log4j12:jar:1.7.5:compile
[INFO] |  +- log4j:log4j:jar:1.2.16:compile
[INFO] |  +- commons-lang:commons-lang:jar:2.6:compile
[INFO] |  +- commons-codec:commons-codec:jar:1.6:compile
[INFO] |  +- javax.validation:validation-api:jar:1.0.0.GA:compile
[INFO] |  +- org.hibernate:hibernate-validator:jar:4.2.0.Final:compile
[INFO] |  +- org.codehaus.jackson:jackson-core-asl:jar:1.9.5:compile
[INFO] |  +- org.codehaus.jackson:jackson-mapper-asl:jar:1.9.5:compile
[INFO] |  +- org.codehaus.jackson:jackson-jaxrs:jar:1.9.5:compile
[INFO] |  +- org.codehaus.jackson:jackson-xc:jar:1.9.5:compile
[INFO] |  \- com.fasterxml.jackson.dataformat:jackson-dataformat-xml:jar:2.2.3:compile
[INFO] |     +- com.fasterxml.jackson.core:jackson-core:jar:2.2.3:compile
[INFO] |     +- com.fasterxml.jackson.core:jackson-annotations:jar:2.2.3:compile
[INFO] |     +- com.fasterxml.jackson.core:jackson-databind:jar:2.2.3:compile
[INFO] |     +- com.fasterxml.jackson.module:jackson-module-jaxb-annotations:jar:2.2.3:compile
[INFO] |     \- org.codehaus.woodstox:stax2-api:jar:3.1.1:compile
[INFO] |        \- javax.xml.stream:stax-api:jar:1.0-2:compile
```
其实有时你会发现简单地用dependency:tree往往并不能查看到所有的传递依赖。不过如果你真的想要看所有的，必须得加一个-Dverbose参数，这时就必定是最全的了。
全是全了，但显示出来的东西太多，头晕目眩，有没有好法呢？当然有了，加上Dincludes或者Dexcludes说出你喜欢或讨厌，dependency:tree就会帮你过滤出来：

过滤串使用groupId:artifactId:version的方式进行过滤，可以不写全啦，如：
```
mvn dependency:tree -Dverbose -Dincludes=asm:asm
```
就会出来asm依赖包的分析信息：
```
[INFO] --- maven-dependency-plugin:2.1:tree (default-cli) @ ridge-test ---
[INFO] com.ridge:ridge-test:jar:1.0.2-SNAPSHOT
[INFO] +- asm:asm:jar:3.2:compile
[INFO] \- org.unitils:unitils-dbmaintainer:jar:3.3:compile
[INFO]    \- org.hibernate:hibernate:jar:3.2.5.ga:compile
[INFO]       +- cglib:cglib:jar:2.1_3:compile
[INFO]       |  \- (asm:asm:jar:1.5.3:compile - omitted for conflict with 3.2)
[INFO]       \- (asm:asm:jar:1.5.3:compile - omitted for conflict with 3.2)
[INFO] ------------------------------------------------------------------------
```
对asm有依赖有一个直接的依赖(asm:asm:jar:3.2)还有一个传递进入的依赖(asm:asm:jar:1.5.3)

## 第二板斧:将不想要的传递依赖剪除掉

承上，假设我们不希望asm:asm:jar:1.5.3出现，根据分析，我们知道它是经由org.unitils:unitils-dbmaintainer:jar:3.3引入的，那么在pom.xml中找到这个依赖，做其它的调整：
```
 <dependency>  
        <groupId>org.unitils</groupId>  
        <artifactId>unitils-dbmaintainer</artifactId>  
        <version>${unitils.version}</version>  
        <exclusions>  
            <exclusion>  
                <artifactId>dbunit</artifactId>  
                <groupId>org.dbunit</groupId>  
            </exclusion>  
            <!-- 这个就是我们要加的片断 -->  
            <exclusion>  
                <artifactId>asm</artifactId>  
                <groupId>asm</groupId>  
            </exclusion>  
        </exclusions>  
    </dependency>
```
再分析一下，你可以看到传递依赖没有了：
```
[INFO]  
    [INFO] --- maven-dependency-plugin:2.1:tree (default-cli) @ ridge-test ---  
    [INFO] com.ridge:ridge-test:jar:1.0.2-SNAPSHOT  
    [INFO] \- asm:asm:jar:3.2:compile  
    [INFO] ------------------------------------------------------------------------  
    [INFO] BUILD SUCCESS  
```

# 第三板斧:查看运行期类来源的JAR包

有时，你以为解决了，但是偏偏还是报类包冲突（典型症状是java.lang.ClassNotFoundException或Method不兼容等异常），这时你可以设置一个断点，在断点处通过下面这个我做的工具类来查看Class所来源的JAR包：
```
 package com.ridge.util;  
      
    import java.io.File;  
    import java.net.MalformedURLException;  
    import java.net.URL;  
    import java.security.CodeSource;  
    import java.security.ProtectionDomain;  
      
    /** 
     * @author : chenxh 
     * @date: 13-10-31 
     */  
    public class ClassLocationUtils {  
      
        /** 
         * 获取类所有的路径 
         * @param cls 
         * @return 
         */  
        public static String where(final Class cls) {  
            if (cls == null)throw new IllegalArgumentException("null input: cls");  
            URL result = null;  
            final String clsAsResource = cls.getName().replace('.', '/').concat(".class");  
            final ProtectionDomain pd = cls.getProtectionDomain();  
            if (pd != null) {  
                final CodeSource cs = pd.getCodeSource();  
                if (cs != null) result = cs.getLocation();  
                if (result != null) {  
                    if ("file".equals(result.getProtocol())) {  
                        try {  
                            if (result.toExternalForm().endsWith(".jar") ||  
                                    result.toExternalForm().endsWith(".zip"))  
                                result = new URL("jar:".concat(result.toExternalForm())  
                                        .concat("!/").concat(clsAsResource));  
                            else if (new File(result.getFile()).isDirectory())  
                                result = new URL(result, clsAsResource);  
                        }  
                        catch (MalformedURLException ignore) {}  
                    }  
                }  
            }  
            if (result == null) {  
                final ClassLoader clsLoader = cls.getClassLoader();  
                result = clsLoader != null ?  
                        clsLoader.getResource(clsAsResource) :  
                        ClassLoader.getSystemResource(clsAsResource);  
            }  
            return result.toString();  
        }  
      
    }  
```
随便写一个测试，设置好断点，在执行到断点处按alt+F8动态执行代码（intelij idea），假设我们输入：
```
ClassLocationUtils.where(org.objectweb.asm.ClassVisitor.class) 
```
即可马上查出org.objectweb.asm.ClassVisitor类在运行期对应的JAR包,如果这个JAR包版本不是你期望你，就说明是你的IDE缓存造成的，这时建议你Reimport一下maven列表就可以了。

Reimport一下，IDE会强制根据新的pom.xml设置重新分析并加载依赖类包，以得到和pom.xml设置相同的依赖。
为了提高效率不建议采用reimport重新起开启项目的方式,建议采用idea自带的功能,File->Invalidate Caches 功能直接完成清除idea cache。

# The Path to Improve Database Performance
https://www.keycdn.com/blog/database-performance

# Collections.singletonMap()
public static Map singletonMap(K key, V value)

The singletonMap() method of java.util.Collections class is used to return an __immutable map__, mapping only the specified key to the specified value. The returned map is serializable.

# Collections.singletonList(T o) v.s. Arrays.asList(T o)
Collections.singletonList(something) is immutable whereas Arrays.asList(something) is a fixed size List representation of an Array where the List and Array gets joined in the heap.

Arrays.asList(something) allows non-structural changes made to it, which gets reflected to **both** the List and the conjoined array. It throws __UnsupportedOperationException__ for adding, removing elements although you can set an element for a particular index.

Any changes made to the List returned by Collections.singletonList(something) will result in UnsupportedOperationException.

Also, the capacity of the List returned by Collections.singletonList(something) will always be 1 unlike Arrays.asList(something) whose capacity will be the size of the backed array.

# NoClassDefFoundError during runtime
While it's possible that this is due to a classpath mismatch between compile-time and run-time, it's not necessarily true.
Exceptions:
1.java.lang.ClassNotFoundException 
This exception indicates that the class was not found on the classpath. This indicates that we were trying to load the class definition, and the class did not exist on the classpath.

2.java.lang.NoClassDefFoundError 
This exception indicates that the JVM looked in its internal __class definition data structure(not runtime classpath)__ for the definition of a class and did not find it. This is different than saying that it could not be loaded from the classpath. Usually this indicates that we previously attempted to load a class from the classpath, but failed for some reason - now we're trying to use the class again (and thus need to load it, since it failed last time), but we're not even going to try to load it, because we already failed once earlier (and reasonably suspect that we would fail again). The earlier failure could be a ClassNotFoundException or an ExceptionInInitializerError (indicating a failure in the static initialization block) or any number of other problems. The point is, a NoClassDefFoundError is not necessarily a classpath problem.
NoClassDefFoundErrorDemo.java:
```
public class NoClassDefFoundErrorDemo {
    public static void main(String[] args) {
        try {
            // The following line would throw ExceptionInInitializerError
            SimpleCalculator calculator1 = new SimpleCalculator();
        } catch (Throwable t) {
            System.out.println(t);
        }
        // The following line would cause NoClassDefFoundError
        SimpleCalculator calculator2 = new SimpleCalculator();
    }
}
```
SimpleCalculator.java:
```
public class SimpleCalculator {
    static int undefined = 1 / 0;
}
```

Possible Problems:
> 1. Not in Classpath.
> 2. Class belongs to a missing JAR file, JAR was not added into classpath or sometimes jar's name has been changed.
> 3. Desired classpath was overrided.
> 4. Permission issue on JAR file.
> 5. Your compiled class defined in a package, doesn’t present in the same package while loading.

Possible Solutions:
> 1. Print System.getproperty("java.classpath") to know actual runtime classpath.
> 2. Check for java.lang.ExceptionInInitializerError in your log file. NoClassDefFoundError due to the failure of static initialization is quite common.
> 3. Any start-up script is overriding Classpath environment variable.
> 4. Because NoClassDefFoundError is a subclass of java.lang.LinkageError it can also come if one of it dependency like native library may not available.
> 5. You might be running your program using jar command and class was not defined in manifest file's ClassPath attribute.

# Regex Pattern Repetition
The finite repetition syntax uses {m,n} in place of star/plus/question mark.

From java.util.regex.Pattern:

X{n}      X, exactly n times  
X{n,}     X, at least n times  
X{n,m}    X, at least n but not more than m times  
All repetition metacharacter have the same precedence, so just like you may need grouping for *, +, and ?, you may also for {n,m}.

ha* matches e.g. "haaaaaaaa"  
ha{3} matches only "haaa"  
(ha)* matches e.g. "hahahahaha"  
(ha){3} matches only "hahaha"  

# Avoid Re-assigning method parameters
By some, it is considered an ill approach to assign values to parameters within a method's body, as it is, at times, confusing. Some developers prefer always assuming that a parameter is never assigned any value during a method's run.
Especially when the parameter is an object, which is passed via reference.

# Intro to Digital Signature
http://www.youdzone.com/signature.html