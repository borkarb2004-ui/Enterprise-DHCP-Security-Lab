# Enterprise DHCP Security Lab

### DHCP Snooping | Rogue DHCP Prevention | OSPF | Cisco Packet Tracer

**Author:** Bhagyashri Borkar
**Project Type:** Enterprise Networking & Network Security
**Platform:** Cisco Packet Tracer
**Focus:** CCNA Practical Lab | DHCP Security | Routing | Troubleshooting

---

## 1. Project Overview

The Enterprise DHCP Security Lab is a network simulation project developed using Cisco Packet Tracer. It demonstrates an enterprise network containing three departments: HR, IT, and Operations.

The network includes five PCs, one router, one switch, and two DHCP servers. DHCP Snooping is used to demonstrate protection against unauthorized DHCP server messages. OSPF is configured as part of the routing practice for the Operations network.

The project covers enterprise network design, DHCP communication, trusted and untrusted ports, rogue DHCP testing, OSPF configuration, packet inspection, and connectivity verification.

## 2. Project Objectives

* Design a multi-department enterprise network.
* Understand the DHCP DORA process.
* Configure DHCP services for client devices.
* Implement DHCP Snooping on a supported Cisco switch.
* Configure Trusted and Untrusted switch ports.
* Simulate and test a Rogue DHCP server.
* Configure and verify OSPF routing.
* Inspect DHCP packets using Packet Tracer Simulation Mode.
* Verify IP addressing, routing, and network connectivity.
* Document practical networking commands and test results.

## 3. Tools and Technologies

* Cisco Packet Tracer
* Cisco IOS CLI
* DHCP and DHCP Snooping
* IPv4 Addressing
* Layer 2 Switching
* OSPF Routing
* ICMP / Ping
* Network Security Fundamentals

## 4. Network Components

| Device  | Quantity | Purpose                                     |
| ------- | -------: | ------------------------------------------- |
| PCs     |        5 | Department client devices                   |
| Router  |        1 | Routing and gateway services, as configured |
| Switch  |        1 | LAN connectivity and DHCP Snooping          |
| Server1 |        1 | Authorized DHCP server                      |
| Server2 |        1 | Rogue DHCP testing server                   |

## 5. Department Mapping

| Device | Department |
| ------ | ---------- |
| PC1    | HR         |
| PC2    | HR         |
| PC3    | IT         |
| PC4    | Operations |
| PC5    | Operations |

## 6. Network Topology

```text
                    Router0
                       |
                       |
                    Switch0
       ________________|________________
      |       |       |       |         |
     PC1     PC2     PC3     PC4       PC5
      HR      HR      IT   Operations Operations
                       |
                 DHCP Servers
                 /           \
             Server1        Server2
           Authorized        Rogue
              DHCP            DHCP
```

**Note:** This is a logical overview. Connect the devices according to the actual Packet Tracer file. OSPF runs on supported Layer 3 router interfaces, not on ordinary PCs. With one router, OSPF can be configured and verified locally, but actual OSPF neighbor adjacency requires another OSPF-speaking router.

## 7. IP Addressing Plan

Use this example as a template and replace it with the actual IP addresses used in your lab.

| Device                | Example IP    | Subnet Mask                    |
| --------------------- | ------------- | ------------------------------ |
| Router0 LAN interface | 192.168.10.1  | 255.255.255.0                  |
| Server1               | 192.168.10.10 | 255.255.255.0                  |
| Server2               | 192.168.10.20 | 255.255.255.0                  |
| PC1 and PC2           | DHCP assigned | 255.255.255.0                  |
| PC3                   | DHCP assigned | 255.255.255.0                  |
| PC4 and PC5           | DHCP assigned | According to the actual subnet |

This is an example only. If your departments use different VLANs or subnets, document each subnet and gateway separately. DHCP Snooping must be enabled on the relevant VLANs.

## 8. Step 1 — Build the Network

1. Open Cisco Packet Tracer.
2. Add five PCs, one router, one switch, and two servers.
3. Connect all devices according to your topology.
4. Use suitable copper Ethernet cables for Ethernet connections.
5. Configure IP addresses and subnet masks on the servers.
6. Configure router interfaces and bring them up.
7. Wait for the link indicators to become green.

### Router interface example

```text
enable
configure terminal
interface gigabitEthernet0/0
ip address 192.168.10.1 255.255.255.0
no shutdown
exit
end
show ip interface brief
```

Use the correct interface name and IP address for your router.

## 9. Step 2 — Configure Server1 (Authorized DHCP)

1. Click Server1.
2. Open Desktop → IP Configuration.
3. Configure a static IP address.
4. Open Services → DHCP.
5. Turn DHCP Service ON.
6. Create the authorized DHCP pool.
7. Configure the starting IP, subnet mask, gateway, and DNS values.
8. Click Add or Save, depending on the Packet Tracer version.

Example DHCP pool:

```text
Pool Name: AUTHORIZED_POOL
Start IP: 192.168.10.100
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.10.1
DNS Server: According to your DNS configuration
```

The DHCP pool must use the correct network and must not allocate addresses already assigned statically.

## 10. Step 3 — Configure Server2 (Rogue DHCP)

1. Click Server2.
2. Open Desktop → IP Configuration.
3. Configure a unique static IP address.
4. Open Services → DHCP.
5. Turn DHCP Service ON.
6. Create a separate pool named `ROGUE_POOL`.
7. Use a test address range and deliberately different gateway/DNS values.

This server is used only for controlled security testing.

**Security objective:** Demonstrate that DHCP Snooping filters unauthorized DHCP server messages arriving on an untrusted switch port.

## 11. Step 4 — Enable DHCP Snooping

Open Switch0 → CLI.

```text
enable
configure terminal
ip dhcp snooping
ip dhcp snooping vlan 1
```

These commands enable DHCP Snooping globally and for VLAN 1.

Replace VLAN 1 with the actual VLAN used in your lab. If the client and servers are in multiple VLANs, enable snooping for each relevant VLAN.

## 12. Step 5 — Configure Trusted and Untrusted Ports

First identify which switch interfaces connect to Server1, Server2, and the PCs.

### Authorized server interface

Example: Server1 is connected to FastEthernet0/2.

```text
interface fastEthernet0/2
description AUTHORIZED_DHCP_SERVER
ip dhcp snooping trust
exit
```

### Rogue server interface

Example: Server2 is connected to FastEthernet0/3.

```text
interface fastEthernet0/3
description ROGUE_DHCP_SERVER
no ip dhcp snooping trust
exit
```

### Client interfaces

Keep PC-facing interfaces untrusted.

```text
interface range fastEthernet0/4 - 8
no ip dhcp snooping trust
exit
end
```

The interface range above is only an example. Use it only if those interfaces actually connect to client PCs and the switch supports the command.

**Important:** Trusted status is assigned to the switch interface. The port connected to the authorized DHCP server is trusted; the rogue server and ordinary client ports should normally remain untrusted.

## 13. Step 6 — Verify DHCP Snooping

Run these commands on Switch0:

```text
show ip dhcp snooping
show ip dhcp snooping binding
show running-config
```

Verify:

* DHCP Snooping is enabled.
* The correct VLAN is protected.
* The authorized server port is trusted.
* The rogue server port is untrusted.
* DHCP bindings appear after clients obtain valid leases.

A binding table may initially be empty if no client has completed DHCP address assignment.

## 14. Step 7 — Understand the DHCP DORA Process

DORA represents the four stages of DHCP address assignment.

1. **Discover:** The client broadcasts a message to find DHCP servers.
2. **Offer:** A DHCP server offers an IP address.
3. **Request:** The client requests the offered configuration.
4. **Acknowledgment:** The server confirms the lease.

```text
PC Client           Switch           DHCP Server
    |                  |                  |
    |--- Discover ---->|----------------->|
    |<---- Offer ------|<-----------------|
    |--- Request ----->|----------------->|
    |<----- ACK -------|<-----------------|
```

The diagram shows the normal DHCP exchange. During Rogue DHCP testing, an unauthorized Offer received on an untrusted interface should be filtered by a correctly configured DHCP Snooping switch.

## 15. Step 8 — Test Rogue DHCP Prevention

1. Confirm DHCP Snooping is enabled.
2. Confirm Server1's port is trusted.
3. Confirm Server2's port is untrusted.
4. Keep both DHCP services ON for the controlled test.
5. On a PC, open Desktop → IP Configuration.
6. Select DHCP to initiate a fresh address request.
7. Switch to Simulation Mode.
8. Open Edit Filters and select DHCP.
9. Click Capture/Forward to inspect packets.
10. Select packet events and inspect PDU details.
11. Verify whether the switch drops DHCP server messages arriving from Server2.

### Expected result

DHCP Snooping should filter unauthorized server messages arriving on the rogue server's untrusted port.

Do not use a red X alone as proof. Inspect the packet direction, event details, and drop reason to confirm that DHCP Snooping caused the drop.

## 16. Step 9 — Configure OSPF

OSPF is a dynamic routing protocol that exchanges routing information between participating Layer 3 routers.

**Important topology note:** Your current project contains one router. OSPF can be configured on this router, but a meaningful neighbor-adjacency demonstration requires at least one additional OSPF router connected through a Layer 3 link.

Example configuration for an existing router interface and a matching network:

```text
enable
configure terminal
router ospf 1
network 192.168.10.0 0.0.0.255 area 0
end
```

Replace the network statement with the actual router-connected network. Configure OSPF on the appropriate router interfaces and advertise only the intended networks.

### OSPF Verification

```text
show ip ospf
show ip ospf neighbor
show ip route ospf
show ip protocols
```

These commands help verify the local OSPF process, neighbors, learned OSPF routes, and routing protocol settings.

With only one router, the neighbor table may be empty and no OSPF-learned routes may appear. This is expected without another participating router.

## 17. Step 10 — Verify IP Configuration and Connectivity

On each PC:

1. Open Desktop → IP Configuration.
2. Select DHCP.
3. Verify the assigned IP address, subnet mask, gateway, and DNS settings.
4. Open Desktop → Command Prompt.

Example commands:

```text
ipconfig
ping 192.168.10.10
ping 192.168.10.20
```

Replace the IP addresses with the actual server addresses.

A successful ping confirms reachability between the tested devices. DHCP address assignment, gateway configuration, VLAN membership, and routing must all be correct for the intended connectivity tests.

## 18. Troubleshooting

### DHCP address not assigned

* Check that the authorized DHCP service is ON.
* Verify the DHCP pool and available addresses.
* Check the client VLAN and server VLAN.
* Verify the trusted server interface.
* Confirm that DHCP Snooping is enabled for the correct VLAN.

### Rogue DHCP Offer not dropped

* Verify the actual switch interface connected to Server2.
* Confirm the port is untrusted.
* Check the protected VLAN.
* Confirm Server2 is sending DHCP Offer messages.
* Inspect Simulation Mode PDU details and event messages.

### OSPF neighbor not appearing

* Confirm that another OSPF-speaking router exists and is connected.
* Check IP addressing and subnet masks on the Layer 3 link.
* Verify OSPF process and area configuration.
* Use `show ip ospf neighbor` and `show ip protocols`.

### Ping fails

* Verify the destination IP.
* Check interface status and VLAN configuration.
* Confirm the client has the correct gateway.
* Check routes and return paths between different networks.

## 19. Test Results

Update the results after performing each test in Packet Tracer.

| Test                             | Actual Result                             |
| -------------------------------- | ----------------------------------------- |
| Network topology and cabling     | To be documented                          |
| DHCP Snooping enabled            | To be documented                          |
| Authorized DHCP lease            | To be documented                          |
| Trusted/Untrusted ports verified | To be documented                          |
| Rogue DHCP message drop          | To be documented                          |
| OSPF configuration               | Configured; verification to be documented |
| Client connectivity              | To be documented                          |

## 20. Screenshots and Repository Structure

Recommended structure:

```text
Enterprise-DHCP-Security-Lab/
├── README.md
├── packet-tracer/
│   └── enterprise-dhcp-security.pkt
└── screenshots/
    ├── network-topology.png
    ├── server1-dhcp.png
    ├── server2-rogue-dhcp.png
    ├── dhcp-snooping-config.png
    ├── trusted-untrusted-ports.png
    ├── dora-process.png
    ├── rogue-dhcp-test.png
    └── ospf-verification.png
```

Upload your actual Packet Tracer file and screenshots. The filenames above are suggestions.

## 21. Learning Outcomes

* Enterprise network design using Cisco Packet Tracer.
* DHCP client-server communication.
* DHCP Snooping configuration and verification.
* Trusted and Untrusted switch port security.
* Rogue DHCP server testing.
* OSPF configuration and routing verification.
* Packet inspection and network troubleshooting.
* Documentation of practical CCNA networking skills.

## 22. Conclusion

This project demonstrates enterprise network design, DHCP security, and routing practice using Cisco Packet Tracer. It provides hands-on experience with DHCP Snooping, unauthorized DHCP server testing, OSPF commands, packet analysis, and network troubleshooting.

## Author

**Bhagyashri Borkar**
Aspiring Network Engineer | CCNA Learner

GitHub: https://github.com/borkar2004-ui

---

**Disclaimer:** This is an educational network simulation. Device support, commands, and test results depend on the actual topology and Packet Tracer version.
