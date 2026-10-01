# ISP Failover Testing

## Objective

The purpose of this test was to verify that the enterprise network could maintain Internet connectivity when one ISP connection became unavailable.

The test was performed in the GNS3 laboratory environment using FortiGate SD-WAN, two simulated ISPs, and an internal Finance client.

---

## Test Environment

### WAN Connections

| ISP | FortiGate Interface | Gateway |
|---|---|---|
| ISP1 | `port1` | `192.168.10.1` |
| ISP2 | `port2` | `192.168.20.1` |

### Test Client

```text
Finance-PC1
IP: 10.10.20.10/28
Gateway: 10.10.20.1
Internet Test Destination
8.8.8.8
Test 1 — ISP1 Failure
Initial Condition

Before the failure, both ISP connections were available to FortiGate.

ISP1 → port1 → Available
ISP2 → port2 → Available

The ISP_Health SD-WAN health check was monitoring the WAN paths.

Failure Simulation

ISP1 was intentionally disconnected from the FortiGate.

The purpose was to simulate an ISP outage.

Expected behaviour:

ISP1
  |
  X
  |
FortiGate port1
  |
DEAD

while:

ISP2
  |
  |
FortiGate port2
  |
ALIVE
Failure Detection

After ISP1 was disconnected, the FortiGate SD-WAN diagnostics showed:

port1 → DEAD
port2 → ALIVE

This demonstrated that the SD-WAN health monitoring detected the loss of the ISP1 path.

Traffic After Failover

Traffic from the internal network was then observed through FortiGate port2.

The observed flow was:
Finance-PC1
10.10.20.10
       |
       v
    Core Network
       |
       v
 FortiGate port3
       |
       v
 FortiGate port2
192.168.20.2
       |
       v
      ISP2
       |
       v
    Internet
       |
       v
    8.8.8.8

Packet capture showed traffic using the ISP2 interface after ISP1 became unavailable.

End-to-End Connectivity

During the ISP1 failure, the internal Finance client continued to reach the Internet.

The client was able to ping:

8.8.8.8

This demonstrated that Internet connectivity was maintained despite the simulated ISP1 outage.

Evidence

The failover test should be supported by the following evidence:

Evidence 1 — SD-WAN Failure Detection

FortiGate diagnostic output showing:

port1 → DEAD
port2 → ALIVE
Evidence 2 — Packet Flow

Packet capture showing Internet traffic forwarded through:

FortiGate port2

after ISP1 became unavailable.

Evidence 3 — End-User Connectivity

Finance-PC1 continuing to reach:

8.8.8.8

during the ISP1 failure.

Test Result

The ISP1 failure test demonstrated the following sequence:

ISP1 Failure
     |
     v
FortiGate Detects WAN Failure
     |
     v
port1 = DEAD
     |
     v
port2 = ALIVE
     |
     v
Traffic Forwarded Through ISP2
     |
     v
Finance-PC1 Maintains Internet Connectivity

This provides practical evidence of WAN failover in the simulated enterprise environment.

Test 2 — ISP2 Failure

A reverse failover test can be performed by disconnecting ISP2 while ISP1 remains available.

Expected behaviour:

ISP2
  |
  X
  |
port2 = DEAD

ISP1
  |
  |
port1 = ALIVE

Traffic should then be observed through port1.

This test should only be marked as completed after the corresponding SD-WAN status and packet-flow evidence has been captured.

Core Gateway Redundancy

The internal core also uses VRRP for gateway redundancy.

The verified VRRP configuration is:

Virtual IP: 10.10.10.1
VRID: 10

Core-SW1:
Role: MASTER
Priority: 150

Core-SW2:
Role: BACKUP
Priority: 100

This provides redundancy between the two core devices for the verified VRRP network.

Verification Commands
FortiGate
diagnose sys sdwan member
diagnose sys sdwan service4
diagnose sys sdwan health-check
MikroTik

VRRP status and routing information were checked using RouterOS commands during the lab.

Client

Connectivity was tested using:

ping 8.8.8.8
Conclusion

The failover test demonstrates how SD-WAN health monitoring can detect an unavailable WAN path and maintain Internet connectivity through an available ISP path.

The test also demonstrates the importance of validating network redundancy through actual traffic and end-to-end connectivity rather than relying only on configuration output.
