---
title: "Remote Cluster Access"
weight: 114
params:
  bookCollapseSection: true
---

---

# Remote Cluster Access

After the Kubernetes cluster has been deployed successfully, you can manage it remotely from your local workstation using **kubectl**.

This section demonstrates how to configure **Windows 11 with WSL (Ubuntu)** as a Kubernetes administration workstation.

---

# Installing kubectl on Windows (WSL)

The following steps are performed inside **Ubuntu on WSL**.

## Install Required Packages

```bash
sudo apt-get update

sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gpg
```

---

## Add the Kubernetes Repository

Download the Kubernetes package signing key.

```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.35/deb/Release.key \
| sudo gpg --dearmor \
-o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

Add the Kubernetes repository.

```bash
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.35/deb/ /' \
| sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Update the package index.

```bash
sudo apt-get update
```

---

## Install Kubernetes CLI Tools

Install the Kubernetes packages.

```bash
sudo apt-get install -y \
    kubelet \
    kubeadm \
    kubectl
```

Prevent the packages from being upgraded automatically.

```bash
sudo apt-mark hold kubelet kubeadm kubectl
```

Enable kubelet.

```bash
sudo systemctl enable --now kubelet
```

---

## Verify the Installation

Check the installed client version.

```bash
kubectl version --client
```

Example output:

```text
Client Version: v1.35.6
Kustomize Version: v5.7.1
```

---

# Configuring kubectl

The Kubernetes administrator configuration file (`admin.conf`) must be copied from the first control plane node.

## Create the kubeconfig Directory

```bash
mkdir -p ~/.kube
```

---

## Copy the Configuration File

From the WSL terminal, copy the configuration file using SCP.

```bash
scp user@<ST.IP>.232:/etc/kubernetes/admin.conf ~/.kube/config
```

> Replace `user` with the appropriate SSH account on your control plane node.

Alternatively, if the file has already been copied to the user's home directory:

```bash
scp user@<ST.IP>.232:/home/devops/admin.conf ~/.kube/config
```

---

## Verify the Configuration

Check that kubectl can communicate with the cluster.

```bash
kubectl get nodes
```

Expected output:

```text
NAME          STATUS   ROLES           AGE    VERSION
k8s-master1   Ready    control-plane   168m   v1.35.x
k8s-master2   Ready    control-plane   145m   v1.35.x
k8s-master3   Ready    control-plane   145m   v1.35.x
st-worker1    Ready    <none>          143m   v1.35.x
st-worker2    Ready    <none>          140m   v1.35.x
```

If all nodes are displayed with the **Ready** status, the remote administration environment has been configured successfully.

---

# Cluster Validation

Before deploying workloads, verify the overall cluster health.

## Verify Nodes

```bash
kubectl get nodes -o wide
```

---

## Verify System Pods

```bash
kubectl get pods -A
```

All Pods should be in the **Running** state.

---

## Verify Cluster Information

```bash
kubectl cluster-info
```

Example output:

```text
Kubernetes control plane is running at https://stlbsv.stit.local:6443
CoreDNS is running at ...
```

---

## Verify API Connectivity

```bash
kubectl get namespaces
```

Expected output:

```text
default
kube-node-lease
kube-public
kube-system
```

---

# Final Infrastructure

The completed environment consists of the following components.

| Component | Quantity |
|-----------|---------:|
| Proxmox VE Hosts | 1 |
| Load Balancers | 2 |
| Virtual IP | 1 |
| Control Plane Nodes | 3 |
| Worker Nodes | 2 |
| NFS Server | 1 |

---

# Technologies Used

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

---

# Project Outcome

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

The cluster is now ready for deploying production-grade Kubernetes services and applications.

---

# Next Steps

The following projects will extend this Kubernetes platform:

1. Deploy MetalLB
2. Deploy NGINX Ingress Controller
3. Deploy cert-manager
4. Deploy Longhorn Distributed Storage
5. Deploy NFS CSI Driver
6. Deploy Argo CD
7. Deploy Prometheus & Grafana
8. Deploy Loki & Promtail
9. Deploy Harbor Container Registry
10. Deploy Sample Applications

These components will transform the current Kubernetes cluster into a complete cloud-native platform suitable for development and production workloads.