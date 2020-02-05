# Typical Errors Which May be Encountered When Reflecting on Classes
https://docs.oracle.com/javase/tutorial/reflect/class/classTrouble.html

When a method is invoked, the types of the argument values are checked and possibly converted. ClassWarning invokes getMethod() to cause a typical unchecked conversion warning:

```
import java.lang.reflect.Method;

public class ClassWarning {
    void m() {
	try {
	    Class c = ClassWarning.class;
	    Method m = c.getMethod("m");  // warning

        // production code should handle this exception more gracefully
	} catch (NoSuchMethodException x) {
    	    x.printStackTrace();
    	}
    }
}
$ javac ClassWarning.java
Note: ClassWarning.java uses unchecked or unsafe operations.
Note: Recompile with -Xlint:unchecked for details.
$ javac -Xlint:unchecked ClassWarning.java
ClassWarning.java:6: warning: [unchecked] unchecked call to getMethod
  (String,Class<?>...) as a member of the raw type Class
Method m = c.getMethod("m");  // warning
                      ^
1 warning
```
Many library methods have been retrofitted with generic declarations including several in Class. Since c is declared as a raw type (has no type parameters) and the corresponding parameter of getMethod() is a parameterized type, an unchecked conversion occurs. The compiler is required to generate a warning. (See The Java Language Specification, Java SE 7 Edition, sections Unchecked Conversion and Method Invocation Conversion.)

There are two possible solutions. The more preferable it to modify the declaration of c to include an appropriate generic type. In this case, the declaration should be:
```
Class<?> c = warn.getClass();
```
Alternatively, the warning could be explicitly suppressed using the predefined annotation @SuppressWarnings preceding the problematic statement.
```
Class c = ClassWarning.class;
@SuppressWarnings("unchecked")
Method m = c.getMethod("m");  
```
// warning gone
> Tip: As a general principle, warnings should not be ignored as they may indicate the presence of a bug. Parameterized declarations should be used as appropriate. If that is not possible (perhaps because an application must interact with a library vendor's code), annotate the offending line using @SuppressWarnings.
> InstantiationException when the Constructor is Not Accessible
> Class.newInstance() will throw an InstantiationException if an attempt is made to create a new instance of the class and the zero-argument constructor is not visible. The ClassTrouble example illustrates the resulting stack trace.

# What is a raw type?
https://stackoverflow.com/questions/2770321/what-is-a-raw-type-and-why-shouldnt-we-use-it


```
Result[] results = table.get(getList);
log.info("Get hbase results: {}", results);
```
non-varargs call of varargs method with inexact argument type for last parameter;

The signature of log.info is:
```
public void invoke(Object obj, __Object...__ args)
```
which is a varargs method, which means that you can pass it any number of arguments (well, at least 1) and Java will internally collect them all in an array of objects. 

However, you have:
```
Results[] parameter;
```

args is converted to an Object[], but is not handled as such, it seems.
Class[] is not just an Object[] that happens to contain Class objects. It's a different type (a subtype of Object[]).

Check this, it should give you a hint at the solution:
```
public class Test {
    public static void main(String[] args) throws Exception {
        String[] greetings = { "Hello", "Hi" };
         
        // no warning
        Object[] objects = greetings;
 
        // no warning
        arrayTest(greetings);
         
        // no warning
        varargTest(objects);
 
        // warning
        varargTest(greetings);
    }
 
    public static Object[] arrayTest(Object[] p) {
        return p;
    }
    public static Object[] varargTest(Object... p) {
        return p;
    }
}
```

It is because String[] and Object... do not exactly match up.

You have to cast the String[] to either Object[] (if you want to pass the Strings as separate parameters) or Object (if you want just one argument that is an array) first.
```
 tva.varArgsMethod((Object[])args);    // you probably want that

 tva.varArgsMethod( (Object) args);    // you probably don't want that, but who knows?
```
Why is this a warning and not an error? Backwards compatibility. Before the introduction of varargs, you had these methods take a Object[] and code compiled against that should still work the same way after the method has been upgraded to use varargs. The JDK standard library is full of cases like that. For example java.util.Arrays.asList(Object[]) has changed to java.util.Arrays.asList(Object...) in Java5 and all the old code that uses it should still compile and work without modifications.


# Collections.EMPTY_LIST v.s. Collections.emptyList()
Collections.EMPTY_LIST returns an old-style List
Collections.emptyList() uses type-inference and therefore returns List<T>
Collections.emptyList() was added in Java 1.5 and it is probably always preferable. This way, you don't need to unnecessarily cast around within your code.

Collections.emptyList() intrinsically does the cast for you.

@SuppressWarnings("unchecked")
public static final <T> List<T> emptyList() {
    return (List<T>) EMPTY_LIST;
}
