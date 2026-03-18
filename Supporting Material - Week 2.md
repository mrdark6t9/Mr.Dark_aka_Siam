

COMP1549: Advanced Programming
## 1
COMP1549: Advanced Programming
## Dr Taimoor Khan
## WWW
•Personal
•Operational Lead - NCSC accredited ACE-CSR
•Deputy Director - NCSC accredited ACE-CSE
•RITICS Fellow – Imperial College London
## 22
nd
## January 2026 - Week 2
Revision of Key Concepts of Java Programming

COMP1549: Advanced Programming
## Announcements
◉Coursework: Announced on Moodle worth 100% of the module
➢Task: Attempt only ONE of the given two tasks
➢Format: 1) implementation 2) a report, and 3) a demo
➢Group: either individual or a group of up to 6 students
➢Deadline: March 30, 2026
➢Submission: A single ZIP file (per group/individual) via Moodle that includes
1.A src folder of the implementation,
2.A contribution.txt file
3.A PDF of the report
➢Demonstration: A 5-10 minutes demonstration on April 2, 2026
◉Groups
➢You must update online file by February 5, 2026, if you are working in a group
➢No request for new group will be entertained after February 5, 2026
Those who fail to report their group members by February 5, 2026, will be grouped
randomly
## 2

COMP1549: Advanced Programming
## Programming Paradigm
## 3

COMP1549: Advanced Programming
## Program
◉Design algorithm (A)
➢Assumptions
➢Sequence of steps/tasks
◉Develop a source code in some language (P)
➢set of instructions with internal data
◉Compile the program P (P’)
➢P’ should be semantically equivalent to P
◉Execute compiled code P’ (P’’)
➢P’’ should be semantically equivalent to P
In practice, any step may go wrong either at design-time or
at run-time resulting in unexpected program behavior
## 4
Design time
Run time

COMP1549: Advanced Programming
Software engineering perspective
## 5
This course is focused on functional behavior

COMP1549: Advanced Programming
Challenges in program development
Typically, the requirements are
◉Changing
➢Contradictory requirements
➢Overlapping requirements
That result in program evolution
Therefore, a program should be
◉Reliable
➢Works as expected in normal as well as compromised
environment
◉Understandable
➢Human and machine can understand the workflow
◉Extendable
➢Modular at fine grain level
How to achieve that?
## 6

COMP1549: Advanced Programming
Art of Programming
Programming is an art because it requires
◉expression/application of human creative skills
➢to design (i.e., view) and develop (i.e., realize)
algorithms
In this module, we will demonstrate various
concepts that are art by design for programming
◉You should understand the art and its
application
Developing a simple program that meets its
requirements requires a lot of creativity
(more on this topic in Week 11)
## 7

COMP1549: Advanced Programming
## 8
Key Concepts of Java
## Programming

COMP1549: Advanced Programming
Inheritance and Polymorphism
## 9

COMP1549: Advanced Programming
Class BankAccount
BankAccount
#accountNumber : String
#balance : float
#interestRate : float
+acceptDeposit(amount)
+acceptWithdrawal(amount)
+getBalance() : float
+getAccountNumber() : String
+setRate(iRate)
+getRate() : float
variables
methods
•What is the meaning of +
and # ?
•How are method return
types shown?
•What does the
underlining indicate?
•Are any constructors
shown here?

COMP1549: Advanced Programming
Class BankAccount
public class BankAccount {
protected float balance;
protected static float interestRate = 0;
protected String accountNumber;
public BankAccount(String accountNumber) {
this.accountNumber = accountNumber;
balance = 0.0F;
## }
public void acceptDeposit(float amount) {
balance += amount;
## }
public void acceptWithdrawal(float amount) {
balance -= amount;
## }
public float getBalance() { ...}
public String getAccountNumber() { ...}
public static void setRate(float iRate) {
interestRate = iRate;
## }
public static float getRate() {
return interestRate;
## }}
Explain the need for the
following:
•this.accountNumber
## •0.0F
BankDemoOne.java

COMP1549: Advanced Programming
◉A form of reuse where one class builds on another
◉A new class inherits variables and methods from an existing
class.
◉The inheriting class is a subclass and the class being
inherited from is the superclass
◉In Java there is no direct multiple inheritance – a class can
only directly extend one other
## Inheritance
BankAccount
LoanAccount
•The extends keyword is used when defining a
class that inherits from another e.g.
public class LoanAccount extends BankAccount {...}
•Quite often you create classes by extending a
class from the Java API e.g.
public class HelloWorld extends JFrame { ... }

COMP1549: Advanced Programming
Inheritance hierarchy
an inheritance
hierarchy shown
using UML
•class StandardLoan inherits variables and methods from
class LoanAccount which in turn inherits variables and
methods from class BankAccount
•Classes become more specialized as you move down
the hierarchy and more generalized as you move up
BankAccount
CashAccountLoanAccount
PremierLoan
StandardLoan

COMP1549: Advanced Programming
◉Inheriting variables and methods a subclass
can
1.add new variables and methods
❖e.g., class LoanAccount may have
•an extra variable called payBackPeriod
•an extra method called calculateInterest()
2.override inherited methods to provide different
behaviour
❖e.g., class CashAccount may override
acceptWithdrawal() to check that the amount being
withdrawn doesn't reduce the balance below zero
What a subclass can do?
What are the rules for overriding?

COMP1549: Advanced Programming
◉When there is an is a or is a kind of
relationship between the two classes
LoanAccount is a kind of BankAccount
◉NOT! when there is a has a relationship
between the classes
When is inheritance the correct technique?
BankAccount
## Customer
class BankAccount extends Customer {
## .......
class BankAccount {
private Customer theCustomer;
## ......

COMP1549: Advanced Programming
◉A key concept
◉An object of a subclass can be used as
as a substitute wherever an object of its
superclass is expected e.g.
public void printStatement(BankAccount  b) {
// print the account statement ....
## }
## ......
StandardLoan sl = new StandardLoan("12345");
printStatement(sl);
◉Why is this allowed?  How can it be useful?
## Substitution
BankAccount
LoanAccount
StandardLoan

COMP1549: Advanced Programming
public void sendSpecialOffer(LoanAccount la) {
// send the special offer
## }
## ......
StandardLoan sl = new StandardLoan("12345");
sendSpecialOffer(sl);
BankAccount ba = new BankAccount("99999");
sendSpecialOffer(ba);
Will this compile?
BankAccount
LoanAccount
StandardLoan

COMP1549: Advanced Programming
◉All Java classes are inherited from class Object
◉Class Object defines methods inherited by all classes
◉method toString() returns a string representation of an
object
System.out.println(myAccount.toString());
// shows something like :bankone.BankAccount@9931f5
◉method equals() returns true only if passed the same
object
accA = new BankAccount("126542");
accB = new BankAccount("126542");
if (accA.equals(accB))  {      // false – why? }}
Object - a super class in Java

COMP1549: Advanced Programming
we need to
override the
inherited toString()
and equals() if we
want them to do
anything useful

COMP1549: Advanced Programming
Overriding toString()
public class BankAccount {
protected String accountNumber;
protected float balance;
protected static float interestRate = 0;
public BankAccount(String accountNumber) {
this.accountNumber = accountNumber;
balance = 0.0F;
## }
## .....
public String toString() {
// what could go here?
## }
## }
BankAccount myAccount = new BankAccount("123456");
## ...
System.out.println(myAccount.toString());
System.out.println(myAccount);  // same effect as above
BankDemoTwo.java

COMP1549: Advanced Programming
Overriding equals()
class BankAccount {
protected String accountNumber;
protected float balance;
protected static float interestRate = 0;
public BankAccount(String accountNumber) {
this.accountNumber = accountNumber;
balance = 0.0F;
## }
## .....
public boolean equals(Object other) // must have same signature as in Object
// The two accounts are equal if their account number has the same value
// If the given account number is this.accountNumber then return true,
// otherwise return false.
// How do we code that?

BankAccount accA = new BankAccount("126542"),
BankAccount accB = new BankAccount("126542");
## ...
if (accA.equals(accB)) {  // True – why? }}
BankDemoTwo.java

COMP1549: Advanced Programming
◉An important feature of OO programming
languages such as Java
◉Also referred to as polymorphism or "late
binding"
◉Relates to substitution and overriding of
inherited methods
Dynamic binding of methods (1)

COMP1549: Advanced Programming
Revisiting program execution
MyClass.java
compile
MyClass.class
run
user
compile time
run time
things that get decided
at compile time are often
referred to as
static or early
binding
things that get decided at
run time are often
referred to as
dynamic or late
binding
programmer

COMP1549: Advanced Programming
Dynamic binding of methods (2)
LoanAccount
CashAccount
acceptWithdrawal()
BankAccount
acceptWithdrawal()
1   CashAccount   a = new CashAccount(“00368754”);
2   a.acceptWithdrawal(50.00);    // CashAccount version of method
3 BankAccount   b = a;
4 b.acceptWithdrawal(99.00);   // which version of method ??

COMP1549: Advanced Programming
◉How the compiler sees line 3
➢CashAccount is substitutable for BankAccount therefore
the assignment is legal
◉How the compiler sees line 4
➢b is a variable of type BankAccount
➢class BankAccount has an acceptWithdrawal() method
➢line 4 is therefore a legal method call
◉How the runtime sees line 4
➢Looks at the object referenced by b to see its class
➢Its class is CashAccount
➢Execute the method acceptWithdrawal() defined for class
CashAccount
Dynamic binding of methods (3)

COMP1549: Advanced Programming
◉Will this compile?
➢If not, why?
➢If yes, what is the output?
Another example
class Shape {
void draw() {  System.out.println("Shape.draw()");   }
## }
class Circle extends Shape {
void draw() {  System.out.println("Circle.draw()");    }
## }
class Square extends Shape {
void draw() {  System.out.println("Square.draw()");  }
## }
public class TestShapes {
public static void main(String[] args) {
Shape sh = new Shape();
Circle c = new Circle();
Square s = new Square();
sh.draw(); c.draw(); s.draw();
sh = s; sh.draw();
sh = c; sh.draw();
## }
## }

COMP1549: Advanced Programming
## Abstraction
(interface and abstract classes)
## 27

COMP1549: Advanced Programming
◉Classes are a way of representing similarities/characteristics
between a set of objects
◉Interfaces allow “Capturing similarities among unrelated
classes without artificially forcing a class relationship”
◉Imagine a program where we have these three class
hierarchies BUT the classes in orange also have something
important in common
Purpose of interfaces
bank accountsinsurance policiesunit trusts

COMP1549: Advanced Programming
◉The Bank also offers some
unrelated products and services
e.g., unit trusts, insurance
◉It wants some of these to appear in its brochure
◉To appear in the brochure the product needs to
have the method getItemDescription()
◉How can this similarity between otherwise unrelated
classes be captured?
◉By defining an interface (e.g., BrochureItem) and
making the appropriate classes implement this
interface
Representing similarity
BankAccount
CashAccountLoanAccount
PremierLoanStandardLoan

COMP1549: Advanced Programming
◉An interface is a bit like a shrunken class
◉It contains method declarations (but not bodies) and
constants e.g.
public interface BrochureItem {
String getItemDescription(); // no method body
## }
◉Classes that implement the interface MUST contain code
for the methods defined in the interface e.g.
public class LoanAccount extends BankAccount
implements BrochureItem {
public String getItemDescription() {
return “Low interest - never need to repay”;
## }
## ....
## }
Interfaces syntax
## Actually
they can in
## Java 8

COMP1549: Advanced Programming
◉Why are interfaces useful?
◉Instances of classes that implement an interface are
substitutable wherever things of the interface type
are required e.g.
public void printBrochureItem(BrochureItem  b) {
// code to print the item description in the brochure
## }
## ....
LoanAccount la = new LoanAccount(“0055634”);
InsurancePolicy ip = new InsurancePolicy();
## ....
printBrochureItem(la);
printBrochureItem(ip);
Interfaces and substitution
BankDemoThree.java

COMP1549: Advanced Programming
Interfaces in UML class diagrams
LoanAccount
InsurancePolicy
BankAccount
## <<interface>>
BrochureItem
<<interface>> stereotype to indicate an interface
to indicate implements

COMP1549: Advanced Programming
◉Classes can only extend one other class (no direct multiple
inheritance) but may implement any number of interfaces
(aka multiple partial inheritance).
◉Interfaces may themselves extend other interfaces
Complex hierarchies with interfaces
ClassW
ClassX
ClassY
## <<interface>>
InferfaceA
## <<interface>>
InferfaceB
## <<interface>>
InferfaceC
## <<interface>>
InferfaceD
## Object
ClassZ
extends
implements
## <<interface>>
InferfaceE

COMP1549: Advanced Programming
“But really, are interfaces useful?"
http://programmers.stackexchange.com/questions/108240/why-are-interfaces-useful
It took me more than two years to really understand what
interfaces are good for. My suggestion: study Design Patterns.
As most of them rely on interfaces, you will quickly understand
why they are so useful.
You've got a lot to
learn friend.
Forget C#, forget Java, forget the language. It's simply thinking in
terms of OO. I would encourage you to pick up some reading
material from folks like Robert C. Martin, Martin Fowler, Michael
Feathers, the Gang of Four, etc., as it will help expand your thinking.
I have been studying and coding in C# for
some time now. But still, I can't figure the
usefulness of Interfaces. They bring too little to
the table.  .......

COMP1549: Advanced Programming
◉Java 8 introduced default methods in interfaces
◉Default methods can contain body of methods
◉Classes that implement interfaces inherit the
default methods if they don’t override them
New feature of interfaces in Java 8
interface InterfaceA {
public void saySomething();  // abstract method
default public void sayHi() {  // new style default method
System.out.println("Hi");
## }
## }
public class MyClass implements InterfaceA {
public void saySomething() {   // must implement this method
System.out.println("Hello World");
## }
//  but no need to implement the default method sayHi()
## }
Nice tutorial at
https://blog.idrsolutions.c
om/2015/01/java-8-
default-methods-
explained-5-minutes/

COMP1549: Advanced Programming
◉Everything in real world is abstract
➢You know something(s) about it
➢You don’t know something(s) about it
What it has to do with program or software design?
◉We want to implement a requirement that has
➢Some known behavior and also
➢Some partially known behavior
◉We can implement that requirement as an abstract class that
has
➢Some methods with implementation
➢Some methods with declarations only
## Abstraction

COMP1549: Advanced Programming
◉Abstract class is a class of which you can not create an
instance e.g.
➢If class Stella is a normal class the you can create an instance
Stella star = new Stella();
➢But if Stella is an abstract class then the line above will give a
compiler error!
◉Methods in the abstract class may be:
➢abstract – contains no code  - must be overridden
➢concrete – contains code - normal methods
◉Used extensively when defining libraries e.g. in the Java API
Abstract classes
abstract method
public abstract int piet();
Concrete method
public int lucian() {
return 5;
## }

COMP1549: Advanced Programming
◉Screen and Printer are normal (i.e. concrete) classes that
inherit from the abstract class OutputDevice
◉Concrete subclasses of abstract classes MUST override any
abstract methods (method output() in this case)
◉In UML you can indicate an abstract class and abstract
methods using italics
Inheriting from an abstract class
OutputDevice
type
output (data :String)
getType() : String
## Screen
output (data :String)
## Printer
output (data :String)

COMP1549: Advanced Programming
public abstract class OutputDevice {
private String type;
public OutputDevice(String type) {
this.type = type;
## }
public OutputDevice() {
this("undefined type");
## }
public abstract void output(String data);
public String getType() {
return type;
## }
## }
Example Abstract class
How is this better than
having a OutputDevice
as a normal class with
an output() method that
just doesn’t do anything?
What does this constructor
do?
Abstract classes can contain instance variables
project AbstractClassExample
What is the point of having a
constructor in an abstract
class?
Abstract classes can be a mixture of abstract and concrete methods.

COMP1549: Advanced Programming
public class Screen extends OutputDevice {
public Screen() {
super("Screen");
## }
public void output(String data) {
System.out.println("Output " + data + " on a screen");
## }
## }
public class Printer extends OutputDevice {
public Printer() {
super("Printer");
## }
public void output(String data) {
System.out.println("Output " + data + " on a printer");
## }
## }
Concrete subclasses
What happens when this
constructor is called?
What will happen if the
output() method is removed
from these two classes?
project AbstractClassExample

COMP1549: Advanced Programming
public class AbstractTest {
public static void main(String[] args) {
List<OutputDevice> ods = new ArrayList<>();
ods.add(new Screen());
ods.add(new Printer());
ods.add(new Screen());
for (OutputDevice o : ods) {
System.out.print(o.getType() + ":");
o.output("Some data");
## }
## }
## }
Use the abstract and concrete classes
Why are we allowed to add a
mixture of Screen and Printer
objects to a List of OutputDevices?
project AbstractClassExample

COMP1549: Advanced Programming
◉When you can answer YES to both the
following
➢Do I want to prevent people from creating an
instance of this class?
➢Are there some methods and/or variables that I
want subclasses of this class to inherit (with
implementation)?
❖Avoid duplicate code
When do we need an abstract class?

COMP1549: Advanced Programming
AbstractContainer
AbstractSequence
## Array
SLList
AbstractStack
PrimitiveArrayStackSequenceStack
DLList
## <<interface>>
## Sequence
## <<interface>>
## Stack
## <<interface>>
## Container
Abstract classes and interfaces in API design
Example is from the
ADS library designed by
## Russel Winder

COMP1549: Advanced Programming
◉Interfaces (Stack, Sequence etc) give maximum
flexibility
➢classes can implement multiple interfaces
➢entirely different implementations can be plugged in
◉Abstract classes (AbstractStack,
AbstractSequence etc.) conform to the interfaces
➢contain code (including instance variables) common to a
number of classes
➢avoids code duplication
◉Concrete classes (Array, SequenceStack etc.)
inherit from the abstract classes
➢provide alternative implementations of the same
abstraction
ADS inheritance hierarchy

COMP1549: Advanced Programming
## J A V A
Which could we live more easily without?
## 45
## ?

COMP1549: Advanced Programming
## 46
End of week 2!