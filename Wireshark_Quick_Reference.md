# Wireshark Quick Reference Card

## Essential Display Filters

### Protocol Filters
```plaintext
tcp                    # TCP only
udp                    # UDP only
http                   # HTTP only
dns                    # DNS only
arp                    # ARP only
icmp                   # ICMP (ping) only
```

### IP Address Filters
```plaintext
ip.addr == 192.168.1.1         # Traffic to/from this IP
ip.src == 192.168.1.1          # Traffic from this IP
ip.dst == 192.168.1.1          # Traffic to this IP
ip.addr == 127.0.0.1           # Localhost traffic
```

### Port Filters
```plaintext
tcp.port == 5000               # TCP traffic on port 5000
tcp.srcport == 5000            # Source port 5000
tcp.dstport == 5000            # Destination port 5000
udp.port == 53                 # UDP port 53 (DNS)
tcp.port >= 1024               # Ephemeral ports
```

### TCP Flags
```plaintext
tcp.flags.syn == 1             # Connection requests
tcp.flags.ack == 1             # Acknowledgments
tcp.flags.fin == 1             # Connection closures
tcp.flags.reset == 1           # Connection resets
tcp.flags.push == 1            # Push data
tcp.flags.syn == 1 and tcp.flags.ack == 0    # SYN only (new connections)
tcp.flags.syn == 1 and tcp.flags.ack == 1    # SYN-ACK (server responses)
```

### Content Filters
```plaintext
tcp contains "Hello"           # TCP packets containing "Hello"
udp contains "ping"            # UDP packets containing "ping"
http.request.uri contains "api"     # HTTP requests with "api" in URI
frame contains "password"      # Any packet containing "password" (security check!)
```

### Length Filters
```plaintext
tcp.len > 0                    # TCP packets with data
tcp.len > 100                  # Large TCP packets
frame.len > 1000               # Large frames
```

### Logical Operators
```plaintext
tcp.port == 5000 and ip.addr == 127.0.0.1    # AND
tcp.port == 5000 or tcp.port == 8080         # OR
not arp                                       # NOT
(tcp.port == 5000) and not (tcp.len == 0)    # Parentheses
```

### Time-Based Filters
```plaintext
frame.time_relative > 10       # Packets after 10 seconds
tcp.time_delta > 1             # Large gaps between packets
```

### Error and Diagnostic Filters
```plaintext
tcp.analysis.retransmission    # Retransmitted packets
tcp.analysis.duplicate_ack     # Duplicate ACKs
tcp.analysis.lost_segment      # Lost segments
tcp.analysis.flags             # TCP problems
icmp.type == 3                 # Destination unreachable
```

---

## Common Capture Filters

```plaintext
port 5000                      # Specific port
host 127.0.0.1                 # Specific host
tcp                            # TCP only
udp                            # UDP only
not port 22                    # Exclude SSH
host 127.0.0.1 and port 5000   # Localhost on specific port
portrange 1000-2000            # Port range
```

---

## TCP Connection States

```
LISTEN       # Server waiting for connections
SYN-SENT     # Client sent SYN, waiting for SYN-ACK
SYN-RECEIVED # Server sent SYN-ACK, waiting for ACK
ESTABLISHED  # Connection established, data can flow
FIN-WAIT-1   # Closing connection, sent FIN
FIN-WAIT-2   # Received ACK, waiting for FIN
CLOSE-WAIT   # Remote side closed, waiting to close locally
CLOSING      # Both sides closing simultaneously
LAST-ACK     # Waiting for final ACK
TIME-WAIT    # Waiting to ensure remote received closure
CLOSED       # Connection fully closed
```

---

## Right-Click Menu Options

```
Follow → TCP Stream            # See complete conversation
Follow → UDP Stream            # See UDP conversation
Copy → Value                   # Copy field value
Copy → Bytes                   # Copy raw bytes
Export Packet Bytes            # Save packet data to file
Protocol Preferences           # Configure protocol settings
Decode As...                   # Treat as different protocol
```

---

## Useful Statistics

Access via Statistics menu:

```
Capture File Properties        # Overview of capture
Protocol Hierarchy             # Protocols breakdown
Conversations                  # Who talked to whom
Endpoints                      # Active hosts
I/O Graphs                     # Traffic over time
Flow Graph                     # Visual flow diagram
```

---

## Color Meaning (Default)

| Color | Meaning |
|-------|---------|
| Light Purple | TCP traffic |
| Light Blue | UDP traffic |
| Black | Packets with errors |
| Light Green | HTTP traffic |
| Yellow | Windows-specific traffic |
| Dark Gray | TCP packets with problems |
| Red | Error/problem packets |

Customize: View → Coloring Rules

---

## Analysis Workflow

### 1. Initial Capture
```
Select Interface → Start Capture → Generate Traffic → Stop Capture
```

### 2. Overview
```
Statistics → Capture File Properties
Statistics → Protocol Hierarchy
```

### 3. Filter Down
```
Apply display filter to focus on relevant traffic
```

### 4. Investigate
```
Follow streams
Examine packet details
Check timing
Look for errors
```

### 5. Export/Save
```
File → Save As (for later analysis)
File → Export Specified Packets (filtered subset)
```

---

## Common Scenarios & Solutions

### Scenario: "My client can't connect"
**Filter:** `tcp.flags.syn == 1 or tcp.flags.reset == 1`
**Look For:** RST response = server not listening

### Scenario: "Messages are slow"
**Filter:** `tcp.port == 5000 and tcp.analysis.ack_rtt`
**Look For:** High RTT values

### Scenario: "Connection keeps dropping"
**Filter:** `tcp.flags.fin == 1 or tcp.flags.reset == 1`
**Look For:** Unexpected FIN/RST packets

### Scenario: "Server not responding to pings"
**Filter:** `tcp.port == 5000 and tcp.len > 0`
**Look For:** PING packets without PONG responses

### Scenario: "Broadcast not reaching all clients"
**Follow TCP streams** for each client connection
**Look For:** Missing broadcasts to specific clients

---

## Command-Line (TShark) Quick Reference

```bash
# Capture on interface
tshark -i lo port 5000

# Capture to file
tshark -i lo -w capture.pcapng port 5000

# Read from file
tshark -r capture.pcapng

# With display filter
tshark -r capture.pcapng -Y "tcp.port == 5000"

# Show only specific fields
tshark -r capture.pcapng -T fields -e ip.src -e ip.dst -e tcp.port

# Statistics
tshark -r capture.pcapng -q -z conv,tcp
tshark -r capture.pcapng -q -z io,phs
```

---

## Keyboard Shortcuts Cheat Sheet

### Navigation
- **↑/↓**: Move between packets
- **←/→**: Collapse/expand packet details
- **Ctrl+↑/↓**: Previous/next packet in conversation
- **Ctrl+.**: Auto-scroll during live capture
- **Ctrl+Home**: Go to first packet
- **Ctrl+End**: Go to last packet

### Capture
- **Ctrl+E**: Start/stop capture
- **Ctrl+K**: Capture options
- **F5**: Reload capture file

### View
- **Ctrl+F**: Find packet
- **Ctrl+N**: Find next
- **Ctrl+B**: Find previous
- **Ctrl+G**: Go to packet number
- **Ctrl+M**: Mark/unmark packet
- **Shift+Ctrl+N**: Next marked packet

### Analysis
- **Ctrl+Alt+Shift+T**: Follow TCP stream
- **Ctrl+Alt+Shift+U**: Follow UDP stream

---

## Protocol Port Numbers (Common)

| Port | Protocol | Description |
|------|----------|-------------|
| 20/21 | FTP | File Transfer |
| 22 | SSH | Secure Shell |
| 23 | Telnet | Remote access |
| 25 | SMTP | Email sending |
| 53 | DNS | Domain names |
| 80 | HTTP | Web traffic |
| 110 | POP3 | Email receiving |
| 143 | IMAP | Email |
| 443 | HTTPS | Secure web |
| 3306 | MySQL | Database |
| 5000 | Custom | Your app! |
| 8080 | HTTP-Alt | Alternative web |

---

## Performance Tips

### 1. Use Capture Filters
Don't capture everything - filter at capture time:
```
port 5000
```

### 2. Stop Capture When Done
Don't leave it running - file size grows quickly

### 3. Use Display Filters
Narrow down to what you need:
```
tcp.port == 5000 and tcp.len > 0
```

### 4. Disable Unnecessary Protocol Dissectors
Edit → Preferences → Protocols → Disable unused protocols

### 5. Increase Memory (for large captures)
Edit → Preferences → Appearance → Layout

---

## Security Note

**IMPORTANT:**
- Never capture traffic on networks you don't own/have permission to monitor
- Don't share packet captures containing sensitive data
- Be aware captures may contain passwords, tokens, private information
- Use Wireshark only for authorized testing and learning

---

## Sample Filters for COMP1549 Project

```plaintext
# Monitor your entire server communication
tcp.port == 5000

# See only new connections to server
tcp.dstport == 5000 and tcp.flags.syn == 1 and tcp.flags.ack == 0

# See only data messages (no handshakes)
tcp.port == 5000 and tcp.len > 0

# Find coordinator ping messages (if you send "PING" in your protocol)
tcp.port == 5000 and tcp contains "PING"

# Find broadcast messages (if you prefix with "BROADCAST:")
tcp.port == 5000 and tcp contains "BROADCAST"

# Find private messages (if you prefix with "PRIVATE:")
tcp.port == 5000 and tcp contains "PRIVATE"

# Monitor all localhost communication for testing
ip.addr == 127.0.0.1

# See connection problems
tcp.analysis.flags or tcp.flags.reset == 1

# Measure server response time
tcp.port == 5000 and tcp.analysis.ack_rtt

# Find large messages
tcp.port == 5000 and tcp.len > 512
```

---

## Emergency Debug Commands

When things aren't working, try these filters:

```plaintext
# 1. Are packets being sent at all?
tcp.port == 5000

# 2. Is the handshake completing?
tcp.flags.syn == 1 or tcp.flags.reset == 1

# 3. Are there any errors?
tcp.analysis.flags

# 4. Is data being transmitted?
tcp.port == 5000 and tcp.len > 0

# 5. Follow the complete conversation
Right-click → Follow → TCP Stream
```

---

**Print this page and keep it next to you while debugging your coursework!**
