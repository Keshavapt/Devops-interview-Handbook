# Kubernetes Tasks, Coding Drills & Interview Drill Workbook

> **Focus:** 5+ Years Experience / Senior DevOps / Platform Engineer
> **Interview Focus:** In Kubernetes coding rounds, you are typically given incomplete manifests or asked to modify existing resources. Interviewers evaluate your understanding of Kubernetes concepts, troubleshooting skills, and production best practices—not your ability to memorize YAML.

---

## Table of Contents

- [Part 1: Task Preparation & Validation](#part-1-task-preparation--validation)
- [Part 2: Coding Drills (Kubernetes Manifests)](#part-2-coding-drills-kubernetes-manifests)
- [Part 3: Kubernetes Troubleshooting Drills](#part-3-kubernetes-troubleshooting-drills)
- [Part 4: Answers, Hints & Solutions](#part-4-answers--hints--solutions)
- [Part 5: Interview Best Practices](#part-5-interview-best-practices)

---

## Part 1: Task Preparation & Validation

**Before applying any manifest, validate it.**

### Step 1: Validate Client Side

```bash
kubectl apply --dry-run=client -f deployment.yaml
```

**Checks:**
- YAML syntax
- Resource format

### Step 2: Validate Against Cluster

```bash
kubectl apply --dry-run=server -f deployment.yaml
```

**Checks:**
- API compatibility
- Existing resources
- Admission controllers

### Step 3: Apply Manifest

```bash
kubectl apply -f deployment.yaml
```

### Step 4: Verify Resources

```bash
kubectl get all
kubectl describe deployment nginx
kubectl logs deployment/nginx
```

### Interview Question: Why use --dry-run?

**Answer:** It validates manifests before creating resources, helping identify syntax or configuration issues without modifying the cluster.

---

## Part 2: Coding Drills (Kubernetes Manifests)

### Task 1: Complete a Deployment Manifest

**Task:** Build a Production-ready Deployment with:
- 3 replicas
- Health checks
- Resource requests/limits
- Labels & selectors

**Hint:** Use RollingUpdate strategy, readiness probe, non-root user.

---

### Task 2: Create a Secret and Inject into Deployment

**Task:** Create a Secret for database credentials, then inject into a Deployment.

**Hint:**
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=  # admin
  password: cGFzc3dvcmQ=  # password
---

apiVersion: v1
kind: Deployment
metadata:
  name: app
spec:
  replicas: 2
  template:
    spec:
      containers:
      - name: app
        image: myapp:1.0
        env:
        - name: DB_USER
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

**Interview Question:** Why use `secretKeyRef` instead of hardcoding passwords?

**Answer:** It securely injects secret values from Kubernetes Secrets into Pods, avoiding exposure in manifests or source code.

---

### Task 3: Expose Deployment Using a Service

**Task:** Create a Service to expose the app Deployment.

**Hint:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: app-service
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 8080
  type: LoadBalancer
```

**Question:** Service types?
- **ClusterIP:** Internal only
- **NodePort:** Node IP + static port
- **LoadBalancer:** Cloud LB (production)

---

### Task 4: Configure ConfigMap

**Task:** Create a ConfigMap for app configuration and mount it into a Pod.

**Hint:**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  app.env: "production"
  log.level: "info"

---

## Part 3: Kubernetes Troubleshooting Drills

### Drill 1: Pod in CrashLoopBackOff

**Scenario:** A Pod continuously restarts.

**Steps to Troubleshoot:**
1. `kubectl get pods` — Verify CrashLoopBackOff status
2. `kubectl describe pod <pod-name>` — Check events and config
3. `kubectl logs <pod-name> --previous` — See previous crash logs
4. `kubectl exec -it <pod-name> -- env` — Check env vars
5. Verify resource limits and image

**Common Causes:**
- Application crash (bad config, missing dependency)
- Missing Secret/ConfigMap
- Database connectivity issues
- Memory limits too low

---

### Drill 2: Pod in ImagePullBackOff

**Scenario:** Pod cannot pull the container image.

**Steps to Troubleshoot:**
1. `kubectl describe pod <pod-name>` — Look for imagePullPolicy errors
2. Check image name and tag
3. Verify registry authentication (imagePullSecrets)
4. Check network access to registry

**Common Causes:**
- Wrong image name/tag
- Private registry without authentication
- Network/proxy blocking registry access
- Incomplete image digest

---

### Drill 3: Pod Pending

**Scenario:** Pod remains pending for several minutes.

**Steps to Investigate:**
1. `kubectl describe pod <pod-name>` — Look at the Events section
2. `kubectl get nodes` — Check node status
3. `kubectl top nodes` — Check available resources
4. Check taints/tolerations
5. Verify PVC availability (for stateful workloads)

**Common Causes:**
- Insufficient CPU/memory
- Node taints without tolerations
- Node selector mismatch
- PVC or storage class issue
- Quotas exceeded

---

### Drill 4: Node NotReady

**Scenario:** A node becomes NotReady.

**Steps to Investigate:**
1. `kubectl get nodes` — Check node status
2. `kubectl describe node <node-name>` — Check conditions
3. `kubectl get pods -o wide` — Check which pods are on the node
4. SSH to node: `ssh node-name`
5. Check kubelet: `systemctl status kubelet`
6. Check node resources: `df -h`, `free -h`, `top`

**Common Causes:**
- Kubelet crashing or misconfigured
- Disk pressure
- Memory pressure
- Network issues
- Node drain (for maintenance)

---
---

apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:1.0
    volumeMounts:
    - name: config
      mountPath: /etc/app
  volumes:
  - name: config
    configMap:
      name: app-config
```

---

### Task 5: Configure Probes

**Task:** Add Readiness and Liveness probes to a Deployment.

**Hint:**
---

## Part 4: Answers, Hints & Solutions

### Task 1: Production-ready Deployment

**Solution:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
  labels:
    app: myapp
    env: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        fsGroup: 1000
      containers:
      - name: app
        image: myapp:1.0
        ports:
        - containerPort: 8080
        readinessProbe:
          httpGet:
            path: /healthz
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 10
        livenessProbe:
          httpGet:
            path: /livez
            port: 8080
          initialDelaySeconds: 15
          periodSeconds: 20
        resources:
          requests:
            cpu: "250m"
            memory: "64Mi"
          limits:
            cpu: "500m"
            memory: "128Mi"
```

**Key Points:**
- Never create bare Pods in production
- Use Labels & Selectors matching
- SecurityContext for non-root
- Readiness & Liveness probes
- Resource requests & limits

---

### Task 2: Secret Injection

**Solution:**
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

**Why secretKeyRef?**
- Avoids hardcoding credentials
- Secrets are base64-encoded
- Can be rotated without redeploying
- Supports encryption at rest

---

### Task 3: Service

**Solution:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: app-service
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 8080
  type: LoadBalancer
```

---

---

## Part 5: Interview Best Practices

### During Coding Rounds

**Always explain what you're doing and why:**

> "Before applying the manifest, I'll run a client-side dry run to validate the YAML. Then I'll use a server-side dry run to ensure it's compatible with the cluster. After deployment, I'll verify the rollout status and inspect the Pod to confirm that Secrets and ConfigMaps have been injected correctly."

### Production Best Practices Checklist

- Always validate manifests before applying
- Never hardcode passwords or API keys
- Use Secrets for sensitive data
- Use ConfigMaps for application configuration
- Deploy workloads into appropriate Namespaces
- Follow the Principle of Least Privilege with RBAC
- Configure Readiness and Liveness probes
- Define CPU and Memory requests/limits
- Use Rolling Updates for zero-downtime deployments
- Verify rollouts before considering a deployment successful
- Monitor deployments with Prometheus/Grafana
- Keep manifests in Git and deploy via CI/CD

### Interview Questions to Prepare

1. Why use `kubectl apply --dry-run`?
2. Why use `secretKeyRef` instead of hardcoding passwords?
3. What is the difference between Readiness and Liveness probes?
4. Why define Resource Requests and Limits?
5. How do you achieve zero-downtime deployments?
6. How do you verify Secret injection?

### Key Commands Cheat Sheet

```bash
# Validation
kubectl apply --dry-run=client -f deployment.yaml
kubectl apply --dry-run=server -f deployment.yaml

# Deploy
kubectl apply -f deployment.yaml

# Verify
kubectl get all
kubectl describe deployment <name>
kubectl logs <pod> --previous
kubectl exec -it <pod> -- bash

# Troubleshoot
kubectl get events --sort-by=.lastTimestamp
kubectl top pods
kubectl describe node <node>
```

---

## Interview Question: How do you troubleshoot CrashLoopBackOff?

**Answer:**
1. `kubectl logs <pod> --previous` — See previous crash logs
2. `kubectl describe pod <pod>` — Check events and config
3. `kubectl exec -it <pod> -- env` — Verify env vars
4. Check resource limits and image
5. Fix config/code, then re-apply

---

## Interview Question: How do you achieve zero-downtime deployments?

**Answer:**
- Use Deployments with RollingUpdate strategy (maxUnavailable: 0)
- Configure multiple replicas
- Add Readiness Probes
- Set `maxSurge: 1`
- Verify rollout with `kubectl rollout status`

---

## Kubernetes Helper Commands

```bash
# Pod management
kubectl get pods -A
kubectl delete pod <name>
kubectl scale deployment <name> --replicas=5

# Rollout
kubectl rollout status deployment <name>
kubectl rollout undo deployment <name>
kubectl rollout history deployment <name>

# Debugging
kubectl debug <pod> -it --image=nicolaka/netshoot
kubectl top pods
```

---

## Final Interview Tip

For Kubernetes interviews, avoid only defining resources. Explain **how they work together** in a production deployment.

A strong answer could be:

> "Developers push code to GitLab, which triggers a CI/CD pipeline. The pipeline builds and scans a Docker image, pushes it to a registry, and deploys it to Kubernetes using Helm. The Deployment performs a rolling update with multiple replicas and readiness probes to ensure zero downtime. Services provide stable networking, Ingress exposes the application externally, ConfigMaps and Secrets manage configuration securely, HPA scales the application based on CPU usage, and Prometheus with Grafana monitors the cluster."

### Task 4: ConfigMap

**Solution:**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  app.env: "production"
  log.level: "info"
```

---

### Task 5: Probes

**Solution:**
```yaml
readinessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
  timeoutSeconds: 2

livenessProbe:
  httpGet:
    path: /livez
    port: 8080
  initialDelaySeconds: 15
  periodSeconds: 20
  timeoutSeconds: 3
```

---

### Task 6: Resource Limits

**Solution:**
```yaml
resources:
  requests:
    cpu: "250m"
    memory: "64Mi"
  limits:
    cpu: "500m"
    memory: "128Mi"
```

---

### Task 7: Rolling Update

**Solution:**
```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1
    updatePeriodSeconds: 5
    intervalSeconds: 10
```

---

### Drill 1: CrashLoopBackOff Solution

**Diagnosis:**
```bash
kubectl get pods
kubectl describe pod <name>
kubectl logs <name> --previous
kubectl exec -it <name> -- env
```

**Resolution:**
1. Fix application config or code
2. Verify Secret/ConfigMap exist
3. Check database connectivity
4. Adjust resource limits
5. Re-apply manifest

---

### Drill 2: ImagePullBackOff Solution

**Diagnosis:**
```bash
kubectl describe pod <name>
kubectl get events --sort-by=.lastTimestamp
```

**Resolution:**
1. Verify image name and tag
2. Add imagePullSecrets for private registry
3. Check network/firewall rules
4. Consider using imagePullPolicy: IfNotPresent

---

### Drill 3: Pending Pod Solution

**Diagnosis:**
```bash
kubectl describe pod <name>
kubectl get nodes
kubectl top nodes
kubectl get pvc
```

**Resolution:**
1. Increase node resources or add nodes
2. Fix taints/tolerations
3. Correct node selector
4. Ensure PVC and storage class are correct

---

### Drill 4: Node NotReady Solution

**Diagnosis:**
```bash
kubectl get nodes
kubectl describe node <node>
ssh node
systemctl status kubelet
```

**Resolution:**
1. Check kubelet status
2. Review disk/memory pressure
3. Check network connections
4. Verify cloud provider metrics
5. Restart kubelet if needed

---
```yaml
readinessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10

livenessProbe:
  httpGet:
    path: /livez
    port: 8080
  initialDelaySeconds: 15
  periodSeconds: 20
```

**Question:** Readiness vs Liveness probe difference?

**Answer:**
- **Readiness Probe:** Determines when a Pod is ready to receive traffic. Until it passes, the Service does not send requests to the Pod.
- **Liveness Probe:** Detects if the application has become unhealthy. If it fails, Kubernetes restarts the container automatically.

---

### Task 6: Set Resource Requests & Limits

**Task:** Add CPU and Memory limits to a Deployment.

**Hint:**
```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

**Question:** Why define resource requests/limits?

**Answer:**
- **Requests** reserve the minimum resources a Pod needs.
- **Limits** prevent a Pod from consuming excessive CPU or memory, ensuring cluster stability.

---

### Task 7: Configure Rolling Update Strategy

**Task:** Configure zero-downtime rolling updates.

**Hint:**
```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1
```

**Question:** How do you achieve zero-downtime deployments?

**Answer:**
- Use Deployments with a RollingUpdate strategy.
- Configure multiple replicas.
- Add Readiness Probes.
- Set `maxUnavailable: 0`.
- Verify rollout before completing the deployment.

---