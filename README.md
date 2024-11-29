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