## Project Overview
This repository contains the configuration files and documentation for a small LAN network. The objective is to upgrade a flat network into a properly segmented, highly available, and secure infrastructure based on the CCNA SRWE (Switching, Routing, and Wireless Essentials) framework.

## Topology Architecture
The network is built using a hierarchical design consisting of the following key devices:

* **S1 (Access Switch):** Provides secure access for end-user devices. Configured with PortFast, BPDU Guard, Port Security, and EtherChannel uplinks.
* **S2 (Core/Distribution Switch):** Acts as the Spanning Tree Protocol (STP) Primary Root Bridge. Aggregates EtherChannel links and provides a dedicated trunk uplink to the router.
* **R1 (Internal Router):** Functions as the Inter-VLAN routing gateway (Router-on-a-Stick) and the centralized DHCP server for all internal networks.
* **R2 (ISP Router):** Simulates the external internet/ISP connection with default and static return routing. Contains a loopback interface (`8.8.8.8`) for end-to-end connectivity testing.

---

## 🌐 VLAN & IP Addressing Plan

| VLAN ID | Name / Purpose | Network Subnet | Default Gateway (R1) |
| :--- | :--- | :--- | :--- |
| **10** | Staff | 192.168.10.0 /24 | 192.168.10.1 |
| **20** | Student | 192.168.20.0 /24 | 192.168.20.1 |
| **30** | Guest_Public | 192.168.30.0 /24 | 192.168.30.1 |
| **99** | Management | 192.168.99.0 /24 | 192.168.99.1 |
| **1000** | Native | N/A | N/A |
| **1001** | Unused | N/A | N/A |

* **ISP Point-to-Point Link:** 209.165.200.0 /30

---

## 🔗 Trunking & Port-Channel Architecture

The network utilizes robust Layer 2 interconnects using 802.1Q trunking and LACP EtherChannels to provide redundancy, increased bandwidth, and secure inter-VLAN routing.

| Device | Interface(s) | Logical Group | Mode | Purpose | Security Features |
| :--- | :--- | :--- | :--- | :--- | :--- | 
| **S1-2** | `f0/21` | `Port-Channel 1` | Trunk (LACP Active) | Redundant Uplink to S2 | DHCP Snooping Trust, ARP Trust, Static |
| **S1-2** | `f0/22` | `Port-Channel 1` | Trunk (LACP Active) | Downlink to S1 | DHCP Snooping Trust, ARP Trust, Static |
| **S1-2** | `f0/23` | `Port-Channel 2` | Trunk (LACP Active) | Redundant Uplink to S2 | DHCP Snooping Trust, ARP Trust, Static |
| **S1-2** | `f0/24` | `Port-Channel 2` | Trunk (LACP Active) | Downlink to S1 | DHCP Snooping Trust, ARP Trust, Static |
| **S2** | `g0/1` | N/A (Standalone) | Trunk | Uplink to Inter-VLAN Router (R1) | DHCP Snooping Trust, ARP Trust, Static |
| **R1** | `g0/1` | N/A (Standalone) | Trunk (802.1Q Subinterfaces) | Router-on-a-Stick gateway for S2 | N/A (Router side) |

## Trunk Vlan Table 
| Device | Logical/Trunk Interface | Connection | Allowed VLANs | Native VLAN | 
| :--- | :--- | :--- | :--- | :--- |
| **S1** | `Port-Channel 1` | Uplink to S2 | 10, 20, 30, 99, 1000 | 1000 |
| **S1** | `Port-Channel 2` | Uplink to S2 | 10, 20, 30, 99, 1000 | 1000 |
| **S2** | `Port-Channel 1` | Downlink to S1 | 10, 20, 30, 99, 1000 | 1000 |
| **S2** | `Port-Channel 2` | Downlink to S1 | 10, 20, 30, 99, 1000 | 1000 |
| **S2** | `g0/1` | Uplink to R1 | 10, 20, 30, 99, 1000 | 1000 |


---

## 🌍 Edge Routing & NAT Integration

The internal network connects to the simulated ISP (R2) using a dynamic DHCP link, protected by Network Address Translation (NAT) overload (PAT).

* **Internal Router (R1):**
  * `g0/2` connects to the ISP and dynamically requests an IP address via dhcp.
  * Utilizes an Standard Access Control List NAT to strictly permit only data and management subnets (VLANs 10, 20, 30, and 99) to reach the internet.
  * All internal subnets are mapped to the dynamic `g0/2` IP address using NAT Overload.
  * A default route (`0.0.0.0/0`) directs all unknown traffic out of `g0/2`.

* **ISP Simulator (R2):**
  * `g0/2` acts as the DHCP server for the WAN link, assigning IPs from the `209.165.200.0/24` pool.
  * Hosts a `Loopback 0` interface (`8.8.8.8`) to simulate external internet connectivity for end-to-end ping testing.
