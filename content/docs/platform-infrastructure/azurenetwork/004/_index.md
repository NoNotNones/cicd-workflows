---
title: "Monitor Networks"
weight: 10
params:
  bookCollapseSection: true
---

# Monitor and troubleshoot network issues with Azure Network Watcher and Azure Monitor Network Insights
{{% hint info %}} 
Configure monitoring, network diagnostics, and logs in Azure Network Watcher; 
Monitor and troubleshoot network health by using Azure Netwatcher; 
Monitor and troubleshoot networks by using Azure Monitor Network Insights
{{% /hint %}}

## Network Watcher
- Multi-function tool for monitoring and troubleshooting
  - Connection Monitor
    - Requires Log Analytics
    - Defines a test group and test configuration
      - Source, endpoint, protocol, and success thresold

{{% hint success %}}
### Demo: Configure connection Monitor
{{% /hint %}}

- Network Watcher > Create Connection Monitor
  - Basic :
    - Connection Monitor Name: **MonitoringDNS**
    - Subscription: Azure Pass - Sponsorship
    - Region: West US
  - Test groups:
    - Test group name: **TestGroup1**
      - Add sources
        - Type: Virtual Machines > **SERVER22**
      - Add Test configuration
        - Test configuration name: **TestConfig1**
        - Protocol: ICMP
        - Test Frequency: Every 30 seconds
        - Success Threshold
          - Checks failed (%): 5%
          - Round trip time (ms): 10
      - Add Destinations
        - External Addresses 
          - Add Endpoint: 
            - Endpoint (URL/IP): **8.8.8.8**
  - Workspace
    - Workspace: Custom Workspace

{{% columns %}}
- {{< image src="./w0.jpg" >}}
- {{< image src="./w1.jpg" >}}
{{% /columns %}}

{{% columns %}}
- {{< image src="./w2.jpg" >}}
- {{< image src="./w3.jpg" >}}
{{% /columns %}}


{{% columns %}}
- {{< image src="./w4.jpg" >}}
- {{< image src="./w5.jpg" >}}
{{% /columns %}}

{{% columns %}}
- {{< image src="./w6.jpg" >}}
- {{< image src="./w7.jpg" >}}
{{% /columns %}}

{{% columns %}}
- {{< image src="./w8.jpg" >}}
- {{< image src="./w9.jpg" >}}
{{% /columns %}}

### Traffic Analytics
- Provides analysis of vNet Flow Logs
  - Requires Log Analytics Workspace
  - Define time interval for analysis
- Identifies hot sports, open ports, and apps that try to access the internet
- Identigy traffic flow patterns to help optimize network performance

{{% hint success %}}
### Demo
{{% /hint %}}

- Network Watcher > Traffic Analytics > Configure
  - **Create a flow log** 
    - Flow log type: Virtual network
    - Select target resource: **vNet1**
    - Subscription: Azure Pass - Sponsorship
      - Storage accounts: y6b8ycaz104vaulasrcache


{{< image src="./t1.jpg" >}}

{{% columns %}}
- {{< image src="./t2.jpg" >}}
- {{< image src="./t3.jpg" >}}
- {{< image src="./t4.jpg" >}}
{{% /columns %}}

{{% columns %}}
- {{< image src="./t5.jpg" >}}
- {{< image src="./t6.jpg" >}}
- {{< image src="./t7.jpg" >}}
{{% /columns %}}

## Actiate and monitor DDoS protection in Azure
## Evaluate network secuirty using Microsoft Defender for Cloud