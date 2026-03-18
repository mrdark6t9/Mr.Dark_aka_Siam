

COMP1549: Advanced Programming
## 1
COMP1549: Advanced Programming
## Dr Markus Wolf
## 5
th
## March, 2026 - Week 8
Part a: Introduction to Design Patterns
Part b: GRASP
Part c: Factory Pattern

COMP1549: Advanced Programming
Introduction to Design
## Patterns
## 2

COMP1549: Advanced Programming
## Why Design Patterns?
◉Imagine that I give everyone here a program
to implement
◉I even specify the programming language
➢ Let’s say it’s Java
◉Will I end up with X identical
implementations?
## 3

COMP1549: Advanced Programming
## Why Design Patterns?
◉There are many ways in which to solve a
problem
➢Especially in software development
◉Knowing what features a language supports and
how to apply them will result in better code
◉Another important aspect is how elegantly a
solution is implemented
➢Flexible
➢Reusable
➢Maintainable
➢Reliable
## 4

COMP1549: Advanced Programming
What are Design Patterns?
◉Design patterns are named problem/solution
pairs that codify good advice and principles
for assigning responsibilities (mainly to
classes)
◉Encapsulate recurring problems and their
solutions
◉Build on past experience
➢Good solutions that have proven themselves
◉Balanced assignment of responsibilities is a
very important aspect of a design (and thus
implementation)
## 5

COMP1549: Advanced Programming
What are Design Patterns?
◉Where is the design patterns library, because
I can’t wait to get hold of this code?
➢Not a reusable code solution
➢Not a finished design that can be directly
translated into code
➢A description or template for solving a problem
❖Often described with the aid of UML diagrams
## 6

COMP1549: Advanced Programming
Constituents of a Design Pattern
◉Name (we said it is a named problem/solution
pair)
➢Why named?
❖Helps understanding and remembering
❖Facilitates communication
◉Problem
➢What are we trying to solve?
◉Solution
➢How the problem is solved
## 7

COMP1549: Advanced Programming
## How Did We Get Design Patterns?
◉Notion of patterns originated with
architectural patterns of Christopher
## Alexander
➢“Each pattern describes a problem which occurs
over and over again, and then describes the
core of the solution”
◉First introduced to the realm of software
development in the 1980s by Kent Beck (the
man who gave us extreme programming and
unit testing)
## 8

COMP1549: Advanced Programming
## How Did We Get Design Patterns
◉Major milestone was the publication of a book by
Gang of Four (GoF, Gamma, Helm, Johnson &
## Vlissides)
➢Design Patterns: Elements of Reusable Object-
## Oriented Software
❖Published in 1994
❖Considered the “Bible” of design pattern books
❖Outlines 23 patterns
◉Many authors have described design patterns
since
➢Some more influential ones include:
❖Craig Larman – GRASP
❖Martin Fowler – Enterprise Patterns
## 9

COMP1549: Advanced Programming
Future-Proofing
◉How flexible and future-proof should our
designs be?
◉According to Craig Larman:
➢Novice developers tend towards brittle designs
➢Intermediate developers tend toward overly
fancy and flexible, generalised ones
❖In ways that never get used
➢Expert designers choose with insight
❖Perhaps a simple and brittle design whose cost of
change is balanced against its likelihood
## 10

COMP1549: Advanced Programming
## GRASP
## 11

COMP1549: Advanced Programming
## GRASP
◉A set of principles (or design patterns) for
assigning responsibility
◉General Responsibility Assignment Software
Patterns (Principles)
◉Nine patterns presented to us by Craig
## Larman
◉Too general to be captured as UML diagrams
◉Some you will have come across before
(maybe not tagged as a pattern)
◉Let’s grasp GRASP
## 12

COMP1549: Advanced Programming
## Low Coupling
◉Coupling refers to how strongly an element
(class, component or subsystem) is
connected to, has knowledge of, or relies on
other elements
◉Problem:
➢When elements are highly coupled, a change in
one may enforce changes in others
➢Highly coupled elements are not very reusable –
if I want to reuse one, I have to get all its related
elements
## 13

COMP1549: Advanced Programming
## Low Coupling
◉Solution:
➢Assign responsibilities such that coupling is low
➢Leads to low impact change and high reusability,
as elements are more independent
## 14

COMP1549: Advanced Programming
## High Cohesion
◉Cohesion or functional cohesion refers to
how focussed and related an element’s
responsibilities are
◉Problem:
➢An element has too much responsibility (work)
❖What happens to you when you do too much or very
unrelated work?
➢An element is responsible for a variety of
unrelated tasks – lacks focus
➢Elements with low cohesion are hard to reuse
and maintain
## 15

COMP1549: Advanced Programming
## High Cohesion
## ◉ Solution:
➢ Assign responsibilities such that cohesion
remains high
➢ This makes an element more reusable,
maintainable and easier to understand
◉A highly cohesive class should have a
relatively small number of related methods
and delegate work to others where a task
requires a considerable amount of work
## 16

COMP1549: Advanced Programming
## High Cohesion Example
◉ Where do we have higher cohesion?
## 17

COMP1549: Advanced Programming
## Creator
◉Problem:
➢Who should be responsible for creating
instances of a class
◉Solution:
➢Class B should be responsible for instantiating
class A if:
❖B is composed of A
❖B records A
❖B closely uses A
❖B has the required initialising data for A
## 18

COMP1549: Advanced Programming
## Creator Example
◉Who should create new instances of
## Question?
## 19

COMP1549: Advanced Programming
## Information Expert
◉Problem:
➢How should responsibility be assigned to
elements
◉Solution:
➢Responsibility should be assigned to the
information expert – the element that has the
information required to fulfil the responsibility
## 20

COMP1549: Advanced Programming
## Information Expert
◉In object-oriented software all elements are
“alive” - can do things that the real-life objects
they represent have done to
➢Can do things
➢Can have responsibility
➢E.g. an exam can calculate its own total
➢Known as the “Do It Myself” strategy
## 21

COMP1549: Advanced Programming
## Information Expert Example
◉Who should know the total score?
Knows how many
points it is worth
Knows how many
points it achieved
Knows the total
score
They are all experts with
assigned responsibility
## 22

COMP1549: Advanced Programming
## Controller
◉Problem:
➢Who should be responsible for processing
system events
◉Solution:
➢Introduce a class behind the UI layer that
dispatches events accordingly
➢Could represent:
❖Overall system or major subsystem (Façade
## Controller)
❖A use case scenario (Use-Case Controller)
❖A session (Session Controller)
## 23

COMP1549: Advanced Programming
Polymorphism in Programming
◉Poly (many) morphism (shapes)
◉Concept of polymorphism is supported by
object-oriented programming languages
## (e.g. Java, C#)
➢Possible to use objects of different types
❖If one is a subtypes (inheritance)
❖If they implement the same type (interface or
abstract class)
## 24

COMP1549: Advanced Programming
## Polymorphism
◉Problem:
➢Handle alternatives based on type in a flexible
way
➢Create pluggable components
◉Solution:
➢Assign type-specific responsibility to a type and
use polymorphism to implement the solution
➢Works when alternative behaviour varies by type
❖E.g. calculating the area of a Triangle and a
Rectangle class
## 25

COMP1549: Advanced Programming
## Polymorphism
◉Instead of having conditional logic to check
for type and take appropriate action,
program to an interface and place the
differing behaviour in the type
◉All alternative types need to provide same
type-specific operation
## Markus A. Wolf
## 26

COMP1549: Advanced Programming
## Polymorphism
◉Polymorphism is generally implemented
using interfaces or abstract classes
➢Using interfaces overcomes the single
inheritance of Java
➢Keeps it flexible for future evolution
◉Using polymorphism makes it easy to extend
an application
➢New implementations can be introduced without
affecting existing clients
## 27

COMP1549: Advanced Programming
## Polymorphism Example
◉Through polymorphism, we have different
persistence classes with similar, but
varying, behaviour
## 28

COMP1549: Advanced Programming
## Pure Fabrication
◉Problem:
➢The Information Expert offers a solution for
assigning responsibility to a domain class, but
this violates High Cohesion, Low Coupling
and/or other design goals (e.g. reuse)
◉Solution:
➢Assign the responsibility to an artificial or
convenience class
❖It does not represent a domain concept
❖You make it up for your convenience – it is a
fabrication
## 29

COMP1549: Advanced Programming
## Pure Fabrication
◉Pure Fabrication classes are normally related
to functionality and behaviour
➢Doesn’t represent a thing in our domain
◉Allows the developer to group related
behaviour
◉Should still be cohesive
➢The behaviour placed in a fabrication should be
related or reassigned to more fabricated classes
## 30

COMP1549: Advanced Programming
## Pure Fabrication Example
◉According to the Information Expert, if we
want to persist an answer, the class that has
the data which should be saved is Answer
itself
Would this be
a good idea?
Would create high
coupling to persistence-
related classes (e.g.
Connection classes,
Command classes)
A pure fabrication – but it makes
the Answer more reusable and
our Persistence class is still
cohesive
## 31

COMP1549: Advanced Programming
## Indirection
◉Problem:
➢Two or more elements have high coupling and
low reuse potential
◉Solution:
➢Create an intermediate element to mediate
between the other elements, which are no longer
directly coupled
◉Is the basis for many design patterns
➢E.g. Facade, Observer
## 32

COMP1549: Advanced Programming
## Protected Variation
◉Problem:
➢Making changes to an element has an
undesirable impact on other elements
◉Solution:
➢Identify points of predicted variation or instability
and assign responsibility to create a stable
interface around them
◉Also known as information hiding
## 33

COMP1549: Advanced Programming
## Protected Variation
◉Provides flexibility and protection from variation
in data, behaviour, software components,
operating systems, hardware
◉Very general principle
➢Applied in data encapsulation, polymorphism,
interfaces, configuration files, and more
◉Special form of Protected Variation is Law of
Demeter (Don’t Talk to Strangers)
➢Protect from structure changes by avoiding designs
that traverse long object structures or send
messages to indirect objects (strangers)
❖E.g.
foo.getA().getB().getC().getD().doStuff();
## 34

COMP1549: Advanced Programming
## Protected Variation Example
◉An example of Protected Variation is the
externalisation of the variant
➢The system/application is parameterised at
runtime with data read in from an outside source
(e.g. configuration file)
◉The language for an application that supports
a number of languages can be read in from a
configuration file
➢The application could be set to use an English,
Spanish or German GUI, without having to
change code
## 35

COMP1549: Advanced Programming
## Factory Pattern
## 36

COMP1549: Advanced Programming
## Factory Pattern
◉Problem:
➢Who should be responsible for creating objects
when there are special considerations
❖E.g.
•Complex creation logic
•Separate creation responsibilities for better cohesion
•Polymorphic creation (use alternative implementations)
◉Solution:
➢Create a pure fabrication object (factory) that
handles creation
Remember the
Creator GRASP
pattern?
## 37

COMP1549: Advanced Programming
## Factory Pattern
◉Also called Simple Factory or Concrete
## Factory
◉A simplification of the GoF Abstract Factory
pattern
◉Its main aim is to decouple code that makes
use of an implementation from the actual
implementation
◉Can also be used to improve performance
➢Caching and reusing objects
## 38

COMP1549: Advanced Programming
## Example
◉Imagine an application for sitting exams
◉Different exams exist for different subjects
➢With a different class per exam
Exam is an interface /
abstract class to keep
flexibility
## 39

COMP1549: Advanced Programming
## Example
◉On the previous slide it looked as if the
ExamApplication and the different
implementations are decoupled, but...
◉You could have code, as the following, in
your ExamApplication:
Exam exam;
if(examType == ExamType.English) {
exam = new EnglishExam();
## }
else if(examType == ExamType.Maths) {
exam = new MathsExam();
## }
ExamType is an
enumeration
Not very flexible
– what if new
exams are
introduced?
## 40

COMP1549: Advanced Programming
## Example
◉If we create a factory, this code is placed in a
class whose responsibility is the creation of
## Exams
Decouples the ExamApplication from
the actual Exam implementations
Returns a
reference of type
## Exam
## 41

COMP1549: Advanced Programming
## Factory Method Pattern
◉A GoF design pattern
◉The problem is the same, but the
implementation is different
◉Encapsulates object creation by delegating
creation to subclasses
All products
must
implement
the same
interface
## Contains
implemented
methods, except
for factory
method
## Markus A. Wolf
## 42

COMP1549: Advanced Programming
## Factory Method Example
◉What would our case-study look like?
## Creator
ConcreteCreator
ConcreteCreator
ConcreteProduct
ConcreteProduct
## Product
## 43

COMP1549: Advanced Programming
◉There are many more design patterns, we
didn’t have time to cover
## 44
## Many More

COMP1549: Advanced Programming
## 45
End of week 8!