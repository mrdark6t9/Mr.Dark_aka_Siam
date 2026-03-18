

COMP1549: Advanced Programming
## 1
COMP1549: Advanced Programming
## Dr Markus Wolf
## 19
th
## March, 2026 -  Week 10
## Unit Testing

COMP1549: Advanced Programming
## 2
Why do we test our code?
◉Is it to:
show what brilliant programmers we are by
demonstrating that our program doesn’t contain any
bugs
## ◉or
find the bugs that certainly exist in our code before
anyone else spots them?
◉“A good test is one that is likely to uncover a flaw
that was previously unknown.” (Sundsted 1999)

COMP1549: Advanced Programming
## 3
A Basic Problem of Testing
Even for simple programs there is never enough
time to carry out exhaustive testing (e.g. all
possible combinations of input)
Hence the development of strategies for
creating tests with the greatest chance of
revealing bugs
Two key strategies: Black-box testing and
White-box testing

COMP1549: Advanced Programming
## Black Box
## 4
## ? ? ?
We don't know what's in the box but we do know what it is
meant to do - i.e. given a certain input we can predict the
correct output
## INPUTS
## OUTPUTS

COMP1549: Advanced Programming
## Choosing Good Test Cases
◉Imagine testing a function called calcCost
## 5
calcCost
## INPUTS
## OUTPUTS
Two  integer
parameters
representing:
•price per unit
•quantity of units
If both parameters are >=
zero return integer
representing cost (price x
quantity)
Otherwise throw
IllegalArgumentException

COMP1549: Advanced Programming
## Choosing Good Test Cases
## 6
caseTest case inputsExpected
output
1price 5, quantity 420
## 2
## 3
## 4
## 5
## 6
## 7
## 8
Are 8 test cases enough?
-  Choose 8 test cases with what you think are the best
chances of uncovering a bug

COMP1549: Advanced Programming
White box
## 7
## ? ? ?
We can see the code in the "box"
Still need to choose test cases with
the best chance of catching the bugs
## INPUTS
## OUTPUTS
## AT THE VERY LEAST
choose test cases so
at every line in the
code gets executed at
least once during
testing

COMP1549: Advanced Programming
## 8
public void adjustSpeed() {
if (speed > 0) {
if (distance < 10) {
accelerate = false;
breakk = true;
} else if (distance < 50) {
accelerate = false;
} else if (speed < speedLimit) {
accelerate = true;
breakk = false;
## }
} else {
accelerate = true;
breakk = false;
## }
resetReadings();
## }
What are the minimum
number of test cases
required to ensure that all
the lines of code the
method are executed?

COMP1549: Advanced Programming
## 9
Another problem of testing
◉Software doesn’t stay tested
◉“But I only made a one line bug fix to the
MedicalRecord class to fix that
NullPointerException.  I can’t possibly have
caused the whole Hospital system to crash.”
◉Oh yes it can!!!!
◉Hence the need for regression testing - a suite of
repeatable tests that can be used to test that
things still work after (perhaps seemingly
unrelated) modifications have been made

COMP1549: Advanced Programming
## 10
Unit/Component testing
◉The type of testing most likely to be carried
out by programmers on their own code
◉Shares many techniques and concepts with
other types of testing (integration testing,
system testing, etc.)
◉The rest of the lecture focuses on this

COMP1549: Advanced Programming
## Unit Testing
◉The first unit testing software was called SUnit
Developed by Kent Beck (from Extreme
Programming fame) and Erich Gamma (one of the
Gang of Four, from Design Patterns fame)
Used for testing SmallTalk code
◉Test frameworks now exist for many
programming languages
JUnit for Java, CppUnit for C++, NUnit for .NET,
many others
Known collectively as xUnit
Support built into most IDEs
## 11

COMP1549: Advanced Programming
## The Problem
◉ Untested Code may not work
 Large applications may have many complex code
paths that have never been entered during
development
 Worse – code may only work some of the time
(because of threading or timing or other external issues
– such as network failure)
 Even worse – some code appears to work, but in
actual fact doesn’t (this happens far more often than
you would believe possible)
## 12

COMP1549: Advanced Programming
The Problem cont.
◉ Untested code is hard to maintain
 If you add one piece of functionality – it is possible it
will break an existing part of you application. How will
you know?
◉ Untested code is a black box – even with the
source code
 Untested code means the assumptions of the
developer are buried deep down in source code. What
if their assumptions were wrong?
 Documentation always falls behind developed code.
No one likes doing documentation
## 13

COMP1549: Advanced Programming
## Am I Infected?
◉Erich Gamma coined the phrase to describe
programmers who made writing tests part of their daily
programming
◉Symptom: seeing a programming problem in terms of
tests first and implementation later
◉Spend less time debugging and more time designing
◉Write many tests – test often – learn testing skills
◉The process is called Test-Driven Development (TDD)
## 14

COMP1549: Advanced Programming
Test-Driven Development
◉When you want a write some new code -
write the test first
 Will automatically give you the highest level view
of the problem domain you are attempting to
address
◉Then implement just enough code for your
test to pass
 May well mean developing dummy classes that
return set values
## 15

COMP1549: Advanced Programming
## Keep Testing
◉Checking your tests fail is as important as
checking your tests pass
◉When you implement new functionality or
change existing code, run all the tests, not
just the one you think is affected
Ensure that nothing you are introducing in your
new code breaks your existing tests
## 16

COMP1549: Advanced Programming
This seems like a lot of work
◉Soon it will save you time
◉You know all of your code works
◉You add something and immediately know whether your
code still works or where it is going wrong
The compiler tells you about syntax errors. Your test will warn
you of logic error
◉You fix something – you know you REALLY fixed it
◉You think you fixed it – but you didn’t. A well written test
will reveal your error straight away
## 17

COMP1549: Advanced Programming
There’s more
◉A well written test documents your
components
It shows you how to use your component, not just
with words but with an example
◉If you run your test – your documentation
stays up to date (unlike real documentation
which is always done last)
## 18

COMP1549: Advanced Programming
Isolation of Tests
◉Tests should be isolated
It should be possible to run any test in isolation
One test should not be affected by another – the
outcome of a test should not be dependent on
another test succeeding
This enables tests being run in any order
◉There is overhead in isolating tests
Each test needs to set all state it needs to
execute – e.g. open connection to database,
instantiate all required objects, etc.
## 19

COMP1549: Advanced Programming
Initialise and Cleanup
◉Creating state for each test can result in very
lengthy and repetitive test code
◉Code can be simplified by extracting common
code to:
Create state necessary for a test – setup
Return to original state after test – teardown
◉Test methods can concentrate on running the
actual tests
Use the setup and teardown to create state and
return to original state, if necessary. May only be
required if test makes use of external resources, e.g.
DB, server, network connection, etc.
## 20

COMP1549: Advanced Programming
## Mock Object
◉A Mock Object is an object designed to ape
real behaviour
◉When you are developing complex
components it can be useful to work to the
fixed point of a particular Mock Object until
you are ready to implement that object
properly
## 21

COMP1549: Advanced Programming
## Mock Object
◉Replace expensive, unreliable or slow resources
with a mock object
E.g. a Database, network connection, sending
emails, etc.
◉Enables you to perform the test without relying
on external sources not to fail
Is my code faulty or is it the connection?
◉Speeds up the testing
◉However –   a mock is not the real object, so it’s
possible your test passes, but the real system
fails
Make it possible to switch between a mock and the
real object
## 22

COMP1549: Advanced Programming
## Test Advice
◉ What should I test?
 Every non trivial algorithm
 Anything that has ever broken
◉ Tests should be
 Quick – If they take too long you won’t run them
so often
 Self contained - If there is an error in one test it
shouldn’t propagate to others
 KISS (Keep It Short and Simple)
## 23

COMP1549: Advanced Programming
JUnit and Eclipse
◉Support for JUnit tests is built-in to Eclipse
◉The testing tools don’t offer features for
testing UIs (e.g. automatically entering text or
clicking buttons)
So, very important to put all your application
logic in “testable” classes – thin UI layer
◉Visibility restrictions apply
Every method you want to test has to be visible
to the test code – the test code is placed in the
same package, so you can test default visibility
## 24

COMP1549: Advanced Programming
## Testing Example
◉Let’s create some unit tests to test the
calculator application we created in a
previous week (Components)
## 25
## Markus A. Wolf

COMP1549: Advanced Programming
## Testing Example
◉We created the application as two separate
modules:
CalculatorModule – containing the logic
CalculatorGUI – containing the user interface
layer
◉We already have a neat structure where no
logic remains in the UI
◉The Calculator class contains the methods
to carry out the calculations
## 26
## Markus A. Wolf

COMP1549: Advanced Programming
## Calculator Class
## 27
package calculatorModule;
import calculatorModule.logger.CalculatorLogger;
public class Calculator {
public float add(float num1, float num2) {
float result =  num1  + num2;
CalculatorLogger.LogResult(result,
CalculatorOperation.addition);
return result;
## }
public float subtract(float num1 , float num2) {
float result =  num1  -  num2;
CalculatorLogger.LogResult(result,
CalculatorOperation.subtraction);
return result;
## }
## ...

COMP1549: Advanced Programming
◉Right-click on the CalculatorModule project
## 28
Creating a Unit Test
Select JUnit Test Case

COMP1549: Advanced Programming
## Creating Unit Tests
The class will be placed
in a separate test
package
Set the name for
the class
containing the
unit tests
## 29
Select the test framework Junit 4
and Jupiter use annotations
This is the class we will
generate tests for
Initialisers and
finalisers could be
generated

COMP1549: Advanced Programming
## 30
## Creating Unit Tests
Select the methods for which
you want to create tests

COMP1549: Advanced Programming
◉As our project uses modules, we need to add
JUnit 5 to the module path
◉We also need to a dependency for
org.junit.jupiter.api to the module-info file
## 31
JUnit and Modules

COMP1549: Advanced Programming
## Test Class
◉Eclipse automatically creates a test class and
skeleton code
## 32
## Markus A. Wolf
Placed in a
separate package
A static import allows us to call
static methods directly, without
specifying the class name

COMP1549: Advanced Programming
## Attributes
◉From version 4 onwards of Junit uses
attributes to annotate test elements
@Test – defines a test
@BeforeAll – runs once before any of the test
methods in the class
@BeforeEach – runs once before each test in
the class
@AfterAll – runs once after the last test in the
class
@AfterEach – runs once after each test in the
class
@Disabled – makes it possible to ignore a test
## 33

COMP1549: Advanced Programming
## Assertions
◉Assertions are used to test whether an actual
post-condition is the same as an expected
post-condition
◉JUnit contains many static methods for
assertion in the Assertions class
◉All static methods in the Assertions class will
evaluate either to true (the test has passed) or
false (not passed), with the exception of fail
which will always fail
◉If more than one assertion exist within one test,
the test will stop as soon as the first assertion
fails
## 34

COMP1549: Advanced Programming
## Assertions
◉Some of the static methods:
assertEquals – true if both arguments have
same value (makes call to Equals method for
non-primitive data types)
assertSame – true if two references point to the
same object
assertTrue – true if it is passed something that
evaluates to true
assertArrayEquals - true if two arrays contain the
same elements
assertNull – true if what is passed in is null
More...
## 35

COMP1549: Advanced Programming
@Test
void testAdd() {
float a = 2.5f;
float b = 8.2f;
Calculator calculator = new Calculator();
float expectedResult = a + b;
float result = calculator.add(a, b);
assertEquals(expectedResult, result);
## }
## Testing Add()
This is what we
expect
This is what
add() returns
Do the results
match?
We could use a third parameter -
delta (the variation in result that
would still pass)
## 36

COMP1549: Advanced Programming
Running Tests in Eclipse
◉When you click on the run button, select the
Run As JUnit Test option
## 37
The tests that are
run
How many tests
are failed

COMP1549: Advanced Programming
## Ignoring Tests
 Sometimes it may be useful to ignore
tests
E.g. if test functionality is not implemented yet
Use Disabled annotation
@Disabled
@Test
public void testMultiply() {
Test will be
ignored
## 38
testMultiply is
now omitted

COMP1549: Advanced Programming
## Testing Exception
◉What if we want to test exception
handling?
◉It is possible to write tests that only pass if
an exception of the expected type is
thrown
Tests for an
exception
## 39
@Test
public void testDivideByZero() {
int a  = 12;
int b  = 0;
Calculator instance = new Calculator();
assertThrows(ArithmeticException.class,
()-> instance.divideInt(a , b));
## }
Test passed
Had to add a method that divides
integers, as a float would be set to
Infinity and not throw an exception

COMP1549: Advanced Programming
## 40
End of week 10!