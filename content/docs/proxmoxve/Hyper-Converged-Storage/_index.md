---
title: "Hyper Converged Storage"
weight: 3
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
# bookHref: ''
# bookIcon: ''
---

## Distributed Storage Infrastructure with Ceph & Proxmox
- Deployed and configured a Ceph storage cluster integrated with Proxmox VE.
- Installed and managed Ceph Monitor (MON), Manager (MGR), and Object Storage Daemon (OSD) services.
- Built distributed and fault-tolerant storage using CephFS and RADOS Block Devices (RBD).
- Configured shared storage for virtual machine and container workloads across a Proxmox cluster.
- Implemented software-defined storage (SDS) in a hyper-converged infrastructure environment.
- Managed storage pools, placement groups (PGs), metadata servers (MDS), and storage replication.
- Designed resilient storage architecture with automatic recovery and high availability capabilities.
- Integrated distributed file and block storage for VM images, backups, ISO repositories, and container data.


## Hyper-Converged Storage

### Add Ceph Storage
- Proxmox VE allow us to run Ceph on our nodes to provide distributed storage.
- We can create resilient file storage and block storage using Ceph.

{{< image src="ceph.png" >}}