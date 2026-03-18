

COMP1549: Advanced Programming
## 1
COMP1549: Advanced Programming
## Dr Markus Wolf
## 19
th
## March, 2026 -  Week 10
## Reflection

COMP1549: Advanced Programming
## Reflection
## 2

COMP1549: Advanced Programming
## Reflection
◉Reflection enables inspection of classes at
runtime
◉Makes it possible to construct instances of
classes for which all you have is the name
◉Once instantiated you can:
Access/modify the fields of the object
Invoke methods on the object
◉It’s also possible to access metadata (e.g.
annotations, assembly attributes)
## 3

COMP1549: Advanced Programming
## Reflection
◉Using reflection, one can
Query objects for their abilities
Call their methods (invoke their methods) without
knowing the method name, parameters and
return type in advance
## 4

COMP1549: Advanced Programming
Why do we need it?
◉Sometimes it is necessary to inspect
compiled code at runtime
◉Plug components together at runtime
◉Factories can create new types
◉Tools that analyse compiled code at runtime
A testing framework can reflect on compiled
code, identify and execute the tests
A tool can reflect on a given assembly or byte
code to establish the classes and their contents
(e.g. for analysing complexity, statistics,
comparing implementations, etc.)
## 5

COMP1549: Advanced Programming
What is Exposed?
◉Fields, constructors, methods, superclasses,
annotations and implemented interfaces by
name
◉Using the Introspector and BeanInfo it will
reveal properties, events and methods
named or defined in accordance with the
naming defined in the JavaBeans
## Specification
## 6

COMP1549: Advanced Programming
◉The type of a variable is known at compile time but
the type of an object it refers to isn't fixed until runtime
Pet  yourPet;
## ...
if (age > 75)
yourPet = new Budgie();
if (age < 12)
yourPet = new Hamster();
◉The type of the variable yourPet is fixed when you
compile the code ...
◉...but the type of object referenced by the variable is
decided according to the value of age at runtime
## 7
## Runtime Type Information

COMP1549: Advanced Programming
◉Before we look in detail at “reflection” itself
we'll review other ways that type information
is available to your program at runtime
## Runtime Type Information
## Polymorphism (aka
dynamic or late binding)
instanceof
using java.lang.Class
java.lang.reflect
package
## Simple
## Common
more
## Complex
less
common
reflection API
## 8

COMP1549: Advanced Programming
◉We use it all the time to make
our programs maintainable
and extensible
List policies<Insurance> = new ArrayList<>();
## ...
for(Insurance policy : policies) {
policy.makeClaim();
## }
## Polymorphism
## <<interface>
## Insurance
makeClaim()
mailShot()
## ....
CarInsurance
makeClaim()
## ....
HolidayInsurance
makeClaim()
## ....
Correct method
found at runtime
## 9

COMP1549: Advanced Programming
◉Sometimes we need to test at runtime if an
object belongs to a specific class
May want to call a method specific to it
May want to process objects of different classes
differently
◉Suppose we want only to mail shot holiday
insurance holders
for(Insurance policy : policies) {
if ( policy instanceof HolidayInsurance ) {
policy.mailShot();
## }
## }
Finding the Type at Runtime Using
instanceof
Is this an instance of
HolidayInsurance?
## 10

COMP1549: Advanced Programming
◉instanceof is simple but limited
◉The class java.lang.Class allows us to do more
◉When a class is first loaded (e.g. when its first
instance is created) an object of class Class is
created representing the class
## The Class Class
HolidayInsurance x = new HolidayInsurance();
:HolidayInsurance
:Class
name=HolidayInsurance
x
I’m an object of
class
HolidayInsurance
I’m an object of
class Class
## 11

COMP1549: Advanced Programming
◉We can get hold of the class object and query it
e.g.
for(Insurance policy : policies) {
Class polClass = policy.getClass();
String polClassName = polClass.getName();
if (polClassName.equals("reflect.HolidayInsurance"))
## {
policy.mailShot();
## }
## }
## The Class Class
This is the same functionality
implemented using the Class
class instead of instanceof
## 12

COMP1549: Advanced Programming
◉Other ways for getting hold of an object of class
## Class
◉Using a "class literal":
If the class name is known at compile time (say,
Button), get class object
Class c = Button.class;
◉Using the static Class method forName():
If class name is known (as a String variable str) at
run time, get class object
Class c = Class.forName(str);
◉If you have an object obj, get its class object with
Class c = obj.getClass();
## The Class Class
## 13

COMP1549: Advanced Programming
◉Class Class has numerous methods e.g.
getSuperclass()
◉Here is code to recursively climb up the class
hierarchy from a given class
Class c = Class.forName(txtClass.getText());
goUpTree(c);
## ...
public void goUpTree(Class c) {
txtOutput.append(c.toString() + "\n");
Class superClass = c.getSuperclass();
if (superClass != null)
goUpTree(superClass);
## }
## Other Class Class Methods
## 14

COMP1549: Advanced Programming
◉Contains more classes that support reflection
including:
## class Constructor
## class Field
## class Method
◉Allows
Instances to be created dynamically without using
new
Methods to be called and fields (i.e. variables) to be
accessed without the compiler knowing the class
◉Widely used when writing tools (IDEs, plugins,
documentation tools, testing tools, etc.)
Package java.lang.reflect
## 15

COMP1549: Advanced Programming
◉There are two ways to get Methods
getMethods();
Returns all the methods for this class and any it
inherits from super classes
getDeclaredMethods();
Returns all the methods for this class only
◉You can also get a specific method, but it
takes more information
## The Method Class
## 16

COMP1549: Advanced Programming
◉Get a specific method
getMethod(String name, Class<?>... parameterTypes));
... means any number of parameters can be passed after
the name (parameters here are a reference to the types
of parameters the method takes)
◉For example, say we have this method:
public int doSomething(String stuff, int times) {}
To get this specific method:
Class<?>[] paramTypes = {String.class, int.class};
getMethod("doSomething", paramTypes );
Types are directly passed because reflection will use the
method "fingerprints " to track it down and return it to us
## The Method Class
## 17

COMP1549: Advanced Programming
◉Some other useful methods
getReturnType()
Gets the type of variable returned by this method
getParameterTypes()
Returns an array of parameters in the order the
method takes them
invoke(Object obj, Object... args )
Calls/Runs this method on the given object, with
parameters
## The Method Class
## 18

COMP1549: Advanced Programming
◉Two ways to get Class constructors:
getConstructors()
Returns all public constructors for the class
getDeclaredConstructors()
Returns all constructors for the class
◉Again, we can get specific constructors with:
getConstructor(Class<?>...
parameterTypes));
Returns the constructor that takes the given
parameters (i.e. parameter types).
## The Constructor Class
## 19

COMP1549: Advanced Programming
An example
◉A Reflector class has been implemented that allows
you to expose the facilities of classes at runtime and
invoke methods
◉Reflector handles anything that might cause an
exception
A lot can go wrong with reflective methods
◉The user interface is implemented as a JFrame, which
makes calls on the Reflector.java class
## 20

COMP1549: Advanced Programming
## The Application
## 21

COMP1549: Advanced Programming
Getting hold of the class type
◉The GUI calls through to the setClassName method of
reflector
◉Here’s the Java code:
public void setClassName(String className)
## {
this.className = ((className == null)?"":className);
this.className = this.className.trim();
try
## {

clazz =
Thread.currentThread().getContextClassLoader().
loadClass(className);
## }
catch(ClassNotFoundException cnfe)
{ /* nothing here */ }
## }
Loads the class
identified by the
string into the JVM
The file to
reflect on has
to be on the
classpath
## 22

COMP1549: Advanced Programming
## The Application
The class name
has to be fully
qualified
A list of all the
constructors
and methods
All the methods
which don’t
take parameters
Whatever is returned
when the selected
method is called
## 23

COMP1549: Advanced Programming
## Getting Superclass Info
private StringBuffer getExtendsString(StringBuffer buf)
## {

if(
clazz.getSuperclass() == Object.class
||clazz.getSuperclass()== null )
## {
return buf;
## }

buf.append("\n    extends " +
clazz.getSuperclass().getName());
return buf;
## }
buf is the string that will be
returned and displayed on
the page – this will display all
of the class details
## 24

COMP1549: Advanced Programming
## Getting Modifers
◉ Java has an int    based Enumeration with tests on the
modifiers class
int mod = constructors[i].getModifiers();
if(Modifier.isPublic(mod))
◉ And you can do this
buf.append("\n    " + Modifier.toString(mod) + " ");
## 25

COMP1549: Advanced Programming
## Method Information
◉ You can get method and constructor information in very
similar ways
private StringBuffer getConstructorString(StringBuffer buf)
## {
Constructor[] constructors = clazz.getConstructors();
if(constructors == null){ return buf; }
for(int i = 0; i < constructors.length; i++)
## {
buf.append("\n    " + Modifier.toString(mod) +
" " + constructors[i].getName() +"(");

Class[] params = constructors[i].getParameterTypes();
if(params.length > 0)
## {
buf = getParameterString(buf, params);
## }
buf.append(");");
## }
return buf;
## }
This is a method we defined
in our Reflector class
We will need the
list of Types of all
parameters (the
types are of type
## Class)
## 26

COMP1549: Advanced Programming
## Parameter Information
private StringBuffer getParameterString(StringBuffer buf,
Class[] params)
## {
buf.append(params[0].getName() + " " +
lowerFirstChar(resolveClassName(params[0].getName())
## ) + "0");
for(int i = 1; i<params.length; i++)
## {
buf.append(", ");
buf.append(params[i].getName() + " " +

lowerFirstChar(resolveClassName(params[i].getName())
) + i);
## }
return buf;
## }
resolveClassName is a method implemented in the Reflector
class that will take the fully qualified name and return only
the name of the item (without package details)
## 27

COMP1549: Advanced Programming
private static String resolveClassName(
String className)
## {
int index = className.lastIndexOf('.');
if((index)>-1)
## {
return className.substring(index+1);
## }
else
## {
return className;
## }
## }

## 28
ResolveClassName Method
Checks for the last full stop in
the fully qualified class name
Strips away the package by
getting a substring from the
last full stop onwards

COMP1549: Advanced Programming
## Invoking Methods
return methods[i].invoke( target, new Object[0]);
The object the method is
invoked on
The parameters for the
method
## 29

COMP1549: Advanced Programming
Don’t forget
◉To invoke a method on a class using dynamic reflection,
you must have the class somewhere in your path
◉In the example, you need to put the compiled classes in
the bin folder and they have to be placed in a folder
structure that matches the package name
## 30

COMP1549: Advanced Programming
◉Many of the object-  oriented design patterns
can benefit from reflection
◉Reflection can extend the decoupling of
objects that design patterns offer
◉Can simplify design patterns, e.g.:
Factory
Observer (i.e. Publish/Subscribe)
E.g. to subscribe to events at runtime
Reflection with Design Patterns
## 31

COMP1549: Advanced Programming
public static Shape getShapeFactory(String sp) {
Shape shapeObj = null;
if (sp.equals("Circle")
shapeObj = new  Circle();
else if (sp.equals("Square")
shapeObj = new Square();
else if (sp.equals("Triangle")
shapeObj = new  Triangle();
// continue for each shape manufactured
return shapeObj;
## }
## Factory Without Reflection
## 32

COMP1549: Advanced Programming
public static Shape getShapeFactory(String sp) {
Shape shapeObj = null;
try {
shapeObj = (Shape)Class.forName(sp).newInstance();
} catch (ClassNotFoundException e) {
## ...
## }
return shapeObj;
## }
## Factory With Reflection
## 33

COMP1549: Advanced Programming
Reflection is Powerful
◉Using reflection, you can
Convert strings into classes and objects at
runtime
Ask detailed questions in code about the abilities
of a type
Dynamically compile, load, and add classes to a
running program
## 34

COMP1549: Advanced Programming
◉Reflection should not be used in “normal”
programming where we already have access to
the classes and interfaces because:
Performance overhead
Since reflection resolves types dynamically, it involves
processing like scanning the class path to find the class to
load, which could cause slow performance
High Maintenance
Reflection code is harder to read, understand and debug
Reflection code is more verbose
Any issues with the code can’t always be found at compile
time -  avoiding compile time error checking increases
likelihood of runtime exceptions
Extensive use in "normal" application programming tends
to suggest design flaw
## 35
Disadvantages of Reflection

COMP1549: Advanced Programming
◉Creating tools (IDEs, testing etc.)
◉Enabling very flexible systems
E.g. write any object to a database by
dynamically creating the SQL
◉Dynamic systems -  perhaps where classes
are added while a system is running
◉Used internally by several APIs
e.g. JavaBeans and RMI
## 36
Uses of Reflection

COMP1549: Advanced Programming
Interface as an Alternative
◉Interfaces are sometimes a better alternative
to reflection
◉Interfaces still enable developers to write
code that uses classes which may not have
been implemented yet
## 37

COMP1549: Advanced Programming
◉We looked at modules in the lecture on
components
◉One of the feature of modules is the ability to
restrict access via reflection
◉Reflection lets us view the structure of a
compiled class, instantiate it and invoke its
methods (even when they are private)
Modules can’t restrict reflection on a class’
structure
Modules can restrict the instantiation of objects
and invocation of methods
## 38
Reflection and Modules

COMP1549: Advanced Programming
◉We have already seen that when using Java
modules, you can control which packages are
accessible to other modules
E.g. exports some.pkg;
◉It is also possible to do a qualified export and
export only to one or more specific modules
E.g. exports some.pkg to ExtModule;
◉If a package is exported, using reflection we
can see its structure, instantiate it and invoke
public methods
## 39
## Modules – Exports

COMP1549: Advanced Programming
◉It is possible to open a module or package for
complete reflective access
◉There are three options:
Open the module (automatically opens all
contained packages)
Open a specific package to any module
Open a specific package to one or more specific
modules
## 40
## Modules -  Opens

COMP1549: Advanced Programming
◉Open the module
open module MyModule {}
◉Open a specific package to any module
opens some.pkg;
◉Open a specific package to one or more
specific modules
opens some.pkg to   ExtModule;
## 41
## Modules -  Opens

COMP1549: Advanced Programming
◉Let’s have a look at an example using three
separate projects
## 42
## Module Reflection Example
TestModule
TestAppTestReflector
Opens package to
Exports package to
Exports package to

COMP1549: Advanced Programming
◉Here is an overview of the three projects:
## 43
## Module Reflection Example

COMP1549: Advanced Programming
◉TestModule
Contains a package containing a class with a
public and a private method
Exports the package to TestApp
Opens the package to TestReflector
◉TestReflector
Contains a package with a utility method which
reflects on a given class, invokes its methods
and returns a list of all methods contained within
it
Exports the package to TestApp
## 44
## Module Reflection Example

COMP1549: Advanced Programming
◉TestApp
Contains a package with a static void main
method which loads the class from the
TestModule project, passes it to the
TestReflector module and prints the methods
returned
Requires the TestModule
Requires the TestReflector
## 45
## Module Reflection Example

COMP1549: Advanced Programming
◉The content of the three module descriptors
## 46
## Module Reflector Example
module TestModule {
opens testPackage to   TestReflector;
exports testPackage to TestApp;
## }
module TestReflector {
exports reflector;
## }
module TestApp {
requires TestModule;
requires TestReflector;
## }

COMP1549: Advanced Programming
◉Code in the MyTestClass (TestModule)
## 47
## Module Reflection Example
package testPackage;
public class MyTestClass {
public void publicTestMethod() {
System.out.println("Running public test");
## }
private void privateTestMethod
## () {
System.out.println("Running private test");
## }
## }

COMP1549: Advanced Programming
◉Code in the ReflectionUtil class (TestReflector)
## 48
## Module Reflection Example
package reflector;
import java.lang.reflect.*;
import java.util.ArrayList;
public class ReflectionUtil {
public static ArrayList<String> getMethods(Class
theClass)
throwsIllegalAccessException,
InvocationTargetException,
InstantiationException {
//Content of the method is on the next slide
## }
## }
Declares that it throws a
number of exceptions which
can occur during reflection

COMP1549: Advanced Programming
◉Code in the getMethods method
## 49
## Module Reflection Example
Method[] declaredMethods = theClass.getDeclaredMethods();
ArrayList<String> names = new    ArrayList<String>();
Object theObject =
theClass.getDeclaredConstructors()[0].newInstance();
for (Method declaredMethod : declaredMethods) {
names.add(declaredMethod.getName());
declaredMethod.setAccessible(true);
declaredMethod
.invoke(theObject, null);
## }
return names;
setAccessible() is called to
suppress the check for
access control

COMP1549: Advanced Programming
◉Code in the MainApp class (TestApp)
## 50
## Module Reflection Example
package testApp;
import java.lang.reflect.InvocationTargetException;
import java.util.ArrayList;
import reflector.*;
public class MainApp {
public static void main(String[] args) throws IllegalAccessException,
ClassNotFoundException, InvocationTargetException,
InstantiationException {
Class obj = Class.forName("testPackage.MyTestClass");
ArrayList<String> names = ReflectionUtil.getMethods(obj);
## System.out.println(names);
## }
## }

COMP1549: Advanced Programming
◉This is what we see at runtime
◉This is what you see if you don’t open the
package for reflection
## 51
## Module Reflection Example

COMP1549: Advanced Programming
## 52
End of week 10!