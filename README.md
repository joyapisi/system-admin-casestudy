# NCBA system-admin-casestudy
<img src="images/logo.png" alt="Project Logo" width="120" height="120">

# 📗 Table of Contents

- [🛜 Question 1](#one)
  - [🛠 VPN](#vpn)
  - [🛠 MPLS](#MPLS)
  - [🛠 OSPF](#OSPF)
  - [🛠 BGP](#BGP)
- [🛜 Question 2](#q2)
  - [🛠 MPLS VPN with IPsec Encryption](#MPLS-IPsec)
  - [🛠 MPLS VPN Configuration Process](#MPLS-config)  
  - [🛠   A Configuration Example](#config-example)  
- [🛜 Question 3](#q3)
  - [🛠   BGP Route Map Configuration](#BGP-Route-Map)  
  - [🛠   Question 3 a](#BGP-Attr)  
  - [🛠   Question 3 b](#Match-clause)  
  - [🛠   Question 3 c](#apply-route-map)  
  - [🛠   Question 3 d](#sample-BGP-config)  

## Question 1 <a name="one"></a>

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

In summary, these technologies work together to ensure there is security, scalability, and efficiency in how systems work.

## Question 2 <a name="q2"></a> 
 
**Scenario:**  
NCBA wishes to integrate Business-to-Business service with a third party located in Johannesburg, which is secure. 
 
**Required:** 
•	Describe in detailed the connectivity mode to be used highlighting the important networking aspect to be noted by all parties 
 
•	Provide a sample configuration for the above connection. Include

-	Router# 
-	asa# 
-	Switch#
	
any other device to use for connectivity 

## MPLS VPN with IPsec Encryption <a name="MPLS-IPsec"></a>
For this question, I shall use MPLS VPN which provides high performance, security, and scalability. MPLS efficiently connects multiple sites over a service provider’s network. When combined with IPsec encryption, it would ensure the confidentiality and integrity of the data being exchanged between NCBA and the third party.

#### Benefits/Reasons for Using MPLS VPN in this Scenario

__Security:__ MPLS VPNs provide private, isolated paths across a service provider’s network, reducing exposure to potential external threats. When combined with IPsec, it ensures end-to-end encryption for data security.

__Scalability:__ MPLS is ideal for large-scale networks and can easily support additional business partners or branch offices in the future.

__Performance:__ MPLS provides efficient traffic management with Quality of Service (QoS) support to ensure high-priority traffic like B2B services is not delayed or dropped.

__Routing:__ MPLS simplifies the routing configuration by using labels instead of relying on traditional IP routing. This enables faster routing decisions.
Key Networking Aspects to Consider

## MPLS VPN Configuration Process <a name="MPLS-config"></a>

Setting up an MPLS VPN is a great way to create secure, isolated routes for NCBA and the third-party provider, ensuring that even though their traffic may travel through a shared service provider’s network, it remains private and protected. This means that sensitive information stays secure, even in a shared environment. We use IPsec encryption to safeguard the data between the two sites, so there’s no risk of unauthorized access.

### Routing Protocols

When it comes to exchanging routing information across different MPLS VPN networks, we typically rely on **BGP (Border Gateway Protocol)**. BGP is perfect for large environments because it can dynamically adapt to changes in the network and keep everything running smoothly. Inside NCBA's network and between the third-party provider's systems, we can use **OSPF (Open Shortest Path First)** for efficient routing.

### Security

Security is a top priority, so we’ll set up authentication using either **pre-shared keys** or **digital certificates** for IPsec encryption. To further secure the environment, **Access Control Lists (ACLs)** will be configured to restrict traffic, making sure that only authorized communication happens between NCBA and the third-party provider.

### Network Design

With MPLS VPN, we can keep NCBA’s network and the third party’s network logically separate, but still allow them to exchange data over a private and secure connection. And to ensure the most important B2B traffic gets priority, we can implement **Quality of Service (QoS)** to make sure mission-critical traffic gets through first, without delay.

## A Configuration Example <a name="config-example"></a>

1. **MPLS Configuration on Router (Provider Edge)**

The provider’s MPLS router (PE router) will need to be configured to establish the MPLS VPN. Below is a simple configuration example to set up the MPLS VPN. It also entails IPsec encryption for a secure tunnel:

```bash 
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

2. **ASA Firewall Configuration for IPsec Encryption**
The ASA firewall will be used to establish IPsec encryption over the MPLS VPN. Below is a sample configuration for the ASA firewall:

```bash 
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

3. **Switch Configuration**
Switch configuration remains the same, but it will support VLANs for internal segmentation and possibly for seperate traffic from NCBA and the third-party provider.

```bash 
Switch# configure terminal
Switch(config)# vlan 10
Switch(config-vlan)# name B2B_VLAN
Switch(config-vlan)# exit

Switch(config)# interface range fa0/1 - 24
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10
Switch(config-if-range)# exit
``` 

## Additional Considerations for MPLS VPN with IPsec:

**Bandwidth and Traffic Shaping:**

Make sure there’s enough bandwidth to handle the expected traffic load between NCBA and the third-party provider. It’s also a good idea to use traffic shaping or policing to manage and control bandwidth usage, ensuring smooth performance even during peak times.

**High Availability:**

To minimize any potential downtime, it’s important to set up redundant MPLS links between NCBA and the third party. This way, if one link goes down, the other can pick up the load. Consider using HSRP (Hot Standby Router Protocol) or VRRP (Virtual Router Redundancy Protocol) for router redundancy, which adds an extra layer of reliability.

**Monitoring and Logging:**

Stay on top of network performance by using SNMP (Simple Network Management Protocol) to monitor devices and track their status. It’s also wise to configure logging on both the ASA firewall and routers so you can easily track connection issues, security events, or troubleshoot any problems that arise.

**Service Provider Coordination:**

Since MPLS VPN is managed by a service provider, it's crucial to maintain good communication and coordination with them. This will ensure that everything is set up correctly and the VPN service between NCBA and the third-party provider runs smoothly.

## Conclusion:

Opting for MPLS VPN with IPsec encryption gives you a secure, scalable, and reliable solution for connecting NCBA with the third-party provider in Johannesburg. This setup keeps traffic isolated, protecting confidentiality, and uses the power of the MPLS backbone to handle routing efficiently. By combining MPLS with IPsec, you’re ensuring both strong security and high performance for the B2B integration.

# Question 3 <a name="q1"></a>

Suppose AS 10 is a multihomed customer of AS 20 and AS 30. AS 10 receives most of its incoming traffic over AS 30 and wants to employ traffic engineering techniques to shift some of this traffic from AS 30 to AS 20. For this purpose, a route map is created. Assume that the following excerpt of the BGP table is a good representation of the BGP table as a whole: 

| Network            | Next Hop     | Metric | LocPrf | Weight | Path                                  |
|----------------    |--------------|--------|--------|--------|---------------------------------------|
| **12.31.126.0/24** | 213.24.40.91 | 0      | 100    | *      | 0 20 209 13606 i                     |
| **12.31.126.0/24** | 62.93.19.27  | 0      | 0      | *      | 30 209 13606 i                       |
| **12.31.127.0/24** | 213.24.40.91 | 0      | 100    | *      | 0 20 209 7018 23087 i               |
| **12.31.127.0/24** | 62.93.19.27  | 0      | 0      | *      | 30 7018 23087 i                      |
| **12.31.159.0/24** | 213.24.40.91 | 0      | 100    | *      | 0 20 209 7018 20457 i               |
| **12.31.159.0/24** | 62.93.19.27  | 0      | 0      | *      | 30 4181 20457 i                      |

# BGP Route Map Configuration <a name="BGP-Route-Map"></a>

### a. Which BGP attributes would AS 10 possibly like to change in the route map "set" clause, in what way (higher/lower, longer/shorter), and which would be the best choice? <a name="BGP-Attr"></a>

In this scenario, AS 10 wants to shift some of its traffic from AS 30 to AS 20. The key BGP attributes that AS 10 can tweak in the "set" clause of the route map include:

#### **Weight:**
- **What to change:** Weight is a Cisco-specific BGP attribute used to decide which path is preferred when multiple routes exist. 
- **How to change:** To shift more traffic to AS 20, AS 10 can assign a **higher weight** to routes learned from AS 20, making them the preferred option over routes from AS 30.
- **Best choice:** Weight is the best option here because it's local to the router and directly influences route selection, making it ideal for traffic engineering.

#### **Local Preference (LocPrf):**
- **What to change:** Local Preference is used to influence the exit path from AS 10. A higher value indicates a more preferred path.
- **How to change:** AS 10 can increase the LocPrf value for routes learned from AS 20, making them more attractive than those from AS 30.
- **Best choice:** While LocPrf is useful for influencing outbound traffic, **Weight** is likely a better option for this scenario because AS 10 is trying to influence **incoming** traffic.

#### **AS Path:**
- **What to change:** The AS Path can be manipulated by changing its length, which indirectly affects route selection.
- **How to change:** AS 10 could prepend AS 30’s AS number to routes received from AS 30, making them appear longer and less attractive.
- **Best choice:** While modifying the AS Path can help influence routing, it’s less direct and effective than adjusting Weight or LocPrf for this case.

**Summary:** The best attribute to modify here is **Weight**, as it will have a local effect on path selection for incoming routes from AS 30, steering traffic toward AS 20 without affecting other BGP peers.

### b. Should a "match" clause be used in this route map? <a name="Match-clause"></a>

Yes, a "match" clause should be used to define the specific conditions under which the attributes are adjusted. This will allow AS 10 to target routes that are coming from AS 30, ensuring only the relevant routes are affected.

For example, AS 10 may want to match specific prefixes like `12.31.126.0/24` that are being advertised by AS 30. The match clause will look for attributes such as the **prefix** or **AS path** to identify the routes that need to be changed.

### c. Should the route map be applied to and explain the reason? <a name="apply-route-map"></a>

The route map should be applied to the following BGP sessions:

#### **The BGP session with AS 30 "in":**
- **Reason:** Since AS 10 receives most of its traffic from AS 30, applying the route map to the incoming session allows AS 10 to modify the routes it learns from AS 30. By adjusting the Weight or LocPrf, AS 10 can make routes from AS 20 more attractive.

#### **The BGP session with AS 30 "out":**
- **Reason:** If AS 10 is advertising routes to AS 30, applying the route map to the outgoing session can influence how AS 30 perceives the return path to AS 10. This helps guide the traffic back through AS 20.

**Note:** The route map should **not** be applied to the BGP sessions with AS 20, since AS 10 is receiving traffic from AS 20. The route map’s goal is to influence incoming traffic from AS 30, so applying it to AS 20 wouldn’t serve this purpose.

### d. Do a sample configuration of BGP with the following details: <a name="sample-BGP-config"></a>

- Remote AS number: 7890
- Neighbor: 1.2.3.4
- Network: 5.6.7.8/25
- Null advertisement for a specific route

Here's a sample BGP configuration that achieves the above:

```bash
router bgp 10
  bgp log-neighbor-changes
  neighbor 1.2.3.4 remote-as 7890
  network 5.6.7.8 mask 255.255.255.128
  neighbor 1.2.3.4 route-map NULL-ADVERT in

! Create a route map to discard a specific route (Null advertisement)
route-map NULL-ADVERT deny 10
  match ip address prefix-list BLOCK-ROUTE
route-map NULL-ADVERT permit 20

! Define a prefix list to block a specific route
ip prefix-list BLOCK-ROUTE seq 5 deny 12.31.126.0/24
ip prefix-list BLOCK-ROUTE seq 10 permit 0.0.0.0/0 le 32
```
### Explanation of Configuration: <a name="config-explained"></a>

- **router bgp 10:**  
  This command sets up BGP for Autonomous System (AS) 10. It tells the router to begin configuring BGP within that AS.

- **neighbor 1.2.3.4 remote-as 7890:**  
  Here, we define a BGP neighbor with the IP address 1.2.3.4 and assign it to AS 7890. This establishes a connection with a peer router.

- **network 5.6.7.8 mask 255.255.255.128:**  
  This command advertises the 5.6.7.8/25 network to BGP, making it known to other BGP routers in the network.

- **route-map NULL-ADVERT in:**  
  We apply a route map called NULL-ADVERT to incoming routes from the defined neighbor. The map controls how routes are handled as they are received.

- **route-map NULL-ADVERT deny 10:**  
  This specific line blocks the advertisement of the 12.31.126.0/24 network from being advertised to our BGP peers.

- **ip prefix-list BLOCK-ROUTE seq 5 deny 12.31.126.0/24:**  
  This creates a prefix list to explicitly block the 12.31.126.0/24 network from being advertised. It ensures that unwanted routes are filtered out.

### Summary: <a name="summary"></a>
- **a:** The most effective BGP attributes to modify in the route map are **Weight** and **LocPrf**. Adjusting these will help shift traffic away from AS 30 and towards AS 20, aligning with the desired routing strategy.
- **b:** Yes, using a "match" clause is essential in this route map. It allows you to target specific routes that need to be modified, providing flexibility and control over which paths are adjusted.
- **c:** The route map should be applied to both the **BGP session with AS 30 "in"** and the **BGP session with AS 30 "out"**. This ensures we influence both incoming and outgoing traffic, helping to control the flow of data through the network.
- **d:** A sample BGP configuration is provided, demonstrating how to advertise a network and block a specific route using a null advertisement. This configuration ensures that the network behaves as intended by controlling route advertisements and influencing traffic flow.
