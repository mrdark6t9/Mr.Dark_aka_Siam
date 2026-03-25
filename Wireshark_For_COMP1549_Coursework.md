# Wireshark for COMP1549: Client-Server Chat System Analysis

## Purpose

This guide shows exactly how to use Wireshark to debug and verify each requirement of your COMP1549 Task 1 (Group Communication System).

---

## Requirement 1: Group Formation and Connection

### What to Verify
- Clients can connect to server
- Each client gets unique ID
- First client becomes coordinator

### Wireshark Procedure

**1. Start Fresh Capture**
```
Interface: Loopback (lo)
Capture Filter: port 5000
```

**2. Start Your Application**
```bash
# Terminal 1
java MainServer 5000

# Terminal 2
java Client client1 127.0.0.1 5000

# Terminal 3
java Client client2 127.0.0.1 5000

# Terminal 4
java Client client3 127.0.0.1 5000
```

**3. Stop Capture After All Clients Connect**

**4. Analyze Connection Sequence**

Display Filter:
```
tcp.dstport == 5000 and tcp.flags.syn == 1 and tcp.flags.ack == 0
```

**Expected:** 3 SYN packets (one per client)

**5. Verify Each Connection Succeeds**

Display Filter:
```
tcp.port == 5000 and tcp.flags.syn == 1
```

For each client, you should see:
```
Packet N:   Client → Server  [SYN]
Packet N+1: Server → Client  [SYN, ACK]
Packet N+2: Client → Server  [ACK]
```

**6. Check Client Registration**

Follow TCP Stream for first client (client1):
```
Right-click first SYN packet → Follow → TCP Stream
```

**Expected to see:**
```
CONNECT:client1:
WELCOME:COORDINATOR:
```

**7. Check Other Clients**

Follow streams for client2 and client3:
```
CONNECT:client2:
WELCOME:COORDINATOR:client1:
```

### ✓ Verification Checklist

- [ ] All 3 clients complete TCP handshake
- [ ] Client1 told it's coordinator
- [ ] Client2 and client3 told who coordinator is
- [ ] Each client has unique ID
- [ ] All connections remain open (no immediate FIN packets)

---

## Requirement 2: Group State Maintenance

### What to Verify
- Server maintains list of active clients
- Clients can request member list
- List includes ID, IP, port for each member

### Wireshark Procedure

**1. With All Clients Connected, Request Member List**

From client2:
```
GET_MEMBERS
```

**2. Capture and Filter**
```
Filter: tcp.port == 5000 and tcp contains "MEMBERS"
```

**3. Follow Stream for client2**

Right-click packet → Follow TCP Stream

**Expected:**
```
GET_MEMBERS
MEMBERS:3:client1:127.0.0.1:54321:client2:127.0.0.1:54322:client3:127.0.0.1:54323
```

Or similar format showing all member details.

**4. Verify Information Accuracy**

Compare with actual client connection info:
```
Filter: tcp.dstport == 5000 and tcp.flags.syn == 1
```

Check source ports match what was reported.

### ✓ Verification Checklist

- [ ] GET_MEMBERS request sent from client
- [ ] Server responds with member list
- [ ] List contains all active clients
- [ ] Each entry has ID, IP, port
- [ ] Coordinator is identified

---

## Requirement 3: Periodic Ping (Every 20 Seconds)

### What to Verify
- Coordinator pings members every 20 seconds
- Dead clients are detected and removed

### Wireshark Procedure

**1. Long Capture (at least 60 seconds)**
```
Interface: Loopback
Filter: port 5000
Duration: 60 seconds
```

**2. Find Ping Messages**
```
Filter: tcp.port == 5000 and tcp contains "PING"
```

**3. Check Timing**

| Packet No. | Time | Info |
|------------|------|------|
| 45 | 20.123 | PING → client2 |
| 46 | 20.124 | PING → client3 |
| 78 | 40.234 | PING → client2 |
| 79 | 40.235 | PING → client3 |
| 112 | 60.456 | PING → client2 |
| 113 | 60.457 | PING → client3 |

**Intervals:** 40.234 - 20.123 ≈ 20 seconds ✓

**4. Verify All Clients Pinged**

Count PING packets per cycle:
- 2 pings (for 2 non-coordinator clients) ✓

**5. Test Dead Client Detection**

**Kill client3** (Ctrl+C in Terminal 4)

**Check Wireshark:**
```
Filter: tcp.port == 5000 and tcp.flags.fin == 1
```

Should see client3 FIN packets.

**Wait 20 seconds** for next ping cycle.

```
Filter: tcp.port == 5000 and tcp contains "PING"
```

**Expected:** Only 1 ping now (to client2), none to client3.

Alternatively, look for removal message:
```
Filter: tcp.port == 5000 and tcp contains "REMOVED"
```

Might see:
```
CLIENT_REMOVED:client3
```

### ✓ Verification Checklist

- [ ] Pings occur every ~20 seconds
- [ ] All non-coordinator clients receive pings
- [ ] Coordinator doesn't ping itself
- [ ] Dead clients stop receiving pings
- [ ] Other clients notified of removal

---

## Requirement 4: Private Messaging

### What to Verify
- Client can send message to specific client
- Only intended recipient receives message
- Messages go through server

### Wireshark Procedure

**1. Send Private Message**

From client1 to client2:
```
PRIVATE:client2:This is a secret!
```

**2. Capture and Analyze**
```
Filter: tcp.port == 5000 and tcp contains "secret"
```

**Expected Packets:**
- Packet X: client1 → server (contains "This is a secret!")
- Packet Y: server → client2 (contains "This is a secret!")
- **No packet to client3** ✓

**3. Verify client3 Didn't Receive It**

Follow TCP stream for client3's connection:
```
Right-click packet from client3 → Follow → TCP Stream
```

Search (Ctrl+F) for "secret" → Should not be found!

**4. Verify Sender and Receiver Info**

In packet details, check:
```
▼ Transmission Control Protocol
  Source Port: [client1's port]
  Destination Port: 5000
```

Then server to client2:
```
▼ Transmission Control Protocol
  Source Port: 5000
  Destination Port: [client2's port]
```

### ✓ Verification Checklist

- [ ] Message sent from client1 to server
- [ ] Message forwarded from server to client2 only
- [ ] Client3 did not receive the message
- [ ] Message content intact (no corruption)
- [ ] Delivered in correct order

---

## Requirement 5: Broadcast Messaging

### What to Verify
- Client can send message to all members
- All members receive the broadcast
- Messages delivered through server

### Wireshark Procedure

**1. Send Broadcast**

From client1:
```
BROADCAST:Hello everyone!
```

**2. Filter for Broadcast**
```
Filter: tcp.port == 5000 and tcp contains "Hello everyone"
```

**3. Count Packets**

You should see:
- 1 packet: client1 → server
- 1 packet: server → client1 (echo back)
- 1 packet: server → client2
- 1 packet: server → client3
- **Total: 4 packets**

**4. Verify Each Client Received It**

Follow TCP streams individually:
- Stream 1 (client1): Should show "Hello everyone"
- Stream 2 (client2): Should show "Hello everyone"
- Stream 3 (client3): Should show "Hello everyone"

**5. Check Message Format**

Verify messages include sender info:
```
FROM:client1:BROADCAST:Hello everyone!
```

This helps recipients know who sent it.

### ✓ Verification Checklist

- [ ] Broadcast sent once to server
- [ ] Server forwarded to all clients
- [ ] All clients received identical message
- [ ] Message includes sender identification
- [ ] Sender receives echo/confirmation

---

## Requirement 6: Member Can Leave (Fault Tolerance)

### What to Verify
- Client can leave gracefully
- Other clients continue communicating
- Group state updated

### Wireshark Procedure

**1. Baseline: 3 Clients Connected**

Verify with:
```
Filter: tcp.port == 5000
Statistics → Conversations → TCP tab
```

Should show 3 conversations (one per client).

**2. Client Leaves**

From client2, send:
```
DISCONNECT
```

Or press Ctrl+C.

**3. Capture Connection Closure**
```
Filter: tcp.port == 5000 and tcp.flags.fin == 1
```

**Expected Sequence:**
```
Packet A: client2 → server [FIN, ACK]
Packet B: server → client2 [FIN, ACK]
Packet C: client2 → server [ACK]
```

This is the graceful TCP closure (4-way handshake).

**4. Verify Notification Sent**
```
Filter: tcp.port == 5000 and tcp contains "LEFT"
```

Look for messages like:
```
CLIENT_LEFT:client2
```

Sent to remaining clients (client1, client3).

**5. Test Communication Continues**

Send message from client1 to client3:
```
PRIVATE:client3:Still working!
```

**Verify in Wireshark:**
```
Filter: tcp.port == 5000 and tcp contains "Still working"
```

Should see message delivered successfully.

**6. Verify client2 Excluded**

Statistics → Conversations → TCP

Should now show only 2 active conversations (client1, client3).

### ✓ Verification Checklist

- [ ] Client2 disconnects cleanly (FIN packets)
- [ ] Remaining clients notified
- [ ] Client1 and client3 can still communicate
- [ ] No errors or crashes
- [ ] Group state updated

---

## Requirement 7: Coordinator Failure (Critical Test!)

### What to Verify
- When coordinator leaves, new one is selected
- Selection happens automatically
- Communication continues without interruption

### Wireshark Procedure

This is the most important test for your coursework!

**1. Initial State**
- client1 = coordinator
- client2, client3 = members
- Wireshark capturing

**2. Verify client1 is Coordinator**
```
Filter: tcp.port == 5000 and tcp contains "PING"
```

Pings should come from client1's connection (check source port).

**3. Note Ping Source Port**

Find PING packet, look at source port:
```
▼ Transmission Control Protocol
  Source Port: 54321    ← This is client1's port
  Destination Port: 5000
```

Remember: 54321 = client1

**4. Kill Coordinator**

Terminal with client1: Ctrl+C

**5. Capture Disconnection**
```
Filter: tcp.srcport == 54321 or tcp.dstport == 54321
```

Should see FIN or RST packets.

**6. Wait for Election**

Clear filter:
```
tcp.port == 5000
```

Look for election messages:
```
COORDINATOR_LEFT:client1
ELECTION_START
NEW_COORDINATOR:client2
```

**7. Verify New Coordinator Starts Pinging**

Wait 20 seconds.

```
Filter: tcp.port == 5000 and tcp contains "PING"
```

Check source port of new PING packets.

**If different from 54321**, new coordinator is active! ✓

**8. Get New Coordinator's Port**

Find the new PING packet:
```
▼ Transmission Control Protocol
  Source Port: 54322    ← This is client2's port (new coordinator)
  Destination Port: 5000
```

**9. Test Communication Still Works**

Send message from client3:
```
BROADCAST:Coordinator changed!
```

Verify client2 receives it:
```
Filter: tcp.port == 5000 and tcp contains "Coordinator changed"
```

### Advanced Analysis: Election Algorithm Timing

**Measure how fast failover occurs:**

1. Find client1 FIN packet → Note time T1
2. Find "NEW_COORDINATOR" message → Note time T2
3. Failover time = T2 - T1

**Good:** < 1 second
**Acceptable:** < 5 seconds
**Needs improvement:** > 5 seconds

### ✓ Verification Checklist

- [ ] client1 (coordinator) disconnects
- [ ] Election triggered
- [ ] Client2 or client3 becomes new coordinator
- [ ] New coordinator starts pinging
- [ ] Communication continues
- [ ] No data loss
- [ ] Failover completes in reasonable time

---

## Demonstration Scenario Walkthrough

### Demo Requirement Breakdown

From coursework description, you must demonstrate:

**A. Scenario Demonstration**
1. Run server and 3 clients, one is coordinator ✓
2. Show how coordinator works ✓
3. Send and reply to messages (private + broadcast) ✓
4. Quit one client (NOT coordinator), others communicate ✓
5. Run another client ✓
6. Quit coordinator, new one selected ✓

Let's use Wireshark to verify each step!

---

### Demo Step 1: Show Running System

**Start:**
- Server on port 5000
- 3 clients connected
- Wireshark capturing

**Show Professor:**
```
Filter: tcp.port == 5000
Statistics → Conversations → TCP tab
```

Point out: "These 3 TCP conversations show our 3 connected clients."

---

### Demo Step 2: Show Coordinator Function

**Explain:** "The coordinator pings members every 20 seconds to maintain group state."

**Show in Wireshark:**
```
Filter: tcp.port == 5000 and tcp contains "PING"
```

**Point to packets:**
"Here at time 20.1 seconds, coordinator sends PING. And here at 40.2 seconds, another cycle. This shows the 20-second interval requirement."

**Follow stream:**
"In this TCP stream, you can see the complete ping-pong exchange:
```
PING:client2
PONG:client2
```
This confirms bidirectional communication."

---

### Demo Step 3: Show Private Message

**Send:**
```
Client1: PRIVATE:client2:Hello Client2
```

**Show in Wireshark:**
```
Filter: tcp.port == 5000 and tcp contains "Hello Client2"
```

**Point out:**
"Here we see 2 packets:
1. Client1 sends to server (this packet)
2. Server forwards to client2 (this packet)
3. Notice there's NO packet to client3 - the message was delivered privately."

**Follow stream for client3:**
"And if we check client3's stream... no 'Hello Client2' message. Verification complete."

---

### Demo Step 4: Show Broadcast Message

**Send:**
```
Client1: BROADCAST:Hello Everyone
```

**Show in Wireshark:**
```
Filter: tcp.port == 5000 and tcp contains "Hello Everyone"
```

**Point out:**
"Notice we now see 4 packets:
1. Client1 → Server
2. Server → Client1 (echo)
3. Server → Client2
4. Server → Client3

All clients received the broadcast."

---

### Demo Step 5: Show Non-Coordinator Leaving

**Action:**
Kill client3 (Ctrl+C)

**Show in Wireshark:**
```
Filter: tcp.flags.fin == 1 or tcp.flags.reset == 1
```

**Explain:**
"This FIN packet shows client3 gracefully disconnecting. The TCP connection is properly closed."

**Then show communication continues:**
```
Client1: PRIVATE:client2:We're still working!
```

**Wireshark:**
```
Filter: tcp.port == 5000 and tcp contains "still working"
```

**Point out:**
"System continues operating. Client1 and client2 can still exchange messages."

---

### Demo Step 6: Show New Client Joining

**Action:**
```bash
java Client client4 127.0.0.1 5000
```

**Show in Wireshark:**
```
Filter: tcp.dstport == 5000 and tcp.flags.syn == 1
```

**Point out:**
"New SYN packet from client4. And here's the SYN-ACK response. Connection established."

**Follow new stream:**
```
CONNECT:client4
WELCOME:COORDINATOR:client1
```

**Show updated member list:**

From client2, request:
```
GET_MEMBERS
```

**Wireshark:**
```
Filter: tcp.port == 5000 and tcp contains "MEMBERS"
```

Should now show 3 members: client1, client2, client4 (client3 gone).

---

### Demo Step 7: Show Coordinator Failover (Critical!)

**Action:**
Kill client1 (coordinator) - Ctrl+C

**Show disconnection:**
```
Filter: tcp.port == 5000 and tcp.flags.fin == 1
```

"Client1 (coordinator) is disconnecting. Watch what happens..."

**Show election:**
```
Filter: tcp.port == 5000 and tcp.len > 0
```

Look for:
```
COORDINATOR_LEFT:client1
ELECTION_START
NEW_COORDINATOR:client2
```

**Wait 20 seconds and show new pings:**
```
Filter: tcp.port == 5000 and tcp contains "PING"
```

**Point out:**
"Notice the source port changed. Previously pings came from port 54321 (client1), now they come from port 54322 (client2). Client2 is the new coordinator!"

**Test communication:**
```
Client4: BROADCAST:New coordinator works!
```

**Wireshark verification:**
```
Filter: tcp.port == 5000 and tcp contains "New coordinator"
```

All remaining clients receive the message.

---

## Common Issues and Wireshark Solutions

### Issue 1: Messages Concatenated

**Symptom:** Multiple messages appear as one packet

**Example in TCP Stream:**
```
MSG1:HelloMSG2:WorldMSG3:Test
```

**Solution:** Add message delimiters/separators

**Use newline:**
```java
writer.println("MSG1:Hello");
writer.println("MSG2:World");
```

**Verify in Wireshark:**
Each message should appear on separate line in TCP stream.

---

### Issue 2: Coordinator Not Detected as Leaving

**Symptom:** No election triggered

**Wireshark Debug:**

1. Verify connection actually closed:
```
Filter: tcp.flags.fin == 1
```

2. Check if server detected it within 20 seconds:
```
Filter: tcp.port == 5000 and tcp contains "ELECTION"
```

3. If no ELECTION message after 20+ seconds:
   - Server's ping mechanism not detecting dead client
   - Check exception handling in server code

**Timing Analysis:**

Find when connection closed:
```
Filter: tcp.flags.fin == 1
```
Time: 45.123 seconds

Find when election started:
```
Filter: tcp contains "ELECTION"
```
Time: 65.234 seconds

**Gap:** 65.234 - 45.123 = 20.111 seconds

This confirms detection happened on next ping cycle. ✓

---

### Issue 3: Ping Not Happening Every 20 Seconds

**Symptom:** Irregular ping intervals

**Wireshark Debug:**

```
Filter: tcp.port == 5000 and tcp contains "PING"
```

**Measure intervals:**

| Packet | Time | Interval |
|--------|------|----------|
| 1 | 20.1 | - |
| 2 | 35.4 | 15.3s ❌ |
| 3 | 60.2 | 24.8s ❌ |

**Diagnosis:** Ping timer not working correctly

**Check:**
- Is timer in separate thread?
- Sleep duration correct?
- Thread being interrupted?

**Code to review:**
```java
// Should be something like:
while (running) {
    Thread.sleep(20000);  // 20 seconds
    pingAllClients();
}
```

---

### Issue 4: Race Condition in Message Delivery

**Symptom:** Sometimes messages delivered out of order

**Wireshark Debug:**

1. Send rapid messages:
```
Client1: MSG1
Client1: MSG2
Client1: MSG3
```

2. Follow TCP stream for client2

3. **Check order:**
```
Expected:
MSG1
MSG2
MSG3

Actual (if bug):
MSG1
MSG3
MSG2
```

4. **Analyze packet timing:**
```
Filter: tcp.port == 5000 and tcp contains "MSG"
```

Look at sequence numbers:
```
Packet 10: Seq=100, Len=10 (MSG1)
Packet 11: Seq=110, Len=10 (MSG2)
Packet 12: Seq=90, Len=10 (MSG3) ← Out of order!
```

**Diagnosis:** Threading issue in server

**Solution:** Synchronize message sending

---

## Advanced Demo Techniques

### Technique 1: Pre-Mark Important Packets

During test run BEFORE demo:

1. Run complete scenario
2. Find key packets (connection, election, messages)
3. Right-click → Mark Packet (Ctrl+M)
4. Marked packets highlighted

During demo:
- Navigate quickly: Shift+Ctrl+N (next marked)
- Jump to important moments instantly

### Technique 2: Save Display Filters

1. Create useful filters
2. Click bookmark icon next to filter bar
3. Save with descriptive name
4. Apply instantly during demo

**Recommended saved filters:**
- "New Connections"
- "Data Only"
- "Coordinator Pings"
- "Errors"

### Technique 3: Use Coloring Rules

View → Coloring Rules → Add

**Example:**
- Name: "My Server Traffic"
- Filter: `tcp.port == 5000`
- Foreground: Black
- Background: Light yellow

Your packets stand out!

### Technique 4: Export Key Packets

Before demo:
1. Filter to critical packets
2. File → Export Specified Packets → Displayed
3. Save as `demo_evidence.pcapng`
4. Load during demo if needed

---

## Grading Rubric Verification with Wireshark

### Group Formation, Connection, and Communication [10 marks]

**Prove with Wireshark:**
```
Filter: tcp.dstport == 5000 and tcp.flags.syn == 1
```
Show N clients connected (N SYN packets).

**Follow streams:** Show messages exchanged without errors.

### Group State Maintenance [5 marks]

**Prove with Wireshark:**
```
Filter: tcp.port == 5000 and tcp contains "MEMBERS"
```
Show state query and response with timestamps.

### Coordinator Selection [5 marks]

**Prove with Wireshark:**

**Initial:**
```
Filter: tcp.port == 5000 and tcp contains "COORDINATOR"
```
Show first client told it's coordinator.

**After failure:**
Show election messages and new coordinator.

### Fault Tolerance [10 marks]

**Prove with Wireshark:**

**Scenario A: Member leaves**
- Show FIN packets
- Show continued communication

**Scenario B: Coordinator leaves**
- Show FIN packets
- Show election process
- Show new coordinator active

---

## Report Writing: Using Wireshark Evidence

### Including Screenshots in Report

**Good Screenshots to Include:**

1. **TCP Handshake**
   - Shows protocol understanding
   - Caption: "Figure 1: TCP three-way handshake establishing client connection"

2. **Message Flow**
   - Shows broadcast distribution
   - Caption: "Figure 2: Broadcast message delivery to all clients"

3. **Coordinator Election**
   - Shows fault tolerance
   - Caption: "Figure 3: Coordinator failover sequence captured in network traffic"

### How to Take Good Screenshots

1. Apply relevant filter
2. Adjust column widths
3. Select interesting packet
4. Expand relevant details
5. Edit → Preferences → Appearance → Font (increase for readability)
6. Take screenshot (Print Screen)
7. Annotate with arrows/labels

---

## Quick Demo Checklist

Print this and follow during demonstration:

### Before Demo
- [ ] Server running
- [ ] 3 clients connected
- [ ] Wireshark capturing
- [ ] Know your port number
- [ ] Test filters work
- [ ] Backup capture saved

### During Demo Part A

- [ ] Show 3 TCP conversations (connections)
- [ ] Show coordinator ping packets
- [ ] Send private message, show 2 packets
- [ ] Send broadcast message, show N+1 packets
- [ ] Kill non-coordinator, show FIN
- [ ] Send another message, still works
- [ ] Start new client, show SYN
- [ ] Kill coordinator, show FIN
- [ ] Show election messages
- [ ] Show new coordinator pings

### During Demo Part B

- [ ] Explain most challenging design pattern
- [ ] Show relevant code
- [ ] Explain most challenging test
- [ ] Show test code

---

## TShark: Command-Line Alternative

If GUI not available during demo, use tshark:

```bash
# Capture to file
tshark -i lo -w demo.pcapng port 5000

# Read and display with filter
tshark -r demo.pcapng -Y "tcp.port == 5000 and tcp.len > 0"

# Show specific fields
tshark -r demo.pcapng -T fields -e frame.time -e ip.src -e tcp.srcport -e ip.dst -e tcp.dstport

# Follow stream (connection 0)
tshark -r demo.pcapng -z follow,tcp,ascii,0

# Statistics
tshark -r demo.pcapng -q -z conv,tcp
```

---

## Troubleshooting Your Demo

### Problem: Wireshark Shows Nothing

**Quick Fixes:**
1. Check interface: should be loopback/lo
2. Check filter: remove it temporarily
3. Verify server running: `netstat -an | grep 5000`
4. Restart capture

### Problem: Too Much Traffic

**Quick Fix:**
```
Filter: ip.addr == 127.0.0.1 and tcp.port == 5000
```

Or stop other applications (browsers, etc.)

### Problem: Can't Find Specific Message

**Quick Fix:**
1. Edit → Find Packet (Ctrl+F)
2. Select "String" search
3. Enter your message text
4. Find

### Problem: Stream Shows Gibberish

**Quick Fix:**
1. Check you're following correct stream
2. Verify you're sending text not binary
3. Check character encoding

---

## Sample Protocol Messages to Look For

Based on common implementations:

### Connection Phase
```
CONNECT:clientID:timestamp
WELCOME:role:coordinatorID
```

### Messaging Phase
```
PRIVATE:senderID:receiverID:message
BROADCAST:senderID:message
ACK:messageID
```

### State Management
```
PING:coordinatorID:clientID
PONG:clientID:timestamp
GET_MEMBERS
MEMBERS:count:id1:ip1:port1:id2:ip2:port2:...
```

### Coordinator Management
```
COORDINATOR_LEFT:oldCoordinatorID
ELECTION_START
VOTE:voterID:candidateID
NEW_COORDINATOR:newCoordinatorID
```

### Disconnection
```
DISCONNECT:clientID
BYE:clientID
CLIENT_LEFT:clientID
```

Adapt filters based on YOUR protocol design!

---

## Wireshark Commands for Demo Day

Have these ready on a notepad:

```plaintext
# Show all traffic
tcp.port == 5000

# Show connections
tcp.flags.syn == 1

# Show data only
tcp.port == 5000 and tcp.len > 0

# Show pings
tcp.port == 5000 and tcp contains "PING"

# Show specific message
tcp.port == 5000 and tcp contains "YOUR_MESSAGE_HERE"

# Show disconnections
tcp.flags.fin == 1 or tcp.flags.reset == 1

# Show problems
tcp.analysis.flags
```

Just copy-paste into filter bar!

---

## Final Tips for Success

### 1. Practice With Wireshark Before Demo
- Run through complete scenario 3+ times
- Know where to find each requirement
- Time yourself - demo is 5-10 minutes

### 2. Prepare Backup Evidence
- Save successful capture files
- Take screenshots
- If live demo fails, show pre-captured evidence

### 3. Explain While Showing
Good: "Here we see the TCP handshake completing, followed by the data transfer."

Better: "At packet 15, client1 sends SYN. Packet 16 shows server's SYN-ACK response within 0.5 milliseconds. Packet 17 completes the handshake with ACK. The connection is now established and we can see the first application data in packet 18 where client1 sends its ID."

### 4. Connect to Coursework Requirements

**For each Wireshark finding, link to requirements:**

"This demonstrates the **fault tolerance** requirement because..."
"This verifies the **group state maintenance** requirement because..."
"This shows the **coordinator selection** algorithm working as..."

### 5. Show Understanding of Network Concepts

Mention:
- TCP reliability mechanisms
- Three-way handshake
- Connection states
- Port numbers
- Sequence/acknowledgment numbers

This shows you understand networking beyond just coding!

---

## Conclusion

Using Wireshark for your COMP1549 project:

**Benefits:**
✓ Faster debugging
✓ Concrete evidence requirements are met
✓ Demonstrates deep technical understanding
✓ Impresses professors during demo
✓ Helps diagnose subtle bugs
✓ Validates protocol design
✓ Measures performance

**Recommendations:**
1. Use Wireshark throughout development, not just at the end
2. Save captures for each milestone
3. Include Wireshark analysis in your report (screenshots)
4. Practice explaining captures for demo
5. Use it to verify ALL requirements systematically

**Good luck with your coursework!**

Remember: Wireshark is your window into the network. Use it to see exactly what's happening!
