---
title: "Multi-Node Proxmox & Ceph Infrastructure Deployment"
weight: 1
---

# Multi-Node Proxmox & Ceph Infrastructure Deployment

## Overview

This project demonstrates the design and implementation of a **private cloud infrastructure** using a **Proxmox VE cluster combined with a Ceph distributed storage system**.

The objective is to simulate an enterprise-grade cloud environment with a strong focus on:

- High Availability (HA)
- Distributed Storage Architecture
- Fault Tolerance & Resilience
- Infrastructure Reliability
- Incident Troubleshooting & System Recovery

The architecture replicates core cloud computing principles found in platforms such as **Microsoft Azure (IaaS compute + storage separation model)**.

{{< image src="final.png" >}}

---

## 🧠 Infrastructure Architecture

The system is divided into two logical layers:

- 🖥️ Compute Layer (Proxmox Cluster)
- 💾 Storage Layer (Ceph Cluster)

---

## 🖥️ Compute Layer

| Node | CPU | RAM | Storage | Notes |
|------|-----|-----|--------|------|
| st-prox | Intel i5-13400 | 40GB | NVMe 512GB + 2x SSD 500GB | Master node + ZFS mirror |
| st-prox04 | Intel i3-7100T | 16GB | NVMe 256GB + SSD 240GB | Lightweight VM node |
| st-prox05 | Intel i5-14500 | 16GB | NVMe 256GB | High-performance compute |

---

## 💾 Ceph Storage Layer

| Node | CPU | RAM | Storage | GPU | Roles |
|------|-----|-----|--------|-----|------|
| st-prox01 | Intel i5-7500T | 16GB | NVMe 512GB + SSD 500GB | NVIDIA GTX 1060 Mobile | MON / MGR / OSD |
| st-prox02 | Intel i5-7500T | 16GB | NVMe 512GB + SSD 500GB | NVIDIA GTX 1060 Mobile | MON / MGR / OSD |
| st-prox03 | Intel i5-7500T | 16GB | NVMe 512GB + SSD 500GB | NVIDIA GTX 1060 Mobile | MON / MGR / OSD |

---

## 🎮 GPU Integration (Hybrid Design)

The Ceph storage nodes (st-prox01–03) include **dedicated NVIDIA GPUs**, enabling a hybrid architecture model:

### Primary role
- Ceph distributed storage (MON / MGR / OSD)

### Secondary capability
- GPU-enabled compute expansion

### Potential use cases
- GPU passthrough to VMs (Proxmox PCIe passthrough)
- AI / inference workloads (future extension)
- VDI / remote desktop acceleration
- Hybrid compute + storage experimentation

> This design reflects a **real-world cloud pattern where storage and specialized compute capabilities coexist in the same infrastructure domain**.

---

## 💾 Ceph Cluster Design

The storage system is implemented as a **3-node Ceph cluster**:

- st-prox01
- st-prox02
- st-prox03

### Roles

- **MON (Monitor):** Cluster quorum & consensus
- **MGR (Manager):** Cluster control & metrics
- **OSD (Object Storage Daemon):** Distributed storage engine

### Features

- Data replication (size = 3)
- Fault tolerance
- Self-healing storage
- RBD block storage for VM disks
- CephFS distributed filesystem

---

## 🖥️ Proxmox Compute Cluster

The compute layer consists of:

- st-prox
- st-prox04
- st-prox05

### Responsibilities

- Virtual machine lifecycle management
- Workload scheduling
- HA orchestration via Corosync
- Local ZFS storage (on st-prox)

---

## ⚙️ Key Technologies

- Proxmox VE Cluster (Corosync HA)
- Ceph Storage (RBD / CephFS)
- Linux Networking & Time Sync (Chrony/NTP)
- Distributed Systems (quorum, replication, consistency)
- Linux System Administration
- Infrastructure troubleshooting & recovery

---

## 🧩 Design Principles

- Separation of compute and storage layers
- High Availability (HA) design (no SPOF)
- Data durability via replication (Ceph size=3)
- Quorum-based cluster consistency
- Infrastructure resilience & self-healing
- Cloud architecture simulation (Azure-style IaaS model)