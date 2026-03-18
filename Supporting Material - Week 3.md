

COMP1549: Advanced Programming
## 1
COMP1549: Advanced Programming
## Dr Taimoor Khan
## WWW
•Personal
•Operational Lead - NCSC accredited ACE-CSR
•Deputy Director - NCSC accredited ACE-CSE
•RITICS Fellow – Imperial College London
## 29
th
## January 2026 - Week 3
Network and Concurrent Programming

COMP1549: Advanced Programming
Network programming
## 2

COMP1549: Advanced Programming
## 3
Computer network
◉A computer network is an interconnected
collection of autonomous computers
Computer is a network now

COMP1549: Advanced Programming
◉How is it possible to send bits across
incompatible LANs, WANs and networks?
➢Solution: protocol software running on each host
and router smooths out the differences between
the different networks
➢Implements an internet protocol (i.e., set of
rules) that governs how hosts and routers should
cooperate when they transfer data from network
to network
➢TCP/IP is the protocol for the global IP Internet
## 4
## Internet

COMP1549: Advanced Programming
◉Most famous example of an internet.
◉Based on the TCP/IP protocol family
➢Level 3 - IP (Internet protocol) :
❖Provides basic naming scheme and unreliable delivery
capability of packets (datagrams) from host-to-host.
➢Level 4 - UDP (User Datagram Protocol)
❖Uses IP to provide unreliable datagram delivery from
process-to-process.
➢Level 4 - TCP (Transmission Control Protocol)
❖Uses IP to provide reliable byte streams from process-
to-process over connections.
◉Accessed via a mix of Java file I/O and
functions from the sockets interface.
## 5
IP Internet

COMP1549: Advanced Programming
◉Every network application is based on the
client-server model:
➢A server process and one or more client processes
➢Server manages some resource.
➢Server provides service by manipulating resource for clients.
## 6
Network programming
## Client
process
## Server
process
- Client sends request
## 2. Server
handles
request
- Server sends response
## 4. Client
handles
response
## Resource
Note: clients and servers are processes (aka programs) running on
hosts/machines (can be the same or different hosts).

COMP1549: Advanced Programming
◉How does a client find the server?
➢The IP address in the server socket address
identifies the host  (more precisely, an adapter
on the host)
➢The (well-known) port in the server socket
address identifies the service, and thus implicitly
identifies the server process that performs that
service.
➢Examples of well-known ports
❖Port 7: Echo server
❖Port 23: Telnet server
❖Port 25: Mail server
❖Port 80: Web server
## 7
## Client

COMP1549: Advanced Programming
◉Servers are long-running processes (daemons).
➢Created at boot-time (typically) by the init process
## (process 1)
➢Run continuously until the machine is turned off.
◉Each server waits for requests to arrive on a
well-known port associated with a particular
service.
➢Port 7: echo server
➢Port 23: telnet server
➢Port 25: mail server
➢Port 80: HTTP server
◉A machine that runs a server process is also
often  referred to as a “server.”
## 8
## Server

COMP1549: Advanced Programming
◉Hosts are mapped to a set of 32-bit IP addresses.
## ➢193.37.244.43
◉The set of IP addresses is mapped to a set of
identifiers called Internet domain names. [A host
name].
➢193.37.244.43 is mapped to  www.gre.ac.uk
➢How do hostnames get matched to IP addresses?
❖What is /etc/hosts?
❖What is a DNS?
◉A process on one Internet host can communicate with
a process on another Internet host over a connection.
➢What is special about addresses 127.0.0.*?
❖Each host has a locally defined domain name localhost which
always maps to the loopback address 127.0.0.1
## 9
Programmer’s view

COMP1549: Advanced Programming
◉Clients and servers communicate by sending streams of
bytes over connections:
➢Point-to-point, full-duplex (2-way communication), and
reliable.
◉A socket is an endpoint of a connection
➢Socket address is an IPaddress:port pair
◉A port is a 16-bit integer that identifies a process:
➢Ephemeral port: Assigned automatically to client when client
makes a connection request
➢Well-known port: Associated with some service provided by a
server (e.g., port 80 is associated with Web servers)
◉A connection is uniquely identified by the socket
addresses of its endpoints (socket pair)
## ➢clientaddr:clientport
## ➢serveraddress:serverport
## 10
Internet connections

COMP1549: Advanced Programming
◉Sockets as an abstraction provide a conduit through which
a process can send data out onto a network to another
process
➢Sockets can be used with both the TCP and the UDP
transport layer protocols
➢TCP and UDP sockets need IP addresses and port numbers
◉What is a socket?
➢To the kernel, a socket is an endpoint of communication.
➢To an application, a socket is a file descriptor that lets the
application read/write from/to the network
❖All I/O devices, including networks, are generally modeled as files
◉Clients and servers communicate with each other by
reading from, and writing to, socket descriptors
◉The main distinction between regular file I/O and socket
I/O is how the application “opens” the socket descriptors
## 11
## Sockets

COMP1549: Advanced Programming
Sockets can
1.Connect to a remote machine
2.Send data
3.Receive data
4.Close a connection
5.Bind to a port
6.Listen for incoming connection
7.Accept connections from remote machines
on a bound port
## 12
The Java Socket class (1)

COMP1549: Advanced Programming
The Socket class supports the
1.Connect to a remote machine
➢Socket socket = new Socket(...)
2.Send data [socket.write()]
3.Receive data [socket.read()]
4.Close a connection [socket.close()]
What is the kind of data that can be transmitted?
◉Transmission as a byte stream
Typically a socket is encapsulated in a
InputStream class or a Reader class
## 13
The Java Socket class (2)

COMP1549: Advanced Programming
The ServerSocket class additionally supports
the [ServerSocket server_socket = new ServerSocket(...)]
5.Bind to a port [server_socket.bind()]
6.Listen for incoming connection
## [server_socket.listen()]
7.Accept connections from remote machines
on a bound port [server_socket.accept()]
## 14
The Java ServerSocket class

COMP1549: Advanced Programming
◉General approach
➢Create and/or open a socket
➢Convert a socket to a standard Java I/O class
❖Input stream
❖Output stream
➢Use standard Java I/O for all operations
◉Works for "normal" TCP connection
## 15
Network programming approach

COMP1549: Advanced Programming
public class Server {
public static void main(String[] args) throws IOException {
try (ServerSocket listener = new ServerSocket(7000)) {
System.out.println("The date server is running...");
while (true) {
try (Socket socket = listener.accept()) {
PrintWriter out =
new PrintWriter(socket.getOutputStream(), true);
out.println(new Date().toString());
## }
## }
## }
## }
## }
## 16
Server – example

COMP1549: Advanced Programming
public class Client {
public static void main(String[] args) throws IOException {
if (args.length != 1) {
System.err.println("Pass the server IP ...");
return;
## }
Socket socket = new Socket(args[0], 7000);
Scanner in = new Scanner(socket.getInputStream());
System.out.println("Server response: " + in.nextLine());
## }
## }
## 17
Client - example

COMP1549: Advanced Programming
Currently we have learned
◉1 client and 1 server
➢Server accepting requests from one client
## Next
◉Many clients and 1 server
➢Server accepting requests from many clients
◉Many clients and many servers (peer to peer)
➢Everyone accepting requests from others
Concurrently listening to many requests through
Threads in Java
## 18
Client server models

COMP1549: Advanced Programming
Concurrent programming
## 19

COMP1549: Advanced Programming
◉To develop concurrent processes
➢Keep things moving when something blocks
➢Make use of multiple cores
## 20
## Threads

COMP1549: Advanced Programming
## 21
## Threads
Normally we write programs that
execute with a single thread of
control - one thing happens after
another
A multi-threaded program may execute
with multiple threads of control - more
than one thing can happen at once
(aka concurrently)
class Joe {
public void run() {
for (int i = 0;i < num; i++) {
// do something
## }
## }
## }
## .....
Joe j1 = new Joe();
j1.run();
for (z = 20; z > 0; z --) {
// do something
## }
class Fred extends Thread {
public void run() {
for (int i = 0;i < num; i++) {
// do something
## }
## }
## }
## .....
Fred f1 = new Fred();
f1.start();
for (z = 20; z > 0; z --) {
// do something
## }

COMP1549: Advanced Programming
◉Java Threads
➢operate within a single Java Virtual Machine.
➢can share memory (i.e., can have references to the same
objects)
◉Distributed Java Systems
➢spread over more than one JVM and probably physical
machine
➢communicate via sockets (perhaps hidden by
technologies such as web services)
## 22
Threads vs distributed system

COMP1549: Advanced Programming
◉A process is a JVM instance
➢Unit of allocation (resources, privileges, etc)
➢The Process contains the heap
➢The heap holds all static memory
◉A thread is a runtime (JVM) state
➢unit of execution (PC, SP, registers)
➢"Java Stack" (runtime stack)
➢Stored registers
➢Local variables
➢Instruction pointer
◉Each process has one or more threads
◉Each thread belong to one process
Threads vs process in Java
## Low Address
## Max Address
Process address space
## PC
## Code
## Static Data
## Constants
## Heap
(Dynamic Data)
## Stack
## Registers
## SP

COMP1549: Advanced Programming
◉Processes define an address
space, which is shared by
threads
◉Process Control Block (PCB)
contains process-specific
information
•Owner, PID, heap pointer,
priority, active thread, and
pointers to thread information
◉Thread Control Block (TCB)
contains thread-specific
information
•Stack pointer, PC, thread state
(running, ...), register values,
a pointer to PCB, ...
## 24
Threads in Java’s memory model
Process address space
## Code
## Static Data
## Constants
## Heap
(Dynamic Data)
Stack (T2)
Registers (T2)
## PC
## SP
## State
## Registers
## ...
TCB for
## Thread2
Stack (T1)
Registers (T1)
## PC
## SP
## State
## Registers
## ...
TCB for
## Thread1

COMP1549: Advanced Programming
◉Every thread is created as an instance of class
java.lang.Thread
◉You either extend Thread class
public class MyThread1 extends Thread {}
◉or implement Runnable interface
public class MyThread2 implements Runnable {}
◉In either case you must provide a method called
run() which is executed when the thread runs
e.g.
public void run() {
for (int i=0; i < 5; i++)
System.out.println("I'm a thread  " + i);
## }
## 25
Threads in Java

COMP1549: Advanced Programming
## 26
Thread states
new
runnable
blocked
new
dead
running
t = new Thread();
Active thread
Blocked for I/O
sleep()
wait()
suspend()
time is up
notifyAll()
resume()
notify()
scheduled
waiting/sleeping

COMP1549: Advanced Programming
## 27
Creating threads
class MyThread1 extends Thread {
public void run() {
for (int i=0; i < 5; i++)
System.out.println("I'm a thread " + i);
## }
## }
public class CreateThread1 {
public static void main (String[] args) {
Thread t = new MyThread1();
t.start();
for (int i=0; i < 5; i++)
System.out.println("I'm main " + i);
## }
## }
what will
this output?
what would happen
if t.start() was
replaced by t.run()
## ?

COMP1549: Advanced Programming
## 28
Joining threads
class MyThread3 extends Thread {
public void run() {
for (int i=0; i < 5; i++)
System.out.println("I'm a thread " + i);
## }}
what can you
predict about
what this will
output?
As well as diverging Threads can be joined using join()
public class JoiningThreads {
public static void main (String[] args) {
Thread t = new MyThread3();
System.out.println("Threads diverging");
t.start();
for (int i=0; i < 5; i++)
System.out.println("I'm main " + i);
try {
t.join();
} catch (InterruptedException ie) {ie.printStackTrace(); }
System.out.println("Threads re-joined");
## }}
wait for thread
t to finish

COMP1549: Advanced Programming
◉New threads always start as instances of
separate objects
◉Once started different threads of control can
pass through shared objects
◉Shared Object’s code is executed by two
different Threads
## 29
Threads sharing objects
Thread instance 1
run() {
## }
Thread instance 2
run() {
## }
## Shared Object

COMP1549: Advanced Programming
## 30
Shared objects Java memory model
## Thread Stack
methodOne()
methodTwo()
## Local Variable 1
## Local Variable 2
## Local Variable 1
methodOne()
methodTwo()
## Local Variable 1
## Local Variable 2
## Local Variable 1
Object 1Object 2Object 3Object 4Object 5
## Heap
## Thread Stack
## JVM
www.javaworlld.com

COMP1549: Advanced Programming
class Shared2 {
private int counter = 0;
public void quickNap() {
try {Thread.sleep((int)(Math.random() * 2)); }
catch(InterruptedException ie) {}
## }
public int increment() {
counter = 0;
for (int i = 0; i < 1000; i++) {
counter++;
quickNap(); }
return counter;
## }
public int decrement() {
counter = 0;
for (int i = 0; i < 1000; i++) {
counter--;
quickNap();   }
return counter;
## }}
## 31
Example – shared objects (1)
sleep for a random
amount of time - will
make the threading
more obvious
taking this code on
face value what do
you think
increment() and
decrement() will
return?

COMP1549: Advanced Programming
class CountDown2 extends Thread {
private Shared2 myShared;
private JTextArea display;
public CountDown2(Shared2 s, JTextArea display) {
myShared = s;
this.display = display;
## }
public void run() {
int tempVal = myShared.decrement();
display.append("Down reports:" + tempVal + "\n");
## }
## }
## 32
Example – shared objects (2)
instances of CountDown2
call the decrement()
method of the shared
object
a reference to the shared
object is passed to the
constructor and stored

COMP1549: Advanced Programming
class CountUp2 extends Thread {
private Shared2 myShared;
private JTextArea display;
public CountUp2(Shared2 s, JTextArea display) {
myShared = s;
this.display = display;
## }
public void run() {
int tempVal = myShared.increment();
display.append("Up reports:" + tempVal + "\n");
## }
## }
## 33
Example – shared objects (3)
CountUp2 is very similar to
the CountDown2 except it
calls the increment() method
on the shared object

COMP1549: Advanced Programming
public class ThreadApp2 extends JFrame implements ActionListener {
private JButton one = new JButton("Go");
private JTextArea display = new JTextArea(10,20);
private Shared2 s = new Shared2();
private CountDown2 down;
private CountUp2 up;
public ThreadApp2() {
setLayout(new FlowLayout());
add(one);
one.addActionListener(this);
add(new JScrollPane(display));
## ........
## }
public void actionPerformed(ActionEvent event) {
down = new CountDown2(s, display);
up = new CountUp2(s, display);
down.start();
up.start();
## }
public static void main(String[] args) {
ThreadApp2 me = new ThreadApp2();
me.setVisible(true);
## }}
## 34
Example – shared objects (4)
ThreadApp2
•provides a GUI
•creates an instance
of the shared objects
•creates and starts the
## Threads

COMP1549: Advanced Programming
◉Why aren’t the results always 1000?
◉How come the threads can be run again after
they finish?
## 35
Example run (1)

COMP1549: Advanced Programming
## 36
Example run (2)
Why aren’t the results always 1000?
•Because both threads share the same
object (Shared2)
•Counter is therefore a shared variable
and continuously changing
How come the threads can be run again after they finish?
•There’re not!
•New threads are created each time Go
is pressed
•But all are sharing the same Shared2
object

COMP1549: Advanced Programming
## 37
Visualizing shared objects
ThreadApp2
actionPerformed() {
CountDown2
run() {
## }
CountUp2
run() {
## }
## Shared2
int counter
increment() {
## }
decrement() {
## }

COMP1549: Advanced Programming
◉The previous program shows an example of a
race condition where the interleaving of actions
from different threads cause corruption or
unpredictable results
◉The problem is caused because Shared2 isn’t a
Thread-safe class
➢Being Thread-safe means that even if an instance of
the class is shared by multiple threads no race
condition can occur
➢If you are writing code for classes that may have
their instances shared amongst threads then you
must consider the issue of thread-safety
## 38
Race condition and thread safety

COMP1549: Advanced Programming
◉Java allows objects to be locked to prevent problems
when they are accessed by multiple threads
◉Object locking is controlled by use of the keyword
synchronized
◉The most common use of synchronized is to apply it
to a method e.g.
public synchronized int increment() {
counter = 0;
## .....
## }
## 39
Thread safety

COMP1549: Advanced Programming
## 40
Semantics of synchronized
◉If a thread has the object lock then
any other threads wanting to execute
synchronized methods will enter a
“seeking a lock state”
:Shared2
int counter
synchronized increment() {
## }
synchronized decrement() {
## }
## T
h
r
e
a
d
## 1
I’ve got
the lock
hurry up
while Thread1 holds
the lock on the
instance of Shared2
any other threads
wanting to execute its
synchronized
methods must wait
◉For a thread to execute a synchronized method it
must obtain a lock on the object (the actual instance)
to which the method belongs

COMP1549: Advanced Programming
◉The lock is on the object on which the synchronized
method is called.
◉If there are multiple instances of a class containing
synchronized methods, then different threads can
be executing synchronized methods on different
instances
◉If one thread is executing a synchronized method
there’s nothing to stop other threads executing non-
synchronized method of the same object
◉A thread releases the lock on an object when it exits
from the synchronized code
◉It keeps the lock while it is sleeping
## 41
Threads and locks

COMP1549: Advanced Programming
◉Should all methods be synchronized?
➢It would certainly ensure thread-safety but at a
cost of performance.
➢Synchronized can be applied to a block of code
as well as a whole method
➢Keep the synchronized code a minimum
➢Document whether or not your code is thread-
safe
➢Providing both a thread-safe and non-thread-
safe version of a class may be sensible
## 42
When to use synchronized?

COMP1549: Advanced Programming
◉Java >= 5 introduced changes to the Java
API to enhance support for threading
◉These have been enhanced since and are
becoming more widely used
◉New classes are in package
java.util.concurrent
◉Class ReentrantLock supports explicit locking
as an alternative to using synchronized
blocks
## 43
A new alternative

COMP1549: Advanced Programming
## 44
Example - reentrant
class Shared3 {
private int counter = 0;
private final Lock theLock =
new ReentrantLock();
public int increment() {
int saveCounter = 0;
theLock.lock();
try {
counter = 0;
for (int i = 0; i < 1000; i++) {
counter++;
quickNap();
## }
saveCounter = counter;
} finally {
theLock.unlock();
return saveCounter;
## }
## }
// similar changes to decrement
## }
class Shared3 {
private int counter = 0;
public synchronized int increment() {
counter = 0;
for (int i = 0; i < 1000; i++) {
counter++;
quickNap();
## }
return counter;
## }
// decrement
## }
older way - using synchronized
new way

COMP1549: Advanced Programming
◉Any systems where concurrent threads or
processes can lock shared resources are prone to
deadlock
◉An obvious example is deadly embrace between
two threads
## 45
## Deadlock
## T
h
r
e
a
d
## 1
o
b
j
e
c
t

## 1
o
b
j
e
c
t

## 2
## I
## ’
v
e

g
o
t

o
b
j
e
c
t

## 1

a
n
d

n
o
w

## I

w
a
n
t

o
b
j
e
c
t

## 2
## T
h
r
e
a
d
## 2
## I
## ’
v
e

g
o
t

o
b
j
e
c
t

## 2

a
n
d

n
o
w

## I

w
a
n
t

o
b
j
e
c
t

## 1
Error prone as developer may forget to lock/unlock

COMP1549: Advanced Programming
◉Keep locking to a minimum
◉Don’t hold locks for longer than necessary
◉Try to make sure resources are always
locked in the same order
◉Use tryLock() with a timeout and if necessary
release your locks and try again
◉Test very thoroughly!!
## 46
Avoid deadlock

COMP1549: Advanced Programming
## 47
End of week 3!