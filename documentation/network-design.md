# Enterprise Network Design

## Overview

The internal network is built around two MikroTik core devices:

- Core-SW1
- Core-SW2

The core devices provide internal routing, VLAN gateways, connectivity toward the FortiGate WAN edge, and gateway redundancy using VRRP.

An access switch connects the departmental end-user networks to the core layer.

---

## Core Devices

| Device | Role |
|---|---|
| Core-SW1 | Primary core router and VRRP MASTER |
| Core-SW2 | Secondary core router and VRRP BACKUP |
| Access-SW1 | Access-layer connectivity |
| Transit-SW | Transit between the core and FortiGate |

---

## Core-to-FortiGate Connectivity

The FortiGate internal interface is:

```text
FortiGate port3
10.255.255.1/30

Core-SW1 uses:

10.255.255.2/30

as its connection toward the FortiGate through the Transit-SW.

Core-SW2 also has a routed connection through the transit infrastructure.

The core network therefore provides the path between internal VLANs and the FortiGate SD-WAN edge.

Core-to-Core Connectivity

Core-SW1 and Core-SW2 have a routed interconnection:

Core-SW1
10.255.254.1/30
       |
       |
10.255.254.2/30
Core-SW2

This provides communication between the two core devices and supports the redundant core design.

VLAN Segmentation

The lab uses separate networks for different departments and infrastructure services.

Network	Addressing
HR	10.10.10.0/27
Finance	10.10.20.0/28
Procurement	10.10.30.0/28
IT	10.10.40.0/28
Administration	10.10.50.0/29
Agents	10.10.56.0/21
Servers	10.10.70.0/24
Management	10.10.80.0/24
DMZ	10.10.100.0/24

The VLAN structure separates departmental and infrastructure traffic into different IP networks.

VRRP Gateway Redundancy

VRRP is used to provide a virtual gateway between the two core routers.

For the verified VRRP instance:

VRID: 10
Virtual IP: 10.10.10.1
Core-SW1
Role: MASTER
Priority: 150
Core-SW2
Role: BACKUP
Priority: 100

The higher priority on Core-SW1 causes it to operate as the MASTER under normal conditions.

Core-SW2 remains available as the BACKUP router.

Conceptually:

                 Virtual Gateway
                   10.10.10.1
                         |
             +-----------+-----------+
             |                       |
        Core-SW1                 Core-SW2
         MASTER                   BACKUP
       Priority 150             Priority 100
Routing

The core network uses static routing toward the FortiGate edge.

Core-SW1 has a default route toward:

10.255.255.1

Core-SW2 has a default route toward:

10.255.254.1

FortiGate also has a route toward the relevant internal transit network.

This creates the forwarding path:

Department Client
       |
       v
Access-SW1
       |
       v
Core Network
       |
       v
Transit-SW
       |
       v
FortiGate
       |
       v
SD-WAN
       |
       v
Internet
Finance Client Example

The Finance client used during the failover test was configured as:

IP Address: 10.10.20.10/28
Gateway:    10.10.20.1

The client successfully reached the Internet during the ISP1 failure test.

This provided an end-to-end validation of:

Client
  ↓
VLAN
  ↓
Core Routing
  ↓
FortiGate
  ↓
SD-WAN
  ↓
ISP
  ↓
Internet
Core Redundancy Verification

The core redundancy configuration was verified using MikroTik RouterOS.

The expected operational state for the verified VRRP instance is:

Core-SW1 → MASTER
Core-SW2 → BACKUP

The VRRP virtual address is:

10.10.10.1

This allows the internal network to use a virtual gateway rather than depending directly on a single physical core device.

Design Summary

The network follows a layered approach:

Internet / ISP Layer
        |
        v
FortiGate SD-WAN
        |
        v
Transit Layer
        |
        v
Core Layer
 Core-SW1 ↔ Core-SW2
        |
        v
Access Layer
    Access-SW1
        |
        v
Departmental Clients

The design combines WAN redundancy through FortiGate SD-WAN with internal gateway redundancy through VRRP.

This provides multiple layers of resilience within the simulated enterprise network.
