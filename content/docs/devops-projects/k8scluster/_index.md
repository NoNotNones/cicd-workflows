---
title: "Deploying the Kubernetes Cluster"
weight: 113
params:
  bookCollapseSection: true
---

---

# Deploying the Kubernetes Cluster

With the High Availability layer in place, the infrastructure is now ready for Kubernetes installation.

This section covers:

- Installing the container runtime
- Installing Kubernetes components
- Initializing the first control plane node
- Installing the CNI plugin
- Joining additional control plane nodes
- Joining worker nodes

---

# Installing Prerequisites

Perform the following steps on **all Kubernetes nodes** (Control Plane and Worker).

---

## Disable Swap

Kubernetes requires swap to be disabled.

Check the current swap status.

```bash
swapon --show
```

Disable swap.

```bash
sudo swapoff -a
```

Disable swap permanently.

```bash
sudo nano /etc/fstab
```

Comment out the swap entry.

Example:

```text
#/swap.img none swap sw 0 0
```

Verify.

```bash
free -h
```

---

## Enable Kernel Modules

Load the required kernel modules.

```bash
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF
```

Load the modules immediately.

```bash
sudo modprobe overlay
sudo modprobe br_netfilter
```

Verify.

```bash
lsmod | grep br_netfilter
```

---

## Configure Kernel Parameters

Create a Kubernetes sysctl configuration.

```bash
cat <<EOF | sudo tee /etc/sysctl.d/kubernetes.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
EOF
```

Apply the configuration.

```bash
sudo sysctl --system
```

Verify.

```bash
sysctl net.ipv4.ip_forward
```

Expected output:

```text
net.ipv4.ip_forward = 1
```

---

# Installing Containerd

Containerd is the container runtime used by Kubernetes.

Install the package.

```bash
sudo apt update
sudo apt install containerd -y
```

Generate the default configuration.

```bash
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
```

Enable Systemd cgroups.

Edit:

```bash
sudo nano /etc/containerd/config.toml
```

Locate:

```toml
SystemdCgroup = false
```

Replace it with:

```toml
SystemdCgroup = true
```

Restart the service.

```bash
sudo systemctl restart containerd
sudo systemctl enable containerd
```

Verify.

```bash
systemctl status containerd
```

Expected output:

```text
Active: active (running)
```

---

# Installing Kubernetes Components

Install:

- kubeadm
- kubelet
- kubectl

---

## Add the Kubernetes Repository

```bash
sudo apt-get update

sudo apt-get install -y \
apt-transport-https \
ca-certificates \
curl \
gpg
```

Download the repository key.

```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.35/deb/Release.key \
| sudo gpg --dearmor \
-o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

Add the repository.

```bash
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.35/deb/ /' \
| sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Install Kubernetes packages.

```bash
sudo apt update

sudo apt install -y \
kubelet \
kubeadm \
kubectl
```

Prevent automatic upgrades.

```bash
sudo apt-mark hold kubelet kubeadm kubectl
```

Enable kubelet.

```bash
sudo systemctl enable kubelet
```

Verify.

```bash
kubectl version --client
```

---

# Resetting a Previous Cluster (Optional)

If the node has previously been part of a Kubernetes cluster, perform a reset before continuing.

```bash
sudo kubeadm reset -f
```

Remove remaining cluster data.

```bash
sudo rm -rf /etc/kubernetes
sudo rm -rf /var/lib/etcd
sudo rm -rf /etc/cni/net.d
sudo rm -rf ~/.kube
```

---

# Initializing the First Control Plane

Perform this step **only on** `k8s-master1`.

Initialize the cluster using the Virtual IP.

```bash
sudo kubeadm init \
    --control-plane-endpoint=stlbsv.stit.local \
    --upload-certs
```

The command will display several important outputs:

- kubeconfig configuration
- Control Plane join command
- Worker join command
- Certificate Key

Save these commands for later use.

---

## Configure kubectl

Copy the Kubernetes administrator configuration.

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Verify cluster access.

```bash
kubectl get nodes
```

At this stage, only one node should be visible.

---

# Installing Calico

Install the Calico Container Network Interface (CNI).

```bash
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

Verify the installation.

```bash
kubectl get pods -n kube-system
```

Wait until all Calico Pods are in the **Running** state.

---

# Joining Additional Control Plane Nodes

Perform the following command on:

- k8s-master2
- k8s-master3

Example:

```bash
sudo kubeadm join stlbsv.stit.local:6443 \
--token <TOKEN> \
--discovery-token-ca-cert-hash sha256:<HASH> \
--control-plane \
--certificate-key <CERTIFICATE_KEY>
```

> **Note**
>
> The values for `<TOKEN>`, `<HASH>`, and `<CERTIFICATE_KEY>` are generated during the `kubeadm init` process.

---

# Joining Worker Nodes

Run the following command on every worker node.

```bash
sudo kubeadm join stlbsv.stit.local:6443 \
--token <TOKEN> \
--discovery-token-ca-cert-hash sha256:<HASH>
```

Repeat this step for:

- st-worker1
- st-worker2

---

# Verifying the Cluster

Verify all nodes have joined successfully.

```bash
kubectl get nodes
```

Example output:

```text
NAME          STATUS   ROLES           VERSION
k8s-master1   Ready    control-plane   v1.35.x
k8s-master2   Ready    control-plane   v1.35.x
k8s-master3   Ready    control-plane   v1.35.x
st-worker1    Ready    <none>          v1.35.x
st-worker2    Ready    <none>          v1.35.x
```

Check system Pods.

```bash
kubectl get pods -A
```

All Pods should be in the **Running** state.

---

## Verification

Cluster nodes.

{{< image src="./k8s1.jpg" >}}

System Pods.

{{< image src="./k8s2.jpg" >}}

---

# Cluster Health Checks

Verify the Kubernetes control plane.

Check node status.

```bash
kubectl get nodes -o wide
```

Check component status.

```bash
kubectl get componentstatus
```

Check system Pods.

```bash
kubectl get pods -A
```

Check cluster information.

```bash
kubectl cluster-info
```

Example output:

```text
Kubernetes control plane is running at https://stlbsv.stit.local:6443
CoreDNS is running at ...
```

---

# Summary

At this stage, the Kubernetes cluster has been successfully deployed.

The environment now includes:

- Three highly available Control Plane nodes
- Two Worker nodes
- HAProxy load balancing
- Keepalived Virtual IP failover
- Containerd runtime
- Calico CNI networking

The cluster is now ready for deploying workloads and installing additional Kubernetes services such as:

- MetalLB
- NGINX Ingress Controller
- cert-manager
- Longhorn
- Argo CD
- Prometheus & Grafana