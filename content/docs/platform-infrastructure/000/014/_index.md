---
title: "Azure Firewall"
weight: 114
params:
  bookCollapseSection: true
---

## Azure Firewall
- Stateful layer 3 to 7 firewall
    - Inspect East-West and North-South

- Serveral categories of feautures
    - Filtering and inspection
    - DNS and threat protection
    - Performance and scale
    - Management and monitoring
    - Compliance and certifications

## Azure Firewall SKUs
- Basic
    - Stateful. NAT, High availability, zone redundancy
- Standard
    - SNI-Based FQDN filtering
    - Network-level trafic filtering
    - Web content filtering
- Premium
    - URL filtering
    - Inbound/Outboud TLS termination
    - Fully-managed IDPS

## Implement Azure Firewall, rules, and Firewall Manager policies
### Azure Firewall Deployment
- Required information to deploy
    - Firewall tier
    - Firewall policy
    - Virtual Network with AzureFirewallSubet
    - Public IP Address
  
### Firewall Policy
- Defines a majority of the firewall-related settings
  - Application, Network, and DNAT rules
  - DNS settings
  - Threat intelligence
  - TLS inspection
  - IDPS
  
  
