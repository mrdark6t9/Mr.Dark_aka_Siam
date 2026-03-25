# Wireshark Practical Lab: Debugging Client-Server Applications

## Lab Overview

This practical lab will teach you how to use Wireshark to debug your COMP1549 Group Communication System (Task 1). You'll learn how to capture, analyze, and troubleshoot network traffic step by step.

---

## Prerequisites

- Wireshark installed
- Basic understanding of TCP/IP
- Java development environment (for testing your coursework)

---

## Lab 1: Setting Up Wireshark for Localhost Testing

### Objective
Configure Wireshark to capture traffic from your client-server application running on localhost.

### Steps

**1. Start Wireshark**
```bash
# Linux
wireshark

# Or if permission issues
sudo wireshark
```

**2. Select Loopback Interface**
- Look for "Loopback: lo" or "lo0" (macOS)
- This captures traffic on 127.0.0.1 (localhost)

**3. Set Capture Filter (Optional but Recommended)**
```
port 5000
```
This captures only traffic on port 5000 (adjust to your server port)

**4. Start Capture**
- Click the blue shark fin icon, or
- Press Ctrl+E

**5. Verify Capture is Running**
- You should see the toolbar change
- Packet counter starts incrementing
- Status bar shows "Capturing from Loopback"

### Expected Result
Wireshark is now ready to capture your application traffic!

---

## Lab 2: Capturing Your First Client-Server Connection

### Objective
Capture and analyze a simple client connecting to your server.

### Setup

**Terminal 1 - Start Server:**
```bash
cd /path/to/your/project
java MainServer 5000
```

**Terminal 2 - Wireshark:**
```
Start capture on loopback, filter: port 5000
```

**Terminal 3 - Start Client:**
```bash
java Client client1 127.0.0.1 5000
```

### Analysis

**Step 1: Find the TCP Handshake**

Apply display filter:
```
tcp.flags.syn == 1
```

You should see:
1. **Packet 1**: Client → Server (SYN)
   - Source Port: Random high port (e.g., 54321)
   - Dest Port: 5000
   - Flags: SYN

2. **Packet 2**: Server → Client (SYN-ACK)
   - Source Port: 5000
   - Dest Port: 54321
   - Flags: SYN, ACK

3. **Packet 3**: Client → Server (ACK)
   - Flags: ACK

**Step 2: Verify Connection Established**

Remove filter (click X on filter bar) or use:
```
tcp.port == 5000
```

You should now see all packets on port 5000.

**Step 3: Follow the TCP Stream**

1. Right-click any packet from your connection
2. Follow → TCP Stream
3. A new window opens showing the conversation
4. Red text = data from client
5. Blue text = data from server

### Expected Result

You should see the complete data exchange between your client and server in plain text!

**Example output:**
```
CONNECT:client1
OK:COORDINATOR
```

---

## Lab 3: Analyzing Message Flow

### Objective
Verify that messages are being sent and received correctly.

### Scenario
You'll send a broadcast message and verify all clients receive it.

### Setup

**Run:**
- 1 Server on port 5000
- 3 Clients (client1, client2, client3)
- Wireshark capturing on loopback

### Test

**From Client1, send:**
```
BROADCAST:Hello everyone!
```

### Analysis

**Step 1: Clear Display**
```
Filter: tcp.port == 5000 and tcp.len > 0
```

**Step 2: Find Your Message**
```
Filter: tcp.port == 5000 and tcp contains "Hello everyone"
```

**Step 3: Count Packets**

You should see:
1. One packet from client1 to server (source port = client1's port)
2. Three packets from server to clients (broadcasts)

**Step 4: Verify Each Client Receives**

For each client connection:
1. Find packets going to that client's port
2. Right-click → Follow TCP Stream
3. Verify message appears

### Expected Result

All three clients should receive the broadcast message, visible in their respective TCP streams.

---

## Lab 4: Debugging Connection Failures

### Objective
Use Wireshark to diagnose why a client can't connect.

### Scenario 1: Server Not Running

**Steps:**
1. DO NOT start server
2. Start Wireshark capture
3. Try to connect client
4. Stop capture after 5 seconds

**Analysis:**
```
Filter: tcp.port == 5000
```

**What You See:**
- Multiple SYN packets from client (retrying)
- Possibly RST (reset) packets
- No SYN-ACK response

**Diagnosis:** Server not listening on port 5000

**Solution:** Start your server first!

### Scenario 2: Wrong Port

**Steps:**
1. Start server on port 5000
2. Start Wireshark capture
3. Connect client to port 6000 (wrong port!)
4. Stop capture

**Analysis:**
```
Filter: tcp.port == 6000 or tcp.port == 5000
```

**What You See:**
- SYN packet to port 6000
- RST (reset) response

**Diagnosis:** Client connecting to wrong port

**Solution:** Fix client configuration

### Scenario 3: Firewall Blocking

**Steps:**
1. Start server
2. Enable firewall to block port 5000
3. Start capture
4. Try to connect

**Analysis:**
```
Filter: tcp.port == 5000
```

**What You See:**
- SYN packets
- No response at all (timeout)

**Diagnosis:** Firewall blocking

**Solution:** Configure firewall to allow port 5000

---

## Lab 5: Measuring Network Performance

### Objective
Measure the latency of your application.

### Setup

**Run your application and capture traffic**

### Analysis

**Method 1: Manual Timing**

1. Clear filter
2. Find packet where client sends message
3. Note the Time value (e.g., 5.234567)
4. Find packet where server responds
5. Note the Time value (e.g., 5.237891)
6. Calculate: 5.237891 - 5.234567 = 0.003324 seconds = **3.3 milliseconds**

**Method 2: Using TCP Analysis**

```
Filter: tcp.port == 5000 and tcp.analysis.ack_rtt
```

Wireshark automatically calculates Round Trip Time (RTT) for each packet.

Look at packet details:
```
▼ Transmission Control Protocol
  ▼ [SEQ/ACK analysis]
    ► iRTT: 0.003324 seconds
```

**Method 3: Statistics**

1. Statistics → TCP Stream Graphs → Round Trip Time Graph
2. Select your connection
3. See RTT over time

### Expected Result

For localhost:
- **Good:** < 1 millisecond
- **Acceptable:** 1-10 milliseconds
- **Slow:** > 10 milliseconds (investigate why!)

---

## Lab 6: Verifying Coordinator Ping System

### Objective
Verify that the coordinator pings clients every 20 seconds.

### Setup

**Run:**
- Server + 3 clients
- Wireshark capturing

**Wait 1 minute** to capture multiple ping cycles

### Analysis

**Step 1: Find Ping Messages**

Assuming your protocol sends "PING" as the message:
```
Filter: tcp.port == 5000 and tcp contains "PING"
```

**Step 2: Check Timing**

1. Click first PING packet
2. Note the Time column value (e.g., 20.145)
3. Click second PING packet
4. Note the Time column value (e.g., 40.234)
5. Calculate: 40.234 - 20.145 = **~20 seconds**

**Step 3: Verify All Clients Pinged**

Count PING packets - should be (number of clients) per cycle

If 3 clients, expect 3 PING packets every 20 seconds.

### Expected Result

Pings occur at regular 20-second intervals to all active clients.

---

## Lab 7: Debugging Coordinator Failover

### Objective
Verify that a new coordinator is selected when current coordinator leaves.

### Setup

**Run:**
- Server
- 3 clients (client1 as coordinator, client2, client3)
- Wireshark capturing

### Test Steps

**1. Initial State**
```
Filter: tcp.port == 5000 and tcp.len > 0
```
Verify client1 is sending PING messages (coordinator behavior)

**2. Terminate Coordinator**
- Kill client1 (Ctrl+C)

**3. Observe Wireshark**

**Find FIN or RST packets:**
```
Filter: tcp.port == 5000 and (tcp.flags.fin == 1 or tcp.flags.reset == 1)
```

You should see connection closure from client1.

**4. Verify New Coordinator**

Look for messages indicating coordinator change:
```
Filter: tcp.port == 5000 and tcp contains "COORDINATOR"
```

**5. Follow TCP Streams**

Follow streams for client2 and client3 to see:
```
COORDINATOR_CHANGED:client2
```

Or new PING messages from client2 (new coordinator).

### Expected Result

After client1 disconnects:
- FIN/RST packets visible
- New coordinator elected (client2 or client3)
- System continues functioning

---

## Lab 8: Analyzing Private vs Broadcast Messages

### Objective
Distinguish between private and broadcast message delivery.

### Setup

**Run:**
- Server + 3 clients

### Test 1: Private Message

**Send from client1:**
```
PRIVATE:client2:Hello Client2!
```

**Wireshark Analysis:**

1. Filter: `tcp.port == 5000 and tcp contains "Hello Client2"`
2. Count packets - should see:
   - 1 packet: client1 → server
   - 1 packet: server → client2
   - **Total: 2 packets**

**Verify:**
- Follow stream for client3 connection
- Should NOT contain "Hello Client2"

### Test 2: Broadcast Message

**Send from client1:**
```
BROADCAST:Hello Everyone!
```

**Wireshark Analysis:**

1. Filter: `tcp.port == 5000 and tcp contains "Hello Everyone"`
2. Count packets - should see:
   - 1 packet: client1 → server
   - 3 packets: server → client1, client2, client3
   - **Total: 4 packets**

**Verify:**
- Follow stream for each client
- All should contain "Hello Everyone"

### Expected Result

Private messages go to one client, broadcasts go to all clients.

---

## Lab 9: Detecting Message Loss

### Objective
Identify if messages are being lost in transmission.

### How to Detect Loss

**1. Retransmissions**
```
Filter: tcp.analysis.retransmission
```
If you see packets here, data was lost and retransmitted.

**2. Duplicate ACKs**
```
Filter: tcp.analysis.duplicate_ack
```
Multiple duplicate ACKs indicate packet loss.

**3. Out-of-Order Packets**
```
Filter: tcp.analysis.out_of_order
```

### For Your Application

If running on localhost, you should **NOT** see:
- Retransmissions (reliability should be 100% on loopback)
- Duplicate ACKs
- Out-of-order packets

If you do see these on localhost, there's a bug in your code!

---

## Lab 10: Complete Debug Session Example

### The Problem
"My client connects, but when coordinator leaves, the new coordinator isn't selected."

### Debug Process

**Step 1: Start Monitoring**
```bash
# Terminal 1
wireshark
# Select loopback, filter: port 5000, start capture

# Terminal 2
java MainServer 5000

# Terminal 3
java Client client1 127.0.0.1 5000

# Terminal 4
java Client client2 127.0.0.1 5000

# Terminal 5
java Client client3 127.0.0.1 5000
```

**Step 2: Establish Baseline**

Verify all 3 clients connected:
```
Filter: tcp.dstport == 5000 and tcp.flags.syn == 1 and tcp.flags.ack == 0
```
Should see 3 SYN packets.

**Step 3: Monitor Coordinator Pings**

Verify client1 (coordinator) is pinging:
```
Filter: tcp.port == 5000 and tcp contains "PING"
```
Wait 20 seconds, should see pings.

**Step 4: Kill Coordinator**

Terminal 3 (client1): Press Ctrl+C

**Step 5: Analyze Disconnection**

```
Filter: tcp.flags.fin == 1 or tcp.flags.reset == 1
```
You should see client1's connection closing.

**Step 6: Look for Coordinator Election Messages**

```
Filter: tcp.port == 5000 and tcp.len > 0
```

Look for messages like:
- "COORDINATOR_ELECTION"
- "NEW_COORDINATOR:client2"

Follow TCP streams for client2 and client3 to see these messages.

**Step 7: Verify New Coordinator**

Wait 20 seconds and check pings:
```
Filter: tcp.port == 5000 and tcp contains "PING"
```

If pings now come from client2's connection, coordinator failover worked!

### Expected Results

✓ Client1 disconnects cleanly (FIN packets)
✓ Election message sent to remaining clients
✓ Client2 becomes coordinator
✓ PING messages resume from client2
✓ System continues functioning

### If It Doesn't Work

**Debug Steps:**

1. **Check if server detected disconnection**
   - Look for exception messages in server log
   - Verify ping mechanism detected client1 is dead

2. **Check if election was triggered**
   - Search for "ELECT" or "COORDINATOR" in packets
   - If not present, server didn't initiate election

3. **Check if election message reached clients**
   - Follow TCP streams for client2 and client3
   - Verify they received election message

---

## Lab 11: Analyzing Protocol Design

### Objective
Use Wireshark to understand and improve your custom protocol.

### Your Protocol Structure

For a well-designed protocol, your messages should have a clear structure.

**Example Protocol:**
```
[MESSAGE_TYPE]:[SENDER]:[RECEIVER]:[CONTENT]
```

**Examples:**
```
CONNECT:client1:server:
PRIVATE:client1:client2:Hello!
BROADCAST:client1:all:Hi everyone!
PING:coordinator:client1:
PONG:client1:coordinator:
DISCONNECT:client1:server:
```

### Analyze Your Protocol

**1. Capture a full session**

**2. Follow TCP Stream**

**3. Check for:**
- ✓ Clear message delimiters
- ✓ Consistent format
- ✓ All necessary fields present
- ✓ No ambiguous parsing

**4. Look for Issues:**
- ✗ Inconsistent delimiters (sometimes : sometimes ;)
- ✗ Missing sender/receiver info
- ✗ Concatenated messages without separator
- ✗ Binary data causing parsing issues

### Example Analysis

**Good Protocol:**
```
→ CONNECT:client1
← OK:COORDINATOR
→ MSG:client1:client2:Hello
← ACK:MSG:12345
```

**Bad Protocol:**
```
→ client1 connect
← ok
→ hello
← got it
```

The good protocol is:
- Machine-parseable
- Unambiguous
- Self-documenting
- Easy to debug

---

## Lab 12: Performance Benchmarking

### Objective
Measure your application's performance using Wireshark.

### Metrics to Measure

#### 1. Connection Time

Time from SYN to established connection.

**Steps:**
1. Find SYN packet (client to server)
2. Note time: T1
3. Find ACK packet (client to server, after SYN-ACK)
4. Note time: T2
5. Connection time = T2 - T1

**Expected:** < 1ms on localhost

#### 2. Message Latency

Time from sending message to receiving response.

**Steps:**
1. Filter: `tcp.port == 5000 and tcp.len > 0`
2. Find client sends "PING"
3. Note time: T1
4. Find server sends "PONG"
5. Note time: T2
6. Latency = T2 - T1

**Expected:** < 5ms on localhost

#### 3. Throughput

Packets per second.

**Statistics → I/O Graph**
- X-axis: Time
- Y-axis: Packets/second
- Shows traffic patterns

#### 4. Message Size

How big are your messages?

**Filter:** `tcp.port == 5000 and tcp.len > 0`

Look at Length column.

**Optimization tip:** Smaller messages = faster transmission

---

## Lab 13: Security Analysis

### Objective
Check if your application has security issues visible in network traffic.

### Security Checks

#### 1. Check for Plaintext Passwords

```
Filter: tcp.port == 5000 and tcp contains "password"
```

**Issue:** If you see passwords in plain text, they're not encrypted!

**Solution:** Use encryption (not required for coursework, but good to know)

#### 2. Check for Sensitive Data

```
Filter: tcp.port == 5000
```

Follow streams and look for:
- User credentials
- Personal information
- System information

For production apps (not coursework), this should be encrypted.

#### 3. Check for Injection Vulnerabilities

Look at messages in TCP streams:
```
'; DROP TABLE users; --
<script>alert('XSS')</script>
```

If your app doesn't sanitize input, these could cause issues.

---

## Lab 14: Troubleshooting Common Problems

### Problem 1: "I can't see my messages in Wireshark"

**Possible Causes:**
1. Wrong interface selected
2. Wrong port filter
3. Not using TCP/UDP (check your code)
4. Connection not established

**Debug:**
```bash
# Verify server is listening
netstat -an | grep 5000

# Should show:
tcp    0    0 127.0.0.1:5000    0.0.0.0:*    LISTEN
```

**Wireshark check:**
- Remove all filters
- Look for ANY packets
- If nothing, interface selection wrong

### Problem 2: "Too many packets, can't find mine"

**Solution:** Use stricter filters

```
# Instead of:
tcp.port == 5000

# Use:
tcp.port == 5000 and tcp.len > 0 and not tcp.analysis.retransmission
```

**Or:**
```
tcp.port == 5000 and frame.time_relative > 10
```
(Only packets after 10 seconds)

### Problem 3: "TCP Stream shows garbage characters"

**Cause:** Binary protocol or encoding issues

**Solutions:**
1. Check if you're sending text or binary
2. Verify character encoding (UTF-8?)
3. If binary, look at Packet Bytes pane instead

### Problem 4: "Client connects but immediately disconnects"

**Wireshark Analysis:**
```
Filter: tcp.port == 5000
```

Look for sequence:
```
SYN → SYN-ACK → ACK → FIN
```

If FIN comes immediately after ACK, client is closing connection right away.

**Check:**
- Client code - is it exiting early?
- Exception in client? (check logs)
- Server rejecting client?

---

## Lab 15: Advanced Filtering Techniques

### Combining Multiple Conditions

```plaintext
# Client1's traffic only (if using port 54321)
tcp.srcport == 54321 or tcp.dstport == 54321

# Large messages only
tcp.port == 5000 and tcp.len > 100

# Connections and data (no ACKs)
tcp.port == 5000 and (tcp.flags.syn == 1 or tcp.len > 0)

# Specific client IP and your server port
ip.addr == 192.168.1.100 and tcp.port == 5000

# Exclude pings to focus on messages
tcp.port == 5000 and not tcp contains "PING"
```

### Regular Expressions

```plaintext
# Match messages starting with "MSG"
tcp.port == 5000 and tcp matches "MSG.*"

# Match any client ID (client followed by digit)
tcp matches "client[0-9]"
```

### Time-Based Filters

```plaintext
# Packets in first 10 seconds
frame.time_relative < 10

# Packets after 30 seconds
frame.time_relative > 30

# Packets in specific time range
frame.time_relative > 10 and frame.time_relative < 20
```

---

## Lab 16: Exporting Data for Analysis

### Export to CSV

File → Export Packet Dissections → As CSV

Useful for:
- Importing to Excel
- Data analysis
- Creating reports

### Export Packets

File → Export Specified Packets

Options:
- All packets
- Selected packet
- Marked packets
- Packet range
- Filtered packets (current display filter)

### Export TCP Stream

Follow TCP Stream → Save As

Saves the conversation to a text file.

---

## Wireshark Best Practices for Your Project

### During Development

1. **Start Wireshark BEFORE starting your app**
   - Captures the connection handshake

2. **Use specific filters**
   - `host 127.0.0.1 and port 5000`

3. **Save captures with descriptive names**
   - `capture_client_connect.pcapng`
   - `capture_broadcast_test.pcapng`
   - `capture_coordinator_failover.pcapng`

4. **Document findings**
   - Screenshot interesting packets
   - Note packet numbers for reference

### During Testing

1. **Create test scenarios**
   - Normal operation
   - Edge cases
   - Failure cases

2. **Capture each scenario**
   - Separate capture per test

3. **Compare before/after**
   - Capture before fix
   - Capture after fix
   - Verify improvement

### During Demonstration

1. **Pre-record successful captures**
   - Show during demo if live demo fails

2. **Have filters ready**
   - Save display filter configurations

3. **Know your packets**
   - Memorize key packet numbers
   - Quick navigation during Q&A

---

## Common Mistakes to Avoid

### 1. Capturing on Wrong Interface

❌ **Wrong:** Capturing on eth0 when testing localhost
✓ **Right:** Capture on loopback (lo) for localhost

### 2. No Filter = Overwhelming Data

❌ **Wrong:** Capturing everything on busy network
✓ **Right:** Use `port 5000` capture filter

### 3. Forgetting to Stop Capture

❌ **Wrong:** Running capture for hours
✓ **Right:** Capture only what you need, then stop

### 4. Not Following Streams

❌ **Wrong:** Looking at individual packets
✓ **Right:** Follow TCP Stream for complete conversation

### 5. Ignoring Timing

❌ **Wrong:** Only looking at data
✓ **Right:** Check Time column for latency issues

---

## Practical Exercise: Complete Debug Workflow

### Scenario
Your client sends "Hello" but server logs show "Helo" (missing 'l').

### Investigation

**Step 1: Capture**
```
Start Wireshark on loopback, port 5000
```

**Step 2: Reproduce**
```
Client sends: "Hello"
```

**Step 3: Find the Packet**
```
Filter: tcp.port == 5000 and tcp contains "Hel"
```

**Step 4: Follow Stream**
```
Right-click → Follow TCP Stream
```

**Step 5: Check Raw Bytes**

In Packet Bytes pane, look at hex dump:
```
48 65 6c 6c 6f    # H e l l o
```

If you see all 5 bytes (48 65 6c 6c 6f), the data was sent correctly!

**Conclusion:** Bug is not in network transmission, it's in server parsing logic.

**Action:** Review server's `readMessage()` method.

---

## Sample Wireshark Display Filter Recipes for Your Coursework

### Development Phase
```plaintext
# Basic monitoring
tcp.port == 5000

# See actual data (no TCP overhead)
tcp.port == 5000 and tcp.len > 0

# Monitor specific client (if on port 54321)
tcp.port == 54321 or tcp.port == 5000
```

### Testing Phase
```plaintext
# Connection tests
tcp.dstport == 5000 and tcp.flags.syn == 1

# Message delivery tests
tcp.port == 5000 and tcp contains "MSG"

# Timing tests
tcp.port == 5000 and tcp.analysis.ack_rtt
```

### Debugging Phase
```plaintext
# Find problems
tcp.analysis.flags

# Connection issues
tcp.flags.reset == 1

# Performance issues
tcp.analysis.retransmission
```

### Demonstration Phase
```plaintext
# Clean view of your application
tcp.port == 5000 and tcp.len > 0 and not tcp.analysis.retransmission
```

---

## Integration with Your Development Process

### Recommended Workflow

```
1. Write Code
   ↓
2. Unit Test (JUnit)
   ↓
3. Integration Test
   ↓
4. Network Test with Wireshark
   ↓
5. Analyze and Fix
   ↓
6. Repeat
```

### When to Use Wireshark

**Use When:**
- Client can't connect
- Messages not delivered
- Timing issues
- Coordinator failover not working
- Need to verify protocol format
- Debugging race conditions
- Measuring performance

**Don't Use When:**
- Logic errors in code (use debugger)
- Null pointer exceptions (use Java debugger)
- Compilation errors
- Algorithm issues

---

## Appendix: TCP Flags Reference

```
Flag    Hex     Binary      Meaning
SYN     0x02    000010      Synchronize (start connection)
ACK     0x10    010000      Acknowledge
FIN     0x01    000001      Finish (close connection)
RST     0x04    000100      Reset (abort connection)
PSH     0x08    001000      Push (deliver immediately)
URG     0x20    100000      Urgent
```

### Common Flag Combinations

| Flags | Meaning |
|-------|---------|
| SYN | Initial connection request |
| SYN+ACK | Server accepting connection |
| ACK | Acknowledgment of data |
| PSH+ACK | Pushing data + acknowledging |
| FIN+ACK | Graceful close + acknowledging |
| RST | Abrupt close (error or refused) |
| RST+ACK | Closing established connection |

---

## Exam/Demo Tips

### Questions Your Professor Might Ask

**Q: "How did you verify the messages were delivered?"**

**A:** "I used Wireshark to capture the network traffic. I applied the filter `tcp.port == 5000 and tcp.len > 0` to see data packets only. Then I followed the TCP stream to verify the complete message content was transmitted from client to server and then broadcast to all connected clients."

**Q: "How do you know your ping system works?"**

**A:** "I captured traffic over 1 minute and filtered for `tcp.port == 5000 and tcp contains 'PING'`. The packets appeared at regular 20-second intervals, confirming the coordinator's ping mechanism functions correctly."

**Q: "What happens at the network level when a client disconnects?"**

**A:** "When a client disconnects gracefully, it sends a FIN packet to the server. The server responds with ACK, then sends its own FIN, and the client sends a final ACK. This is the TCP connection termination handshake. I verified this in Wireshark using the filter `tcp.flags.fin == 1`."

### Demonstration Preparation

**Before Demo Day:**

1. **Capture successful scenarios**
   - Normal operation
   - Client join/leave
   - Coordinator failover
   - Private messaging
   - Broadcast messaging

2. **Save captures**
   - Name them clearly
   - Know which file shows what

3. **Prepare filters**
   - Have filter strings ready
   - Practice applying them quickly

4. **Know packet numbers**
   - For each scenario, note key packet numbers
   - Jump to them with Ctrl+G

5. **Practice explanation**
   - "This packet at 0.123 seconds shows..."
   - "Following this stream demonstrates..."

---

## Summary

### Core Wireshark Skills Acquired

After completing these labs, you can:

✓ Capture network traffic on specific interfaces
✓ Apply capture and display filters effectively
✓ Follow TCP streams to see conversations
✓ Identify connection issues (refused, timeout)
✓ Measure latency and performance
✓ Verify message delivery
✓ Debug protocol implementations
✓ Analyze coordinator failover
✓ Distinguish private vs broadcast messages
✓ Export data for further analysis

### For COMP1549 Success

Wireshark helps you:
- ✓ Debug your implementation faster
- ✓ Understand what's actually happening on the network
- ✓ Verify requirements are met
- ✓ Demonstrate deep understanding during demo
- ✓ Impress your professor with network-level analysis

### Keep Practicing!

The more you use Wireshark, the more comfortable you'll become. Make it part of your development workflow!

---

## Additional Resources

### Wireshark Documentation
- Official User Guide: https://www.wireshark.org/docs/wsug_html/
- Display Filter Reference: https://www.wireshark.org/docs/dfref/

### Practice Datasets
- Wireshark Sample Captures: https://wiki.wireshark.org/SampleCaptures
- PacketLife.net: https://packetlife.net/captures/

### Community
- Wireshark Q&A: https://ask.wireshark.org/
- Reddit: r/wireshark

### Related Tools
- **tshark**: Command-line version of Wireshark
- **tcpdump**: Simple packet capture tool
- **netcat (nc)**: Network testing utility
- **nmap**: Network scanner

---

**You're now ready to use Wireshark effectively for your coursework and beyond!**

Start with Lab 1 and work your way through. Each lab builds on the previous one.

**Happy packet hunting!** 🦈📦
