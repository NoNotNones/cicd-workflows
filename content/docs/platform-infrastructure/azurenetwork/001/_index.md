---
title: "Azure Networking"
weight: 101
params:
  bookCollapseSection: true
---

# Plan and implement network segmentation and address spaces

## Network Segmentation & Address Spaces
- Virtual Network security boundaries
- Address Spaces vs Subnets
- Vitual Network Scope
  - Subscription
  - Region
- Best practices
  - Avoid overlapping addresses
  - Use subnets as segmentation
  - Secure with NSGs and Firewalls
  
## Design and implement IP Addressing for Azure Resources
{{% hint info %}} Info
Create a virtual network (VNet) and a prefix for public IP addresses,
choose when to use a public IP address prefix and create custom IP prefixes {{% /hint %}}

### Create a Virtual Network
- Scope & Placement
    - Subscription
    - Region
    - Resource Group
- Address Space & Subnet

{{% hint success %}}
### Demo: Create a virtual network 
{{% /hint %}}

- Home > Network foundation | Virtual Networks
- Basic: 
  - Subscription: **Azure Pass- Sponsorship**
  - Resource group: **(New) az-700course**
  - Virtual network name: **vNet1**
  - Region: **(US) West US**
- Security:  Default
- IP addresses: Change to > `10.5.0.0 /16` > Review + Create
 
| Subnet | IP address range | Size
| --   | -- | --
| `default` | 10.5.0.0 - 10.5.0.255 | /24 (256 addresese)\

{{< image src="./vnet1.jpg" >}}
{{< image src="./vnet2.jpg" >}}

- Create New Subnet in  **vNet1**:
  - Settings > Address space : Add IPv4 address space
    - Add a subnet: **subnet10 (10.10.0.0/24)** 

{{< image src="./subnet10.jpg" >}}
{{< image src="./subnet101.jpg" >}}


## Public IP address Prefixes
- Pre-defined range of public addresses
- IPv4 and IPv6 ranges
  - /31 or /127 - 2 addresses
  - /30 or /126 - 4 addresses
  - /29 or /125 - 8 addresses
  - /28 or /124 - 16 addresses
- Scope
  - Subscription & Region
  
{{% columns %}}

- ### Assign static IPs to individual resources
  - Virtual machines
  - Load balancers
  - Azure firewalls
  - VPN Gateways

- ### Assign full prefixes to other resources
  - Virtual Machinec Scale Sets
  - Load Blancers
  - NAT Gateways
{{% /columns %}}

## Custom Public IP Prefix
- Bring your own IPs to Azure
  - Must be obtained from a Regial Internet Registry
  - Cannot be smaller than /24
- Multi-step process to use in Azure
  1. Requires route authorization
  2. Certificate with public key
  3. Cretae Azure resource object
  4. After validation, comission an IP address : IP address are not advertised or reachable until commisioned

{{% hint success %}}
### Demo: Create a public IP prefix
{{% /hint %}}

- Home > Public IP Prefixes
    - Create a public IP prefix: **prefix1**
      - Create a public IP addess: **publicprefix1**
      - Checking static IP
  
{{< image src="./publicip.jpg" >}}
{{< image src="./pip1.jpg" >}}
{{< image src="./pip2.jpg" >}}

# Plan and configure subnetting for Azure services
## Virtual Network Subnetting
- Must be part of the address space
- Defined in CIDR notation: 192.168.1.0/24
- Subnet delegation
  - Associates a subnet with a PaaS resource
  - Give the resource full control over the subnet
  
{{% hint success %}}
### Demo: Delegate a subnet
{{% /hint %}}

- **vNet1**> Subnets > **subnet1**
  - Subnet Delegation *(Delegate subnet to a service)*: **Microsoft.Sql/managedInstanced**
  
{{< image src="./de1.jpg" >}}
{{< image src="./del2.jpg" >}}

## Dedicated Subnets
- Similar to subnet delegation: Resources still 'own' the entire subnet
- Certain resources required specific subnet: Name or Address range
- Excample: Azure Bastion
  - Name: AzureBastionSubnet
  - Size: /26 minimum
- Other resource types: Firewall, App Gateway, VPN gateway

{{% hint success %}}
### Demo: Dedicated Subnets
{{% /hint %}}

- vNet1 > Add Subnet : **Checking status**

{{< image src="./ded1.jpg" >}}
{{< image src="./ded2.jpg" >}}
{{< image src="./ded3.jpg" >}}

- Bastion checking status

{{< image src="./ded4.jpg" >}}