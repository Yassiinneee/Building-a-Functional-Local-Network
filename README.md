# LAN Network Design, Subnetting, and Routing Analysis

## Project Overview

This project demonstrates the design and analysis of a small Local Area Network (LAN) for a company floor. The objective is to understand how devices communicate through the Data Link Layer (Layer 2) and Network Layer (Layer 3) of the OSI model.

The network includes:

- 1 Router with Internet access
- 2 Switches
- 4 Employee PCs
- 1 Network Printer
- 1 Wireless Access Point (AP)
- 2 Wireless Devices (Laptop and Smartphone)

The project covers:

- MAC Addressing
- Ethernet Frame Structure
- ARP Communication
- Collision Handling
- IP Addressing
- Subnetting
- DHCP
- Routing
- Packet Forwarding
- NAT (Network Address Translation)

---

# Network Topology

```text
                    INTERNET
                        |
                    [Router]
                        |
        ---------------------------------
        |                               |
     [Switch1]                     [Switch2]
        |                               |
   ------------                  -------------
   |    |     |                  |    |      |
  PC1  PC2 Printer             PC3  PC4   Access Point
                                              |
                                       ----------------
                                       |              |
                                    Laptop       Smartphone
```

The router acts as the gateway between the internal network and the Internet.

Switches connect wired devices.

The Access Point provides wireless connectivity.

---

# Part 1: MAC Addressing and Frame Handling

## MAC Address Assignment

Each network device is assigned a unique MAC address.

| Device | MAC Address |
|----------|------------|
| Router | 00:AA:BB:CC:DD:01 |
| Switch 1 | 00:AA:BB:CC:DD:11 |
| Switch 2 | 00:AA:BB:CC:DD:12 |
| PC1 | 00:AA:BB:CC:DD:21 |
| PC2 | 00:AA:BB:CC:DD:22 |
| PC3 | 00:AA:BB:CC:DD:23 |
| PC4 | 00:AA:BB:CC:DD:24 |
| Printer | 00:AA:BB:CC:DD:30 |
| Access Point | 00:AA:BB:CC:DD:40 |
| Laptop | 00:AA:BB:CC:DD:50 |
| Smartphone | 00:AA:BB:CC:DD:51 |

---

## Ethernet Frame Structure

When PC1 sends a print request to the printer, an Ethernet frame is created.

### Frame Format

```text
+------------------------------------------------+
| Destination MAC | Source MAC | Data | FCS |
+------------------------------------------------+
```

### Example

- Destination MAC: Printer MAC
- Source MAC: PC1 MAC
- Data: Print request
- FCS: Error detection information

### Communication Process

1. PC1 checks if the printer is on the same subnet.
2. PC1 sends an ARP request.
3. Printer replies with its MAC address.
4. PC1 builds the Ethernet frame.
5. Switch forwards the frame.
6. Printer receives the print job.

---

## Collision Handling

### Wired Ethernet

Modern Ethernet networks use switches and full-duplex communication.

Advantages:

- No shared medium
- Separate collision domain per port
- No collisions

Older Ethernet used CSMA/CD:

1. Listen before transmitting.
2. Transmit if medium is free.
3. Detect collision.
4. Stop transmission.
5. Wait random time.
6. Retransmit.

### Wireless Networks

Wi-Fi uses CSMA/CA.

Process:

1. Listen to the channel.
2. Wait a random backoff time.
3. Send data.
4. Receive acknowledgment.
5. Retransmit if necessary.

This method helps avoid collisions in wireless communication.

---

# Part 2: IP Addressing and Subnetting

## Selected Network

Private IP Range:

```text
192.168.10.0/24
```

Subnet Mask:

```text
255.255.255.0
```

Available Hosts:

```text
254
```

---

## Subnet Design

To separate wired and wireless devices, two subnets are created.

### Wired Network

```text
Network Address: 192.168.10.0/25
Subnet Mask:     255.255.255.128
Host Range:      192.168.10.1 - 192.168.10.126
Broadcast:       192.168.10.127
```

### Wireless Network

```text
Network Address: 192.168.10.128/25
Subnet Mask:     255.255.255.128
Host Range:      192.168.10.129 - 192.168.10.254
Broadcast:       192.168.10.255
```

---

## IP Address Assignment

### Wired Devices

| Device | IP Address |
|----------|------------|
| Router | 192.168.10.1 |
| PC1 | 192.168.10.10 |
| PC2 | 192.168.10.11 |
| PC3 | 192.168.10.12 |
| PC4 | 192.168.10.13 |
| Printer | 192.168.10.50 |

### Wireless Devices

| Device | IP Address |
|----------|------------|
| Router | 192.168.10.129 |
| Access Point | 192.168.10.130 |
| Laptop | 192.168.10.140 |
| Smartphone | 192.168.10.141 |

---

## Default Gateway

### Wired Devices

```text
192.168.10.1
```

### Wireless Devices

```text
192.168.10.129
```

The default gateway is used whenever a device needs to communicate outside its local network.

---

## DHCP

DHCP (Dynamic Host Configuration Protocol) automatically assigns:

- IP Address
- Subnet Mask
- Default Gateway
- DNS Server

### DHCP Process

1. DHCP Discover
2. DHCP Offer
3. DHCP Request
4. DHCP Acknowledgment

Benefits:

- Automatic configuration
- Reduced administration effort
- Fewer configuration errors

---

# Part 3: Routing and Packet Forwarding

## Scenario

A wireless laptop wants to access:

```text
www.example.com
```

---

## Step 1: DNS Resolution

The laptop asks a DNS server for the website's IP address.

DNS Reply:

```text
93.184.216.34
```

---

## Step 2: Packet Creation

Source IP:

```text
192.168.10.140
```

Destination IP:

```text
93.184.216.34
```

The laptop creates an IP packet.

---

## Step 3: Forward to Router

Since the destination is outside the local subnet, the packet is sent to the default gateway.

Path:

```text
Laptop
→ Access Point
→ Router
```

---

## Step 4: Router Reads IP Header

The router examines:

- Source IP
- Destination IP
- Protocol
- TTL
- Header Checksum

The router determines where the packet should be forwarded.

---

## Step 5: Routing Decision

Example Routing Table:

| Destination Network | Next Hop |
|---------------------|----------|
| 192.168.10.0/24 | Local |
| 0.0.0.0/0 | ISP Gateway |

The router selects the default route to reach the Internet.

---

## Step 6: NAT Translation

Private addresses cannot be routed on the Internet.

### Before NAT

```text
192.168.10.140
```

### After NAT

```text
41.225.100.10
```

The router stores this translation in its NAT table.

---

## Step 7: Internet Communication

Packet Path:

```text
Laptop
↓
Access Point
↓
Router
↓
ISP
↓
Internet Routers
↓
Web Server
```

Each router:

1. Reads the destination IP.
2. Searches its routing table.
3. Selects the next hop.
4. Forwards the packet.

---

## Step 8: Response Returns

The web server sends data back.

Path:

```text
Web Server
↓
Internet
↓
ISP
↓
Router
↓
Laptop
```

The router uses its NAT table to identify the correct internal device.

---

# Key Concepts Learned

- Data Link Layer Communication
- MAC Addressing
- ARP Requests and Replies
- Ethernet Frame Structure
- CSMA/CD
- CSMA/CA
- IPv4 Addressing
- Subnetting
- DHCP Operation
- Routing Tables
- Packet Forwarding
- Default Gateway
- NAT Translation
- Internet Communication Flow

---

# Conclusion

This project successfully demonstrates how a small enterprise network operates. It shows how wired and wireless devices communicate locally, how routers forward packets to external networks, and how NAT enables private devices to access the Internet securely.

The design provides an efficient, scalable, and realistic network architecture suitable for a small business environment.