# Kubernetes Workloads — ReplicaSet

# 1. What is a ReplicaSet?
A **ReplicaSet** makes sure that a specific number of identical Pods are running.

Example:
```yaml
replicas: 3
```

Kubernetes tries to keep 3 Pods running:

```text
ReplicaSet
   │
   ├── Pod 1
   ├── Pod 2
   └── Pod 3
```

If one Pod fails, the ReplicaSet creates a replacement Pod.

### Simple definition

> **ReplicaSet = Maintains the desired number of Pods.**

---

# 2. Why do we use ReplicaSet?

ReplicaSet is useful when we need:

- Multiple copies of the same application
- Automatic Pod replacement
- A fixed number of Pods
- Basic scaling

Example:

```text
Desired = 3

Pod 1 ✅
Pod 2 ❌
Pod 3 ✅

ReplicaSet creates a new Pod

Pod 1 ✅
Pod 2 ❌
Pod 3 ✅
Pod 4 ✅

Current = 3
Desired = 3
```

---

# 3. ReplicaSet YAML — Starting of the File

A ReplicaSet starts with:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: web-rs
```

### Meaning

| Field | Meaning |
|---|---|
| `apiVersion` | Kubernetes API version |
| `kind` | Type of Kubernetes object |
| `metadata` | Information about the object |
| `name` | Name of the ReplicaSet |

For ReplicaSet:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
```

---

# 4. Basic ReplicaSet YAML Structure

```yaml
apiVersion: apps/v1
kind: ReplicaSet

metadata:
  name: web-rs

spec:
  replicas: 3

  selector:
    matchLabels:
      app: web

  template:
    metadata:
      labels:
        app: web

    spec:
      containers:
        - name: web
          image: nginx:1.25
          ports:
            - containerPort: 80
```

---

# 5. ReplicaSet YAML Parts

The main structure is:

```text
ReplicaSet
│
├── metadata
│
└── spec
    │
    ├── replicas
    │
    ├── selector
    │
    └── template
        │
        ├── metadata
        │
        └── spec
            └── containers
```

---

# 6. `metadata`

`metadata` contains information about the ReplicaSet itself.

```yaml
metadata:
  name: web-rs
```

Here:

```text
ReplicaSet name = web-rs
```

The ReplicaSet name does **not** need to be the same as the Pod name.

---

# 7. `replicas`

`replicas` tells Kubernetes how many Pods should be running.

```yaml
spec:
  replicas: 3
```

Means:

```text
Desired Pods = 3
```

You can use:

```yaml
replicas: 5
```

to request 5 Pods.

---

# 8. `selector`

The selector tells the ReplicaSet which Pods it should manage.

```yaml
selector:
  matchLabels:
    app: web
```

This means:

```text
Find Pods with:

app = web
```

---

# 9. `template`

The `template` is the **blueprint for creating Pods**.

```yaml
template:
  metadata:
    labels:
      app: web

  spec:
    containers:
      - name: web
        image: nginx:1.25
```

You do **not** normally create a separate Pod YAML file for these Pods.

The Pod definition is written inside:

```yaml
spec:
  template:
```

---

# 10. Most Important Rule ⭐

The ReplicaSet selector must match the labels in the Pod template.

### Correct

```yaml
selector:
  matchLabels:
    app: web

template:
  metadata:
    labels:
      app: web
```

### Wrong

```yaml
selector:
  matchLabels:
    app: web

template:
  metadata:
    labels:
      app: frontend
```

Why?

```text
Selector → app=web
Pod      → app=frontend

They do not match.
```

Remember:

```text
selector.matchLabels
        =
template.metadata.labels
```

---

# 11. Complete Real-Life ReplicaSet Example

```yaml
apiVersion: apps/v1
kind: ReplicaSet

metadata:
  name: web-rs

spec:
  replicas: 3

  selector:
    matchLabels:
      app: web

  template:
    metadata:
      labels:
        app: web

    spec:
      containers:
        - name: web
          image: nginx:1.25
          ports:
            - containerPort: 80
```

---

# 12. Create the YAML File

Create a file:

```bash
vim replicaset.yaml
```

or:

```bash
nano replicaset.yaml
```

Paste the YAML and save it.

Check the file:

```bash
cat replicaset.yaml
```

---

# 13. Validate YAML Before Applying

Check whether Kubernetes accepts the configuration:

```bash
kubectl apply --dry-run=client -f replicaset.yaml
```

If you want a more detailed validation:

```bash
kubectl apply --dry-run=server -f replicaset.yaml
```

If the YAML is valid, Kubernetes should accept it without creating the resource when using dry-run.

---

# 14. Create ReplicaSet

```bash
kubectl apply -f replicaset.yaml
```

Expected type of output:

```text
replicaset.apps/web-rs created
```

---

# 15. Check ReplicaSet

```bash
kubectl get rs
```

Example:

```text
NAME     DESIRED   CURRENT   READY
web-rs   3         3         3
```

Meaning:

```text
DESIRED = Pods you requested
CURRENT = Pods currently created
READY   = Pods ready
```

---

# 16. Check Pods

```bash
kubectl get pods
```

Example:

```text
NAME           READY   STATUS    RESTARTS
web-rs-abc12   1/1     Running   0
web-rs-def34   1/1     Running   0
web-rs-ghi56   1/1     Running   0
```

---

# 17. Check Pods with Labels

```bash
kubectl get pods --show-labels
```

This helps verify:

```text
app=web
```

You can also select only matching Pods:

```bash
kubectl get pods -l app=web
```

This is very useful when troubleshooting selectors.

---

# 18. Describe ReplicaSet

```bash
kubectl describe rs web-rs
```

This is one of the most important troubleshooting commands.

Look at:

```text
Replicas
Selector
Pods Status
Events
```

Especially check the **Events** section at the bottom.

---

# 19. Check ReplicaSet YAML

```bash
kubectl get rs web-rs -o yaml
```

Useful when you want to see what Kubernetes actually stored.

---

# 20. Check Pod Details

First get the Pod name:

```bash
kubectl get pods
```

Then:

```bash
kubectl describe pod <pod-name>
```

Example:

```bash
kubectl describe pod web-rs-abc12
```

---

# 21. View ReplicaSet Events

```bash
kubectl get events --sort-by=.lastTimestamp
```

This is useful when a Pod is not being created or is failing.

---

# 22. Scale ReplicaSet

Current:

```yaml
replicas: 3
```

Scale to 5 using the command:

```bash
kubectl scale rs web-rs --replicas=5
```

Check:

```bash
kubectl get rs
kubectl get pods
```

---

# 23. Scale Using YAML

Change:

```yaml
replicas: 3
```

to:

```yaml
replicas: 5
```

Then:

```bash
kubectl apply -f replicaset.yaml
```

---

# 24. Delete ReplicaSet

```bash
kubectl delete rs web-rs
```

Or using the YAML file:

```bash
kubectl delete -f replicaset.yaml
```

---

# 25. Important Command Cheat Sheet

| Task | Command |
|---|---|
| Create | `kubectl apply -f replicaset.yaml` |
| Validate | `kubectl apply --dry-run=client -f replicaset.yaml` |
| List ReplicaSets | `kubectl get rs` |
| Detailed ReplicaSet | `kubectl describe rs web-rs` |
| List Pods | `kubectl get pods` |
| Pods + labels | `kubectl get pods --show-labels` |
| Filter Pods | `kubectl get pods -l app=web` |
| Pod details | `kubectl describe pod <pod-name>` |
| ReplicaSet YAML | `kubectl get rs web-rs -o yaml` |
| Events | `kubectl get events --sort-by=.lastTimestamp` |
| Scale | `kubectl scale rs web-rs --replicas=5` |
| Delete | `kubectl delete rs web-rs` |

---

# 26. Troubleshooting

## Problem 1: ReplicaSet has 0 Pods

Check:

```bash
kubectl get rs
```

Then:

```bash
kubectl describe rs web-rs
```

Check the **Events** section.

Also check:

```bash
kubectl get pods
```

### Common causes

- YAML indentation error
- Wrong `apiVersion`
- Wrong `kind`
- Invalid container image
- Selector and labels don't match
- Invalid Pod specification

---

# 27. Problem 2: Selector and Labels Don't Match

Wrong:

```yaml
selector:
  matchLabels:
    app: web

template:
  metadata:
    labels:
      app: frontend
```

Fix:

```yaml
selector:
  matchLabels:
    app: web

template:
  metadata:
    labels:
      app: web
```

Remember:

```text
selector = template labels
```

---

# 28. Problem 3: Pod is Pending

Check:

```bash
kubectl get pods
```

Then:

```bash
kubectl describe pod <pod-name>
```

Look at:

```text
Events
```

Common causes include:

- Scheduling problems
- Resource constraints
- Node problems
- Image-related issues

---

# 29. Problem 4: Pod is ImagePullBackOff

Check:

```bash
kubectl get pods
```

Then:

```bash
kubectl describe pod <pod-name>
```

Look at the Events.

Example of a bad image:

```yaml
image: nginx:this-version-does-not-exist
```

Kubernetes cannot pull that image, so the Pod may enter:

```text
ImagePullBackOff
```

Fix the image:

```yaml
image: nginx:1.25
```

Then:

```bash
kubectl apply -f replicaset.yaml
```

---

# 30. Problem 5: Pod is CrashLoopBackOff

Check:

```bash
kubectl get pods
```

Then:

```bash
kubectl describe pod <pod-name>
```

For container logs:

```bash
kubectl logs <pod-name>
```

If there are multiple containers:

```bash
kubectl logs <pod-name> -c <container-name>
```

`CrashLoopBackOff` generally means the container starts, crashes, and Kubernetes keeps trying to restart it.

---

# 31. Problem 6: ReplicaSet Shows 3 Desired but Only 2 Ready

Check:

```bash
kubectl get rs
kubectl get pods
```

Then inspect the unhealthy Pod:

```bash
kubectl describe pod <pod-name>
```

Also check:

```bash
kubectl get events --sort-by=.lastTimestamp
```

Remember:

```text
DESIRED = 3
CURRENT = 3
READY   = 2
```

This means 3 Pods exist, but only 2 are ready.

---

# 32. Problem 7: Wrong Image Version

Suppose your ReplicaSet uses:

```yaml
image: nginx:1.25
```

But the required version is:

```yaml
image: nginx:1.27
```

Update the YAML:

```yaml
image: nginx:1.27
```

Then apply:

```bash
kubectl apply -f replicaset.yaml
```

> In production, Deployment is normally preferred for controlled application version updates and rollouts.

---

# 33. Problem 8: A Pod Was Deleted

Suppose:

```bash
kubectl get pods
```

shows 3 Pods.

Delete one:

```bash
kubectl delete pod <pod-name>
```

Then immediately check:

```bash
kubectl get pods
```

The ReplicaSet should create a replacement because it is trying to maintain the desired replica count.

---

# 34. Troubleshooting Flow

When something is wrong, follow this order:

```text
1. Check ReplicaSet
       ↓
2. Check Pods
       ↓
3. Describe ReplicaSet
       ↓
4. Describe failing Pod
       ↓
5. Check Events
       ↓
6. Check Logs if container is crashing
       ↓
7. Check selector + labels
       ↓
8. Fix YAML
       ↓
9. Apply again
       ↓
10. Verify
```

Useful commands:

```bash
kubectl get rs
kubectl get pods
kubectl describe rs <rs-name>
kubectl describe pod <pod-name>
kubectl get events --sort-by=.lastTimestamp
kubectl logs <pod-name>
```

---

# 35. Practical Troubleshooting Tasks

## Task 1 — Pod Replacement

Create a ReplicaSet with:

```text
Name: web-rs
Replicas: 3
Image: nginx:1.25
Label: app=web
```

Verify:

```bash
kubectl get rs
kubectl get pods
```

Delete one Pod:

```bash
kubectl delete pod <pod-name>
```

Check again:

```bash
kubectl get pods
```

### Goal

Understand how ReplicaSet automatically replaces a deleted Pod.

---

## Task 2 — Scaling

Start with:

```text
replicas: 2
```

Scale to:

```text
5 Pods
```

Use:

```bash
kubectl scale rs web-rs --replicas=5
```

Verify:

```bash
kubectl get rs
kubectl get pods
```

---

## Task 3 — Selector Troubleshooting

Create this mistake:

```yaml
selector:
  matchLabels:
    app: web

template:
  metadata:
    labels:
      app: frontend
```

Try applying it.

Understand why Kubernetes rejects or cannot correctly manage the configuration.

Then fix:

```yaml
selector:
  matchLabels:
    app: web

template:
  metadata:
    labels:
      app: web
```

---

## Task 4 — Image Troubleshooting

Use:

```yaml
image: nginx:invalid-version
```

Apply the ReplicaSet.

Check:

```bash
kubectl get pods
```

Then:

```bash
kubectl describe pod <pod-name>
```

Find the image-pull error.

Fix the image:

```yaml
image: nginx:1.25
```

Apply again.

---

## Task 5 — CrashLoopBackOff Investigation

Use an application/container that exits or crashes.

Check:

```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

Identify:

- Pod status
- Container state
- Restart count
- Events
- Container logs

---

# 36. ReplicaSet vs Pod

| Feature | Pod | ReplicaSet |
|---|---|---|
| Runs container | ✅ | Through Pods |
| Multiple replicas | ❌ | ✅ |
| Maintains Pod count | ❌ | ✅ |
| Replaces failed Pods | ❌ | ✅ |
| Scaling | Limited/manual | ✅ |
| Pod definition | Directly | Inside `template` |

---

# 37. Important Concept: Don't Create Separate Pod YAML

For a standalone Pod:

```text
pod.yaml
   ↓
Pod
```

For ReplicaSet:

```text
replicaset.yaml
   ↓
ReplicaSet
   ↓
template
   ↓
Pods
```

The Pod definition is inside:

```yaml
spec:
  template:
```

You normally don't create a separate `pod.yaml` for those Pods.

---

# 38. ReplicaSet vs Deployment

```text
Deployment
    ↓
ReplicaSet
    ↓
Pods
```

A Deployment manages ReplicaSets and provides additional features such as:

- Rolling updates
- Rollbacks
- Version management
- Easier application updates

ReplicaSet mainly focuses on:

> **Maintaining the desired number of Pods.**

For most production application deployments, **Deployment is normally preferred over creating a ReplicaSet directly.**

---

# 39. Quick Revision

Remember these 5 things:

```text
1. ReplicaSet maintains Pod count.

2. replicas = desired number of Pods.

3. selector = identifies Pods to manage.

4. template = Pod blueprint.

5. selector.matchLabels must match
   template.metadata.labels.
```

### One-line flow

```text
ReplicaSet
    ↓
selector
    ↓
template
    ↓
Pods
    ↓
maintains desired count
```

---

# 40. Final Example to Practice

Try writing this without looking at the answer:

```text
ReplicaSet name: production-web
Replicas: 4
Container name: nginx
Image: nginx:1.27
Container port: 80
Label: app=production-web
```

Then run:

```bash
kubectl apply -f replicaset.yaml
kubectl get rs
kubectl get pods
kubectl get pods --show-labels
```

Delete one Pod:

```bash
kubectl delete pod <pod-name>
```

Then verify:

```bash
kubectl get pods
```

Finally troubleshoot/inspect:

```bash
kubectl describe rs production-web
kubectl describe pod <pod-name>
kubectl get events --sort-by=.lastTimestamp
```

---

# 📌 ReplicaSet Cheat Sheet

```text
API:
apps/v1

Kind:
ReplicaSet

Main sections:
metadata
spec

Inside spec:
replicas
selector
template

Inside template:
metadata
spec

Inside Pod spec:
containers
```

### Core relationship

```text
replicas
    ↓
How many?

selector
    ↓
Which Pods?

template
    ↓
How to create Pods?
```

### Definition

> **ReplicaSet ensures that the desired number of identical Pods are running.**
