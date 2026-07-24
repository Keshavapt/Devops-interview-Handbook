# Kubernetes Tasks (Hands-on & Interview Guide)

> **Interview Focus:**  
> In Kubernetes coding rounds, you are typically given incomplete manifests or asked to modify existing resources. Interviewers evaluate your understanding of Kubernetes concepts, troubleshooting skills, and production best practices—not your ability to memorize YAML.

---

# Table of Contents

1. Validate and Apply Manifests
2. Secrets and Environment Variables
3. Deployments
4. Services
5. ConfigMaps
6. Secrets
7. Namespaces
8. RBAC
9. Readiness & Liveness Probes
10. Resource Requests & Limits
11. Rolling Updates
12. Verify Rollout
13. Verify Environment Variables
14. Common Interview Tasks
15. Production Best Practices

---

# 1. Validate and Apply Manifests

Before applying any manifest, validate it.

## Validate Client Side

```bash
kubectl apply --dry-run=client -f deployment.yaml
```

Checks

- YAML syntax
- Resource format

---

## Validate Against Cluster

```bash
kubectl apply --dry-run=server -f deployment.yaml
```

Checks

- API compatibility
- Existing resources
- Admission controllers

---

## Apply Manifest

```bash
kubectl apply -f deployment.yaml
```

---

## View Created Resources

```bash
kubectl get all
```

---

## Describe Resource

```bash
kubectl describe deployment nginx
```

---

# Interview Question

### Why use --dry-run?

### Answer

It validates manifests before creating resources, helping identify syntax or configuration issues without modifying the cluster.

---

# 2. Inject Environment Variables from Secrets

Sensitive information should never be hardcoded.

Example Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=
  password: cGFzc3dvcmQ=
```

Inject into Deployment

```yaml
env:
  - name: DB_USERNAME
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: username

  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: password
```

---

# Interview Question

### Why use secretKeyRef?

### Answer

It securely injects secret values into containers without hardcoding credentials in application code or manifests.

---

# 3. Deployments

Deployment responsibilities

- Create Pods
- Maintain replicas
- Rolling updates
- Rollbacks
- Scaling

Common Commands

```bash
kubectl create deployment nginx --image=nginx

kubectl get deployments

kubectl describe deployment nginx

kubectl delete deployment nginx
```

---

# Common Interview Task

Complete a Deployment by adding:

- replicas
- labels
- selectors
- image
- containerPort
- probes
- resources

---

# 4. Services

Service Types

| Type         | Usage                    |
| ------------ | ------------------------ |
| ClusterIP    | Internal communication   |
| NodePort     | External access via Node |
| LoadBalancer | Cloud Load Balancer      |
| ExternalName | External DNS             |

---

Create Service

```bash
kubectl expose deployment nginx \
--port=80 \
--target-port=80
```

---

Interview Task

Create a Service exposing a Deployment on port 80.

---

# 5. ConfigMaps

Used for

- URLs
- Feature flags
- Configuration
- Application settings

Create

```bash
kubectl create configmap app-config \
--from-literal=APP_ENV=production
```

Use in Deployment

```yaml
env:
  - name: APP_ENV
    valueFrom:
      configMapKeyRef:
        name: app-config
        key: APP_ENV
```

---

Interview Task

Inject ConfigMap values into a Pod.

---

# 6. Secrets

Create Secret

```bash
kubectl create secret generic db-secret \
--from-literal=username=admin \
--from-literal=password=password123
```

Use

- Environment variables
- Volume mounts

---

Interview Task

Inject username/password into Deployment using `secretKeyRef`.

---

# 7. Namespaces

Create Namespace

```bash
kubectl create namespace production
```

Deploy Resource

```bash
kubectl apply -f deployment.yaml \
-n production
```

List Namespaces

```bash
kubectl get ns
```

---

Interview Task

Deploy application into the **production** namespace.

---

# 8. RBAC

RBAC Components

- Role
- ClusterRole
- RoleBinding
- ClusterRoleBinding

Example

Developer

Can

- Get Pods
- View Logs

Cannot

- Delete Namespace
- Create ClusterRole

---

Interview Task

Grant read-only Pod access to a user.

---

# 9. Add Readiness & Liveness Probes

## Readiness Probe

Determines if Pod is ready for traffic.

Example

```yaml
readinessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 10
  periodSeconds: 5
```

---

## Liveness Probe

Checks whether the application is healthy.

Example

```yaml
livenessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 30
  periodSeconds: 10
```

---

Interview Task

Add both probes to an existing Deployment.

---

# 10. Add Resource Requests & Limits

Example

```yaml
resources:
  requests:
    cpu: "200m"
    memory: "256Mi"

  limits:
    cpu: "500m"
    memory: "512Mi"
```

---

Why?

Requests

Minimum resources guaranteed.

Limits

Maximum resources allowed.

---

Interview Task

Add production-ready CPU and memory limits.

---

# 11. Plan Rolling Updates

Example

```yaml
strategy:
  type: RollingUpdate

  rollingUpdate:
    maxSurge: 1
    maxUnavailable: 0
```

---

Benefits

- Zero downtime
- Safe deployments
- Easy rollback

---

Interview Task

Modify Deployment to support zero-downtime updates.

---

# 12. Verify Rollout

Check rollout status

```bash
kubectl rollout status deployment/nginx
```

View rollout history

```bash
kubectl rollout history deployment/nginx
```

Rollback

```bash
kubectl rollout undo deployment/nginx
```

---

Interview Task

Rollback to the previous Deployment revision.

---

# 13. Verify Environment Variable Injection

View Pod

```bash
kubectl get pods
```

Open Shell

```bash
kubectl exec -it <pod-name> -- sh
```

View Environment Variables

```bash
env
```

Or

```bash
printenv
```

Verify

```text
DB_USERNAME

DB_PASSWORD

APP_ENV
```

---

Interview Task

Confirm Secret values are successfully injected into a running container.

---

# 14. Common Hands-on Interview Tasks

### Task 1

Validate a Deployment manifest using `--dry-run`.

---

### Task 2

Create a Secret.

---

### Task 3

Inject Secret into Deployment.

---

### Task 4

Create ConfigMap.

---

### Task 5

Inject ConfigMap into Deployment.

---

### Task 6

Expose Deployment using a Service.

---

### Task 7

Deploy application in a custom Namespace.

---

### Task 8

Add CPU and Memory limits.

---

### Task 9

Configure Readiness Probe.

---

### Task 10

Configure Liveness Probe.

---

### Task 11

Scale Deployment to five replicas.

---

### Task 12

Verify rollout.

---

### Task 13

Rollback Deployment.

---

### Task 14

Verify environment variables.

---

### Task 15

Grant read-only Pod access using RBAC.

---

# 15. Production Best Practices

- Always validate manifests before applying.
- Never hardcode passwords or API keys.
- Use Secrets for sensitive data.
- Use ConfigMaps for application configuration.
- Deploy workloads into appropriate Namespaces.
- Follow the Principle of Least Privilege with RBAC.
- Configure Readiness and Liveness probes.
- Define CPU and Memory requests/limits.
- Use Rolling Updates for zero-downtime deployments.
- Verify rollouts before considering a deployment successful.
- Monitor deployments with Prometheus/Grafana.
- Keep manifests in Git and deploy via CI/CD.

---

# Common Interview Questions with Answers

### Q1. Why use `kubectl apply --dry-run`?

**Answer:**  
To validate manifests before creating resources, preventing syntax or configuration errors from reaching the cluster.

---

### Q2. Why use `secretKeyRef` instead of hardcoding passwords?

**Answer:**  
It securely injects sensitive values from Kubernetes Secrets into Pods, avoiding exposure in manifests or source code.

---

### Q3. What is the difference between Readiness and Liveness probes?

**Answer:**

- **Readiness Probe:** Determines when a Pod is ready to receive traffic.
- **Liveness Probe:** Detects unhealthy containers and restarts them automatically.

---

### Q4. Why define Resource Requests and Limits?

**Answer:**

- **Requests** reserve the minimum resources a Pod needs.
- **Limits** prevent a Pod from consuming excessive CPU or memory, ensuring cluster stability.

---

### Q5. How do you achieve zero-downtime deployments?

**Answer:**

- Use Deployments with a RollingUpdate strategy.
- Configure multiple replicas.
- Add Readiness Probes.
- Set `maxUnavailable: 0`.
- Verify rollout before completing the deployment.

---

### Q6. How do you verify Secret injection?

**Answer:**

Use:

```bash
kubectl exec -it <pod-name> -- printenv
```

or

```bash
kubectl exec -it <pod-name> -- env
```

to confirm the environment variables are available inside the running container.

---

## Interview Tip

In coding rounds, always explain **what you're doing and why**. For example:

> "Before applying the manifest, I'll run a client-side dry run to validate the YAML. Then I'll use a server-side dry run to ensure it's compatible with the cluster. After deployment, I'll verify the rollout status and inspect the Pod to confirm that Secrets and ConfigMaps have been injected correctly."

This demonstrates not only Kubernetes knowledge but also a production-oriented workflow.
