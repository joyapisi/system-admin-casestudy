# NCBA system-admin-casestudy
## Question 1 

Briefly describe the role of the following in networking environment. Draw the diagrammatic representation depicting the same. 
•	VPN 
•	MPLS 
•	OSPF
•	BGP 

1. VPN (Virtual Private Network)

The role of a VPN is that it is used to provide encrypted security when external users are remotely accessing a private network. The type of security that a VPN offers includes ensuring authorised access, and secure data transmission through an encrypted format.

Below is a diagram depicting a remote entity securely connecting to a private network.

```css
<[Remote Entity/User/Device] ----> [VPN Tunnel] ----> [Private Network]
```

2. MPLS (Multiprotocol Label Switching)

The role of a MPLS is to direct data from one node to the next based on short path labels rather than long network addresses. It allows for efficient traffic management, offering faster data transmission, scalability, and better control over traffic routing. It is often used in large enterprise or service provider networks.

Diagram:

```css
[Client A] ----> [MPLS Network] ----> [Client B]
```

3. OSPF (Open Shortest Path First)
Role: OSPF is a link-state routing protocol used within an autonomous system to find the best path for data transmission. It dynamically adjusts to network changes and quickly adapts to new network conditions. OSPF is efficient and scalable, making it ideal for large networks.

Diagram:
```css
[Router A] ---|  
              | OSPF Route Exchange 
[Router B] ---|  
```

4. BGP (Border Gateway Protocol)
Role: BGP is a path vector protocol used to exchange routing information between different autonomous systems (ASes) on the internet. It helps manage how data is routed between large-scale networks and ensures that data finds the most efficient path across the internet.

```css
Diagram:

[AS1] ---> [BGP] ---> [AS2]
```

These technologies work together to enable secure, efficient, and flexible networking, with VPNs providing security, MPLS optimizing traffic, OSPF handling internal routing, and BGP managing inter-network routing.

## Question 2
 
**Scenario:**  
NCBA wishes to integrate Business-to-Business service with a third party located in Johannesburg, which is secure. 
 
**Required:** 
•	Describe in detailed the connectivity mode to be used highlighting the important networking aspect to be noted by all parties 
 
•	Provide a sample configuration for the above connection. Include

-	Router# 
-	asa# 
-	Switch#
	
any other device to use for connectivity 

### MPLS VPN with IPsec Encryption
MPLS VPN is a secure, scalable, and high-performance way to connect multiple sites over a service provider’s network. When combined with IPsec encryption, it can ensure the confidentiality and integrity of the data being exchanged between NCBA and the third party.

#### Why MPLS VPN?

__Security:__ MPLS VPNs provide private, isolated paths across a service provider’s network, reducing exposure to potential external threats. When combined with IPsec, it ensures end-to-end encryption for data security.
__Scalability:__ MPLS is ideal for large-scale networks and can easily support additional business partners or branch offices in the future.
__Performance:__ MPLS provides efficient traffic management with Quality of Service (QoS) support to ensure high-priority traffic like B2B services is not delayed or dropped.
__Routing:__ MPLS simplifies the routing configuration by using labels instead of relying on traditional IP routing. This enables faster routing decisions.
Key Networking Aspects to Consider

## MPLS VPN Configuration:

Layer 3 MPLS VPN is used to create secure, isolated routes for NCBA and the third-party provider. This ensures that even though traffic might pass through a common service provider’s network, each customer’s traffic remains private.
IPsec encryption is used to protect the data end-to-end between the sites, preventing unauthorized access.
Routing Protocols:

BGP (Border Gateway Protocol) is typically used to exchange routing information between different MPLS VPN networks. It’s ideal for large-scale environments, as it can handle the dynamic nature of routing in MPLS.
For internal communication, OSPF (Open Shortest Path First) can be used for routing within NCBA’s network and the third-party provider’s network.
Security:

Authentication can be done using pre-shared keys or digital certificates for IPsec.
Access Control Lists (ACLs) should be configured to restrict traffic between NCBA and the third-party provider.
Network Design:

MPLS VPN can ensure that both NCBA and the third party’s networks are logically separated but can still exchange data over a private and secure connection.
Quality of Service (QoS) can be implemented to ensure that mission-critical B2B traffic receives priority over other types of traffic.

## A Configuration Example

1. MPLS Configuration on Router (Provider Edge)
The MPLS provider’s router (PE router) needs to be configured to establish the MPLS VPN. Below is a simple configuration to set up the MPLS VPN with IPsec encryption for the tunnel:

``` 
Router# configure terminal
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# mpls ip

Router(config)# ip vrf NCBA_VPN
Router(config-vrf)# rd 100:1
Router(config-vrf)# route-target export 100:1
Router(config-vrf)# route-target import 100:1
Router(config-vrf)# exit

Router(config)# interface GigabitEthernet0/1
Router(config-if)# ip vrf forwarding NCBA_VPN
Router(config-if)# ip address 10.1.1.1 255.255.255.252
Router(config-if)# no shutdown

Router(config)# ip route vrf NCBA_VPN 0.0.0.0 0.0.0.0 192.168.1.2
Router(config)# exit
```

2. ASA Firewall Configuration for IPsec Encryption
The ASA firewall will be used to establish IPsec encryption over the MPLS VPN. Below is a sample configuration for the ASA firewall:

``` 
asa# configure terminal
asa(config)# interface Ethernet0/0
asa(config-if)# nameif outside
asa(config-if)# security-level 0
asa(config-if)# ip address 192.168.1.1 255.255.255.0
asa(config-if)# no shutdown

asa(config)# interface Ethernet0/1
asa(config-if)# nameif inside
asa(config-if)# security-level 100
asa(config-if)# ip address 10.0.0.1 255.255.255.0
asa(config-if)# no shutdown

asa(config)# crypto ipsec ikev1 transform-set TS esp-aes-256 esp-sha-hmac
asa(config)# crypto ipsec ikev1 enable outside

asa(config)# tunnel-group 41.62.23.1 type ipsec-l2l
asa(config)# tunnel-group 41.62.23.1 ipsec-attributes
asa(config-ipsec)# ikev1 pre-shared-key YourPreSharedKey
asa(config-ipsec)# exit
``` 

3. Switch Configuration
Switch configuration remains the same, but it will support VLANs for internal segmentation and possibly for segregating traffic from NCBA and the third-party provider.

``` 
Switch# configure terminal
Switch(config)# vlan 10
Switch(config-vlan)# name B2B_VLAN
Switch(config-vlan)# exit

Switch(config)# interface range fa0/1 - 24
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10
Switch(config-if-range)# exit
``` 

#### Additional Considerations for MPLS VPN with IPsec:

__Bandwidth and Traffic Shaping:__

Ensure that the bandwidth is sufficient to support the traffic load between the two parties, and use traffic shaping or policing techniques to control bandwidth usage.
High Availability:

Implement redundant MPLS links between NCBA and the third-party provider to ensure high availability in case one link fails.
Consider using HSRP (Hot Standby Router Protocol) or VRRP (Virtual Router Redundancy Protocol) for router redundancy.

__Monitoring and Logging:__

Use SNMP (Simple Network Management Protocol) to monitor network devices for performance and status.
Configure logging on ASA and routers to track connection status, security incidents, and troubleshooting.
Service Provider Coordination:

Since MPLS VPN is a managed service by a service provider, coordination with the service provider is necessary to ensure the correct configuration of the VPN service between NCBA’s and the third-party’s networks.

## Conclusion:
Using MPLS VPN with IPsec encryption provides a secure, scalable, and reliable solution for connecting NCBA to the third-party provider in Johannesburg. This approach isolates traffic, ensuring confidentiality, and also leverages the MPLS backbone for efficient routing. By combining MPLS with IPsec, this solution guarantees both security and performance for the B2B integration.

