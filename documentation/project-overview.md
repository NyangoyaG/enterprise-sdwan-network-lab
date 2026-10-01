# Project Overview

## Purpose

This project is a practical enterprise network laboratory developed in GNS3 to demonstrate resilient enterprise connectivity using FortiGate SD-WAN, dual ISP connectivity, MikroTik routing, VLAN segmentation, SLA monitoring, and gateway redundancy.

## Environment

The lab contains:

- FortiGate 7.4.12
- Two simulated ISPs
- MikroTik Core-SW1
- MikroTik Core-SW2
- Access-SW1
- Transit-SW
- Departmental VPCS clients
- GNS3 NAT/Internet nodes

## Primary Objectives

The lab was designed to demonstrate:

1. Dual ISP connectivity
2. FortiGate SD-WAN configuration
3. WAN health monitoring
4. SLA-based path monitoring
5. ISP failover
6. Internal network routing
7. VLAN segmentation
8. VRRP gateway redundancy
9. End-to-end Internet connectivity
10. Packet-flow verification

## Practical Scenario

The simulated enterprise has internal departments connected through the core and access switching infrastructure.

Internet connectivity is provided through two separate simulated ISP paths.

FortiGate operates at the WAN edge and monitors both paths through SD-WAN health checks.

When a WAN path becomes unavailable, the SD-WAN configuration can select an available path according to the configured service and health state.

## Validation

The lab was tested using:

- Ping
- FortiGate SD-WAN diagnostics
- MikroTik RouterOS commands
- Interface status
- SD-WAN health checks
- Packet captures
- Controlled ISP failure

The purpose of the testing was to verify actual network behaviour rather than relying only on configuration output.
