# 04-Kubernetes.md

# Kubernetes Interview Handbook

---

# What is Kubernetes?

Kubernetes is a container orchestration platform that automates the deployment, scaling, networking, service discovery, and lifecycle management of containerized applications across a cluster of machines.

Interview Answer:

Docker helps us run containers, while Kubernetes helps us manage containers at scale. In production environments where hundreds of containers may be running, Kubernetes ensures applications remain highly available, scalable, and self-healing.

---

# Why Kubernetes?

As organizations move toward microservices, managing containers manually becomes difficult. Kubernetes provides automated deployment, scaling, load balancing, service discovery, rolling updates, rollback capabilities, and self-healing mechanisms.

Example:

Without Kubernetes:

```text
10 Servers
100 Containers
Manual Management
```

With Kubernetes:

```text
Cluster
↓
Automatic Scheduling
↓
Automatic Recovery
↓
Automatic Scaling
```

---

# Kubernetes Architecture

A Kubernetes cluster consists of a Control Plane and Worker Nodes.

```text
Control Plane
├── API Server
├── Scheduler
├── Controller Manager
└── ETCD

Worker Nodes
├── Kubelet
├── Kube Proxy
└── Container Runtime
```

Interview Answer:

The Control Plane makes cluster decisions and maintains desired state, while Worker Nodes run the actual application workloads.

---

# API Server

The API Server acts as the front door of Kubernetes and receives all requests from users, kubectl, CI/CD pipelines, and internal components.

Interview Answer:

Whenever I execute a command such as:

```bash
kubectl get pods
```

the request first reaches the API Server, which validates the request and communicates with ETCD or other components to return the response.

---

# ETCD

ETCD is a distributed key-value database used as Kubernetes' source of truth.

Interview Answer:

Every Kubernetes object such as Pods, Deployments, Services, Secrets, and ConfigMaps is stored in ETCD. If ETCD becomes unavailable, cluster management operations are affected because Kubernetes loses access to its state information.

---

# Scheduler

The Scheduler determines which worker node should run a newly created Pod.

Interview Answer:

The Scheduler evaluates CPU, memory, taints, tolerations, affinity rules, and resource availability before assigning a Pod to a node.

---

# Controller Manager

The Controller Manager continuously compares the actual state of the cluster with the desired state.

Interview Answer:

If a Deployment specifies three replicas but one Pod crashes, the Controller Manager detects the mismatch and creates a replacement Pod automatically.

---

# Worker Node Components

## Kubelet

Kubelet is the primary agent running on each worker node.

Interview Answer:

Kubelet continuously communicates with the API Server and ensures that Pods assigned to its node are running as expected.

---

## Kube Proxy

Kube Proxy manages network routing and service connectivity within the cluster.

Interview Answer:

Kube Proxy enables Pods to communicate with Services by maintaining networking rules on each node.

---

# Pod

A Pod is the smallest deployable unit in Kubernetes and contains one or more containers that share networking and storage resources.

Interview Answer:

Although Pods can be created directly, in production environments we usually manage them through Deployments because Deployments provide self-healing and scaling capabilities.

---

# Deployment

A Deployment is a higher-level Kubernetes object used to manage Pods and ReplicaSets.

Interview Answer:

A Deployment ensures that the desired number of application replicas remain running and provides rolling updates, rollbacks, and self-healing capabilities.

---

# ReplicaSet

A ReplicaSet ensures a specified number of Pod replicas are always running.

Interview Answer:

If one Pod crashes, the ReplicaSet immediately creates a replacement Pod to maintain the desired count.

---

# Deployment vs ReplicaSet

Interview Answer:

A ReplicaSet only maintains the desired number of Pods, whereas a Deployment manages ReplicaSets and additionally provides versioning, rolling updates, and rollback capabilities.

---

# StatefulSet

A StatefulSet is used for applications that require stable identities and persistent storage.

Examples:

- MongoDB
- Cassandra
- PostgreSQL
- Kafka

Interview Answer:

Unlike Deployments, StatefulSets preserve Pod names and storage associations. If Pod `mongo-0` fails, Kubernetes recreates it as `mongo-0` instead of assigning a random name.

---

# DaemonSet

A DaemonSet ensures one Pod runs on every node in the cluster.

Examples:

- FluentBit
- Prometheus Node Exporter
- Security Agents

Interview Answer:

DaemonSets are typically used for logging, monitoring, and security workloads that must run on every node.

---

# Services

A Service provides a stable network endpoint for accessing Pods.

Without Services:

```text
Pod IP changes
```

With Services:

```text
Stable Service Endpoint
```

---

# ClusterIP

ClusterIP exposes applications internally within the cluster.

Interview Answer:

ClusterIP is the default Service type and is used when applications only need internal communication.

---

# NodePort

NodePort exposes applications through a port on every node.

Interview Answer:

NodePort is commonly used for testing but is rarely preferred in production because it exposes infrastructure directly.

---

# LoadBalancer

LoadBalancer creates an external cloud load balancer.

Interview Answer:

In AWS EKS environments, creating a LoadBalancer Service automatically provisions an AWS Load Balancer that routes traffic to the application Pods.

---

# Ingress

Ingress provides HTTP and HTTPS routing to applications.

Interview Answer:

Ingress allows multiple applications to share a single load balancer and routes traffic based on hostnames or URL paths.

Example:

```text
app.company.com → frontend
api.company.com → backend
```

---

# Ingress Controller

An Ingress resource is only a configuration object. The actual traffic routing is performed by an Ingress Controller.

Examples:

- NGINX Ingress Controller
- AWS Load Balancer Controller
- Traefik

---

# ConfigMap

A ConfigMap stores non-sensitive application configuration data.

Examples:

```text
Database URL
Application Name
Environment Variables
```

Interview Answer:

ConfigMaps help separate configuration from application code, making deployments easier to manage.

---

# Secret

A Secret stores sensitive information.

Examples:

```text
Passwords
API Tokens
Certificates
```

Interview Answer:

Secrets should be used instead of ConfigMaps whenever the data is sensitive and requires controlled access.

---

# ConfigMap vs Secret

Interview Answer:

Both store configuration data, but Secrets are designed for sensitive information and support additional security controls.

---

# Requests and Limits

Requests define the minimum resources a Pod requires.

Limits define the maximum resources a Pod can consume.

Interview Answer:

Requests help Kubernetes schedule Pods appropriately, while Limits prevent individual Pods from consuming excessive cluster resources.

---

# Liveness Probe

A Liveness Probe determines whether an application is still functioning correctly.

Interview Answer:

If the liveness probe fails repeatedly, Kubernetes automatically restarts the container.

---

# Readiness Probe

A Readiness Probe determines whether an application is ready to receive traffic.

Interview Answer:

If the readiness probe fails, Kubernetes temporarily removes the Pod from service endpoints without restarting it.

---

# Pod Pending State

Interview Answer:

A Pod may remain in Pending state due to insufficient CPU, memory, storage, node selector issues, taints, PVC problems, or image pull delays.

Debug Steps:

```bash
kubectl describe pod <pod-name>
kubectl get events
```

---

# CrashLoopBackOff

Interview Answer:

CrashLoopBackOff indicates that a container starts but repeatedly crashes after startup.

Common causes include:

- Application errors
- Missing environment variables
- Incorrect configuration
- Resource limits
- Dependency failures

Debug:

```bash
kubectl logs pod-name
kubectl describe pod pod-name
```

---

# Taints and Tolerations

Taints prevent Pods from being scheduled onto specific nodes.

Tolerations allow Pods to bypass those restrictions.

Interview Answer:

Taints protect specialized nodes while tolerations grant exceptions for workloads that are allowed to run there.

---

# Node Affinity

Node Affinity allows Pods to prefer or require specific nodes.

Interview Answer:

Affinity is commonly used when workloads need GPUs, SSD storage, or dedicated hardware.

---

# Horizontal Pod Autoscaler (HPA)

HPA automatically scales the number of Pods.

Interview Answer:

HPA adjusts replica counts based on metrics such as CPU, memory, or custom application metrics.

---

# Pod Disruption Budget

A Pod Disruption Budget ensures a minimum number of Pods remain available during maintenance activities.

Interview Answer:

PDBs prevent accidental downtime during node upgrades or draining operations.

---

# Rolling Updates

Rolling Updates replace Pods gradually without downtime.

Interview Answer:

Instead of replacing all Pods simultaneously, Kubernetes updates Pods in batches while maintaining application availability.

---

# Rollback

Interview Answer:

If a deployment fails after an update, Kubernetes allows rollback to the previous version.

Command:

```bash
kubectl rollout undo deployment app
```

---

# Network Policies

Network Policies control traffic between Pods.

Interview Answer:

By default, Pods can communicate freely. Network Policies enforce security by explicitly allowing or denying traffic.

---

# RBAC

Role-Based Access Control governs permissions within Kubernetes.

Interview Answer:

RBAC ensures users and service accounts receive only the permissions necessary to perform their tasks.

---

# Most Common Interview Scenario

### Pod Running but Application Not Accessible

Interview Answer:

I would verify the Service configuration, Endpoints, Ingress rules, Network Policies, readiness probes, application logs, and DNS resolution. Many times the Pod is healthy but traffic routing components are misconfigured.

---

# Kubernetes Cheat Sheet

```bash
kubectl get pods
kubectl get svc
kubectl get deployments
kubectl describe pod
kubectl logs pod-name
kubectl exec -it pod-name -- bash
kubectl get events
kubectl rollout status deployment app
kubectl rollout undo deployment app
kubectl top pods
kubectl top nodes
kubectl get ingress
kubectl get pvc
kubectl get pv
```

---

# Top Interview Questions

1. Explain Kubernetes Architecture.
2. Deployment vs StatefulSet.
3. Deployment vs ReplicaSet.
4. Pod vs Container.
5. Service Types.
6. Ingress vs LoadBalancer.
7. ConfigMap vs Secret.
8. Liveness vs Readiness Probe.
9. CrashLoopBackOff troubleshooting.
10. Pod Pending troubleshooting.
11. HPA.
12. Taints and Tolerations.
13. Node Affinity.
14. Network Policies.
15. RBAC.
16. Rolling Updates.
17. Rollback.
18. Pod Disruption Budget.
19. Storage Classes.
20. EKS Architecture.
