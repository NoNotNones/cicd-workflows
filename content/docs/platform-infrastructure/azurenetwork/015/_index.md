---
title: "WAF"
weight: 115
params:
  bookCollapseSection: true
---

# Design and configure WAF deployments and detection/prevention modes

### Web App Firewall
- Policy-based solution
    - Global - WAF + Azure Font Door : Policy tier must match Font Door Tier
    - Regional - WAF + Application Gateway

- Policy mode
    - Detection
    - Prevention

- Blocks common web-based attacks
    - SQL injection, cross-site scripting, command injection

# Configure WAF rule sets on Application Gateway and Azure Front Door
## App Gateway Managed Rules
- Managed rulesets
    - Microsoft default or OWASP
    - Bot Manager additional rules avilable
## App Gateway WAF Policy Settings
- Requeset body inspection
- Maximun request body limit
- Custom rules
    - Match or Rate limit
    - Priotiry
    - Match type: IP address, number, string, geo-location
    - If-Then conditions: Allow, Deny, Log traffic

# Font Door WAF Managed Rules
- Default ruleset
    - Micosoft default
    - Default
    - None

- Anomaly score action
    - Block, log, or redirect

- Additional ruleset
    - Bot Manager
    - Bot Protection
    - None

# Font Door WAF Policy Settings
- Inspect request body
- Redirect URL
- Block response code and body
- Custom rules
    - Match or Rate limit
    - Priotity
    - Match type: IP address, size, string, geo-location
- If-Then conditions: Allow, Deny, log traffic