---
title: "Peering - Network Manager - UDR - NAT "
weight: 105
params:
  bookCollapseSection: true
---

## **Design service chaining and implement VNet peering**

{{% columns ratio="1:1" %}}
- ### Virtual Netwok Peering
  -  Connect Vnets
  -  Address spaces cannot overlap
  -  Same or different regions
  -  Uni-directional or Bi-directional
  -  Facilitates hub and spoke topololy model

- {{< image src="./p1.jpg" >}}
{{% /columns %}}

{{% columns %}}
- ### Service Change
  - By default, peering is not transitive
  - Extends HUb and Spoke topology
  - Requires additional overhead
    - Route table
    - Virtual Network Gateway
    - Firewall / Route Server
  
- {{< image src="./p2.jpg" >}}
{{% /columns %}}

{{% hint success %}}
### Demo: Create a peering connection
{{% /hint %}}

- Network: {{< image src="./net.jpg" >}}

{{% columns %}}
- **vNet1** > Add Peering: **vNet1toSpoke1**
  - Vitual network: Spoke1 (az-700course)
  - Remote vituavirnetwork peering settings:
    -  Allow 'Spoke1' to access 'vNet1'
  - Local virtual network summary:
    - Peering link name: **Spoke1toVnet1**
  - Local virtual network peering settings
     - Allow 'vNet1' access 'Spoke1'
  
-  {{< image src="./p3.jpg" >}}
{{% /columns %}}

- **Spoke1** > Peerings checking
   {{< image src="./p4.jpg" >}}


## **Manage VNet connectivity with Azure Virtual Network Manager**
### Azure Virtual Network Manager
- Facilitates centralized management of vNets and IP addresses
- Simplifies vNet to vNet connectivity
  - Mesh, Hub and spoke, direct connectivity
- Centralizes vNet security configuration with Security Admin Rules
- Scoped at a subscription or management group

{{% hint success %}}
### Demo: Create a Virtual Network Manager
{{% /hint %}}

{{% columns ratio="1:1" %}}
- Virtual Network Manager > Create a network manager
  - Subscription: Azure Pass - Sponsorship
    - Resource group: az-700course
  - Instance details
    - Name: NetworkMgr1
    - Region: (US) West US
    - Features:
      - Connectivity
      - Security admin
      - User defined routing
  - Management scope
      - Add: Subscription [Azure Pass - Sponsorship]

- {{< image src="./v1.jpg" >}} 
{{% /columns %}}

{{% columns ratio="1:2" %}}
- {{< image src="./v2.jpg" >}}
- {{< image src="./v3.jpg" >}}
{{% /columns %}}

- NetworkMrg1 > Create a connectivity configuration
  - Name: **FullMesh1**
  - Topology: Mesh
    - Mesh connectivity access regions [Checked]
    - Network groups:
      - Create new: **vNetGroup1** , **Group2**, **Group3** 
      - View topology

{{% columns %}}
- {{< image src="./v4.jpg" >}}
- {{< image src="./v5.jpg" >}}
{{% /columns %}}

- **FullMesh1**  > Deploy a configuration
  - Target Region: East US > Deploy
  
{{% columns %}}
- {{< image src="./v6.jpg" >}}
- {{< image src="./v7.jpg" >}}
- {{< image src="./v8.jpg" >}}
{{% /columns %}}

## **Design UDRs, route tables, routing, and Azure Route server**
### Virtual Network Default Routes
{{% columns %}}
- 
  - Intergrated into the virtual network
  - Cannot be mofified, but overridden with UDRs
- {{< image src="./u1.jpg" >}}
{{% /columns %}}

### User Defined Routes

{{% columns %}}
- 
  - Create with route tables
  - Modifies how a virtual network sends traffic
  - Define address prefix and next hop
    - Virtual appliance, Gateway, Internet

- {{< image src="./u2.jpg" >}}
{{% /columns %}}

### Forced Tunneling
{{% columns ratio="2:1" %}}
- 
  - Combines UDRs with a VPN
  - Common in high-secyrity environments
    - No outbound connectivity from Azure
    - Force traffic through on-premises hardware
  
-  {{< image src="./u3.jpg" >}}
{{% /columns %}}

{{% hint success %}}
### Demo: Create a Route Table and UDRs
{{% /hint %}}

{{% columns %}}
- Route table > Create Route table
  - Subscription: Azure Pass - Sponsorship
    - Resource group: az-700course
  - Region: West US
  - Name: RT1
  - Propagate gateway routes: Yes
- {{< image src="./rt1.jpg" >}}
{{% /columns %}}

{{% columns %}}
- RT1 > Add route
  - Route name: **to-onprem**
  - Destination type: IP addresses
  - Destination IP addresses/CIDR ranges: 172.16.0.0/16
  - Next hop type: Virtual applicance
  - Next hop address: 10.0.0.5

- {{< image src="./rt2.png" >}}
{{% /columns %}}

{{< image src="./rt3.jpg" >}}


- RT1 > Associate subnet: **subnet1**, **subnet10**
{{% columns %}}
- {{< image src="./rt4.jpg" >}}
- {{< image src="./rt5.jpg" >}}
{{% /columns %}}

### Troubeshooting Routing
{{% columns %}}
- 
  - Remember process priority
    1. User-defined routes
    2. BGB routes
    3. System routes
  - View Effective Routes form a VM
  - View Next Hop targets from Network Watcher

- Network Watcher: {{< image src="./w1.jpg" >}}
{{% /columns %}}

{{% columns %}}
- {{< image src="./w2.jpg" >}} 
- {{< image src="./w3.jpg" >}}
{{% /columns %}}

## **User NAT Gateway and configure forced tunnelings**
{{% hint info %}} 
Identify appropriate use cases for a network address translation (NAT) gateway, implement forced tunneling
{{% /hint %}}


{{% columns ratio="4:1" %}}
- ### NAT gateway
    - Recommended resource for outbound connectivity
    - VMs, AKS, Containers, Function Apps, and more
  - Does not require a route table or UDRs
    - Associate the gateway with a subnet
      - 1:1 relationship at the subnet level
      - Subnets cannot have more than 1 NAT Gateway
    - Default route can be overridden with UDR
  - Combine with Azure Firewall for hub & spoke outbound connectivity

- {{< image src="./n1.jpg" >}}
{{% /columns %}}


{{% hint success %}}
### Demo: Create a NAT Gateway
{{% /hint %}}

- NAT Gateway > Create network address translation (NAT) gateway
  - Basic:
    - Subscription: Azure Pass - Sponsorship
      - Resource group: az-700course
    - NAT Gateway name: **gw1**
    - Region: West US
    - Availability zone: <empty>
    - TCP idle timeout (minitutes): 4
  - Outbound IP
    - Public IP address: **publicprefix1 (168.62.192.84)**
  - Subnet
    - Virtual network: **vNet1**
      - Subnet name: **subnet1**, **subnet10**

{{% columns %}}
- {{< image src="./n2.jpg" >}} 
- {{< image src="./n3.jpg" >}}
{{% /columns %}}
