## Kubernetes Pod Commands — Revision

| Sr. No. | Command / Syntax                               | Uses                                                                                                                                        | Example                                       |
| ------: | ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------- |
|       1 | `kubectl get pods`                             | Checks all Pods in the current namespace and shows **name, Ready status, status, restart count, and age**.                                  | `kubectl get pods`                            |
|       2 | `kubectl get pods -o wide`                     | Shows extra Pod information like **Pod IP and Worker Node**. Useful for networking and Node troubleshooting.                                | `kubectl get pods -o wide`                    |
|       3 | `kubectl get pods -A`                          | Shows Pods from **all namespaces**. Useful when you don't know where an application is running.                                             | `kubectl get pods -A`                         |
|       4 | `kubectl get pods -n <ns>`                     | Shows Pods inside a **specific namespace**, such as production or development.                                                              | `kubectl get pods -n production`              |
|       5 | `kubectl describe pod <pod>`                   | Gives **detailed Pod information** including containers, image, IP, Node, conditions, volumes, and Events. Mainly used for troubleshooting. | `kubectl describe pod nginx`                  |
|       6 | `kubectl logs <pod>`                           | Shows the **application/container logs** to find errors, crashes, startup problems, and application issues.                                 | `kubectl logs nginx`                          |
|       7 | `kubectl logs -f <pod>`                        | Continuously displays logs in **real time**, useful while monitoring or reproducing an issue.                                               | `kubectl logs -f nginx`                       |
|       8 | `kubectl logs <pod> --previous`                | Shows logs from the **previous container instance**, especially useful for `CrashLoopBackOff` and restarted containers.                     | `kubectl logs nginx --previous`               |
|       9 | `kubectl logs <pod> -c <container>`            | Shows logs of a **specific container** when a Pod has multiple containers.                                                                  | `kubectl logs nginx -c web`                   |
|      10 | `kubectl exec -it <pod> -- /bin/bash`          | Opens a shell **inside the container** so you can check files, processes, environment variables, configuration, etc.                        | `kubectl exec -it nginx -- /bin/bash`         |
|      11 | `kubectl exec <pod> -- <cmd>`                  | Runs a **specific command inside the container** without opening a shell. Useful for quick checks.                                          | `kubectl exec nginx -- ls`                    |
|      12 | `kubectl apply -f <file>`                      | Creates a Pod or **updates its configuration from YAML**. Common declarative Kubernetes method.                                             | `kubectl apply -f pod.yaml`                   |
|      13 | `kubectl delete pod <pod>`                     | **Deletes a Pod**. If it is managed by a Deployment, Kubernetes can create a replacement Pod.                                               | `kubectl delete pod nginx`                    |
|      14 | `kubectl run <pod> --image=<image>`            | Quickly creates a **standalone Pod from the command line**, useful for testing and learning.                                                | `kubectl run nginx --image=nginx`             |
|      15 | `kubectl get pod <pod> -o yaml`                | Shows the **complete YAML/configuration** of an existing Pod for inspection and troubleshooting.                                            | `kubectl get pod nginx -o yaml`               |
|      16 | `kubectl get pods --show-labels`               | Displays Pods along with their **labels**, useful for checking Service/Deployment selectors.                                                | `kubectl get pods --show-labels`              |
|      17 | `kubectl get pods -l <key>=<value>`            | Finds Pods using a **label selector**, useful for identifying a specific application or group of Pods.                                      | `kubectl get pods -l app=nginx`               |
|      18 | `kubectl label pod <pod> <key>=<value>`        | Adds or updates a **label** used for identification, grouping, and selectors.                                                               | `kubectl label pod nginx app=web`             |
|      19 | `kubectl get events --sort-by=.lastTimestamp`  | Shows the latest **Kubernetes Events**, useful for finding scheduling, image, volume, probe, and container errors.                          | `kubectl get events --sort-by=.lastTimestamp` |
|      20 | `kubectl top pod`                              | Shows **CPU and memory usage** of Pods, useful for finding resource-heavy applications.                                                     | `kubectl top pod`                             |
|      21 | `kubectl top node`                             | Shows **CPU and memory usage of Nodes**, useful for identifying overloaded or resource-limited Nodes.                                       | `kubectl top node`                            |
|      22 | `kubectl port-forward pod/<pod> <local>:<pod>` | Creates a temporary connection from your **local computer to a Pod**, mainly for testing without exposing the Pod publicly.                 | `kubectl port-forward pod/nginx 8080:80`      |
|      23 | `kubectl get nodes`                            | Shows all Kubernetes Nodes and whether they are **Ready or not**. Useful when a Pod cannot be scheduled.                                    | `kubectl get nodes`                           |
|      24 | `kubectl describe node <node>`                 | Shows detailed **Node resources, conditions, Pods, capacity, and Events** for Node-level troubleshooting.                                   | `kubectl describe node worker-1`              |
|      25 | `kubectl get namespaces`                       | Lists Kubernetes **namespaces**, useful for identifying where applications and Pods are separated.                                          | `kubectl get namespaces`                      |
|      26 | `kubectl cluster-info`                         | Displays **Kubernetes cluster/API Server information** and confirms cluster connectivity.                                                   | `kubectl cluster-info`                        |
|      27 | `kubectl config current-context`               | Shows which **Kubernetes cluster/context** your terminal is currently connected to. Important before making production changes.             | `kubectl config current-context`              |
|      28 | `kubectl explain pod`                          | Shows built-in **Kubernetes documentation** for Pod configuration and YAML fields.                                                          | `kubectl explain pod`                         |

### 🔥 Daily Troubleshooting Flow

```text
1. Check Pod
   kubectl get pods

2. Check Node + IP
   kubectl get pods -o wide

3. Find detailed error
   kubectl describe pod <pod>

4. Check application error
   kubectl logs <pod>

5. If Pod restarted/crashed
   kubectl logs <pod> --previous

6. Check CPU/Memory
   kubectl top pod

7. Enter container
   kubectl exec -it <pod> -- /bin/bash

8. Check latest Kubernetes events
   kubectl get events --sort-by=.lastTimestamp
```

---

# Kubernetes Pods — Detailed Notes

## 1. What is a Pod?

A **Pod is the smallest deployable unit in Kubernetes**.

In simple words:

> **Pod = A wrapper around one or more containers that Kubernetes manages together.**

If Docker runs a container, Kubernetes normally runs that container **inside a Pod**.

### Desi-style meaning

Think of a **room**:

```text
Room             = Pod
Person           = Container
Building Manager = Kubernetes
```

Usually:

```text
Pod
└── Container
    └── Application
```

A Pod can also contain multiple containers:

```text
Pod
├── Main Application Container
└── Helper/Sidecar Container
```

---

## 2. Why does Kubernetes use Pods?

Kubernetes uses Pods as a unit to manage related containers together.

A Pod provides:

* Shared network
* Shared storage
* Shared lifecycle
* Shared configuration
* Container grouping
* Scheduling as a single unit

> Kubernetes schedules **Pods**, not individual containers.

---

## 3. Pod Architecture

```text
Kubernetes Cluster
        |
    Worker Node
        |
       Pod
   ┌────┴────┐
   |         |
Container  Container
    |          |
  App       Sidecar
```

A Pod can contain one or multiple containers.

---

## 4. Pod vs Container

| Container                           | Pod                                  |
| ----------------------------------- | ------------------------------------ |
| Runs the application                | Runs one or more containers          |
| Container runtime concept           | Kubernetes concept                   |
| Has its own process                 | Provides environment for containers  |
| Has container filesystem            | Can provide shared volumes           |
| Has networking                      | Containers in same Pod share network |
| Not normally scheduled individually | Kubernetes schedules Pods            |

### Easy memory

```text
Kubernetes
    ↓
   Pod
    ↓
Container
    ↓
Application
```

---

## 5. Single-Container Pod

Most Pods contain **one main container**.

Example:

```text
Pod
└── Nginx Container
```

Example use case:

```text
Pod
└── nginx
```

This is the most common type when learning Kubernetes.

---

## 6. Multi-Container Pod

A Pod can contain multiple containers when they are **tightly coupled**.

Example:

```text
Pod
├── Application Container
└── Logging Container
```

Common examples:

* Application + Log collector
* Application + Proxy
* Application + Monitoring agent
* Main application + Sidecar

### Important

Don't put unrelated applications into the same Pod just because Kubernetes allows multiple containers.

---

# 7. Pod vs Virtual Machine

A Pod is **not a Virtual Machine**.

### VM

```text
Virtual Machine
└── Operating System
    └── Applications
```

### Pod

```text
Worker Node
└── Pod
    └── Container
        └── Application
```

A Pod is much lighter than a VM.

---

# 8. Pod Lifecycle

Common Pod phases:

```text
Pending
   ↓
Running
   ↓
Succeeded / Failed
```

### Pending

Pod is accepted but not running yet.

Possible reasons:

* Image downloading
* No suitable node
* Resource shortage
* Volume issue
* Scheduling problem

### Running

Pod has been scheduled and containers are running.

### Succeeded

All containers completed successfully.

Common for Jobs/batch tasks.

### Failed

Containers terminated unsuccessfully.

---

# 9. Pod IP Address

Every Pod normally receives its own IP address.

Example:

```text
Pod 1 → 10.244.1.10
Pod 2 → 10.244.1.11
Pod 3 → 10.244.2.10
```

Pod IP is different from the Node IP.

> Pod IPs are generally temporary.

---

# 10. Containers in Same Pod Share Network

Containers inside the same Pod share the **network namespace**.

Example:

```text
Pod
├── Container A → Port 8080
└── Container B
```

Container B can communicate with Container A using:

```text
localhost:8080
```

### Important

Two containers in the same Pod normally cannot bind to the same port.

Example:

```text
Container A → 8080
Container B → 8080
```

This causes a port conflict.

---

# 11. Pod and Storage

Containers are generally **ephemeral**.

If a container is recreated, data stored only inside its container filesystem may be lost.

Pods can use **Volumes** for shared or persistent data.

```text
Pod
├── Container A
├── Container B
└── Volume
```

Containers can access the shared volume when configured.

---

# 12. Pod YAML

Basic Pod YAML:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      ports:
        - containerPort: 80
```

### Important fields

```text
apiVersion     → Kubernetes API version
kind           → Resource type
metadata       → Resource information/name
spec           → Desired configuration
containers     → Containers inside Pod
image          → Container image
containerPort  → Application port
```

> `containerPort` does **not automatically expose the application to the Internet**.

---

# 13. Creating a Pod

Save the YAML as:

```text
pod.yaml
```

Create:

```bash
kubectl apply -f pod.yaml
```

Check:

```bash
kubectl get pods
```

Example:

```text
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          20s
```

---

# 14. Understanding `kubectl get pods`

Example:

```text
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          20s
```

| Column   | Meaning                             |
| -------- | ----------------------------------- |
| NAME     | Pod name                            |
| READY    | Ready containers / total containers |
| STATUS   | Current Pod status                  |
| RESTARTS | Number of container restarts        |
| AGE      | How long Pod has existed            |

Example:

```text
2/2
```

means:

```text
2 containers ready
2 containers total
```

---

# 15. Get Detailed Pod Information

Use:

```bash
kubectl describe pod nginx-pod
```

Very important for troubleshooting.

It shows:

* Node
* Pod IP
* Containers
* Images
* Ports
* Volumes
* Events
* Scheduling information
* Restart information
* Errors

---

# 16. View Pod Logs

View logs:

```bash
kubectl logs nginx-pod
```

Follow logs:

```bash
kubectl logs -f nginx-pod
```

For multiple containers:

```bash
kubectl logs nginx-pod -c nginx-container
```

Previous container logs:

```bash
kubectl logs nginx-pod --previous
```

---

# 17. Execute Commands Inside a Pod

Enter a container:

```bash
kubectl exec -it nginx-pod -- /bin/bash
```

If Bash is unavailable:

```bash
kubectl exec -it nginx-pod -- /bin/sh
```

For multiple containers:

```bash
kubectl exec -it nginx-pod -c nginx-container -- /bin/bash
```

Example commands after entering:

```bash
ls
ps
env
```

---

# 18. Delete a Pod

Delete:

```bash
kubectl delete pod nginx-pod
```

If the Pod was created directly, Kubernetes **will not automatically recreate it**.

Example:

```text
Direct Pod
    ↓
Delete Pod
    ↓
Pod is gone
```

If managed by a Deployment, a replacement Pod can be created.

---

# 19. Pod vs Deployment

### Direct Pod

```text
Pod
└── Nginx
```

If the Pod dies:

```text
Pod ❌
```

No automatic replacement when it is a standalone Pod.

### Deployment

```text
Deployment
     ↓
ReplicaSet
     ↓
┌────┼────┐
Pod  Pod  Pod
```

If one Pod dies:

```text
Pod 1 ❌
Pod 2 ✅
Pod 3 ✅
```

The Deployment/ReplicaSet creates another Pod to maintain the desired replica count.

> **Pod = runs application**
> **Deployment = manages Pods**

---

# 20. Pod is Ephemeral

**Ephemeral = temporary.**

A Pod can be:

```text
Created
   ↓
Running
   ↓
Deleted
```

A replacement Pod may have:

* Different IP
* Different name
* Different node

Therefore:

> Don't design applications assuming a Pod will exist forever.

---

# 21. Pod IP Can Change

Example:

```text
Old Pod
IP = 10.244.1.10
```

Pod gets deleted.

New Pod:

```text
New Pod
IP = 10.244.1.25
```

The IP changed.

Therefore, don't depend directly on Pod IP for stable communication.

Use a **Service**.

```text
Client
  ↓
Service
  ↓
Pod
```

---

# 22. Pod and Service Relationship

Example:

```text
             Service
                |
       ┌────────┼────────┐
       ↓        ↓        ↓
     Pod 1    Pod 2    Pod 3
```

The Service provides a stable endpoint while Pods can be recreated.

---

# 23. Pod Scheduling

When a Pod is created, Kubernetes Scheduler selects a suitable Worker Node.

Example:

```text
Worker Node 1 → 2 CPU
Worker Node 2 → 8 CPU
Worker Node 3 → 4 CPU
```

Simplified flow:

```text
kubectl apply
      ↓
API Server
      ↓
Scheduler
      ↓
Select Worker Node
      ↓
Kubelet
      ↓
Container Runtime
      ↓
Container starts
```

---

# 24. Pod Resources

You can specify CPU and memory requirements.

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "128Mi"

  limits:
    cpu: "500m"
    memory: "256Mi"
```

### Request

Minimum resources required by the container.

```text
CPU    = 250m
Memory = 128Mi
```

### Limit

Maximum resources the container can use.

```text
CPU    = 500m
Memory = 256Mi
```

---

# 25. Pod Restart Policy

Common restart policies:

```text
Always
OnFailure
Never
```

### Always

Restart container when it terminates.

Common for long-running applications.

### OnFailure

Restart when the container exits with failure.

### Never

Don't automatically restart the container.

---

# 26. Pod Labels

Labels identify and organize Kubernetes resources.

Example:

```yaml
metadata:
  name: nginx-pod
  labels:
    app: nginx
    environment: production
```

Labels:

```text
app = nginx
environment = production
```

Check labels:

```bash
kubectl get pods --show-labels
```

Labels are commonly used by:

* Services
* Deployments
* Selectors
* Monitoring
* Filtering

---

# 27. Pod Selector

Suppose:

```text
Pod 1 → app=nginx
Pod 2 → app=nginx
Pod 3 → app=apache
```

A Service selects:

```text
app=nginx
```

Therefore:

```text
Service
   |
   ├── Pod 1
   └── Pod 2
```

Pod 3 is not selected.

---

# 28. Pod Annotations

Annotations store additional metadata.

Example:

```yaml
metadata:
  annotations:
    description: "Production nginx application"
```

### Labels vs Annotations

```text
Labels
→ Identification / Selection

Annotations
→ Additional metadata / configuration information
```

---

# 29. Pod Namespaces

Namespaces logically separate Kubernetes resources.

Examples:

```text
default
development
testing
production
```

Create/apply in a namespace:

```bash
kubectl apply -f pod.yaml -n production
```

Check:

```bash
kubectl get pods -n production
```

---

# 30. Realistic Pod YAML

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod
  labels:
    app: nginx
    environment: production

spec:
  containers:
    - name: nginx
      image: nginx:1.27

      ports:
        - containerPort: 80

      resources:
        requests:
          cpu: "250m"
          memory: "128Mi"

        limits:
          cpu: "500m"
          memory: "256Mi"

  restartPolicy: Always
```

---

# 31. Important Pod Commands

### List Pods

```bash
kubectl get pods
```

### List Pods with Node/IP information

```bash
kubectl get pods -o wide
```

### List Pods in all namespaces

```bash
kubectl get pods -A
```

### Describe Pod

```bash
kubectl describe pod <pod-name>
```

### View logs

```bash
kubectl logs <pod-name>
```

### Follow logs

```bash
kubectl logs -f <pod-name>
```

### Execute command

```bash
kubectl exec -it <pod-name> -- /bin/bash
```

### Delete Pod

```bash
kubectl delete pod <pod-name>
```

### Create/update from YAML

```bash
kubectl apply -f pod.yaml
```

### View complete YAML

```bash
kubectl get pod <pod-name> -o yaml
```

---

# 32. Troubleshooting Pods

## `Pending`

Possible reasons:

```text
Insufficient CPU
Insufficient Memory
Scheduling issue
Volume issue
Node issue
```

Check:

```bash
kubectl describe pod <pod-name>
```

---

## `ImagePullBackOff`

Kubernetes cannot pull the container image.

Possible reasons:

```text
Wrong image name
Wrong image tag
Private registry authentication issue
Registry unavailable
```

Check:

```bash
kubectl describe pod <pod-name>
```

---

## `CrashLoopBackOff`

The container repeatedly starts and crashes.

```text
Container starts
      ↓
Application crashes
      ↓
Container restarts
      ↓
Application crashes
      ↓
Container restarts
```

Check:

```bash
kubectl logs <pod-name>
```

Previous logs:

```bash
kubectl logs <pod-name> --previous
```

---

## `CreateContainerConfigError`

Usually configuration-related.

Possible causes:

```text
Missing Secret
Missing ConfigMap
Incorrect configuration
Invalid environment variable
```

Check:

```bash
kubectl describe pod <pod-name>
```

---

# 33. Pod Health Checks

Kubernetes provides three important probes.

### Liveness Probe

Checks:

> Is the application still alive?

If it fails, Kubernetes can restart the container.

### Readiness Probe

Checks:

> Is the application ready to receive traffic?

If it fails, the Pod can be removed from Service traffic.

### Startup Probe

Checks:

> Has the application successfully started?

Useful for slow-starting applications.

```text
Startup Probe
      ↓
Application starts
      ↓
Readiness Probe
      ↓
Ready for traffic
      ↓
Liveness Probe
      ↓
Keep checking health
```

---

# 34. Pod Security

Important Pod security concepts:

* Security Context
* Non-root containers
* Read-only filesystem
* Resource limits
* Network Policies
* Secrets
* RBAC
* Pod Security Standards

Example:

```yaml
securityContext:
  runAsNonRoot: true
```

This helps prevent the container from running as root.

---

# 35. Pod Communication

Example:

```text
Pod A
10.244.1.10
    |
    | Kubernetes Network
    ↓
Pod B
10.244.1.11
```

Kubernetes networking is designed so Pods can communicate with other Pods across the cluster, including across nodes, according to the cluster's networking implementation.

---

# 36. Pod Lifecycle — Real Example

Suppose you run:

```bash
kubectl apply -f app.yaml
```

Flow:

```text
YAML
 ↓
API Server
 ↓
Pod object created
 ↓
Scheduler selects Node
 ↓
Kubelet receives assignment
 ↓
Container Runtime pulls image
 ↓
Container starts
 ↓
Pod becomes Running
```

If the application crashes:

```text
Application crashes
        ↓
Container exits
        ↓
Kubelet follows restart policy
        ↓
Container restarted
```

If the Pod is managed by a Deployment:

```text
Pod disappears
      ↓
ReplicaSet detects desired count is not met
      ↓
New Pod created
```

---

# 37. Important Real-World Example

For an online shopping application:

```text
                    Internet
                       |
                       ↓
                    Service
                       |
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
      Pod 1          Pod 2          Pod 3
        |              |              |
       App            App            App
```

In production, you normally don't manually manage all three Pods.

Instead:

```text
Deployment
     ↓
ReplicaSet
     ↓
┌────┼────┐
Pod 1 Pod 2 Pod 3
```

If Pod 2 crashes:

```text
Pod 1 ✅
Pod 2 ❌
Pod 3 ✅
```

The Deployment/ReplicaSet creates a replacement:

```text
Pod 1 ✅
Pod 3 ✅
Pod 4 ✅
```

---

# 38. Pod vs Deployment — Interview Question

### Question

**Why don't we directly create Pods in production?**

### Answer

> Pods are ephemeral and don't provide application-level self-healing, rolling updates, or replica management by themselves. Deployments manage Pods through ReplicaSets and maintain the desired number of replicas.

### Easy answer

```text
Pod        → Runs application
Deployment → Manages Pods
ReplicaSet → Maintains Pod replicas
Service    → Provides stable networking
```

---

# 39. Most Important Things to Remember

| Concept             | Remember                                   |
| ------------------- | ------------------------------------------ |
| Pod                 | Smallest deployable unit in Kubernetes     |
| Container           | Runs application inside Pod                |
| Multiple containers | Used for tightly coupled workloads         |
| Same Pod            | Containers share network                   |
| `localhost`         | Can be used between containers in same Pod |
| Pod IP              | Usually temporary                          |
| Service             | Provides stable networking                 |
| Deployment          | Manages Pods                               |
| ReplicaSet          | Maintains desired number of Pods           |
| Scheduler           | Selects Worker Node                        |
| Kubelet             | Manages Pods on the Node                   |
| Volume              | Provides storage to containers             |
| Request             | Minimum resource requirement               |
| Limit               | Maximum resource usage                     |
| Label               | Identification/selection                   |
| Annotation          | Additional metadata                        |
| Liveness            | Is application alive?                      |
| Readiness           | Is application ready for traffic?          |
| Startup             | Has application started?                   |

---

# 40. Easy Memory Diagram

```text
                 KUBERNETES CLUSTER
                         |
              ┌──────────┴──────────┐
              |                     |
         Control Plane          Worker Node
                                    |
                        ┌───────────┼───────────┐
                        |           |           |
                      Pod 1       Pod 2       Pod 3
                        |           |           |
                    Container   Container   Container
                        |           |           |
                       App         App         App
```

### Production architecture

```text
                    Kubernetes
                        |
                   Deployment
                        |
                   ReplicaSet
                        |
          ┌─────────────┼─────────────┐
          ↓             ↓             ↓
        Pod 1         Pod 2         Pod 3
          |             |             |
        App           App           App
          └─────────────┼─────────────┘
                        ↓
                     Service
                        ↓
                    Users/Clients
```

---

# 41. One-Line Revision

> **Pod = Kubernetes' smallest deployable unit that provides an environment for one or more containers to run together, sharing networking and optionally storage.**

---

# 42. Kubernetes Learning Sequence

After learning **Pods**, follow this order:

```text
1.  Pods                    ← Today
2.  ReplicaSet
3.  Deployment
4.  Services
5.  Namespaces + Labels & Selectors
6.  ConfigMap + Secret
7.  Volumes
8.  PV + PVC
9.  Probes
10. DaemonSet
11. StatefulSet
12. Jobs + CronJobs
13. Ingress
14. HPA
15. RBAC
16. NetworkPolicy
17. Helm
18. AKS
```

### 🔥 Most important chain to memorize

```text
Container
    ↓
Pod
    ↓
ReplicaSet
    ↓
Deployment
    ↓
Service
    ↓
Ingress
    ↓
Users
```

For **DevOps/Azure AKS**, this chain is especially important because it forms the foundation for understanding how applications are deployed and exposed in Kubernetes.

```
```
