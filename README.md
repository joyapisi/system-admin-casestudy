# NCBA system-admin-casestudy
<img src="images/logo.png" alt="Project Logo" width="120" height="120">

## Question 1 

Briefly describe the role of the following in networking environment. Draw the diagrammatic representation depicting the same.  

a. **VPN (Virtual Private Network)**

The role of a VPN is that it is used to provide encrypted security when external users are remotely accessing a private network. The type of security that a VPN offers includes ensuring authorised access, and secure data transmission through an encrypted format.

Below is a diagram depicting a remote entity securely connecting to a private network.

```css
<[Remote Entity/User/Device] ----> [VPN Tunnel] ----> [Private Network]
```

b. **MPLS (Multiprotocol Label Switching)**

An MPLS in a networking environment takes the role of using short path labels to transmit data from one node to another. It helps to avoid using long network addresses and ensures efficiency through quick transmission of data, management of traffic, trafic routing control and scalability.

Diagram:

```css
[Client A] ----> [MPLS Network] ----> [Client B]
```

c. **OSPF (Open Shortest Path First)**
OSPF works as a routing protocol that links states and is highly adaptable. It is ideal for large networks. It helps to find the best data transmission path in independent systems.

Diagram:
```css
[Router A] ---|  
              | OSPF Route Exchange 
[Router B] ---|  
```

d. **BGP (Border Gateway Protocol)**
The role of a BGP is to set a standard way of communicating between path vectors when various autonomous systems (ASes) exchange routing information on the internet. A BGP is dominantly used in large-scale networks and ensures that data crosses through the most efficient path when being interchanged on the internet.

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

# Question 3

Suppose AS 10 is a multihomed customer of AS 20 and AS 30. AS 10 receives most of its incoming traffic over AS 30 and wants to employ traffic engineering techniques to shift some of this traffic from AS 30 to AS 20. For this purpose, a route map is created. Assume that the following excerpt of the BGP table is a good representation of the BGP table as a whole: 

| Network            | Next Hop     | Metric | LocPrf | Weight | Path                                  |
|----------------    |--------------|--------|--------|--------|---------------------------------------|
| **12.31.126.0/24** | 213.24.40.91 | 0      | 100    | *      | 0 20 209 13606 i                     |
| **12.31.126.0/24** | 62.93.19.27  | 0      | 0      | *      | 30 209 13606 i                       |
| **12.31.127.0/24** | 213.24.40.91 | 0      | 100    | *      | 0 20 209 7018 23087 i               |
| **12.31.127.0/24** | 62.93.19.27  | 0      | 0      | *      | 30 7018 23087 i                      |
| **12.31.159.0/24** | 213.24.40.91 | 0      | 100    | *      | 0 20 209 7018 20457 i               |
| **12.31.159.0/24** | 62.93.19.27  | 0      | 0      | *      | 30 4181 20457 i                      |

### a. Which BGP attributes would AS 10 possibly like to change in the route map "set" clause, in what way (higher/lower, longer/shorter), and which would be the best choice?

In this case, AS 10 wants to shift some of its traffic from AS 30 to AS 20. The BGP attributes that AS 10 can modify in the "set" clause of the route map include:

__Weight:__

What to change: The Weight is a Cisco-specific BGP attribute that helps determine the preferred path when multiple routes are available.
How to change: To shift traffic from AS 30 to AS 20, AS 10 can set a higher weight for routes learned from AS 20, making them more preferred.
Best choice: This is the best choice for traffic engineering because Weight is local to the router and directly impacts route selection.

__Local Preference (LocPrf):__

What to change: Local Preference is used to indicate the preferred exit point from AS 10. The higher the value, the more preferred the path.
How to change: AS 10 can increase the LocPrf value for routes learned from AS 20, which would make them more preferred than routes from AS 30.
Best choice: Local Preference is typically used for influencing outbound traffic from AS 10, and since AS 10 wants to shift incoming traffic, Weight might be more effective for this scenario.

__AS Path:__

What to change: Changing the AS Path is possible by manipulating the AS path length.
How to change: To make AS 20's path more attractive, AS 10 could prepend AS 30's AS number to the path for routes received from AS 30.
Best choice: While this can influence routing decisions, modifying the AS Path is less direct than using Weight and LocPrf for this specific scenario.
In summary, the best attribute to change is Weight because it will locally affect the selection of the path from AS 20 over AS 30 without impacting other BGP peers.

### b. Should a "match" clause be used in this route map?
Yes, a "match" clause should be used in this route map to specify the conditions under which the attributes should be changed. The match clause will define which prefixes or routes from AS 30 (that AS 10 wants to influence) will be affected by the changes in the set clause.

For example, AS 10 might want to match specific prefixes (e.g., 12.31.126.0/24) that are being advertised by AS 30.
A possible match condition could be matching the prefix or AS path of routes received from AS 30.
The match clause can look for specific attributes such as the next hop or AS path to ensure that only relevant routes are modified.

### c. Should the route map be applied to and explain the reason?
The route map should be applied to the following BGP sessions:

The BGP session with AS 30 "in":

Reason: AS 10 receives most of its incoming traffic from AS 30. By applying the route map to the incoming session, AS 10 can modify the routes learned from AS 30 and adjust the Weight (or LocPrf) to make routes from AS 20 more preferred.
The BGP session with AS 30 "out":

Reason: If AS 10 is redistributing routes to AS 30, the route map can also be applied to the outgoing session to manipulate how AS 30 perceives AS 10's advertised routes. This can be used to influence AS 30's selection of the return path to AS 10.
Note: The route map should not be applied to the BGP sessions with AS 20 because AS 10 is receiving traffic from AS 20, and the purpose of the route map is to modify the incoming traffic from AS 30 to shift it toward AS 20. Applying the route map to the AS 20 sessions would not serve this purpose.

### d. Do a sample configuration of BGP with the following details:
Remote AS number 7890
Neighbour 1.2.3.4
Network 5.6.7.8/25
Null advertisement from your route

Below, I havve given a sample BGP configuration to achieve this:

```
router bgp 10

  bgp log-neighbor-changes
  neighbor 1.2.3.4 remote-as 7890
  network 5.6.7.8 mask 255.255.255.128
  neighbor 1.2.3.4 route-map NULL-ADVERT in

! Create a route map for null advertisement (discard a specific route)
route-map NULL-ADVERT deny 10
  match ip address prefix-list BLOCK-ROUTE
route-map NULL-ADVERT permit 20

! Define a prefix list to block specific route
ip prefix-list BLOCK-ROUTE seq 5 deny 12.31.126.0/24
ip prefix-list BLOCK-ROUTE seq 10 permit 0.0.0.0/0 le 32
``` 

### Explanation of Configuration:
- **router bgp 10:**
Configures BGP for AS 10.

- **neighbor 1.2.3.4 remote-as 7890:** 
Defines the BGP neighbor with IP 1.2.3.4 and remote AS 7890.

- **network 5.6.7.8 mask 255.255.255.128:** 
Advertises the network 5.6.7.8/25 to BGP.

- **route-map NULL-ADVERT in:**
Applies the route map NULL-ADVERT to incoming routes from the neighbor.

- **route-map NULL-ADVERT deny 10:** 
Denies the route 12.31.126.0/24 from being advertised.

- **ip prefix-list BLOCK-ROUTE seq 5 deny 12.31.126.0/24:** 
Defines a prefix list to block the advertisement of 12.31.126.0/24.


__Summary:__
- a: The best BGP attributes to change are Weight and LocPrf to shift traffic from AS 30 to AS 20.
- b: Yes, a "match" clause should be used to identify which routes to modify in the route map.
- c: The route map should be applied to the BGP session with AS 30 "in" and BGP session with AS 30 "out" to influence both incoming and outgoing traffic.
- d: A sample BGP configuration is provided to advertise a network and block a specific route using a null advertisement.