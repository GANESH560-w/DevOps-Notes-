# Kubernetes Namespace – Standard Operating Procedure (SOP)

## Command Quick Reference

| Syntax | Use Case | Example |
|---|---|---|
| `kubectl get ns` | List all Namespaces in the cluster | `kubectl get ns` |
| `kubectl get namespace` | List all Namespaces using the full command | `kubectl get namespace` |
| `kubectl create ns <namespace-name>` | Create a new Namespace | `kubectl create ns nginx` |
| `kubectl create namespace <namespace-name>` | Create a new Namespace using the full command | `kubectl create namespace nginx` |
| `kubectl apply -f <file>.yaml` | Create/apply a Namespace from a YAML manifest | `kubectl apply -f namespace.yaml` |
| `kubectl get pods -n <namespace>` | List Pods inside a specific Namespace | `kubectl get pods -n nginx` |
| `kubectl run <pod-name> -n <namespace> --image=<image>` | Create a Pod inside a specific Namespace | `kubectl run nginx-pod -n nginx --image=nginx` |
| `kubectl describe pod <pod-name> -n <namespace>` | View detailed information about a Pod | `kubectl describe pod nginx-pod -n nginx` |
| `kubectl exec -it <pod-name> -n <namespace> -- /bin/bash` | Open a Bash shell inside a Pod | `kubectl exec -it nginx-pod -n nginx -- /bin/bash` |
| `kubectl exec -it <pod-name> -n <namespace> -- /bin/sh` | Open a Shell inside a Pod when Bash is unavailable | `kubectl exec -it nginx-pod -n nginx -- /bin/sh` |
| `kubectl delete ns <namespace-name>` | Delete a Namespace and its contained resources | `kubectl delete ns nginx` |
| `kubectl delete namespace <namespace-name>` | Delete a Namespace using the full command | `kubectl delete namespace nginx` |

---

## 1. Objective

This SOP explains how to create, view, manage, and use **Namespaces in Kubernetes**. It also covers creating Pods inside a Namespace, using Namespace YAML manifests, and executing commands inside Pods.

---

## 2. What is a Namespace?

A **Namespace** in Kubernetes is a logical group used to organize and separate resources within a Kubernetes cluster.

A single Kubernetes cluster can run multiple projects or applications. Namespaces help us organize and identify the resources belonging to different projects or environments.

### Key Points

- A single Kubernetes cluster can run **multiple projects/applications**.
- A separate Namespace can be created for each **project or environment**.
- Resources such as **Pods, Deployments, Services, ConfigMaps, and Secrets** can be organized inside a Namespace.
- Namespaces help us **identify, manage, and isolate resources** from different projects.
- A Namespace does **not** create a separate Kubernetes cluster. Multiple Namespaces exist inside the same cluster.

---

## 3. Namespace Architecture Example

```text
Kubernetes Cluster
│
├── Namespace: Project-A
│   ├── Pod
│   ├── Deployment
│   └── Service
│
├── Namespace: Project-B
│   ├── Pod
│   ├── Deployment
│   └── Service
│
└── Namespace: Development
    ├── Pod
    └── Service
```

---

## 4. Real-Life Example

Think of a **WhatsApp account**:

```text
WhatsApp Account
│
├── Family Group
│   ├── Member 1
│   ├── Member 2
│   └── Member 3
│
├── Friends Group
│   ├── Member 1
│   └── Member 2
│
└── Office Group
    ├── Member 1
    └── Member 2
```

### Mapping to Kubernetes

| Real Life | Kubernetes |
|---|---|
| WhatsApp Account | Kubernetes Cluster |
| WhatsApp Groups | Namespaces |
| Group Members | Kubernetes Resources |

Just as WhatsApp groups help organize different members, Kubernetes Namespaces help organize resources belonging to different projects or environments.

> **Important:** A Namespace does not create a separate Kubernetes cluster. Multiple Namespaces exist inside the same cluster.

---

## 5. Prerequisites

Before performing the activities, make sure:

1. Kubernetes cluster is running.
2. `kubectl` is installed.
3. `kubectl` is configured to communicate with the Kubernetes cluster.
4. The user has sufficient permissions to create and manage Namespaces.

### Check Kubernetes Connection

```bash
kubectl cluster-info
```

### Check Current Context

```bash
kubectl config current-context
```

---

# Part 1 – Namespace Management

## 6. List Namespaces

To view all Namespaces in the current Kubernetes cluster:

```bash
kubectl get ns
```

or:

```bash
kubectl get namespace
```

Both commands perform the same operation.

### Example

```bash
kubectl get ns
```

Example output:

```text
NAME              STATUS   AGE
default           Active   10d
kube-node-lease   Active   10d
kube-public       Active   10d
kube-system       Active   10d
nginx             Active   5m
```

### Linux Comparison

It can be remembered similarly to the Linux `ls` command:

```text
Linux:
ls
→ Lists files/directories

Kubernetes:
kubectl get ns
→ Lists Namespaces
```

---

## 7. Create a Namespace Using kubectl

### Syntax

```bash
kubectl create namespace <namespace-name>
```

Short form:

```bash
kubectl create ns <namespace-name>
```

Here, `ns` is the short form of `namespace`.

### Example

```bash
kubectl create ns nginx
```

### Verify

```bash
kubectl get ns
```

You should see the `nginx` Namespace with an `Active` status.

---

## 8. Create a Namespace Using YAML Manifest

For production and real-world Kubernetes environments, using **YAML manifest files** is generally preferred because configurations can be version-controlled, reviewed, reused, and applied consistently.

Create a file:

```text
namespace.yaml
```

Add:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: nginx
```

### YAML Field Explanation

| Field | Use |
|---|---|
| `apiVersion` | Defines the Kubernetes API version |
| `kind` | Defines the type of Kubernetes object |
| `metadata` | Contains information about the object |
| `name` | Defines the Namespace name |

### Apply the Manifest

```bash
kubectl apply -f namespace.yaml
```

### Verify

```bash
kubectl get ns
```

---

## 9. Important YAML Syntax

Kubernetes YAML field names are **case-sensitive**.

### Incorrect

```yaml
Kind: Namespace
Apiversion: v1
Metadata:
  Name: nginx
```

### Correct

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: nginx
```

Always use the correct Kubernetes field names and YAML indentation.

---

## 10. Delete a Namespace

### Syntax

```bash
kubectl delete namespace <namespace-name>
```

Short form:

```bash
kubectl delete ns <namespace-name>
```

### Example

```bash
kubectl delete ns nginx
```

### Important Warning

Deleting a Namespace can delete the resources contained within that Namespace.

Before deleting, verify the resources:

```bash
kubectl get pods -n nginx
```

---

# Part 2 – Working with Pods in a Namespace

## 11. Create a Pod Inside a Namespace

A Pod can be created inside a specific Namespace using the `-n` option.

### Syntax

```bash
kubectl run <pod-name> -n <namespace-name> --image=<image-name>
```

### Example

```bash
kubectl run nginx-pod -n nginx --image=nginx
```

### Verify the Pod

```bash
kubectl get pods -n nginx
```

Example output:

```text
NAME         READY   STATUS    RESTARTS   AGE
nginx-pod    1/1     Running   0          30s
```

---

## 12. Why `-n` Is Used

The `-n` option specifies the **Namespace** where the Kubernetes resource exists.

Example:

```bash
kubectl get pods -n nginx
```

This means:

```text
Get Pods
   │
   └── From the nginx Namespace
```

Without `-n`, `kubectl` normally works with the **current/default Namespace**.

---

## 13. Check Pods in a Specific Namespace

### List Pods

```bash
kubectl get pods -n nginx
```

### Get Detailed Information

```bash
kubectl get pods -n nginx -o wide
```

### Describe a Pod

```bash
kubectl describe pod nginx-pod -n nginx
```

---

# Part 3 – Execute Commands Inside a Pod

## 14. Execute a Command Inside a Pod in a Namespace

You cannot directly `exec` into a Namespace.

You **exec into a Pod** and specify the Namespace using `-n`.

### Syntax

```bash
kubectl exec -it <pod-name> -n <namespace-name> -- <command>
```

### Example – Bash

```bash
kubectl exec -it nginx-pod -n nginx -- /bin/bash
```

### Example – Shell

If Bash is not available:

```bash
kubectl exec -it nginx-pod -n nginx -- /bin/sh
```

### Explanation

```text
kubectl exec -it POD_NAME -n NAMESPACE -- COMMAND
                    │       │
                    │       └── Namespace
                    └────────── Pod
```

`-n` tells Kubernetes which Namespace contains the Pod.

### Exit the Pod

```bash
exit
```

---

# 15. Complete Practical Example

### Step 1 – Create Namespace

```bash
kubectl create ns nginx
```

### Step 2 – Verify Namespace

```bash
kubectl get ns
```

### Step 3 – Create Pod Inside Namespace

```bash
kubectl run nginx-pod -n nginx --image=nginx
```

### Step 4 – Check Pod

```bash
kubectl get pods -n nginx
```

### Step 5 – Get Detailed Pod Information

```bash
kubectl describe pod nginx-pod -n nginx
```

### Step 6 – Enter the Pod

```bash
kubectl exec -it nginx-pod -n nginx -- /bin/bash
```

If Bash is unavailable:

```bash
kubectl exec -it nginx-pod -n nginx -- /bin/sh
```

### Step 7 – Exit the Pod

```bash
exit
```

### Step 8 – Delete the Namespace

```bash
kubectl delete ns nginx
```

---

# 16. Best Practice

For learning, testing, and quick troubleshooting, `kubectl` imperative commands are useful.

For production environments, prefer **Kubernetes manifest/YAML files** where practical.

Example:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: nginx
```

Apply:

```bash
kubectl apply -f namespace.yaml
```

### Benefits of YAML Manifests

- Configuration can be stored in Git.
- Changes can be reviewed.
- Configuration can be reused.
- Infrastructure becomes more consistent.
- Easier to maintain in team environments.
- Reduces manual command-entry mistakes.

---

# 17. Important Points to Remember

1. A **Namespace is a logical grouping of Kubernetes resources**.
2. Multiple Namespaces can exist inside one Kubernetes cluster.
3. `kubectl get ns` lists Namespaces.
4. `kubectl create ns <name>` creates a Namespace.
5. `kubectl delete ns <name>` deletes a Namespace.
6. `-n` specifies the Namespace.
7. Pods can be created inside a Namespace using `-n`.
8. `kubectl exec` executes commands inside a Pod, not directly inside a Namespace.
9. YAML manifests are preferred for repeatable and manageable Kubernetes configurations.
10. Deleting a Namespace can delete resources contained within it.

---

## 18. Quick Learning Flow

```text
Create Namespace
       ↓
kubectl create ns nginx
       ↓
Check Namespace
       ↓
kubectl get ns
       ↓
Create Pod inside Namespace
       ↓
kubectl run nginx-pod -n nginx --image=nginx
       ↓
Check Pod
       ↓
kubectl get pods -n nginx
       ↓
Enter Pod
       ↓
kubectl exec -it nginx-pod -n nginx -- /bin/bash
       ↓
Exit
       ↓
exit
       ↓
Delete Namespace
       ↓
kubectl delete ns nginx
```
