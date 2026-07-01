---
title: "Configuring High Availability"
weight: 112
params:
  bookCollapseSection: true
---

# Configuring High Availability

To ensure continuous availability of the Kubernetes API Server, this project uses:

- **Keepalived** to provide a Virtual IP (VIP) with automatic failover.
- **HAProxy** to load balance traffic across all Kubernetes control plane nodes.

The following architecture is implemented:

```text
                    +----------------------+
                    |  Virtual IP (VIP)    |
                    | <ST.IP>.245          |
                    +----------+-----------+
                               |
                +--------------+--------------+
                |                             |
         +-------------+               +-------------+
         |   st-lb01   |               |   st-lb02   |
         | HAProxy     |               | HAProxy     |
         | Keepalived  |               | Keepalived  |
         +------+------+               +------+------+
                |                             |
                +--------------+--------------+
                               |
        +----------------------+----------------------+
        |                      |                      |
+---------------+      +---------------+      +---------------+
| k8s-master1   |      | k8s-master2   |      | k8s-master3   |
| API :6443     |      | API :6443     |      | API :6443     |
+---------------+      +---------------+      +---------------+
```

---

# Configuring Keepalived

Keepalived provides a **Virtual Router Redundancy Protocol (VRRP)** implementation that automatically moves the Virtual IP (VIP) between load balancers if the active node becomes unavailable.

---

## Install Keepalived

Perform the following steps on **both load balancer nodes**.

Install Keepalived.

```bash
sudo apt update
sudo apt install keepalived -y
```

Enable the service.

```bash
sudo systemctl enable keepalived
sudo systemctl restart keepalived
```

Verify the service status.

```bash
systemctl status keepalived
```

---

## Configure Load Balancer 01 (MASTER)

Edit the configuration file.

```bash
sudo nano /etc/keepalived/keepalived.conf
```

Replace the default configuration with the following.

{{% details title="keepalived.conf (MASTER)" open=true %}}

```conf
vrrp_instance VI_1 {
    state MASTER
    interface ens18
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1234
    }
    virtual_ipaddress {
        <ST.IP>.245
    }
}
```

{{% /details %}}

---

## Configure Load Balancer 02 (BACKUP)

Edit the configuration file.

```bash
sudo nano /etc/keepalived/keepalived.conf
```

Configure the BACKUP node.

{{% details title="keepalived.conf (BACKUP)" open=true %}}

```conf
vrrp_instance VI_1 {
    state BACKUP
    interface ens18
    virtual_router_id 51
    priority 90
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1234
    }
    virtual_ipaddress {
        <ST.IP>.245
    }
}
```

{{% /details %}}

Restart the service.

```bash
sudo systemctl restart keepalived
```

---

## Verify the Virtual IP

On the MASTER node, verify that the Virtual IP has been assigned.

```bash
ip addr
```

Example output:

{{< image src="./ha1.jpg" >}}

The VIP should appear on **st-lb01** while it is the active node.

To test failover:
1. Stop Keepalived on **st-lb01**.
2. Verify that the VIP automatically moves to **st-lb02**.

```bash
sudo systemctl stop keepalived
```

---

# Configuring HAProxy

HAProxy distributes incoming Kubernetes API requests across all available control plane nodes.

This allows clients to communicate with a single endpoint while maintaining high availability.

---

## Install HAProxy

Perform the following steps on **both load balancer nodes**.

```bash
sudo apt update
sudo apt install haproxy -y
```

---

## Configure HAProxy

Edit the configuration file.

```bash
sudo nano /etc/haproxy/haproxy.cfg
```

Replace the default configuration with the following.

{{% details title="haproxy.cfg" open=true %}}

```cfg
global
    log /dev/log local0
    log /dev/log local1 notice
    daemon
    maxconn 2000

defaults
    log global
    mode tcp
    option tcplog
    timeout connect 10s
    timeout client 1m
    timeout server 1m

frontend k8s-api
    bind *:6443
    default_backend k8s-api-backend

backend k8s-api-backend
    balance roundrobin
    option tcp-check
    server k8s-master1 <ST.IP>.232:6443 check
    server k8s-master2 <ST.IP>.233:6443 check
    server k8s-master3 <ST.IP>.234:6443 check

listen stats
    bind *:8404
    mode http
    stats enable
    stats uri /stats
    stats refresh 10s
```

{{% /details %}}

---

## Validate the Configuration

Before restarting HAProxy, verify the configuration syntax.

```bash
sudo haproxy -c -f /etc/haproxy/haproxy.cfg
```

Expected output:

```text
Configuration file is valid
```

---

## Start the Service

Restart HAProxy.

```bash
sudo systemctl restart haproxy
```

Enable the service at boot.

```bash
sudo systemctl enable haproxy
```

Verify the service status.

```bash
sudo systemctl status haproxy
```

---

## Verify HAProxy

Check that HAProxy is listening on port **6443**.

```bash
ss -tlnp | grep 6443
```

Example output:

```text
LISTEN 0      4096      *:6443      *:*      users:(("haproxy"))
```

---

## Access the HAProxy Statistics Dashboard

Open a web browser.

```
http://<LoadBalancer-IP>:8404/stats
```

Example:

```
http://<ST.IP>.247:8404/stats
```

The dashboard displays:

- Backend status
- Health checks
- Session statistics
- Request distribution
- Server availability

Example:

{{< image src="./ha2.jpg" >}}

---

# High Availability Validation

After completing the configuration, verify the following:

| Component | Expected Status |
|-----------|-----------------|
| Keepalived | Running |
| HAProxy | Running |
| VIP | Assigned to MASTER |
| TCP Port 6443 | Listening |
| HAProxy Stats | Accessible |
| Backend Servers | UP |

---

## Testing Failover

Simulate a failure of the active load balancer.

Stop Keepalived on **st-lb01**.

```bash
sudo systemctl stop keepalived
```

Verify:

- The Virtual IP moves to **st-lb02**.
- Kubernetes API remains accessible.
- HAProxy continues forwarding requests.

Restart the service after verification.

```bash
sudo systemctl start keepalived
```

---

# Next Steps
In the next section, you will:
- Install the Kubernetes prerequisites.
- Configure the container runtime (containerd).
- Install kubeadm, kubelet, and kubectl.
- Initialize the Kubernetes control plane.
- Join additional control plane and worker nodes.