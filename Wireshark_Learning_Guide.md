# Wireshark Learning Guide

## Table of Contents
1. [Introduction to Wireshark](#introduction-to-wireshark)
2. [Installation](#installation)
3. [Basic Concepts](#basic-concepts)
4. [Getting Started](#getting-started)
5. [Understanding the Interface](#understanding-the-interface)
6. [Capture Filters](#capture-filters)
7. [Display Filters](#display-filters)
8. [Analyzing Common Protocols](#analyzing-common-protocols)
9. [Practical Exercises](#practical-exercises)
10. [Tips and Best Practices](#tips-and-best-practices)
11. [Troubleshooting Network Applications](#troubleshooting-network-applications)

---

## Introduction to Wireshark

**Wireshark** is the world's most popular network protocol analyzer. It lets you capture and interactively browse the traffic running on a computer network.

### What is Wireshark Used For?

- **Network troubleshooting** - Diagnose network problems
- **Security analysis** - Examine security problems
- **Protocol development** - Debug protocol implementations
- **Learning** - Understand network protocols and how they work
- **Application debugging** - Debug client-server applications (like your COMP1549 coursework!)

### Why Learn Wireshark?

For your coursework (Task 1 - Group Communication System), Wireshark will help you:
- Debug your client-server communication
- Verify messages are being sent/received correctly
- Analyze network latency and performance
- Understand TCP/UDP protocols in practice
- Troubleshoot connection issues

---

## Installation

### Windows
1. Download from: https://www.wireshark.org/download.html
2. Run the installer (includes WinPcap/Npcap for packet capture)
3. Accept default settings
4. Restart if required

### Linux (Ubuntu/Debian)
```bash
sudo apt update
sudo apt install wireshark
sudo usermod -aG wireshark $USER
# Log out and log back in for group changes to take effect
```

### macOS
```bash
# Using Homebrew
brew install --cask wireshark
```

### Verify Installation
Launch Wireshark and you should see the main interface with network interfaces listed.

---

## Basic Concepts

### What is a Packet?
A **packet** is a unit of data transmitted over a network. When you send data (like a message in your chat application), it's broken into packets.

### Network Layers (Simplified)
```
Application Layer    (HTTP, FTP, your Java app)
    ↓
Transport Layer      (TCP, UDP)
    ↓
Network Layer        (IP)
    ↓
Link Layer           (Ethernet, WiFi)
```

### Key Terms

- **Capture**: Recording network traffic
- **Filter**: Displaying only specific packets
- **Protocol**: Rules for communication (TCP, UDP, HTTP, etc.)
- **Port**: A number identifying a specific service (your server might use port 5000)
- **Source/Destination**: Where packets come from and go to

---

## Getting Started

### First Capture

1. **Launch Wireshark**
2. **Select Network Interface**
   - For local testing: Select "Loopback: lo" or "lo0" (for localhost/127.0.0.1)
   - For network testing: Select your active network adapter (WiFi or Ethernet)
3. **Start Capture**
   - Double-click the interface OR
   - Click the blue shark fin icon
4. **Generate Traffic**
   - Open a web browser and visit a website
   - Or run your Java client-server application
5. **Stop Capture**
   - Click the red square icon
   - File → Save to save the capture

### Understanding What You See

Each row in Wireshark represents one packet with columns:

| Column | Description |
|--------|-------------|
| No. | Packet number in capture |
| Time | Time since capture started |
| Source | Source IP address |
| Destination | Destination IP address |
| Protocol | Protocol used (TCP, UDP, HTTP, etc.) |
| Length | Packet size in bytes |
| Info | Summary of packet contents |

---

## Understanding the Interface

Wireshark has three main panes:

```
┌─────────────────────────────────────────────┐
│  [Packet List Pane]                         │
│  Lists all captured packets                 │
├─────────────────────────────────────────────┤
│  [Packet Details Pane]                      │
│  Shows protocol layers of selected packet   │
├─────────────────────────────────────────────┤
│  [Packet Bytes Pane]                        │
│  Shows raw data in hexadecimal and ASCII    │
└─────────────────────────────────────────────┘
```

### Packet Details Pane

When you click a packet, you'll see its layers:
```
▼ Frame (physical layer info)
▼ Ethernet II (link layer)
▼ Internet Protocol Version 4 (network layer)
▼ Transmission Control Protocol (transport layer)
▼ Application Data (your actual message!)
```

Click the arrows to expand/collapse each layer.

---

## Capture Filters

**Capture filters** determine which packets are captured. Applied BEFORE capturing starts.

### Syntax
```
Protocol Direction Host(s) Value Logic Operations
```

### Common Capture Filters

```plaintext
# Capture only traffic on port 5000 (your server port)
port 5000

# Capture only TCP traffic
tcp

# Capture traffic to/from specific host
host 192.168.1.100

# Capture traffic between two hosts
host 192.168.1.100 and host 192.168.1.101

# Capture only localhost traffic (for testing your app locally)
host 127.0.0.1

# Capture traffic on localhost port 5000
host 127.0.0.1 and port 5000

# Capture UDP traffic
udp

# Capture everything EXCEPT SSH (to reduce noise)
not port 22
```

### How to Apply Capture Filter

1. Before starting capture, type filter in capture filter box
2. Press Enter or click Start

---

## Display Filters

**Display filters** determine which packets are displayed. Applied AFTER capture.

### Syntax Examples

```plaintext
# Show only TCP packets
tcp

# Show only packets to/from specific IP
ip.addr == 192.168.1.100

# Show only packets from specific source
ip.src == 192.168.1.100

# Show only packets to specific destination
ip.dst == 192.168.1.100

# Show only specific port
tcp.port == 5000

# Show only source port
tcp.srcport == 5000

# Show only destination port
tcp.dstport == 5000

# Show only HTTP
http

# Show TCP connections (SYN packets)
tcp.flags.syn == 1

# Show TCP connection terminations (FIN packets)
tcp.flags.fin == 1

# Show packets containing specific text
tcp contains "Hello"

# Combine filters with logical operators
tcp.port == 5000 and ip.addr == 127.0.0.1

# OR condition
tcp.port == 5000 or tcp.port == 8080

# NOT condition
not arp

# Show only data packets (no connection setup)
tcp.len > 0
```

### How to Apply Display Filter

1. Type filter in display filter box (top of window)
2. Press Enter
3. Green = valid filter, Red = invalid filter

---

## Analyzing Common Protocols

### TCP (Transmission Control Protocol)

**Connection-Oriented**: Establishes connection before sending data

**Three-Way Handshake** (Connection Setup):
```
Client                          Server
   |  1. SYN                        |
   |----------------------------->  |
   |                                |
   |  2. SYN-ACK                    |
   |  <-----------------------------|
   |                                |
   |  3. ACK                        |
   |----------------------------->  |
   |                                |
   |  [DATA TRANSFER]               |
```

To see this in Wireshark:
1. Filter: `tcp.flags.syn == 1`
2. Look for packets with SYN flag set
3. Follow the sequence numbers

**Important TCP Fields:**
- **Source Port**: Sending application's port
- **Destination Port**: Receiving application's port
- **Sequence Number**: Order of data bytes
- **Acknowledgment Number**: Next expected byte
- **Flags**: SYN, ACK, FIN, RST, PSH
- **Window Size**: Flow control

### UDP (User Datagram Protocol)

**Connectionless**: No handshake, just sends data

Simpler than TCP, used when speed > reliability

```
Client                          Server
   |  UDP Packet                    |
   |----------------------------->  |
   |  (no acknowledgment)           |
```

**Important UDP Fields:**
- **Source Port**
- **Destination Port**
- **Length**
- **Checksum**

### HTTP (HyperText Transfer Protocol)

Built on top of TCP

**Request Example:**
```
GET / HTTP/1.1
Host: www.example.com
```

**Response Example:**
```
HTTP/1.1 200 OK
Content-Type: text/html
```

Filter: `http`

---

## Practical Exercises

### Exercise 1: Capture Localhost Traffic

**Goal**: Capture traffic from your Java client-server application

**Steps:**
1. Start Wireshark
2. Select "Loopback: lo" interface
3. Apply capture filter: `port 5000` (or your server port)
4. Start capture
5. Run your Java server on port 5000
6. Run your Java client connecting to localhost:5000
7. Send a message from client to server
8. Stop capture
9. Analyze the packets

**What to Look For:**
- TCP handshake (SYN, SYN-ACK, ACK)
- Your message data in the packet bytes
- Connection termination (FIN packets)

### Exercise 2: Follow a TCP Stream

**Goal**: See complete conversation between client and server

**Steps:**
1. Capture some traffic (as in Exercise 1)
2. Right-click on any packet from your application
3. Select "Follow" → "TCP Stream"
4. A new window shows the complete conversation
5. Red = client to server, Blue = server to client

**What You'll See:**
- All messages sent between client and server
- In order, reassembled
- Easy to read format

### Exercise 3: Analyze Message Timing

**Goal**: Measure network latency

**Steps:**
1. Capture traffic
2. Find the packet where client sends "PING"
3. Note the time (Time column)
4. Find the packet where server responds "PONG"
5. Calculate difference = latency

**Display Filter:**
```
tcp.port == 5000 and tcp.len > 0
```

### Exercise 4: Identify Connection Issues

**Goal**: Debug failed connections

**Common Problems to Look For:**

**Problem 1: Connection Refused**
- Look for RST (reset) packets immediately after SYN
- Filter: `tcp.flags.reset == 1`
- Means: Server not listening on that port

**Problem 2: Connection Timeout**
- Multiple SYN packets with no response
- Filter: `tcp.flags.syn == 1 and !tcp.flags.ack`
- Means: Server not reachable or firewall blocking

**Problem 3: Data Retransmission**
- Same data sent multiple times
- Look in Info column for "[TCP Retransmission]"
- Means: Packet loss or network issues

### Exercise 5: Verify Broadcast Messages

**Goal**: Confirm server broadcasts to all clients

**Steps:**
1. Run server and 3 clients
2. Capture on loopback
3. Send broadcast message from one client
4. Filter: `tcp.port == 5000`
5. Verify you see:
   - One packet from client to server (the broadcast request)
   - Three packets from server to each client (the broadcast distribution)

---

## Tips and Best Practices

### 1. Use Specific Filters

Don't capture everything - too much data! Use filters to focus on what matters.

**Good:**
```
host 127.0.0.1 and port 5000
```

**Bad:**
```
(no filter - captures everything)
```

### 2. Save Captures

File → Save As → .pcapng format

Why: Can analyze later, share with others, compare before/after

### 3. Use Coloring Rules

View → Coloring Rules

Wireshark colors packets based on type:
- **Light purple**: TCP traffic
- **Light blue**: UDP traffic
- **Black**: Errors
- **Light green**: HTTP

### 4. Statistics

Statistics menu provides useful info:
- **Conversations**: Who talked to whom
- **Protocol Hierarchy**: What protocols were used
- **I/O Graphs**: Traffic over time

### 5. Export Objects

If HTTP traffic, you can export files:
File → Export Objects → HTTP

### 6. Time Display Format

View → Time Display Format
- Choose "Seconds Since Previous Displayed Packet" to see timing

### 7. Search Packets

Edit → Find Packet (Ctrl+F)
- Search by display filter
- Search by string
- Search by hex value

---

## Troubleshooting Network Applications

### Debugging Your COMP1549 Client-Server Application

#### Scenario 1: Client Can't Connect to Server

**Symptoms:** Connection timeout or refused

**Wireshark Investigation:**
1. Start capture on loopback
2. Try to connect client
3. Look for SYN packet from client
4. Check what happens next:
   - **RST from server** → Server not listening, check server is running
   - **No response** → Firewall blocking, wrong IP/port
   - **SYN-ACK** → Connection successful, problem elsewhere

**Display Filter:**
```
tcp.flags.syn == 1 or tcp.flags.reset == 1
```

#### Scenario 2: Messages Not Being Received

**Symptoms:** Send message but other side doesn't get it

**Wireshark Investigation:**
1. Filter for your port: `tcp.port == 5000`
2. Send test message: "TEST123"
3. Find packet with your message:
   - Right-click → Follow TCP Stream
   - Search for "TEST123"
4. Check if message was sent
5. Check if server acknowledged (ACK)

**Display Filter:**
```
tcp.port == 5000 and tcp.len > 0
```

#### Scenario 3: Coordinator Ping Not Working

**Symptoms:** Ping messages not arriving

**Wireshark Investigation:**
1. Capture during ping interval (every 20 seconds)
2. Filter: `tcp.port == 5000`
3. Look for packets at 20-second intervals
4. Verify PING messages in TCP stream
5. Verify PONG responses

**Check for:**
- Are packets being sent?
- Are they arriving at destination?
- Are responses coming back?

#### Scenario 4: Private Message Goes to Wrong Client

**Symptoms:** Message intended for Client2 goes to Client3

**Wireshark Investigation:**
1. Send private message: "PRIVATE:client2:Hello"
2. Filter: `tcp.port == 5000`
3. Follow TCP streams for each connection
4. Verify which client receives the message
5. Check if server is routing correctly

#### Scenario 5: High Latency

**Symptoms:** Messages are slow

**Wireshark Investigation:**
1. Capture traffic
2. Send message, note time
3. Find response, note time
4. Calculate delta time
5. Statistics → I/O Graph to see traffic patterns

**Display Filter:**
```
tcp.port == 5000 and tcp.analysis.ack_rtt
```

---

## Common Wireshark Shortcuts

| Shortcut | Action |
|----------|--------|
| Ctrl+E | Start/Stop capture |
| Ctrl+K | Capture options |
| Ctrl+F | Find packet |
| Ctrl+N | Next packet in search |
| Ctrl+G | Go to packet number |
| Ctrl+↑/↓ | Previous/Next packet |
| Ctrl+→/← | Next/Previous conversation packet |
| Ctrl+. | Auto scroll during capture |

---

## Display Filter Quick Reference

### Comparison Operators
```
==    Equal
!=    Not equal
>     Greater than
<     Less than
>=    Greater than or equal
<=    Less than or equal
```

### Logical Operators
```
and   &&   Logical AND
or    ||   Logical OR
not   !    Logical NOT
```

### Common Filters for Your Project

```plaintext
# All traffic on your server port
tcp.port == 5000

# Only client connections
tcp.dstport == 5000 and tcp.flags.syn == 1

# Only data packets (exclude handshakes)
tcp.port == 5000 and tcp.len > 0

# Failed connections
tcp.flags.reset == 1

# Retransmissions (potential network issues)
tcp.analysis.retransmission

# All localhost communication
ip.addr == 127.0.0.1

# Specific client-server conversation
ip.addr == 127.0.0.1 and tcp.port == 5000
```

---

## Real-World Example: Debugging Client-Server Chat

Let's walk through debugging your coursework application:

### Setup
- Server running on localhost:5000
- Three clients connected

### Step 1: Verify Connections
```
Display Filter: tcp.port == 5000 and tcp.flags.syn == 1
```
You should see 3 SYN packets (one per client connecting)

### Step 2: Send Test Message
Client1 sends: "Hello from Client1"

### Step 3: Capture and Analyze
```
Display Filter: tcp.port == 5000 and tcp.len > 0
```

### Step 4: Follow the Stream
Right-click packet → Follow → TCP Stream

You should see:
```
Client → Server: "Hello from Client1"
Server → Client2: "Client1: Hello from Client1"
Server → Client3: "Client1: Hello from Client1"
```

### Step 5: Verify Timing
Select packet, look at Time column
Should be milliseconds apart for localhost

---

## Advanced Topics (Optional)

### 1. Protocol Dissection

Wireshark can decode many protocols. For custom protocols (like your client-server protocol), you can:
- Define your protocol format
- Create a Lua dissector
- Wireshark will then decode your custom messages

### 2. Traffic Generation for Testing

Generate test traffic:
```bash
# Using netcat to test server
nc localhost 5000

# Send test message
echo "TEST MESSAGE" | nc localhost 5000
```

### 3. Command-Line Capture

```bash
# Capture to file
tshark -i lo -w capture.pcapng port 5000

# Capture and display
tshark -i lo port 5000

# Read from file with filter
tshark -r capture.pcapng -Y "tcp.port == 5000"
```

---

## Learning Resources

### Official Documentation
- Wireshark User Guide: https://www.wireshark.org/docs/wsug_html/
- Wiki: https://wiki.wireshark.org/

### Video Tutorials
- Wireshark Official Channel (YouTube)
- "Wireshark 101" series

### Practice Captures
- Wireshark Sample Captures: https://wiki.wireshark.org/SampleCaptures
- PacketLife Captures: https://packetlife.net/captures/

### Books
- "Practical Packet Analysis" by Chris Sanders
- "Wireshark Network Analysis" by Laura Chappell

---

## Summary

### Key Takeaways

1. **Wireshark captures and analyzes network traffic**
2. **Use capture filters to reduce captured data**
3. **Use display filters to find specific packets**
4. **TCP is connection-oriented (handshake)**
5. **UDP is connectionless (no handshake)**
6. **Follow TCP Stream to see complete conversations**
7. **Time column helps measure latency**
8. **Filter for your port number to focus on your app**

### For Your COMP1549 Project

Use Wireshark to:
- ✓ Verify client-server connections
- ✓ Debug message delivery
- ✓ Test coordinator ping system
- ✓ Confirm broadcast messages reach all clients
- ✓ Measure network performance
- ✓ Troubleshoot connection issues
- ✓ Validate fault tolerance scenarios

### Next Steps

1. **Install Wireshark**
2. **Do Exercise 1** (Capture localhost traffic)
3. **Practice with web browsing** (visit websites and analyze HTTP)
4. **Test with your Java application**
5. **Learn 5 useful display filters**
6. **Practice following TCP streams**
7. **Use it to debug your coursework!**

---

## Quick Start Checklist

- [ ] Install Wireshark
- [ ] Start first capture on loopback interface
- [ ] Apply basic filter: `tcp.port == 5000`
- [ ] Run your server on port 5000
- [ ] Run a client and connect
- [ ] Find the TCP handshake packets (SYN, SYN-ACK, ACK)
- [ ] Follow a TCP stream
- [ ] Find a packet containing your message data
- [ ] Measure time between request and response
- [ ] Save your capture file

---

## Troubleshooting Wireshark Itself

### Issue: Can't See Any Interfaces

**Solution (Linux):**
```bash
sudo usermod -aG wireshark $USER
# Log out and back in
```

**Solution (Windows):** Run as Administrator

### Issue: Permission Denied on Interface

**Solution (Linux):**
```bash
sudo chmod +x /usr/bin/dumpcap
```

### Issue: Too Many Packets, Can't Find Mine

**Solution:** Use stricter capture/display filters
```
host 127.0.0.1 and port 5000
```

### Issue: Can't See Packet Contents

**Solution:**
- Check if traffic is encrypted (HTTPS, TLS)
- Make sure you're looking at correct packet
- Expand packet details pane layers

---

## Glossary

| Term | Definition |
|------|------------|
| ACK | Acknowledgment - confirms receipt of data |
| FIN | Finish - terminates connection |
| RST | Reset - aborts connection |
| SYN | Synchronize - initiates connection |
| PSH | Push - deliver data immediately |
| TTL | Time To Live - hops before packet is discarded |
| MSS | Maximum Segment Size |
| RTT | Round Trip Time - time for packet and response |
| Pcap | Packet Capture file format |

---

**Good luck with your Wireshark learning and your COMP1549 coursework!**

Remember: Wireshark is a powerful tool that helps you see what's actually happening on the network. Use it to learn, debug, and understand network protocols better.

**Practice makes perfect!** Start capturing and exploring today.
