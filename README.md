# VPN Implementation for Corporate Sites

## Project Overview
This project aims to securely connect two corporate sites using a VPN (Virtual Private Network) and enhance the network's performance, reliability, and security by integrating VLANs, OSPF, EGIRP, BGP, DHCP, ACLs, and using Cisco Packet Tracer for simulation.

## Objectives
- Establish a secure site-to-site VPN connection using IPsec.
- Segment the network using VLANs for improved efficiency and security.
- Enable dynamic routing between sites with OSPF, EGIRP and BGP.
- Automate IP address allocation with DHCP.
- Implement ACLs to enhance network security.

## Network Topology


![topology](./topology.png)

- **Site A:** 
  - **Router R1**
    - Interface Serial0/0/0: IP address 5.1.0.1 255.255.255.252
    - Interface GigabitEthernet0/1: IP address 13.0.81.1 255.255.255.0
  - **Router R2**
    - Interface Serial0/0/0: IP address 5.1.0.2 255.255.255.252
    - Interface GigabitEthernet0/1: IP address 32.0.0.1 255.255.255.0
  - **Router R3**
    - Interface Serial0/0/0: IP address 5.1.0.2 255.255.255.252
    - Interface GigabitEthernet0/2: IP address 32.0.0.10 255.255.255.0
  - **Router R4**
    - Interface GigabitEthernet0/2: IP address 33.0.0.1 255.255.255.0
  - **Router R5**
    - Interface Serial0/0/0: IP address 7.3.0.2 255.255.255.252

- **Site B:** 
  - **Router R7**
    - Interface GigabitEthernet0/0: IP address 10.0.1.1 255.255.255.0
    - Interface GigabitEthernet0/2: IP address 10.0.2.1 255.255.255.0

- **Additional Components:**
  - **Switches:**
    - Cisco Catalyst 2960 or 3650 Series
  - **Servers:**
    - Dedicated DHCP servers or routers with DHCP capabilities

## Protocols and Technologies
- **IPsec (Internet Protocol Security):** Secure communication between the two sites.
- **VLAN (Virtual Local Area Network):** Network segmentation for efficiency and security.
- **OSPF (Open Shortest Path First):** Dynamic routing protocol for optimal path selection.
- **DHCP (Dynamic Host Configuration Protocol):** Automated IP address allocation for network devices.
- **ACL (Access Control Lists):** Enhanced network security by controlling access based on predefined rules.

## Implementation Steps

1. **Define Requirements**
   - Determine the purpose and requirements for the VPN connection (bandwidth, security, availability).

2. **Choose VPN Protocol**
   - Select IPsec for the site-to-site VPN.

3. **Select VPN Devices**
   - Use appropriate devices for each site, such as Cisco routers or firewalls (e.g., Cisco ASA).

4. **Configure the VPN Gateway**
   - Set up the VPN gateways on both sites with the IPsec settings.

5. **Set Up VLANs**
   - Configure VLANs on switches and assign devices to the appropriate VLANs.
   - Use 802.1Q for VLAN tagging.

6. **Enable OSPF**
   - Configure OSPF on routers for dynamic routing between the sites.

7. **Set Up DHCP**
   - Configure DHCP on routers or dedicated DHCP servers to automatically assign IP addresses to network devices.
   - Define DHCP pools for each VLAN.

8. **Implement ACLs**
   - Configure ACLs to control access to the network and enhance security.
   - Examples:
     - Ban anyone not belonging to the HFC network from accessing router HFC-Network via Telnet.
     - Ban computers in the 33.0.0.0/24 network from browsing the web but allow all other communication.

9. **Test and Monitor**
    - Verify connectivity, test the VPN tunnel, and monitor performance and availability.

## Simulation Using Cisco Packet Tracer
- **Purpose:** Use Cisco Packet Tracer to simulate the network setup and configurations.
- **Application:** Create a virtual network topology in Packet Tracer, configure the protocols and devices, and test the connectivity and performance.

## Example Configuration
### Site A Configuration
```plaintext
! Site A Configuration
crypto isakmp policy 1
 authentication pre-share
 encryption aes
 hash sha
 group 2
 lifetime 86400
crypto isakmp key YOUR_SHARED_KEY address SITE_B_IP
crypto ipsec transform-set MY_TRANSFORM_SET esp-aes esp-sha-hmac
crypto map MY_CRYPTO_MAP 10 ipsec-isakmp
 set peer SITE_B_IP
 set transform-set MY_TRANSFORM_SET
 match address VPN_ACL
interface GigabitEthernet0/0
 ip address SITE_A_IP 255.255.255.0
 crypto map MY_CRYPTO_MAP
access-list VPN_ACL permit ip SITE_A_NETWORK 0.0.0.255 SITE_B_NETWORK 0.0.0.255
! DHCP Configuration
ip dhcp pool VLAN10
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
ip dhcp pool VLAN20
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8
```
### Site B Configuration
```plaintext
! Site B Configuration
crypto isakmp policy 1
 authentication pre-share
 encryption aes
 hash sha
 group 2
 lifetime 86400
crypto isakmp key YOUR_SHARED_KEY address SITE_A_IP
crypto ipsec transform-set MY_TRANSFORM_SET esp-aes esp-sha-hmac
crypto map MY_CRYPTO_MAP 10 ipsec-isakmp
 set peer SITE_A_IP
 set transform-set MY_TRANSFORM_SET
 match address VPN_ACL
interface GigabitEthernet0/0
 ip address SITE_B_IP 255.255.255.0
 crypto map MY_CRYPTO_MAP
access-list VPN_ACL permit ip SITE_B_NETWORK 0.0.0.255 SITE_A_NETWORK 0.0.0.255
! DHCP Configuration
ip dhcp pool VLAN10
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
ip dhcp pool VLAN20
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8
 ```

### **Lab Setup**
## 1. hostnames on all routers
```
Router(config)#hostname R1
```

### 2. **Set up HFC and PSTN clouds**
- **HFC Cloud:**
1. Go to the "Config" tab, find "Ethernet6" under "INTERFACES" and select "Cable".
2. Connect "Coaxial7" and "Ethernet6", and click "Add".

- **DSL Cloud:**
Follow a similar configuration to the HFC cloud.

- **PSTN Cloud:**
1. Go to the "Config" tab, find "Modem4" under "INTERFACES" and connect it to "Ethernet6".

### 3. **IP addressing**
- **Static IP addressing on router interfaces:**
```plaintext
R1(config)#interface Serial0/0/0
R1(config-if)#clock rate 4000000
R1(config-if)#ip address 5.1.0.1 255.255.255.252
R1(config-if)#no shutdown
```

- **Static IP addressing on PCs and servers:**
- Use the IP Configuration tab on each device's Desktop.

### 4. **Dynamic NAT on routers HFC-Home and DSL-Home**
```plaintext
HFC-H(config)#ip access-list standard 1
HFC-H(config-std-nacl)#permit 192.168.1.0 0.0.0.255
HFC-H(config)#ip nat inside source list 1 interface GigabitEthernet0/0 overload
HFC-H(config)#interface GigabitEthernet0/0
HFC-H(config-if)#ip nat outside
HFC-H(config)#interface GigabitEthernet0/1
HFC-H(config-if)#ip nat inside
```

### 5. **Static NAT on Router R7**
```plaintext
R7(config)#ip nat inside source static 10.0.1.1 20.0.0.3
R7(config)#ip nat inside source static 10.0.1.2 20.0.0.4
R7(config)#ip nat inside source static 10.0.2.1 20.0.0.5
R7(config)#ip nat inside source static 10.0.2.2 20.0.0.6
R7(config)#ip nat inside source static 10.0.2.3 20.0.0.7
R7(config)#interface GigabitEthernet0/0
R7(config-if)#ip nat inside
R7(config)#interface GigabitEthernet0/2
R7(config-if)#ip nat inside
R7(config)#interface GigabitEthernet0/1
R7(config-if)#ip nat outside
```

### 6. Routing
## AS 2500: OSPF + Route Propagation
```plaintext
R2(config)#ip route 0.0.0.0 0.0.0.0 Serial 0/0/0
R2(config)#router ospf 1
R2(config-router)#network 31.0.0.0 0.0.0.7 area 0
R2(config-router)#default-information originate

R3(config)#router ospf 1
R3(config-router)#network 31.0.0.0 0.0.0.7 area 0
R3(config-router)#network 32.0.0.0 0.0.0.255 area 0

R4(config)#router ospf 1
R4(config-router)#network 31.0.0.0 0.0.0.7 area 0
R4(config-router)#network 33.0.0.0 0.0.0.255 area 0
```

## AS 2700: Static Routing
```plaintext
R7(config)#ip route 0.0.0.0 0.0.0.0 GigabitEthernet 0/1

## AS 2100: EIGRP + Static Routes
```plaintext
HFC-H(config)#ip route 0.0.0.0 0.0.0.0 GigabitEthernet 0/0
HFC-H(config)#router eigrp 2100
HFC-H(config-router)#no auto-summary
HFC-H(config-router)#network 183.2.0.0 0.0.0.255

HFC-N(config)#ip route 0.0.0.0 0.0.0.0 GigabitEthernet 0/1
HFC-N(config)#router eigrp 2100
HFC-N(config-router)#no auto-summary
HFC-N(config-router)#network 183.2.0.0 0.0.0.255
HFC-N(config-router)#network 13.0.81.0 0.0.0.255

R1(config)#router eigrp 2100
R1(config-router)#no auto-summary
R1(config-router)#network 13.0.81.0 0.0.0.255
```

## AS 2200: OSPF + Route Propagation
```plaintext
DSL-N(config)#ip route 0.0.0.0 0.0.0.0 GigabitEthernet 0/1
DSL-N(config)#router ospf 1
DSL-N(config-router)#default-information originate
DSL-N(config-router)#network 7.3.0.0 0.0.0.3 area 0
DSL-N(config-router)#network 99.0.248.0 0.0.0.255 area 0

R5(config)#router ospf 1
R5(config-router)#network 7.3.0.0 0.0.0.3 area 0

DSL-H(config)#router ospf 1
DSL-H(config-router)#network 99.0.248.0 0.0.0.255 area 0
DSL-H(config-router)#network 192.168.1.0 0.0.0.255 area 0
```

### VPN Configuration between R1 and R5
#### Configuration for R1:

1. **Define ISAKMP Policy**
```
    R1(config)#crypto isakmp policy 1
    R1(config-isakmp)#authentication pre-share
    R1(config-isakmp)#encryption aes
    R1(config-isakmp)#hash sha
    R1(config-isakmp)#group 2
    R1(config-isakmp)#lifetime 86400
```
2. **Define Pre-shared Key**
```
    R1(config)#crypto isakmp key YOUR_SHARED_KEY address 5.3.0.2
```
3. **Define Transform Set**
```
    R1(config)#crypto ipsec transform-set MY_TRANSFORM_SET esp-aes esp-sha-hmac
```
4. **Create Crypto Map**
```
    R1(config)#crypto map MY_CRYPTO_MAP 10 ipsec-isakmp
    R1(config-crypto-map)#set peer 5.3.0.2
    R1(config-crypto-map)#set transform-set MY_TRANSFORM_SET
    R1(config-crypto-map)#match address VPN_ACL
```
5. **Define ACL for VPN Traffic**
```
    R1(config)#access-list VPN_ACL permit ip 13.0.81.0 0.0.0.255 33.0.0.0 0.0.0.255
```
6. **Apply Crypto Map to Interface**
```
    R1(config)#interface Serial0/0/0
    R1(config-if)#crypto map MY_CRYPTO_MAP
```
#### Configuration for R5:

1. **Define ISAKMP Policy**
```
    R5(config)#crypto isakmp policy 1
    R5(config-isakmp)#authentication pre-share
    R5(config-isakmp)#encryption aes
    R5(config-isakmp)#hash sha
    R5(config-isakmp)#group 2
    R5(config-isakmp)#lifetime 86400
```
2. **Define Pre-shared Key**
```
    R5(config)#crypto isakmp key YOUR_SHARED_KEY address 5.1.0.1
```
3. **Define Transform Set**
```
    R5(config)#crypto ipsec transform-set MY_TRANSFORM_SET esp-aes esp-sha-hmac
```
4. **Create Crypto Map**
```
    R5(config)#crypto map MY_CRYPTO_MAP 10 ipsec-isakmp
    R5(config-crypto-map)#set peer 5.1.0.1
    R5(config-crypto-map)#set transform-set MY_TRANSFORM_SET
    R5(config-crypto-map)#match address VPN_ACL
```
5. **Define ACL for VPN Traffic**
```
    R5(config)#access-list VPN_ACL permit ip 33.0.0.0 0.0.0.255 13.0.81.0 0.0.0.255
```
6. **Apply Crypto Map to Interface**
```
    R5(config)#interface Serial0/0/0
    R5(config-if)#crypto map MY_CRYPTO_MAP
```

