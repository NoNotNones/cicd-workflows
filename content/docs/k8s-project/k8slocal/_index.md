---
title: "Running Explore California Locally on Kubernetes (Kind)"
weight: 103
params:
  bookCollapseSection: true
---

# Objectives
This guide demonstrates how to deploy the Explore California website to a local Kubernetes cluster using Kind.
After completing this guide, you will be able to:
- Create a local Kubernetes cluster with Kind.
- Build and deploy the Explore California application.
- Expose the application using a Kubernetes Service and Ingress.
- Configure HTTPS locally using cert-manager with a self-signed certificate.

---

# Environment

| Component | Version |
|-----------|---------|
| Docker Desktop | 4.77.0 (228796) |
| Kind | v0.31.0 |
| Helm | v3.21.2 |
| Working Directory | `D:\k8s-projects` |

Development environment:

- Windows
- VSCode


{{< image src="./p1.jpg" >}}

---
# Building the Application Image

## Dockerfile

{{% details title="Dockerfile" open=true %}}

```dockerfile
FROM nginx:alpine
LABEL maintainer="STST <dev@stit.local>"
COPY website /usr/share/nginx/website
COPY nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
```

{{% /details %}}

---

## Build the Docker Image

```powershell
docker build -t explorecalifornia.com .
```

Verify the image.

```powershell
docker images
```

Example:

```text
IMAGE                          ID             DISK USAGE   CONTENT SIZE
explorecalifornia.com:latest   829d7d0ad19f   122MB        37.8MB
```

---
## Test the Container

Run the application locally.

```powershell
docker run --rm \
    --name explorecalifornia \
    -p 5000:80 \
    explorecalifornia.com
```

Open:

```
http://localhost:5000
```

{{< image src="./w1.jpg" >}}

{{< image src="./w2.jpg" >}}

---

# Creating a Kind Cluster
## Create `cluster.yaml`

{{% details title="cluster.yaml :" open=true %}}
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: explorecalifornia.com
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP

```
- Why use `extraPortMappings:` This option gives us the ability to map ports outside of our cluster to ports within it. It's kind of like that -p outside colon inside trick that we used earlier.
  

{{% /details %}}

## Create a cluster
```powershell
kind create cluster --name explorecalifornia.com
 ```

Verify the cluster:
```powershell
kubectl get nodes
```

Access the control-plane container.
```py
docker exec -it explorecalifornia.com-control-plane sh
```

Exit: 
```
ctrl + D
```

Delete the cluster
```py
kind delete cluster --name explorecalifornia.com
```

{{< image src="./k1.jpg" >}}

# Deploying the Application

{{% details title="Pods and deployments" open=false %}}
- Containers in Kubenetes always run within **pods**.
  - Like a pod of dolphins cruising through an ocean, pods within Kubernetes are groups of containers that run as a single unit. Most of the things you can do with containers in Podman, like mounting volumes or exposing network ports, can also be done with pods. 
  - However, unlike Docker or Podman, containers within pods share quite a few things, including any volumes that you create, as well as their network namespace. 
- **Deployments** make it easier to scale and manage pods.
  - These give you the ability to scale pods and protect them from things that could cause their applications to crash, like node failures or unexpected migrations between nodes. 
  - In fact, deployments are so useful, these are almost always used to create pods, even if the pod has a single container within it. 
{{% /details %}}

## Create a deployment
### Load the Docker Image into Kind

```py
kind load docker-image explorecalifornia.com --name explorecalifornia.com
```

### Create deployment
```py
kubectl create deployment explore-california \
--image localhost/explorecalifornia.com \
--port=80
```

Verify:

```
kubectl get deployment
kubectl get pods
```
### Troubleshooting
If the Pod status is:
```ImagePullBackOff```

Edit the deployment. 
```kubectl edit deployment explore-california```

Update: 
```yaml
imagePullPolicy: IfNotPresent
```
or

```yaml
imagePullPolicy: Never
```

This prevents Kubernetes from pulling the image from an external registry.

### Test the Deployment

Forward the Deployment.

```powershell
kubectl port-forward deployment/explore-california 8080:80
```

Browse to:

```
http://localhost:8080
```

{{< image src="./k2.jpg" >}}
{{< image src="./k3.jpg" >}}


## Exposing the Application

Create a ClusterIP Service.

```powershell
kubectl create service clusterip explore-california \
    --tcp=80:80
```

Verify.

```powershell
kubectl get service
```

Example:

```text
NAME                 TYPE        CLUSTER-IP      PORT(S)
explore-california   ClusterIP   10.96.224.106   80/TCP
```

Forward the Service.

```py
kubectl port-forward service/explore-california 8080:80
```

Open:

```
http://localhost:8080
```

---
## Configuring Ingress

Create an Ingress.

```powershell
kubectl create ingress explore-california \
    --rule="explorecalifornia.local/*=explore-california:80"
```

Verify.

```powershell
kubectl get ingress
```

  ```
  NAME                 CLASS    HOSTS                     ADDRESS   PORTS   AGE
  explore-california   <none>   explorecalifornia.local             80      4s
  ```
---

### Installing NGINX Ingress Controller

```powershell
kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/deploy-ingress-nginx.yaml
```

Verify.

```powershell
kubectl get deployment -n ingress-nginx

kubectl get svc -n ingress-nginx
```
```
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
ingress-nginx-controller             LoadBalancer   10.96.160.27   <pending>     80:32726/TCP,443:31413/TCP   2m31s
ingress-nginx-controller-admission   ClusterIP      10.96.157.38   <none>        443/TCP                      2m31s
```
---

### Testing the Ingress

Verify the Ingress.

```powershell
kubectl get ingress
```
```
NAME                 CLASS    HOSTS                     ADDRESS     PORTS   AGE
explore-california   <none>   explorecalifornia.local   localhost   80      15m
```

Update the hosts file.

```powershell
Write-Output "127.0.0.1 explorecalifornia.local" |
Add-Content C:\Windows\System32\drivers\etc\hosts
```

Browse to:

```
http://explorecalifornia.local
```

{{< image src="./k4.jpg" >}}

---

## Enabling HTTPS with cert-manager

Install cert-manager.

```powershell
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.20.3/cert-manager.yaml
```

Verify.

```powershell
kubectl get pods -n cert-manager
```
```
NAME                                     READY   STATUS    RESTARTS      AGE
cert-manager-767885ff64-q52pw            1/1     Running   3 (30m ago)   18h
cert-manager-cainjector-d8d76db4-49mw6   1/1     Running   4 (30m ago)   18h
cert-manager-webhook-69d8bdfff-jgrqp     1/1     Running   2 (30m ago)   18h
```
---

### Create an Issuer

Create `issuer.yaml`.

{{% details title="issuer.yaml" open=true %}}

```yaml
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: explorecalifornia-ingress
spec:
  selfSigned: {}
```

{{% /details %}}

Apply it.

```powershell
kubectl apply -f issuer.yaml
```

---

### Configure TLS

Edit the Ingress.

```powershell
kubectl edit ingress explore-california
```

Add:

```yaml
metadata:
  annotations:
    cert-manager.io/issuer: explorecalifornia-ingress
```

and

```yaml
tls:
- hosts:
  - explorecalifornia.local
  secretName: explorecalifornia-tls-data
```

---

### Verify

```powershell
kubectl get ingress
```
```
NAME                 CLASS    HOSTS                     ADDRESS     PORTS     AGE
explore-california   <none>   explorecalifornia.local   localhost   80, 443   145m
```

```powershell
kubectl get certificates
```
```
NAME                         READY   SECRET                       AGE
explorecalifornia-tls-data   True    explorecalifornia-tls-data   2m23s
```

---

# Access the Application

Open:

```
https://explorecalifornia.local
```

{{< image src="./k5.jpg" >}}

---

# Summary

In this lab, you successfully:

- Built a Docker image.
- Created a local Kubernetes cluster using Kind.
- Deployed the application.
- Exposed it using a ClusterIP Service.
- Configured an NGINX Ingress.
- Enabled HTTPS with cert-manager.

The application is now available at:

```
https://explorecalifornia.local
```