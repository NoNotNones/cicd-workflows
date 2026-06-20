---
title: "Connectivity and Routing"
weight: 10
params:
  bookCollapseSection: true
---

# Design and Implement VNet Connectivity and Routing

{{% hint warning %}} 
##### Imagine the following architecture:
- 🏢 Hub = Headquarters (Central Network)
- 🏢 Spoke = Branch Office (Remote Network)

Each branch office:
- Has its own isolated network
- Cannot directly communicate with other branches by default
To connect to the headquarters: ➡️ A **VNet Peering (or Network Peering)** is established.

**Key Takeaway**: In a Hub-and-Spoke architecture, connectivity between Spoke networks is not automatically enabled. Inter-spoke communication requires additional routing, peering, or centralized network services such as a Firewall or Gateway.
{{% /hint %}}

{{% hint warning %}} 
##### Imagine your organization has:
- 🏢 50 branch offices
- 🌍 Operations in 10 countries
- 🌐 200 internal virtual networks (VNets)
#### If you manage each network individually:
- ❌ High operational overhead
- ❌ Difficult to maintain governance and consistency
- ❌ Increased risk of configuration errors

**Key Takeaway**: **Azure Virtual Network Manager (AVNM)** is a centralized management service that enables organizations to define, deploy, and manage connectivity and security configurations across multiple Azure Virtual Networks at scale. It simplifies network administration by automating peering, topology management, routing, and security policy deployment from a single control plane.
{{% /hint %}}

{{% hint warning %}} 
##### Imagine you're driving from your home to a destination.
🏠 Home -> Several roads are available.

- By default, Google Maps selects the most efficient route based on its routing logic. **System Routes** are like the default routes chosen by Google Maps.
  - No manual configuration is required: VM -> Internet (Default Azure Route)
- Azure networking works in a similar way. Azure automatically creates and manages these routes to enable connectivity between:
  - VNets
  - Subnets
  - On-premises networks
  - The Internet
- A **User-Defined Route (UDR)** allows administrators to override Azure's default routing decisions. 
- Think of it as telling Google Maps: "Do not use the default route. Use this specific road instead."
{{% /hint %}}

{{% hint warning %}} 
#### Imagine a company with:
- 🏢 500 employees
- Each employee has an internal extension number:
101
102
103
...
- These extension numbers are only valid inside the company.
- However, when employees make calls to people outside the organization:
  - 📞 External callers do not see the internal extension numbers.
- Instead, everyone appears to be calling from:
Company Hotline
028-12345678

**Key Takeaway**: Azure NAT Gateway provides scalable outbound Internet connectivity for resources using private IP addresses. It performs Network Address Translation (NAT), allowing multiple virtual machines within a subnet to share one or more public IP addresses while keeping the resources private and protected from direct inbound Internet access.

{{% /hint %}}