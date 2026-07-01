---
title: "Building a Highly Available Kubernetes Cluster on Proxmox"
weight: 111
params:
  bookCollapseSection: true
---

# Building a Highly Available Kubernetes Cluster on Proxmox

## Objectives

This project demonstrates how to build a **Highly Available (HA) Kubernetes cluster** on **Proxmox VE** using **kubeadm**. The environment is designed to provide high availability for the Kubernetes control plane through **HAProxy** and **Keepalived**, while **Calico** provides container networking.

After completing this guide, you will be able to:

- Deploy virtual machines on Proxmox VE.
- Configure a consistent environment across all Kubernetes nodes.
- Configure time synchronization using Chrony.
- Prepare the infrastructure for a highly available Kubernetes cluster.
- Build an HA control plane using HAProxy and Keepalived.
- Deploy a production-ready Kubernetes cluster with kubeadm.

---

# Infrastructure Overview

## Architecture

The infrastructure consists of:

- 2 Load Balancer nodes
- 1 Virtual IP (VIP)
- 3 Kubernetes Control Plane nodes
- 2 Kubernetes Worker nodes
- 1 NFS Server

---

## Infrastructure Inventory

| Role | Hostname | IP Address |
|------|----------|------------|
| Load Balancer | `st-lb01.stit.local` | `<ST.IP>.247` |
| Load Balancer | `st-lb02.stit.local` | `<ST.IP>.248` |
| Virtual IP | `stlbsv.stit.local` | `<ST.IP>.245` |
| Control Plane | `k8s-master1.stit.local` | `<ST.IP>.232` |
| Control Plane | `k8s-master2.stit.local` | `<ST.IP>.233` |
| Control Plane | `k8s-master3.stit.local` | `<ST.IP>.234` |
| Worker Node | `st-worker1.stit.local` | `<ST.IP>.211` |
| Worker Node | `st-worker2.stit.local` | `<ST.IP>.212` |
| NFS Server | `st-nfs-sv.stit.local` | `<ST.IP>.204` |

---

# Prerequisites

## Software Versions

| Component | Version |
|-----------|---------|
| Proxmox VE | 9.x |
| Ubuntu Server | 24.04 LTS |
| Kubernetes | v1.35.x |
| Container Runtime | containerd |
| Calico | Latest |
| HAProxy | Latest |
| Keepalived | Latest |
| Chrony | Latest |

---

# Preparing the Servers

Perform the following configuration on **every virtual machine** before installing Kubernetes.

---

## Configure the Time Zone

Set the system time zone.

```bash
sudo timedatectl set-timezone Asia/Ho_Chi_Minh
```

Verify the current configuration.

```bash
timedatectl
```

---

## Update System Packages

Update the package index and install the latest system updates.

```bash
sudo apt update
sudo apt upgrade -y
```

It is recommended to reboot the server after completing the upgrade.

```bash
sudo reboot
```

---

## Configure Hostname Resolution

To simplify communication between cluster nodes, configure hostname resolution on every server.

Edit the `/etc/hosts` file.

```bash
sudo nano /etc/hosts
```

Add the following entries.

{{% details title="/etc/hosts" open=true %}}

```text
<ST.IP>.247 st-lb01.stit.local st-lb01
<ST.IP>.248 st-lb02.stit.local st-lb02

<ST.IP>.245 stlbsv.stit.local stlbsv

<ST.IP>.232 k8s-master1.stit.local k8s-master1
<ST.IP>.233 k8s-master2.stit.local k8s-master2
<ST.IP>.234 k8s-master3.stit.local k8s-master3

<ST.IP>.211 st-worker1.stit.local st-worker1
<ST.IP>.212 st-worker2.stit.local st-worker2

<ST.IP>.204 st-nfs-sv.stit.local st-nfs-sv
```

{{% /details %}}

Verify hostname resolution.

```bash
ping k8s-master1
ping stlbsv
```

All hosts should resolve successfully.

---

# Configuring Time Synchronization

Accurate time synchronization is essential for Kubernetes components, TLS certificates, and distributed systems.

This project uses **Chrony** as the Network Time Protocol (NTP) service.

---

## Configure the NTP Server

> Perform this configuration only on the dedicated time server.

Server IP:

```text
<ST.IP>.240
```

Install Chrony.

```bash
sudo apt update
sudo apt install chrony -y
```

Edit the configuration file.

```bash
sudo nano /etc/chrony/chrony.conf
```

Append the following configuration.

{{% details title="chrony.conf" open=true %}}

```conf
allow <ST.IP>.0/24
local stratum 10
```

{{% /details %}}

Restart the Chrony service.

```bash
sudo systemctl restart chrony
sudo systemctl enable chrony
```

Verify the service status.

```bash
systemctl status chrony
```

---

## Configure NTP Clients

Perform this configuration on **every Kubernetes node**.

Install Chrony.

```bash
sudo apt update
sudo apt install chrony -y
```

Edit the Chrony configuration.

```bash
sudo nano /etc/chrony/chrony.conf
```

Replace or append the following configuration.

{{% details title="chrony.conf" open=true %}}

```conf
server <ST.IP>.240 iburst
makestep 1.0 3
rtcsync
```

{{% /details %}}

Restart the Chrony service.

```bash
sudo systemctl restart chrony
sudo systemctl enable chrony
```

Verify synchronization.

```bash
chronyc tracking
```

---

## Verification

On the NTP server, verify that clients are successfully synchronized.

```bash
chronyc clients
```

Example output:

{{< image src="./time1.jpg" >}}

If all Kubernetes nodes appear in the client list, the time synchronization service has been configured successfully.

---

# Next Steps

The infrastructure is now prepared for deploying the **High Availability layer**.

In the next section, you will configure:

- Keepalived
- HAProxy
- Virtual IP (VIP)
- Kubernetes API Load Balancer