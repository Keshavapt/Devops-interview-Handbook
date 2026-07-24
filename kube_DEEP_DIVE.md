# Kubernetes Deep Dive

> **Interview Focus:**  
> Kubernetes is one of the most important topics for a DevOps/Platform Engineer with 4–6 years of experience. Interviewers expect you to understand not only the core objects but also networking, scaling, security, deployments, and production best practices.

---

# Table of Contents

1. Kubernetes Architecture
2. Core Components
3. Pods
4. ReplicaSets
5. Deployments
6. Services
7. ConfigMaps
8. Secrets
9. Namespaces
10. Networking
11. Ingress Controllers
12. Scaling
13. Horizontal Pod Autoscaler (HPA)
14. Rolling Updates
15. Zero-Downtime Deployments
16. Helm Charts
17. Kubernetes Operators
18. RBAC
19. Kubernetes Security Best Practices
20. Common Production Scenarios
21. Common Interview Questions
22. 5-Minute Revision

---

# 1. Kubernetes Architecture

## What is Kubernetes?

Kubernetes is an open-source container orchestration platform used to deploy, manage, scale, and operate containerized applications.

It automates:

- Deployment
- Scaling
- Load balancing
- Self-healing
- Service discovery
- Rolling updates

---

## Kubernetes Architecture

```text
                 Kubernetes Cluster

        +------------------------------+
        |        Control Plane         |
        |------------------------------|
        | API Server                   |
        | Scheduler                    |
        | Controller Manager           |
        | etcd                         |
        +------------------------------+

                    │
     ---------------------------------------

          Worker Node              Worker Node

      kubelet                     kubelet
      kube-proxy                  kube-proxy
      Pods                        Pods
```

---

## Control Plane Components

### API Server

The entry point for all Kubernetes requests.

Responsibilities

- Authentication
- Authorization
- Resource validation
- Cluster communication

---

### Scheduler

Assigns Pods to suitable worker nodes.

Considers

- CPU
- Memory
- Affinity
- Taints
- Resource availability

---

### Controller Manager

Ensures the cluster matches the desired state.

Examples

- Deployment Controller
- ReplicaSet Controller
- Node Controller

---

### etcd

Distributed key-value database.

Stores

- Cluster state
- Configuration
- Secrets
- Metadata

---

# 2. Core Components

Core Kubernetes resources include:

- Pods
- ReplicaSets
- Deployments
- Services
- ConfigMaps
- Secrets
- Ingress
- Persistent Volumes
- Namespaces

---

# 3. Pods

A Pod is the smallest deployable unit in Kubernetes.

A Pod contains one or more containers sharing:

- Network namespace
- Storage
- IP Address
- Volumes

---

## Pod Lifecycle

```text
Pending

↓

Running

↓

Succeeded

↓

Failed
```

---

## Common Pod Commands

```bash
kubectl get pods

kubectl describe pod <pod-name>

kubectl logs <pod-name>

kubectl delete pod <pod-name>

kubectl exec -it <pod-name> -- /bin/sh
```

---

## Best Practices

- One application per Pod
- Never create Pods directly in production
- Use Deployments

---

# 4. ReplicaSets

ReplicaSets ensure a specified number of Pod replicas are always running.

Example

```text
Desired Replicas = 3

↓

One Pod Fails

↓

ReplicaSet Creates New Pod
```

---

# 5. Deployments

Deployments manage ReplicaSets and provide declarative updates.

Responsibilities

- Rolling Updates
- Rollbacks
- Scaling
- Version Management

---

## Deployment Workflow

```text
kubectl apply

↓

Deployment

↓

ReplicaSet

↓

Pods
```

---

## Common Commands

```bash
kubectl get deployments

kubectl rollout status deployment/nginx

kubectl rollout history deployment/nginx

kubectl rollout undo deployment/nginx

kubectl scale deployment nginx --replicas=5
```

---

# 6. Services

Pods are ephemeral.

Services provide stable networking.

Types

## ClusterIP

Default.

Accessible only inside the cluster.

---

## NodePort

Accessible through:

```
NodeIP:Port
```

---

## LoadBalancer

Creates cloud load balancer.

Used in production.

---

## ExternalName

Maps service to an external DNS.

---

## Service Flow

```text
Client

↓

Service

↓

Pods
```

---

# 7. ConfigMaps

ConfigMaps store non-sensitive configuration.

Examples

- URLs
- Feature flags
- Configuration files
- Environment variables

---

## Benefits

- Decouple configuration from application
- Easy updates
- Reusable

---

# 8. Secrets

Secrets store sensitive information.

Examples

- Passwords
- Tokens
- Certificates
- API Keys

---

## Best Practices

- Never hardcode credentials
- Encrypt secrets at rest
- Use RBAC
- Rotate secrets regularly

---

# 9. Namespaces

Namespaces logically isolate workloads.

Examples

```text
default

development

testing

production

monitoring
```

Benefits

- Resource isolation
- RBAC separation
- Quotas
- Multi-team environments

---

# 10. Networking

Every Pod receives its own IP address.

Pods communicate directly without NAT.

---

## Kubernetes Networking Model

```text
Client

↓

Ingress

↓

Service

↓

Pod

↓

Container
```

---

## Networking Components

- CNI Plugin
- kube-proxy
- Services
- DNS
- Ingress

---

## Popular CNI Plugins

- Calico
- Flannel
- Cilium
- Weave Net

---

# 11. Ingress Controllers

Ingress exposes HTTP/HTTPS applications.

Instead of multiple LoadBalancers:

```text
Internet

↓

Ingress Controller

↓

Application A

Application B

Application C
```

---

## Popular Controllers

- NGINX Ingress
- AWS Load Balancer Controller
- Traefik
- HAProxy

---

## Benefits

- SSL termination
- Path-based routing
- Host-based routing
- Centralized traffic management

---

# 12. Scaling

Scaling ensures applications handle increased traffic.

Types

- Manual Scaling
- Automatic Scaling

---

## Manual

```bash
kubectl scale deployment nginx --replicas=5
```

---

# 13. Horizontal Pod Autoscaler (HPA)

Automatically adjusts replicas.

Based on

- CPU
- Memory
- Custom Metrics

---

## Workflow

```text
Traffic Increases

↓

CPU > Threshold

↓

HPA

↓

More Pods Created
```

---

## Benefits

- Better performance
- Cost optimization
- Automatic scaling

---

# 14. Rolling Updates

Rolling Updates replace old Pods gradually.

```text
Version 1

↓

One Pod Updated

↓

Health Check

↓

Next Pod

↓

Version 2
```

Benefits

- Zero downtime
- Easy rollback
- Controlled deployment

---

# 15. Zero-Downtime Deployments

Production deployments should avoid service interruption.

Requirements

- Multiple replicas
- Rolling updates
- Readiness probes
- Liveness probes
- Load balancing

---

## Workflow

```text
Old Pod

↓

New Pod Created

↓

Readiness Check

↓

Traffic Shifted

↓

Old Pod Removed
```

---

## Best Practices

- Minimum 2 replicas
- Use readiness probes
- Configure rolling updates
- Monitor rollout status

---

# 16. Helm Charts

Helm is the package manager for Kubernetes.

Benefits

- Reusable templates
- Version management
- Easy upgrades
- Parameterized deployments

---

## Helm Workflow

```text
helm install

↓

Templates Rendered

↓

Resources Created
```

---

## Common Commands

```bash
helm install

helm upgrade

helm rollback

helm list

helm uninstall
```

---

# 17. Kubernetes Operators

Operators extend Kubernetes using Custom Resources (CRDs) and controllers.

Purpose

Automate complex application lifecycle management.

Examples

- Database provisioning
- Backup automation
- Cluster upgrades
- Certificate management

---

## Popular Operators

- Prometheus Operator
- Elastic Operator
- Strimzi (Kafka)
- ArgoCD Operator

---

# 18. RBAC (Role-Based Access Control)

RBAC controls who can perform actions within the cluster.

Resources

- Role
- ClusterRole
- RoleBinding
- ClusterRoleBinding

---

## Example

Developer

```text
Can:

- View Pods
- View Logs

Cannot:

- Delete Namespaces
- Modify RBAC
```

---

## Principle of Least Privilege

Grant only the permissions required to perform a task.

---

# 19. Kubernetes Security Best Practices

- Enable RBAC
- Use Namespaces
- Apply Network Policies
- Use Secrets for credentials
- Avoid running containers as root
- Use resource limits
- Scan container images
- Keep Kubernetes updated
- Restrict API access
- Enable audit logging

---

# 20. Common Production Scenarios

## Pod CrashLoopBackOff

Check:

- kubectl logs
- kubectl describe pod
- Events
- Readiness/Liveness probes
- Environment variables
- Image

---

## ImagePullBackOff

Possible causes

- Wrong image name
- Invalid tag
- Missing imagePullSecret
- Registry authentication
- Network issues

---

## Pending Pod

Investigate

- Available CPU/Memory
- Node status
- Taints/Tolerations
- PVC binding
- Scheduler events

---

## Node NotReady

Check

- kubelet
- Disk space
- Network connectivity
- Node conditions
- Cloud instance health

---

## Service Not Reachable

Verify

- Service selector
- Pod labels
- Endpoints
- Network policies
- Ingress rules

---

# 21. Common Interview Questions with Sample Answers

---

## 1. Explain Kubernetes Architecture.

### Answer

Kubernetes follows a **Control Plane + Worker Node** architecture.

**Control Plane Components**

- API Server → Entry point for all cluster communication.
- Scheduler → Assigns Pods to suitable nodes.
- Controller Manager → Maintains the desired state of the cluster.
- etcd → Stores the cluster state and configuration.

**Worker Node Components**

- kubelet → Communicates with the API Server and manages Pods.
- kube-proxy → Handles networking and Service routing.
- Container Runtime → Runs containers (containerd, CRI-O, etc.).

---

## 2. What is a Pod?

### Answer

A Pod is the **smallest deployable unit** in Kubernetes.

It contains one or more containers that share:

- Network
- Storage
- IP Address

Pods are **ephemeral**, meaning if one fails, Kubernetes replaces it.

---

## 3. Difference between Deployment and ReplicaSet?

### Answer

| Deployment               | ReplicaSet                    |
| ------------------------ | ----------------------------- |
| Manages ReplicaSets      | Manages Pods                  |
| Supports Rolling Updates | No Rolling Updates            |
| Supports Rollbacks       | No Rollbacks                  |
| Production Recommended   | Usually managed by Deployment |

**Interview Tip**

> We generally don't create ReplicaSets directly. Deployments create and manage ReplicaSets automatically.

---

## 4. Difference between ConfigMap and Secret?

### Answer

| ConfigMap             | Secret                                        |
| --------------------- | --------------------------------------------- |
| Stores configuration  | Stores sensitive information                  |
| Plain text            | Base64 encoded (optionally encrypted at rest) |
| URLs                  | Passwords                                     |
| Feature flags         | API Keys                                      |
| Environment variables | Certificates                                  |

**Best Practice**

Never store passwords in ConfigMaps.

---

## 5. Difference between ClusterIP, NodePort and LoadBalancer?

### Answer

### ClusterIP

Default service.

Accessible only within the cluster.

Example

```
Frontend → Backend
```

---

### NodePort

Accessible from outside using

```
NodeIP:Port
```

Mostly used for testing.

---

### LoadBalancer

Creates a Cloud Load Balancer.

Production use.

```
Internet

↓

AWS ELB

↓

Service

↓

Pods
```

---

## 6. What is an Ingress Controller?

### Answer

Ingress Controller manages external HTTP/HTTPS traffic.

Instead of creating multiple LoadBalancers,

```
Internet

↓

Ingress Controller

↓

App1

App2

App3
```

Benefits

- SSL Termination
- Path Routing
- Host Routing
- Centralized traffic

Popular Controllers

- NGINX
- AWS Load Balancer Controller
- Traefik

---

## 7. Explain Kubernetes Networking.

### Answer

Every Pod gets its own IP.

Pods communicate directly.

Traffic Flow

```
Client

↓

Ingress

↓

Service

↓

Pod

↓

Container
```

Networking Components

- CNI Plugin
- kube-proxy
- CoreDNS
- Services
- Ingress

---

## 8. What is Horizontal Pod Autoscaler?

### Answer

HPA automatically increases or decreases Pod replicas based on metrics.

Example

```
CPU > 70%

↓

HPA

↓

Increase Replicas
```

Benefits

- Better Performance
- Cost Optimization
- Automatic Scaling

---

## 9. How do Rolling Updates work?

### Answer

Instead of replacing all Pods at once,

Kubernetes updates them gradually.

```
Old Pod

↓

New Pod Created

↓

Readiness Check

↓

Traffic Shift

↓

Old Pod Deleted
```

Benefits

- No downtime
- Easy rollback
- Safe deployments

---

## 10. How do you achieve Zero-Downtime Deployments?

### Answer

Requirements

- Multiple replicas
- Rolling Update strategy
- Readiness probes
- Liveness probes
- LoadBalancer/Service
- Health checks

Deployment Flow

```
Old Pod

↓

New Pod

↓

Ready

↓

Traffic Shift

↓

Delete Old Pod
```

---

## 11. What is Helm?

### Answer

Helm is Kubernetes' package manager.

Instead of maintaining hundreds of YAML files,

Helm uses templates.

Benefits

- Reusable templates
- Versioning
- Rollbacks
- Parameterized deployments

---

## 12. Why use Helm instead of raw YAML?

### Answer

Without Helm

- Duplicate YAML
- Difficult maintenance
- Manual updates

With Helm

- Single reusable chart
- Values.yaml
- Easier upgrades
- Better version control

---

## 13. What are Kubernetes Operators?

### Answer

Operators extend Kubernetes functionality using

- CRDs
- Custom Controllers

Used to automate complex applications.

Examples

- Prometheus
- Kafka
- Elasticsearch
- Databases

---

## 14. Explain RBAC.

### Answer

RBAC controls user permissions.

Components

- Role
- ClusterRole
- RoleBinding
- ClusterRoleBinding

Example

Developer

Can

- View Pods
- View Logs

Cannot

- Delete Namespace
- Change Cluster Configuration

Follow the **Principle of Least Privilege**.

---

## 15. How would you secure a Kubernetes cluster?

### Answer

Best Practices

- Enable RBAC
- Use Namespaces
- Use Network Policies
- Store credentials in Secrets
- Enable Audit Logs
- Use Pod Security Standards
- Avoid running containers as root
- Scan Docker Images
- Keep Kubernetes updated
- Restrict API Server access

---

## 16. How would you troubleshoot CrashLoopBackOff?

### Answer

Steps

1. Check Pod logs

```bash
kubectl logs <pod>
```

2. Describe Pod

```bash
kubectl describe pod <pod>
```

3. Check Events

4. Verify Readiness/Liveness probes

5. Check Environment Variables

6. Verify ConfigMaps & Secrets

7. Verify application startup

---

## 17. How would you troubleshoot ImagePullBackOff?

### Answer

Possible causes

- Wrong image name
- Incorrect image tag
- Private registry authentication issue
- Missing imagePullSecret
- Registry unavailable
- Network issue

Commands

```bash
kubectl describe pod
```

Check the Events section for image pull errors.

---

## 18. What would you check if a Pod remains Pending?

### Answer

Investigate

- Node availability
- CPU/Memory resources
- Scheduler events
- Taints & Tolerations
- Persistent Volume Claims
- Resource quotas
- Node selectors

Commands

```bash
kubectl describe pod
kubectl get nodes
kubectl get events
```

---

## 19. How do you roll back a Deployment?

### Answer

View rollout history

```bash
kubectl rollout history deployment nginx
```

Rollback

```bash
kubectl rollout undo deployment nginx
```

Verify

```bash
kubectl rollout status deployment nginx
```

---

## 20. How do you monitor Kubernetes?

### Answer

Common Stack

| Tool         | Purpose      |
| ------------ | ------------ |
| Prometheus   | Metrics      |
| Grafana      | Dashboards   |
| Alertmanager | Alerts       |
| Loki         | Logs         |
| ELK          | Log Analysis |

Monitor

- CPU
- Memory
- Pod Restarts
- Node Health
- API Server
- Storage
- Network
- Application Response Time

---

# Bonus Interview Questions

---

## Explain the Kubernetes Deployment lifecycle.

### Sample Answer

Developer pushes code →

CI/CD pipeline builds Docker image →

Image is pushed to the registry →

Deployment manifest or Helm chart is updated →

Kubernetes creates a new ReplicaSet →

Pods are created →

Readiness probes validate the Pods →

Service routes traffic to healthy Pods →

Old Pods are terminated after the rollout completes.

---

## Explain how Services and Ingress work together.

### Sample Answer

A Service provides stable networking and load balancing for Pods within the cluster. An Ingress Controller exposes one or more Services externally over HTTP/HTTPS, handling routing rules, SSL termination, and host/path-based routing.

---

## What happens when a Pod crashes?

### Sample Answer

The kubelet detects the failure and restarts the container according to the Pod's restart policy. If the Pod is managed by a Deployment, the ReplicaSet ensures the desired number of replicas are maintained by creating a replacement Pod if necessary.

---

## How do ConfigMaps and Secrets get injected into a Pod?

### Sample Answer

They can be mounted as volumes or injected as environment variables. ConfigMaps are used for non-sensitive configuration, while Secrets are used for sensitive data such as passwords, tokens, and certificates.

---

## Why are Readiness and Liveness probes both needed?

### Sample Answer

- **Readiness Probe:** Determines when a Pod is ready to receive traffic. Until it passes, the Service does not send requests to the Pod.
- **Liveness Probe:** Detects if the application has become unhealthy. If it fails, Kubernetes restarts the container automatically.

Using both ensures reliable traffic routing and automatic recovery.

---

# 22. 5-Minute Revision

Remember these key points:

- **Pod** → Smallest deployable unit
- **ReplicaSet** → Maintains desired number of Pods
- **Deployment** → Manages ReplicaSets and rolling updates
- **Service** → Stable networking for Pods
- **ConfigMap** → Non-sensitive configuration
- **Secret** → Sensitive data
- **Namespace** → Logical isolation
- **Ingress** → HTTP/HTTPS routing into the cluster
- **HPA** → Automatic Pod scaling
- **Rolling Update** → Gradual application updates
- **Helm** → Kubernetes package manager
- **Operator** → Automates complex application lifecycle
- **RBAC** → Authorization and access control

---

# Final Interview Tip

For Kubernetes interviews, avoid only defining resources. Explain **how they work together** in a production deployment.

A strong answer could be:

> "Developers push code to GitLab, which triggers a CI/CD pipeline. The pipeline builds and scans a Docker image, pushes it to a registry, and deploys it to Kubernetes using Helm. The Deployment performs a rolling update with multiple replicas and readiness probes to ensure zero downtime. Services provide stable networking, Ingress exposes the application externally, ConfigMaps and Secrets manage configuration securely, HPA scales the application based on CPU usage, and Prometheus with Grafana monitors the cluster."

This kind of end-to-end explanation demonstrates both conceptual understanding and practical production experience.
