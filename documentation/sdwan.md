# FortiGate SD-WAN Implementation

## Overview

FortiGate is used as the WAN edge and SD-WAN device in this lab.

Two simulated ISP connections are configured as SD-WAN members. FortiGate monitors the availability and quality of the WAN paths using an SD-WAN health check and SLA thresholds.

The purpose is to provide resilient Internet connectivity and demonstrate WAN path failover.

---

## WAN Interfaces

| Interface | IP Address | Gateway | ISP |
|---|---|---|---|
| `port1` | `192.168.10.2/24` | `192.168.10.1` | ISP1 |
| `port2` | `192.168.20.2/24` | `192.168.20.1` | ISP2 |
| `port3` | `10.255.255.1/30` | Internal | Core Network |

---

## SD-WAN Members

The two WAN interfaces are configured as SD-WAN members:

```text
Member 1
Interface: port1
Gateway: 192.168.10.1

Member 2
Interface: port2
Gateway: 192.168.20.1
The members provide two independent WAN paths from the FortiGate to the simulated Internet.

Health Check

An SD-WAN health check named:

ISP_Health

is configured to monitor:

8.8.8.8

The health check measures:

Latency
Jitter
Packet loss

This provides FortiGate with operational information about the state of each WAN path.

SLA Configuration

The configured SLA thresholds are:

Metric	Threshold
Latency	300 ms
Jitter	200 ms
Packet Loss	50%

The thresholds provide criteria that FortiGate can use when evaluating WAN path health.

SD-WAN Service

The Internet failover service is associated with the ISP_Health health check.

Conceptually:

                 Internet Traffic
                        |
                        v
                FortiGate SD-WAN
                        |
             +----------+----------+
             |                     |
          ISP1                   ISP2
        port1                  port2
             |                     |
          Health                 Health
          Check                  Check
             |                     |
             +----------+----------+
                        |
                 Available Path

The SD-WAN service evaluates the available WAN members and their health status.

Verification Commands

The following FortiGate commands were used during the lab to inspect SD-WAN operation:

diagnose sys sdwan member

Displays the SD-WAN member status.

diagnose sys sdwan service4

Displays SD-WAN service information and path selection.

diagnose sys sdwan health-check

Displays health-check and SLA information.

These commands were used during both normal operation and failover testing.

Health Monitoring During Normal Operation

During normal operation, both ISP connections were monitored by the ISP_Health health check.

The health-check output provided measurements including:

Packet loss
Latency
Jitter
SLA status
WAN member availability

This allowed the lab to demonstrate how FortiGate continuously evaluates the WAN paths.

Relationship Between SD-WAN and Failover

The failover process can be summarized as:

WAN Path Available
       |
       v
Health Check
       |
       v
SLA Evaluation
       |
       v
SD-WAN Service
       |
       v
Traffic Uses Available Path

When a WAN path becomes unavailable:

ISP Failure
     |
     v
Health Check Detects Failure
     |
     v
WAN Member Becomes Unavailable
     |
     v
SD-WAN Selects Available Path
     |
     v
Traffic Continues
Practical Validation

The configuration was not validated only through configuration output.

The lab also used:

Controlled ISP disconnection
SD-WAN diagnostic output
Continuous connectivity testing
Packet capture
Internal client testing

During the ISP1 failure test, FortiGate detected port1 as unavailable while port2 remained available, and traffic was observed through port2.

This provided practical evidence that the SD-WAN configuration was functioning during the simulated outage.
