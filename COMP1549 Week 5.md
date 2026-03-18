

COMP1549: Advanced Programming
## 1
COMP1549: Advanced Programming
## Dr Taimoor Khan
## WWW
•Personal
•Operational Lead - NCSC accredited ACE-CSR
•Deputy Director - NCSC accredited ACE-CSE
•RITICS Fellow – Imperial College London
## 12
th
## February 2026 - Week 5
## Secure Programs

COMP1549: Advanced Programming
## Secure Programs
## 2

COMP1549: Advanced Programming
A program is a set of instructions that encode
requirements (or intentions) that involve
◉concepts from real-life, e.g., age, and distance
A program is secure iff it is not vulnerable
Vulnerability is
◉Either a weakness in a program
◉Or a feature of a program that can be misused
which can be exploited by an adversary for harm
## Program

COMP1549: Advanced Programming
How to a develop a vulnerability free program?
◉By encoding concepts in an adequate way, i.e., without missing
key details that may be misinterpreted
But current programming languages
◉Either do not have built-in support for the concepts, e.g., age
◉Or partially supports the concepts subject to validity, e.g., time
How to encode concepts adequately in a program?
◉Through describing/modelling key details of the concepts to the
program or run-time
We focus on vulnerabilities that arise from programming languages
Secure program

COMP1549: Advanced Programming
One way to model simple but key details of the
concepts is
◉Using Java annotations
➢To avoid compile-time errors but
➢Can also be used to avoid run-time errors
## Modelling

COMP1549: Advanced Programming
◉A Java compiler plug-in framework that
extends type checking to catch bugs at
compile time
➢Extends data types with additional constraints
❖Types represent values that form a concept
❖Type constraints introduces legit use of operations
with the values
◉The checker guarantees that
➢type annotations reflect facts about run-time
values, and
➢vulnerable operations (on values) are not
performed
## Checker Framework (1)

COMP1549: Advanced Programming
◉The checker is open-source and free,
accessible via https://checkerframework.org
◉The checker guarantees that
➢type annotations reflect facts about run-time
values, and
➢vulnerable operations (on values) are not
performed
## Checker Framework (2)

COMP1549: Advanced Programming
Example – Null values
◉Write a program to get name as an input
➢Where first and last name cannot be null, while
➢Middle name can be null
◉Can a Java compiler find any error in this
program?
public void getName(String firstName, String middleName, String lastName){
System.out.println(”First name is ”+firstName.toString());
System.out.println(”Middle name is ”+middleName.toString());
System.out.println(”Last name is ”+lastName.toString());
## }

COMP1549: Advanced Programming
## Demo
public void getName(String firstName, @Nullable String middleName, String lastName){
System.out.println(”First name is ”+firstName.toString());
System.out.println(”Middle name is ”+middleName.toString());
System.out.println(”Last name is ”+lastName.toString());
## }
http://tinyurl.com/yeyrcrux

COMP1549: Advanced Programming
@Nullable

COMP1549: Advanced Programming
Example – Injection vulnerability
◉Write a program to fetch data from a database using a
➢Query that requires input username from the user
◉Can a Java compiler find any error in this program?
public String getUserInput() {
return ”untrustedInput";
## }
public  void processRequest() {
String input = getUserInput();
executeQuery(input);
## }
public void executeQuery(String input) {
// Run the SQL Query with the provided input
## }

COMP1549: Advanced Programming
Example – Vulnerable run

COMP1549: Advanced Programming
## Demo
public String getUserInput() {
return ”untrustedInput";
## }
public  void processRequest() {
@Tainted String input = getUserInput();
executeQuery(input);
## }
public void executeQuery(@Untainted String input) {
// Run the SQL Query with the provided input
## }
http://tinyurl.com/5n76ntsw

COMP1549: Advanced Programming
@Tainted

COMP1549: Advanced Programming
◉The checker can be extended to
➢Introduce new types of your choice with desired
constraints
➢Update existing types with additional constraints
➢Model advanced and generic concepts as types
in an automated way
## ➢...
Further demo
## More...

COMP1549: Advanced Programming
## 16
End of week 5!