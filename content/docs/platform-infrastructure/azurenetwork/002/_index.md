---
title: "Azure DNS"
weight: 102
params:
  bookCollapseSection: true
---

# Design and Implementing Name Solutions 
## Design Public DNS Zones
- Determine Public or Private
  - Public is registered with a domain registrar
- REgion and/or zone place ments
  - DNS zones can exist in multiple regions
  - Different set of nameservers
- Resolved at 168.63.129.16
- Support common Azure and DNS capabilities
  - RBAC, Activity Log, Resource Locks
  - Record types, Record sets, DNSSEC
 
{{% hint success %}}
### Demo: Create a Public DNS Zone
{{% /hint %}}

- Create a DNS Zone: **hugelab.net**
  - Add Recordsets: **webapp1**

{{< image src="./dns0.jpg" >}}
{{< image src="./dns01.jpg" >}}
{{< image src="./dns02.jpg" >}}

## Design private DNS zones
- DNS linked to one or more Virtua Networks
  - Still resolves form 168.63.129.16, but must orifinate from a vNet
    - Unless Custom DNS servers a defined on the vNet
- Restrictions
  - Cannot be single-lable names
  - Cannot create NS records
  - Restricted zone names: microsoft.com, azure.com
  - 
{{% hint success %}}
### Demo: Create a Private DNS Zone
{{% /hint %}}

{{< image src="./dns1.jpg" >}}
{{< image src="./dns2.jpg" >}}

## Design VNet name resolution and Azure DNS Private Resolver
{{% hint info %}} 
Design name resolution in side a VNet, 
configure DNS settings fo a Vnets,
configure public and private DNS zones, 
and link a private DNS zone to a VNet,
Design and implement Azure DNS Private Resolver
 {{% /hint %}}

### Azure DNS Private Resolver
- Bi-directional DNS for on-premises and vitual networks
  - Requires ExpressRoute or a VPN
- Use Private DNS Zone in Azure + On-premises DNS zone
- Inbound Endpoints
  - Name resolution for on-premises resources
- Outbound Endpoints
  - Uses DNS forwarding rulesets to determine where to send a query

{{% hint success %}}
### Demo: Create a DNS Private Resolver
{{% /hint %}}

{{< image src="./r1.jpg" >}}
{{< image src="./r2.jpg" >}}
{{< image src="./r3.jpg" >}}
{{< image src="./r4.jpg" >}}
{{< image src="./r5.jpg" >}}
{{< image src="./r6.jpg" >}}