## Project Overview
This repository contains the configuration files and documentation for a small-to-medium enterprise network. The objective is to upgrade a flat network into a properly segmented, highly available, and secure infrastructure based on the CCNA SRWE (Switching, Routing, and Wireless Essentials) framework.

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
| **30** | Guest/Public | 192.168.30.0 /24 | 192.168.30.1 |
| **99** | Management | 192.168.99.0 /24 | 192.168.99.1 |
| **1000** | Native | N/A | N/A |
| **1001** | Unused | N/A | N/A |

* **ISP Point-to-Point Link:** 209.165.200.0 /30
