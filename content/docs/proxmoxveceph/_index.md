---
title: "Multi-Node Proxmox & Ceph Infrastructure Deployment"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
# bookHref: ''
# bookIcon: ''
---

# Multi-Node Proxmox & Ceph Infrastructure Deployment
## Overview
This project demonstrates the design and implementation of a **private cloud infrastructure** using a **Proxmox VE cluster combined with a Ceph distributed storage system**.

The objective is to simulate an **enterprise-grade cloud environment** with a strong focus on:

- High Availability (HA)
- Distributed Storage Architecture
- Fault Tolerance & Resilience
- Infrastructure Reliability
- Incident Troubleshooting & System Recovery

The architecture replicates core cloud computing principles found in platforms such as **Microsoft Azure (IaaS compute + storage separation model)**.

{{< image src="final.png" >}}

## Infrastructure Architecture
| Node        | Role        | CPU | RAM | Storage | Notes |
|-------------|------------|-----|-----|----------|------|
| st-prox     | Compute + ZFS | i5-13400 | 40GB | 2x SSD + NVMe | Master node |
| st-prox04   | Compute     | i3-7100T | 16GB | NVMe + SSD | Lightweight VM node |
| st-prox05   | Compute     | i5-14500 | 16GB | NVMe | High-performance node |
| st-prox01   | Ceph Storage | i5-7500T | 16GB | NVMe + SSD | MON/MGR/OSD |
| st-prox02   | Ceph Storage | i5-7500T | 16GB | NVMe + SSD | MON/MGR/OSD |
| st-prox03   | Ceph Storage | i5-7500T | 16GB | NVMe + SSD | MON/MGR/OSD |


The system is composed of **6 physical nodes**, logically separated into compute and storage layers.

### 💾 Storage Layer (Ceph Cluster)

The storage layer is built using a **3-node Ceph cluster**:

- **st-prox01**
- **st-prox02**
- **st-prox03**

#### Roles:
- **MON (Monitor):** Cluster quorum and consensus management
- **MGR (Manager):** Cluster monitoring and control services
- **OSD (Object Storage Daemon):** Distributed storage backend

This layer provides:
- Replicated storage (fault-tolerant design)
- Shared block storage (RBD)
- Shared filesystem (CephFS)

---

### 🖥️ Compute Layer (Proxmox Cluster)

The compute layer consists of **3 Proxmox nodes**:

- **st-prox**
- **st-prox04**
- **st-prox05**

#### Roles:
- Virtual Machine workloads
- Application hosting environment
- Local ZFS storage (on st-prox node)

This layer handles:
- VM lifecycle management
- Compute workload distribution
- High availability orchestration via Proxmox cluster (Corosync)

---
## ⚙️ Key Technologies

The infrastructure is built using the following core technologies:

- **Proxmox VE Cluster**
  - Corosync-based clustering
  - High Availability (HA) management

- **Ceph Storage System**
  - RBD (block storage for VM disks)
  - CephFS (distributed file system)
  - MON / MGR / OSD architecture

- **Linux Infrastructure Services**
  - Network configuration & routing
  - Time synchronization using Chrony/NTP
  - System-level service management

- **Distributed Systems Concepts**
  - Quorum-based consensus
  - Data replication (size=3)
  - Consistency and fault tolerance

- **Linux System Administration**
  - Cluster operations
  - Storage provisioning
  - Service troubleshooting & recovery

---

## 🧩 Design Principles

The architecture follows enterprise cloud design principles:

- **Separation of Compute and Storage**
  - Independent scaling of compute and storage resources

- **High Availability (HA)**
  - No single point of failure across cluster nodes

- **Data Durability via Replication**
  - Ceph replication factor = 3 for fault tolerance

- **Quorum-Based Cluster Stability**
  - MON cluster ensures consistent system state

- **Infrastructure Resilience**
  - Self-healing storage and service recovery mechanisms

- **Cloud-Style Architecture Simulation**
  - Mimics Azure IaaS architecture patterns (VM + distributed storage)

---
