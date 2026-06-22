---
title: "K8S First Project"
weight: 11
---

# Project workflow
  
- We'll deploy a website for a fictional travel agency into a container image, deploy it into a Kubernetes cluster in your machine with kind, package it into a Helm chart
-  Deploy and run it inside of real Kubernetes clusters in AWS and Azure.
```
Website
   ↓
Container
   ↓
Kubernetes
   ↓
Helm
   ↓
AWS / Azure Kubernetes
```

### Our application before Kubernetes

{{% details title="Explore California concepts" = false %}}
 Explore California is California's premier website for touring and exploring the Golden State. 
 Each year, millions of visitors from all over the world use Explore California to plan their perfect West Coast adventure. 
 The company moved all of their infrastructure over to AWS many years ago. 
 Engineers are also using tools like Selenium and GitHub Actions to test and deploy changes to the website and its many services on every release. 
 Unfortunately, there are several issues that have made those deployments complicated. 
 First, while most of Explore California runs on containers, those containers run on a **limited set of machines** that devs often fight over for resources, as well, 
 all of their pre-production environments differ from each other, so some changes that pass during testing still break when customers begin to use them. 
 Testing is also complicated further by downstream test services, like databases and message queues, going down frequently.
 All of this causes additional headaches during deployment that make new features take months to deploy. 
 Competitors like Explore Texas can ship cool new stuff overnight. 
 After much nudging from the board, the CTO of Explore California gave their engineers a challenge:
 Move all of Explore California into Kubernetes, starting with Explore california.com. Many engineers were worried.
 "Kubernetes is complicated. Those YAML files are huge. We'll need to change so much of our process to make this happen."
 Can they do it?
{{% /details %}}

{{% hint danger %}}
- Limited set of machines, creating contention
- Lots of downtime due to environment
- Test services that go down frequently
{{% /hint %}}

{{% hint success %}}
**Challenge:** move all of Explore Califonia into Kubernetes
{{% /hint %}}

### Our application after Kubernetes
- Explore California is moved into AKS or EKS
- Helm is used to deploy the website
- Creating dev enviromnents is easy now!

### What is kubenetes ?

{{% details title="What is kubenetes ?" open=false %}}

- Kubernetes manages containers at global scale
- Kubernetes can orchestrate tens of thoudsands of nodes and hundred of thousands of containers

### Kubernetes is made up of three major components.
{{< image src="./k1.jpg" >}}

{{% columns %}}
- The **control plan** is the heart of the cluster. The control plane runs all of the services that Kubernetes uses to **create**, **manage**, and **connect containers to each other**. While you probably won't interact too deeply with this component if you're using Kubernetes through a cloud provider, it's important to know that it exists and is doing a lot of the heavy lifting. 
- {{< image src="./k2.jpg" >}}
{{% /columns %}}

{{% columns %}}
- **Worker nodes** or kubelets on the other hand, are the machines that **run containers**. One of the jobs the control plane has is to essentially perform a **docker** run action on these machines whenever a new **pod** or a grouping of **containers** is created
- {{< image src="./k3.jpg" >}}
{{% /columns %}}


- You can mix and match worker nodes within a cluster. You might have a collection of worker nodes that have **GPUs** for **AI stuff**, another collection that runs window stuff, and yet another collection that runs top secret stuff that only a handful of people know about.

{{% columns %}}
-  Finally, we have the **database**. This stores cluster state and some configuration information. **Etcd**, a distributed and decentralized database engine, has historically been the database of choice for Kubernetes clusters. While it's still the default for most deployments, some projects like K3s by Rancher use other database engines like PostgreSQL or even SQLite. So that's Kubernetes in a nutshell.

- {{< image src="./k4.jpg" >}}
{{% /columns %}}

{{% /details %}}