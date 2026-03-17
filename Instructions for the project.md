**TASK 1 --- Group Communication System**

This task builds a **network communication system**.

Example idea:

A **group chat system** where multiple clients communicate through a server.

**System Architecture**

Client 1  
Client 2 \-\-\-\--\> Server  
Client 3  
Client 4

One client becomes **Coordinator (Leader)**.

**When Program Starts**

User provides:

Client ID  
Server IP (optional)  
Server Port (optional)

Example:

java Client user1 127.0.0.1 5000

**4. Main Features You Must Implement**

**1️⃣ Group Formation**

When clients connect:

- first client → becomes **Coordinator**

- other clients join group

Example:

Client1 connected  
Client1 is Coordinator  
  
Client2 joined group  
Client3 joined group

**2️⃣ Group State Maintenance**

Coordinator must track:

Active Clients  
Client IDs  
IP Address  
Port  
Messages

Example structure:

ArrayList\<Client\>

**3️⃣ Ping System (every 20 seconds)**

Coordinator checks if members are alive.

Example:

Ping Client2  
Ping Client3

If someone disconnects → remove them.

**4️⃣ Messaging**

Two types:

**Private Message**

Client1 → Client2

Example:

/msg client2 Hello

**Broadcast Message**

Client1 → Everyone

Example:

/all Hello everyone

**5️⃣ Leaving the Group**

Clients can quit.

Example:

Client2 left group

System should still run.

**6️⃣ Coordinator Failure**

If coordinator leaves:

System automatically chooses **new coordinator**.

Example:

Coordinator left  
Client3 becomes new coordinator

This is called:

**Fault Tolerance**

**5. Programming Concepts You Must Use**

Now I will explain **the concepts you asked about** and how they apply.

**Array / ArrayList**

Used to store clients and messages.

Example:

ArrayList\<Client\> clients = new ArrayList\<\>();

Stores:

Client1  
Client2  
Client3

**Conditional Statements**

Used to check conditions.

Example:

if(client.isCoordinator()){  
System.out.println(\"You are the coordinator\");  
}

Another example:

if(clientDisconnected){  
chooseNewCoordinator();  
}

**Functions (Methods)**

Used to organize program logic.

Example:

public void sendMessage(String msg){  
System.out.println(msg);  
}

Another:

public void chooseNewCoordinator(){  
}

**Inheritance**

Used to create specialized classes.

Example:

User  
↑  
Client

Example code:

class User{  
String id;  
}  
  
class Client extends User{  
String ip;  
}

**Encapsulation**

Encapsulation means **protecting data using private variables**.

Example:

Client ID should not be modified directly

Code:

class Client{  
  
private String id;  
  
public String getId(){  
return id;  
}  
  
public void setId(String id){  
this.id = id;  
}  
}

**Association**

Association means **one class uses another class**.

Example:

Server \-\-\-- manages \-\-\-- Clients

Code:

class Server{  
  
ArrayList\<Client\> clients;  
  
}

Server is associated with Clients.

**6. Design Patterns Requirement**

You must use **Design Patterns**.

Example patterns:

**Singleton**

Only one server object exists.

Server.getInstance()

**Observer Pattern**

Clients get notified when new message arrives.

**7. JUnit Testing**

You must write **unit tests**.

Example:

Test sending message  
Test client connection  
Test coordinator change  
Test private message

Example:

@Test  
public void testSendMessage(){  
assertEquals(\"Hello\", message);  
}

**8. Fault Tolerance**

System must survive failures.

Example cases:

| **Scenario**       | **Expected Behaviour**   |
|--------------------|--------------------------|
| Client leaves      | system still works       |
| Coordinator leaves | new coordinator selected |
| Network error      | program continues        |

**9. Demonstration Requirements**

During demo you must show:

**Scenario**

1️⃣ Run server  
2️⃣ Run **3 clients**

Example:

Client1  
Client2  
Client3

**Show**

1.  Coordinator selection

2.  Send private message

3.  Send broadcast message

4.  One client leaves

5.  Communication continues

6.  Coordinator leaves

7.  New coordinator selected

**10. TASK 2 (Alternative Project)**

This project builds a **Security Access System**.

Example real-world idea:

University Resource System

**Users**

User1  
User2  
User3

Each has role.

**Roles**

GUEST  
STUDENT  
STAFF  
ADMIN

**Resources**

Example:

Printer  
Exam Paper  
Lecture Material

**Access Scopes**

| **Scope**    | **Meaning**     |
|--------------|-----------------|
| PUBLIC       | anyone can read |
| INTERNAL     | students/staff  |
| CONFIDENTIAL | restricted      |

**Example Rules**

STUDENT → read Lecture Material  
STUDENT → cannot access Exam Paper  
  
ADMIN → read/write everything

**Log System**

Every access must be logged.

Example:

20-01-2025 16:00  
user2 ADMIN  
ExamPaper  
WRITE  
ALLOW

**11. Generics Requirement (Task 2)**

Example:

Capability\<Read\>  
Capability\<Write\>

Example code:

class Capability\<T\>{  
  
T permission;  
  
}

**12. Report Requirements**

Maximum:

**3 pages**

Font:

Times New Roman  
Size 11

Tables must include:

**Design Patterns Table**

\| Pattern \| Classes \| Reason \|

**Testing Table**

\| Test \| Classes \| Description \|

**Fault Tolerance Table**

\| Feature \| Classes \| Description \|

**AI Usage Table**

\| AI Tool \| Classes \| Contribution \|

**13. Very Simple Summary**

Faiza, here is the **super short version**.

Your coursework requires:

**Step 1**

Choose **ONE project**

Task 1 → Chat System  
or  
Task 2 → Security System

**Step 2**

Build Java application using:

- Arrays / ArrayList

- Conditional statements

- Functions

- Encapsulation

- Inheritance

- Association

- Design Patterns

- JUnit Testing

**Step 3**

Submit ZIP file containing:

Java Code  
3 Page Report  
Contribution File

**Step 4**

Give **Live Demo**

**14. What I Recommend (Very Important)**

Faiza, **Task 1 is easier**.

Why?

Because it only needs:

Socket programming  
Client Server  
Basic OOP

Task 2 requires:

Generics  
Security Design  
Complex architecture

\#### ANSWER OF TASK1#######

**1️⃣ Full Java Class Structure (Recommended Design)**

For the **Client-Server Group Communication System**, the project should be divided into logical classes.

**Core Classes**

MainServer  
ClientHandler  
Client  
CoordinatorManager  
Message  
MessageService  
GroupState  
PingService  
Logger

**Testing Classes**

MessageServiceTest  
CoordinatorManagerTest  
GroupStateTest

**Explanation of Each Class**

**1. MainServer**

This class starts the server and waits for client connections.

Responsibilities:

- open server socket

- accept client connections

- create ClientHandler threads

Example structure:

class MainServer {  
  
private ServerSocket serverSocket;  
private List\<ClientHandler\> clients;  
  
public void startServer(int port){  
}  
  
public void broadcast(Message msg){  
}  
  
}

**2. Clienthandler**

Each client connection runs in a separate thread.

Responsibilities:

- receive client messages

- send messages

- disconnect client

class ClientHandler extends Thread {  
  
private Socket socket;  
private String clientId;  
  
public void run(){  
}  
  
public void sendMessage(String msg){  
}  
  
}

**3. Client**

Represents a client user.

class Client {  
  
private String id;  
private String ip;  
private int port;  
  
}

Encapsulation example:

public String getId()  
public void setId(String id)

**4. CoordinatorManager**

Handles **coordinator selection logic**.

Responsibilities:

- choose coordinator

- update coordinator when one leaves

class CoordinatorManager {  
  
private Client coordinator;  
  
public Client selectCoordinator(List\<Client\> clients){  
}  
  
}

**5. Message**

Represents a message object.

class Message {  
  
private String senderId;  
private String receiverId;  
private String content;  
private String timestamp;  
  
}

Types:

PRIVATE  
BROADCAST  
SYSTEM

**6. MessageService**

Handles message sending.

Responsibilities:

- private message

- broadcast message

class MessageService {  
  
public void sendPrivate(Message msg){  
}  
  
public void broadcast(Message msg){  
}  
  
}

**7. GroupState**

Stores group information.

class GroupState {  
  
private List\<Client\> activeClients;  
  
public void addClient(Client c)  
public void removeClient(Client c)  
  
}

**8. PingService**

Used by coordinator to check alive members.

Runs every **20 seconds**.

class PingService implements Runnable {  
  
public void run(){  
while(true){  
pingClients();  
}  
}  
  
}

**9. Logger**

Stores logs.

Example log:

10:22 Client1 sent message to Client2

class Logger {  
  
public static void log(String msg){  
}  
  
}

**2️⃣ Complete Project Architecture**

Professional architecture structure:

GroupChatSystem  
│  
├── server  
│ ├── MainServer.java  
│ ├── ClientHandler.java  
│ └── CoordinatorManager.java  
│  
├── client  
│ ├── ClientApp.java  
│ └── Client.java  
│  
├── messaging  
│ ├── Message.java  
│ └── MessageService.java  
│  
├── group  
│ ├── GroupState.java  
│ └── PingService.java  
│  
├── utils  
│ └── Logger.java  
│  
└── tests  
├── MessageServiceTest.java  
├── CoordinatorManagerTest.java  
└── GroupStateTest.java

This structure demonstrates **clean software architecture**, which professors appreciate.

**3️⃣ Ready Class Diagram**

A simplified UML diagram:

+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+  
\| MainServer \|  
+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+  
\|  
\|  
manages clients  
\|  
+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+  
\| ClientHandler \|  
+\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\--+  
\|  
\|  
uses  
\|  
+\-\-\-\-\-\-\-\-\-\--+  
\| Client \|  
+\-\-\-\-\-\-\-\-\-\--+  
  
MainServer \-\--\> CoordinatorManager  
  
ClientHandler \-\--\> MessageService  
  
MessageService \-\--\> Message  
  
MainServer \-\--\> GroupState  
  
CoordinatorManager \-\--\> Client  
  
PingService \-\--\> GroupState

Concept relationships:

| **Concept**   | **Where Used**               |
|---------------|------------------------------|
| Encapsulation | Client, Message              |
| Association   | Server → Clients             |
| Inheritance   | ClientHandler extends Thread |
| Composition   | GroupState contains Clients  |

**4️⃣ Step-by-Step Implementation Plan**

Faiza, follow this **exact order**.

**Step 1 --- Create Project**

Create Java project:

GroupChatSystem

**Step 2 --- Implement Client Model**

Create:

Client.java

class Client {  
  
private String id;  
private String ip;  
private int port;  
  
}

Concept used:

✔ Encapsulation

**Step 3 --- Implement Message Class**

class Message {  
  
private String sender;  
private String receiver;  
private String content;  
  
}

Concept used:

✔ Object modelling

**Step 4 --- Implement Server**

Create:

MainServer.java

Responsibilities:

- start server

- accept clients

Example:

ServerSocket serverSocket = new ServerSocket(5000);

**Step 5 --- Implement ClientHandler**

Handles communication.

class ClientHandler extends Thread {  
  
public void run(){  
  
}  
  
}

Concept used:

✔ Multithreading

**Step 6 --- Implement Messaging**

Create:

MessageService.java

Features:

- broadcast

- private message

**Step 7 --- Implement Coordinator Logic**

Create:

CoordinatorManager.java

Logic:

if first client  
→ coordinator

If coordinator leaves:

choose next client

**Step 8 --- Implement Group State**

Track active clients.

ArrayList\<Client\>

**Step 9 --- Implement Ping System**

Every 20 seconds:

ping clients  
remove dead clients

**Step 10 --- Implement Logging**

Store messages:

timestamp  
sender  
receiver  
content

**Step 11 --- Write JUnit Tests**

Example tests:

testBroadcastMessage()  
testPrivateMessage()  
testCoordinatorSelection()  
testClientDisconnect()

**5️⃣ Exact Java Code Structure Professors Expect**

This structure scores high because it shows **OOP design**.

**Example Server Code Skeleton**

public class MainServer {  
  
private List\<ClientHandler\> clients = new ArrayList\<\>();  
  
public void startServer(int port){  
  
try{  
  
ServerSocket serverSocket = new ServerSocket(port);  
  
while(true){  
  
Socket socket = serverSocket.accept();  
  
ClientHandler handler =  
new ClientHandler(socket,this);  
  
clients.add(handler);  
  
handler.start();  
  
}  
  
}catch(Exception e){  
e.printStackTrace();  
}  
  
}  
  
}

**Example ClientHandler**

public class ClientHandler extends Thread{  
  
private Socket socket;  
private BufferedReader reader;  
private PrintWriter writer;  
  
public void run(){  
  
try{  
  
String message;  
  
while((message = reader.readLine()) != null){  
  
System.out.println(message);  
  
}  
  
}catch(Exception e){  
e.printStackTrace();  
}  
  
}  
  
}

**Key Concepts Your Professor Will Look For**

| **Concept**    | **Example**          |
|----------------|----------------------|
| ArrayList      | store clients        |
| Conditional    | coordinator logic    |
| Functions      | sendMessage()        |
| Encapsulation  | private variables    |
| Inheritance    | Thread class         |
| Association    | Server ↔ Clients     |
| Design Pattern | Singleton / Observer |
| Testing        | JUnit                |

**Final Summary (Very Important)**

Faiza, your project should include:

**Classes**

MainServer  
ClientHandler  
Client  
Message  
MessageService  
CoordinatorManager  
GroupState  
PingService  
Logger

**Concepts**

ArrayList  
Conditional statements  
Functions  
Encapsulation  
Inheritance  
Association  
Design Patterns  
JUnit Testing  
Fault Tolerance

**Demo Requirements**

Run:

Server  
3 Clients

Show:

Coordinator selection  
Private message  
Broadcast message  
Client leaving  
Coordinator leaving  
New coordinator
