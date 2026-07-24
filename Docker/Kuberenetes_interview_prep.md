# Kubernetes Interview Crash Course

> **Goal:** Crack Kubernetes interview questions for DevOps Engineers (2–8 Years Experience)

---

# 1. What is Kubernetes?

## Definition

Kubernetes (K8s) is an open-source container orchestration platform used to deploy, manage, scale, and heal containerized applications automatically.

It was originally developed by Google and is now maintained by the Cloud Native Computing Foundation (CNCF).

---

## Why Kubernetes?

Docker can run a single container.

But in production, applications consist of hundreds or thousands of containers.

Problems without Kubernetes:

- Manual deployment
- Manual scaling
- No self-healing
- No load balancing
- Difficult rolling updates
- No service discovery

Kubernetes automates all of these.

---

## Real-world Example

E-commerce Application

```
Frontend

↓

Backend API

↓

Payment Service

↓

Notification Service

↓

Redis

↓

MySQL
```

Hundreds of containers run together.

Kubernetes manages them automatically.

---

# 2. Why Kubernetes?

Kubernetes provides:

- Container Orchestration
- Auto Scaling
- Self Healing
- Load Balancing
- Rolling Updates
- Rollbacks
- Service Discovery
- Secret Management
- High Availability

---

# 3. Kubernetes Architecture

```
                 Kubernetes Cluster

        +-----------------------------+
        |        Control Plane        |
        +-----------------------------+
        | API Server                  |
        | Scheduler                   |
        | Controller Manager          |
        | ETCD                        |
        +-------------+---------------+
                      |
        -------------------------------
        |                             |
+-------v-------+             +--------v-------+
| Worker Node 1 |             | Worker Node 2 |
+---------------+             +---------------+
| Kubelet       |             | Kubelet       |
| Kube Proxy    |             | Kube Proxy    |
| Container     |             | Container     |
| Runtime       |             | Runtime       |
| Pods          |             | Pods          |
+---------------+             +---------------+
```

---

## Control Plane Components

### API Server

Entry point to the cluster.

All kubectl commands communicate with the API Server.

---

### ETCD

Distributed key-value database.

Stores the cluster state.

If ETCD is lost, the cluster state is lost.

---

### Scheduler

Decides which worker node should run a newly created Pod.

---

### Controller Manager

Continuously monitors the cluster and ensures the desired state matches the actual state.

Example:

Desired Pods = 3

Running Pods = 2

Controller creates the missing Pod automatically.

---

## Worker Node Components

### Kubelet

Agent running on every worker node.

Responsibilities:

- Receives instructions from the API Server.
- Creates Pods.
- Monitors Pod health.
- Reports status back to the Control Plane.

---

### Kube Proxy

Handles networking.

Routes traffic to Pods and Services.

---

### Container Runtime

Runs containers.

Examples:

- containerd
- CRI-O

(Docker itself is no longer the runtime in modern Kubernetes.)

---

# 4. What is a Pod?

## Definition

A Pod is the smallest deployable unit in Kubernetes.

It can contain:

- One container (most common)
- Multiple tightly coupled containers

---

## Pod Architecture

```
Pod

↓

Application Container

↓

Shared Network

↓

Shared Storage
```

All containers in a Pod share:

- IP Address
- Network Namespace
- Volumes

---

## Interview Tip

**Container ≠ Pod**

A Pod is a wrapper around one or more containers.

---

# 5. Why not create Pods directly?

Pods are ephemeral.

If a Pod dies, it is gone.

Production applications use higher-level controllers like:

- Deployment
- StatefulSet
- DaemonSet
- Job

These controllers recreate Pods automatically.

---

# Quick Revision

- Kubernetes = Container Orchestration
- Cluster = Control Plane + Worker Nodes
- API Server = Entry Point
- ETCD = Cluster Database
- Scheduler = Chooses Worker Node
- Controller Manager = Maintains Desired State
- Kubelet = Node Agent
- Kube Proxy = Networking
- Pod = Smallest Deployable Unit

---

# 6. ReplicaSet

## What is a ReplicaSet?

A ReplicaSet ensures that a specified number of identical Pods are always running.

Example:

Desired Pods = **3**

Current Running Pods = **2**

ReplicaSet automatically creates **1 new Pod**.

```
ReplicaSet

↓

Pod 1

Pod 2

Pod 3
```

---

## Why ReplicaSet?

Without ReplicaSet

```
Pod Dies

↓

Application Down
```

With ReplicaSet

```
Pod Dies

↓

ReplicaSet Detects Failure

↓

Creates New Pod

↓

Application Continues Running
```

---

## Interview Question

### What is the difference between Pod and ReplicaSet?

**Answer**

A Pod runs an application.

A ReplicaSet manages Pods and ensures the required number of replicas are always available.

---

# 7. Deployment

## What is a Deployment?

A Deployment is the most commonly used Kubernetes object.

It manages ReplicaSets and Pods.

Think of it as

```
Deployment

↓

ReplicaSet

↓

Pods
```

Never create Pods directly in production.

Always create Deployments.

---

## Responsibilities

- Creates ReplicaSets
- Creates Pods
- Rolling Updates
- Rollbacks
- Scaling
- Self-Healing

---

## Deployment Architecture

```
Deployment

↓

ReplicaSet

↓

Pod 1

Pod 2

Pod 3
```

---

## Why Deployment?

Suppose

```
Payment Service

Version 1

↓

Need Version 2
```

Deployment performs Rolling Update without downtime.

---

## Scale Application

Increase Pods

```bash
kubectl scale deployment payment --replicas=5
```

Decrease Pods

```bash
kubectl scale deployment payment --replicas=2
```

---

## Check Deployment

```bash
kubectl get deployments
```

Describe

```bash
kubectl describe deployment payment
```

---

## Interview Question

### Why use Deployment instead of ReplicaSet?

**Answer**

ReplicaSet only maintains the desired number of Pods.

Deployment adds advanced features like:

- Rolling Updates
- Rollbacks
- Version History
- Scaling

---

# 8. Rolling Update

## What is Rolling Update?

Rolling Update replaces old Pods with new Pods gradually.

Example

Version 1

```
Pod

Pod

Pod
```

↓

Deploy Version 2

```
Pod v2

Pod v1

Pod v1
```

↓

```
Pod v2

Pod v2

Pod v1
```

↓

```
Pod v2

Pod v2

Pod v2
```

Users never experience downtime.

---

## Check Rollout

```bash
kubectl rollout status deployment payment
```

---

## Rollout History

```bash
kubectl rollout history deployment payment
```

---

# 9. Rollback

Suppose

Version 2 has bugs.

Rollback

```bash
kubectl rollout undo deployment payment
```

Deployment returns to Version 1.

---

## Interview Question

### Difference between Rolling Update and Rollback?

Rolling Update

Deploys a newer version gradually.

Rollback

Returns to the previous stable version.

---

# 10. Service

## Why Service?

Pods are temporary.

If Pod dies,

New Pod receives a new IP.

Applications cannot depend on Pod IPs.

Service provides a stable endpoint.

---

Architecture

```
Service

↓

Pod 1

Pod 2

Pod 3
```

Clients communicate only with the Service.

---

# Types of Services

1. ClusterIP
2. NodePort
3. LoadBalancer

---

## ClusterIP

Default Service.

Accessible only inside the cluster.

Example

```
Frontend

↓

Backend Service

↓

Backend Pods
```

Most commonly used.

---

## NodePort

Exposes application on every worker node.

```
Browser

↓

Worker Node IP:30080

↓

Service

↓

Pods
```

Useful for testing.

---

## LoadBalancer

Used in Cloud.

AWS

↓

ALB/NLB

↓

Service

↓

Pods

Cloud provider automatically creates the Load Balancer.

---

## Service Commands

View Services

```bash
kubectl get svc
```

Describe

```bash
kubectl describe svc payment
```

---

## Interview Question

### Why do we need Services?

Pods are ephemeral and their IP addresses change when they are recreated.

A Service provides a stable IP/DNS name and load balances traffic across healthy Pods.

---

# 11. Ingress

## What is Ingress?

Ingress manages HTTP/HTTPS traffic entering the Kubernetes cluster.

Without Ingress

```
Frontend

↓

LoadBalancer

Backend

↓

LoadBalancer

Payment

↓

LoadBalancer
```

Three Load Balancers.

Expensive.

---

With Ingress

```
Internet

↓

Ingress Controller

↓

Frontend Service

↓

Backend Service

↓

Payment Service
```

Single entry point.

---

## Benefits

- Single Load Balancer
- SSL Termination
- Path Routing
- Host Routing
- Cost Reduction

---

## Example

```
company.com

↓

Frontend

api.company.com

↓

Backend

payment.company.com

↓

Payment
```

---

## Interview Question

### Difference between Service and Ingress?

Service

Routes traffic **inside** the cluster.

Ingress

Routes **external HTTP/HTTPS** traffic into the cluster.

---

# 12. Namespaces

## What is a Namespace?

Namespaces logically separate resources within the same Kubernetes cluster.

Example

```
default

dev

qa

uat

prod

monitoring
```

---

## Commands

View

```bash
kubectl get ns
```

Create

```bash
kubectl create namespace dev
```

Deploy into namespace

```bash
kubectl apply -f deployment.yaml -n dev
```

---

## Why Namespaces?

- Environment isolation
- Resource quotas
- Team separation
- Security

---

# 13. Most Important kubectl Commands

## Cluster

```bash
kubectl cluster-info

kubectl get nodes
```

---

## Pods

```bash
kubectl get pods

kubectl get pods -A

kubectl describe pod pod-name

kubectl logs pod-name

kubectl exec -it pod-name -- bash

kubectl delete pod pod-name
```

---

## Deployments

```bash
kubectl get deploy

kubectl describe deploy payment

kubectl scale deployment payment --replicas=5

kubectl rollout status deployment payment

kubectl rollout undo deployment payment
```

---

## Services

```bash
kubectl get svc

kubectl describe svc payment
```

---

## Apply Resources

```bash
kubectl apply -f deployment.yaml

kubectl delete -f deployment.yaml
```

---

# Production Flow

```
Developer

↓

Git Push

↓

GitLab CI

↓

Docker Build

↓

Push to ECR

↓

Update Kubernetes Manifest

↓

kubectl apply

↓

Deployment

↓

ReplicaSet

↓

Pods

↓

Service

↓

Ingress

↓

Users
```

---

# Top Interview Questions

### What is Kubernetes?

Container orchestration platform.

---

### What is a Pod?

Smallest deployable unit.

---

### Why not create Pods directly?

Pods are temporary.

Deployments manage Pods.

---

### Deployment vs ReplicaSet?

ReplicaSet manages replicas.

Deployment manages ReplicaSets and supports rolling updates, rollbacks, and scaling.

---

### Why do we need a Service?

Stable endpoint for Pods.

---

### Types of Services?

- ClusterIP
- NodePort
- LoadBalancer

---

### What is Ingress?

Single entry point for HTTP/HTTPS traffic.

---

### Rolling Update?

Gradually replaces old Pods with new ones without downtime.

---

### Rollback?

Reverts to the previous stable deployment.

---

### Why Namespaces?

Logical isolation of resources.

---

# 5-Minute Revision

- Pod = Smallest Unit
- ReplicaSet = Maintains Replica Count
- Deployment = Production Controller
- Service = Stable Network Endpoint
- ClusterIP = Internal Access
- NodePort = Expose via Node
- LoadBalancer = Cloud Exposure
- Ingress = HTTP/HTTPS Entry Point
- Rolling Update = Zero Downtime Deployment
- Rollback = Revert to Previous Version
- Namespace = Resource Isolation
