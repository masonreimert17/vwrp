---
VWRP is in its infancy. The protocol does not function (yet), and needs work. If you need it to work sooner than later, I suggest you learn C :)
---

# VWRP (Virtual WAN Reduncancy Protocol)
VWRP is an open protocol, and a test implementation written in C, to conduct near seamless failover on multiple routers with only 1 available WAN IP.

### Goal / Current Standard
Virtual WAN Redundancy Protocol is a protocol that facilitates messages between two WAN facing routers in instances when only one IP is available for use. Initally inspired by limitations around having multiple routers at the edge of a home internet connection to provide router level redundancy. VWRP attaches to user defined trackers agnostic to the protocol. When the trackers change state routers in the L2 domain can act acordingly, including stepping up / stepping down from forwarding packets to the WAN. Because only one IP is available, only one forwarder can be active. The routers will send hellos to eachother that contain paramaters about the interface, DHCP state, etc. The protocol is designed to be able to be used with DHCP enabled links (because of its home network use case) by sharing DHCP state between all routers in the L2 domain. When a router is not active, it filters all ingress and egress packets, and only allows frames with the VWRP configured ether type to be processed by the CPU. This allows the hellos and stepup/down messages to go through the filter. When a router "steps up" to be the active forwarder the ingress/egress filters are removed off the interface to allow all traffic to forward through the CPU. DHCP is obtained by the active forwarder and then shifted to any subsequent active forwarders through the hello messages. Since the MACs on all the interfaces are set to the same MAC, the DHCP address shifting is non interruptive, and DHCP should renew to the same IP upon renewal time. 

### Future Additions
1. NAT State Sharing - Ability to share NAT state amoung the routers to stop NAT xlate tables from having to be rebult each time the active forwarder changes.
2. DHCP Sockets - Better hooks into the DHCP daemon to allow the state to be "injected" to standby forwarders DHCP process.
