# Kubernetes Basics — Session 1

## 1. What is Kubernetes?

**Kubernetes (K8s)** is an open-source **container orchestration platform**.

Simple words:

> Kubernetes ka main kaam hai **containers ko manage karna** — unko run karna, monitor karna, restart karna, scale karna, aur application ko available rakhna.

### Real-Life Example

Suppose we have an application:

```text
User
  ↓
Frontend
  ↓
Backend
  ↓
Database
```

Initially, we may have only one Docker container.

```text
User
  ↓
Docker Container
  ↓
Application
```

Now suppose application par traffic increase ho gaya and thousands of users are accessing it.

One container may not be enough, so we create multiple containers:

```text
Container 1
Container 2
Container 3
Container 4
```

Now we have to manage:

* Which container should run?
* What if one container crashes?
* What if traffic increases?
* How to create more containers?
* How to distribute traffic?
* What if one server goes down?

Managing all this manually becomes difficult.

**Kubernetes solves these container-management problems.**

---

# 2. What does K8s mean?

Kubernetes is commonly written as **K8s**.

```text
K U B E R N E T E S
↑                 ↑
K                 S
```

There are **8 letters between K and S**.

Therefore:

```text
K + 8 letters + S = K8s
```

So:

> **Kubernetes = K8s**

---

# 3. History of Kubernetes

Kubernetes was originally developed by **Google**.

Google had huge experience running containers and large-scale applications.

Before Kubernetes, Google used an internal container management system called **Borg**.

The experience gained from systems like Borg influenced the development of Kubernetes.

Basic history:

```text
Google
   ↓
Borg
   ↓
Large-scale container management experience
   ↓
Kubernetes
   ↓
Open-sourced in 2014
   ↓
Cloud Native ecosystem
```

Kubernetes later became a major open-source platform for running containerized applications.

---

# 4. Why was Kubernetes needed?

Imagine a company has:

```text
100 Applications
500 Containers
20 Servers
```

Managing these containers manually would be very difficult.

Without Kubernetes:

```text
Administrator
      ↓
Manually manage containers
      ↓
Start
Stop
Restart
Scale
Deploy
Monitor
```

This becomes time-consuming and error-prone.

With Kubernetes:

```text
              Kubernetes
                   ↓
       ┌───────────┼───────────┐
       ↓           ↓           ↓
    Deploy       Scale       Restart
    Apps         Apps        Apps
```

Kubernetes automates many of these operations.

---

# 5. Main Features of Kubernetes

## 5.1 Container Orchestration

Kubernetes manages containerized applications.

```text
Application
     ↓
Kubernetes
     ↓
Multiple Containers
```

It helps manage the lifecycle of containers.

---

## 5.2 Self-Healing

Suppose an application container crashes.

Without Kubernetes:

```text
Container crashes ❌
       ↓
Administrator needs to restart it
```

With Kubernetes:

```text
Container crashes ❌
       ↓
Kubernetes detects the problem
       ↓
New container is created
       ↓
Application becomes available again ✅
```

This capability is called **Self-Healing**.

---

## 5.3 Scaling

Suppose normally:

```text
100 Users
   ↓
2 Application Containers
```

During a sale or high-traffic event:

```text
10,000 Users
   ↓
More Containers Required
```

Kubernetes can increase the number of application instances.

```text
2 Containers
     ↓
5 Containers
     ↓
10 Containers
```

This is called **Scaling**.

---

## 5.4 Load Balancing

Suppose we have three application instances:

```text
Container 1
Container 2
Container 3
```

Kubernetes can distribute incoming traffic between them.

```text
             Users
               ↓
        Kubernetes Service
          ↙     ↓     ↘
        C1      C2      C3
```

This helps prevent one application instance from receiving all the traffic.

---

## 5.5 Rolling Updates

Suppose the current application version is:

```text
Application v1
```

Now we want to deploy:

```text
Application v2
```

Kubernetes can gradually replace old instances with new ones.

```text
v1 → v2
v1 → v2
v1 → v2
```

This helps reduce application downtime during deployment.

---

## 5.6 Rollback

Suppose we deployed version 2 but it has a problem.

Kubernetes can help us return to the previous version.

```text
v1
 ↓
v2
 ↓
Problem ❌
 ↓
Rollback
 ↓
v1 ✅
```

---

## 5.7 Desired State

This is one of the most important Kubernetes concepts.

Suppose we tell Kubernetes:

> "I want 3 application instances running."

Kubernetes maintains this desired state.

```text
Desired State = 3
Current State = 2
```

Kubernetes detects the difference:

```text
Desired = 3
Current = 2
```

Then it creates another instance:

```text
Desired = 3
Current = 3 ✅
```

So Kubernetes continuously works to keep the **actual state close to the desired state**.

---

# 6. Advantages of Kubernetes

### High Availability

Applications can continue running even when some containers or infrastructure components fail.

### Automatic Scaling

Applications can be scaled according to workload.

### Self-Healing

Failed containers can be recreated automatically.

### Automated Deployment

Kubernetes helps manage application deployments.

### Load Balancing

Traffic can be distributed across application instances.

### Resource Management

CPU and memory resources can be controlled using Kubernetes configurations.

### Rollback

A problematic deployment can be rolled back to an earlier version.

### Portability

Kubernetes can run in different environments such as:

* On-premises
* AWS
* Microsoft Azure
* Google Cloud
* Other Kubernetes-supported environments

---

# 7. Disadvantages of Kubernetes

## Complexity

Kubernetes has many concepts:

```text
Pod
Deployment
Service
ReplicaSet
Namespace
ConfigMap
Secret
PV
PVC
Ingress
StatefulSet
```

Initially, these concepts can be difficult to understand.

---

## Learning Curve

To work effectively with Kubernetes, a DevOps Engineer should understand:

```text
Linux
Docker
Networking
YAML
Containers
Cloud
Kubernetes
```

---

## Resource Consumption

Kubernetes itself requires infrastructure and resources to operate.

For very small applications, this may be unnecessary overhead.

---

## Troubleshooting Complexity

When an application is not working, the problem could be related to:

```text
Pod
Deployment
Service
Network
DNS
Node
Storage
Configuration
```

Therefore, Kubernetes troubleshooting requires good knowledge of containers, networking, Linux and cloud infrastructure.

---

## Overkill for Small Applications

If we have a very small application:

```text
1 Server
2 Containers
Low Traffic
```

Using Kubernetes may be unnecessary.

For such cases, Docker Compose or another simpler solution may be sufficient.

---

# 8. Docker vs Kubernetes

This is an important concept for a DevOps Engineer.

### Docker

Docker is mainly used for **containerization**.

```text
Docker
   ↓
Build
Package
Run
Containers
```

### Kubernetes

Kubernetes is used for **container orchestration**.

```text
Kubernetes
     ↓
Manage Containers
     ↓
Deploy
Scale
Restart
Load Balance
Update
Rollback
```

### Simple Real-Life Understanding

Think of a restaurant:

```text
Docker
   ↓
Helps you prepare individual food packets.

Kubernetes
   ↓
Manages hundreds of food packets,
workers, distribution and availability.
```

So remember:

> **Docker = Containerization**

> **Kubernetes = Container Orchestration**

---

# 9. Kubernetes in a DevOps Environment

A typical DevOps workflow can look like:

```text
Developer
    ↓
Git / GitHub
    ↓
CI/CD Pipeline
    ↓
Docker Build
    ↓
Container Image
    ↓
Container Registry
    ↓
Kubernetes
    ↓
Application Deployment
    ↓
Users
```

For example:

```text
GitHub
   ↓
GitHub Actions / Jenkins
   ↓
Docker Image
   ↓
Docker Hub / ECR
   ↓
Kubernetes Cluster
   ↓
Application
```

This is why Kubernetes is an important technology for a **DevOps Engineer**.

---

# 10. Interview Definition

If an interviewer asks:

**"What is Kubernetes?"**

You can answer:

> Kubernetes is an open-source container orchestration platform used to automate the deployment, scaling, management, and self-healing of containerized applications.

---

# 11. Quick Revision

```text
Kubernetes
     ↓
Container Orchestration
     ↓
Manage Containerized Applications
     ↓
Deploy
Scale
Self-Heal
Load Balance
Rolling Updates
Rollback
Resource Management
```

### Important Points to Remember

| Topic          | Basic Meaning                           |
| -------------- | --------------------------------------- |
| Kubernetes     | Container orchestration platform        |
| K8s            | Short form of Kubernetes                |
| History        | Developed by Google, influenced by Borg |
| Open Source    | Released publicly in 2014               |
| Self-Healing   | Automatically handles failed containers |
| Scaling        | Increase/decrease application instances |
| Load Balancing | Distribute traffic                      |
| Rolling Update | Gradually deploy a new version          |
| Rollback       | Return to previous version              |
| Desired State  | Maintain the state defined by the user  |
| Docker         | Containerization                        |
| Kubernetes     | Container orchestration                 |

---

## Next Session

**Kubernetes Architecture**

Topics:

```text
Kubernetes Cluster
       ↓
Control Plane
       ↓
API Server
Scheduler
Controller Manager
etcd
       ↓
Worker Nodes
       ↓
Kubelet
Kube Proxy
Container Runtime
       ↓
Pods
```
