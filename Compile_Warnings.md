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

