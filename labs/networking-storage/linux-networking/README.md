# Week 9: Linux networking for containers

[← Back to Month 3: Cloud-Native Networking and Storage](../README.md)

## Weekly Overview
**Goal:** Deep technical investigation and hands-on laboratory exercises for Week 9.

## Concepts to Investigate
- Network namespaces, veth pairs and bridges.
- Routing, ARP and neighbor discovery.
- NAT, conntrack and firewall rules.
- iptables and nftables.
- VXLAN and encapsulation.
- MTU and fragmentation.
- DNS resolution and packet capture.

## Hands-on Laboratory Modules
1. [**Lab 01 Veth Bridge**](lab-01-veth-bridge/README.md)
2. [**Lab 02 Routing Nat**](lab-02-routing-nat/README.md)
3. [**Lab 03 Packet Capture**](lab-03-packet-capture/README.md)
4. [**Lab 04 Network Troubleshooting**](lab-04-network-troubleshooting/README.md)

### Detailed Curriculum Specifications
1. Build two isolated network namespaces connected by a veth pair and Linux bridge.
2. Configure routing and NAT between namespaces.
3. Capture and inspect ARP, DNS, TCP handshakes and HTTP traffic.
4. Break routes, firewall rules and MTU settings; diagnose with `ip`, `ss`, `tcpdump`, `tracepath` and conntrack tools.

## Blog Article Target
**“Linux Networking for Containers: Namespaces, veth, Bridges, NAT and Packet Flow”**
