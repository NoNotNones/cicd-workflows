---
title: "DevOps Projects"
weight: 103
params:
  bookCollapseSection: true
---

# Building a Highly Available Kubernetes Cluster on Proxmox
## Architecture Highlights
```py

                  Proxmox Cluster
+----------------------------------------------------------------+
                    +--------------------+
                    | Chrony Server      |
                    | .240               |
                    +--------------------+
                             |
                    +--------------------+
                    | NFS Server         |
                    | .204               |
                    +--------------------+
                             |
 +------------------+                    +------------------+
 | LB01             |<------VRRP-------->| LB02             |
 | .247             |                    | .248             |
 | Keepalived       |                    | Keepalived       |
 | HAProxy          |                    | HAProxy          |
 +--------+---------+                    +---------+--------+
          \                                      /
           \                                   /
            \_______  ____VIP .245 ____________/
                             |
      -------------------------------------------------
      |                      |                         |
    +-----------+      +-----------+       +-----------+
    | Master1   |      | Master2   |       | Master3   |
    | .232      |      | .233      |       | .234      |
    +-----+-----+      +-----+-----+       +-----+-----+
    |                        |                         |
    +------------------------+-------------------------+
                             |
                 Kubernetes Control Plane
                             |
                   +---------+---------+
                   |                   |
         +------+-----+      +------+------+
         | Worker1    |      | Worker2     |
         | .211       |      | .212        |
         +------------+      +-------------+
```


---
## Project Outcome
This project successfully delivers a **Highly Available Kubernetes Cluster** built on Proxmox VE.

The environment provides:

- High Availability Kubernetes Control Plane
- Virtual IP failover using Keepalived
- Kubernetes API load balancing with HAProxy
- Three Control Plane nodes
- Two Worker nodes
- Containerd container runtime
- Calico networking
- Centralized time synchronization using Chrony
- Remote cluster administration with kubectl

## Technologies Used

| Category | Technology |
|----------|------------|
| Virtualization | Proxmox VE |
| Operating System | Ubuntu Server 24.04 LTS |
| Container Runtime | containerd |
| Kubernetes | kubeadm |
| Networking | Calico |
| Load Balancer | HAProxy |
| High Availability | Keepalived |
| Time Synchronization | Chrony |
| Remote Administration | kubectl (WSL) |

The cluster is now ready for deploying production-grade Kubernetes services and applications.