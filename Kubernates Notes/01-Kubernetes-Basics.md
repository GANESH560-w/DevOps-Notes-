# Kubernetes Architecture 
                         KUBERNETES CLUSTER
                                │
              ┌─────────────────┴─────────────────┐
              │                                   │
        CONTROL PLANE                         WORKER NODES
          (Brain)                               (Work)
              │                                   │
      ┌───────┼────────┐                ┌─────────┼─────────┐
      │       │        │                │         │         │
 API Server  etcd  Scheduler       kubelet  kube-proxy  Runtime
      │                │                │                   │
      │        Controller Manager      │                   │
      │                │                │                   ↓
      │                │                │                  POD
      │                │                │                   │
      └────────────────┴────────────────┘                   ↓
                                                     Container
                                                          │
                                                          ↓
                                                     Application

## 1. What is Kubernetes Architecture?

### Short Definition — English

Kubernetes Architecture describes how the **Control Plane, Worker Nodes, Pods, networking, and other components work together** to deploy and manage containerized applications.

### Marathi-English

Simple bhasha mein:

> **Control Plane = Brain / Manager**

> **Worker Node = Actual kaam karne wali machine**

```text
                 Kubernetes Cluster
                        |
          +-------------+-------------+
          |                           |
     Control Plane               Worker Nodes
       "Brain"                     "Workers"
          |                           |
      Manages                    Runs Pods
      Cluster                   Applications
```

Your notes also describe the cluster using the Control Plane and Worker Node model.

---

# 2. Real-Life Company Example

Imagine a company has an online shopping application:

```text
www.myshop.com
```

Application has:

```text
Frontend
Backend API
Payment Service
Order Service
```

Production मध्ये हजारो users application वापरत आहेत.

Company multiple Worker Nodes वापरते:

```text
Kubernetes Cluster
│
├── Control Plane
│
├── Worker Node 1
│   ├── Frontend Pod
│   └── Backend Pod
│
├── Worker Node 2
│   ├── Frontend Pod
│   └── Backend Pod
│
└── Worker Node 3
    ├── Payment Pod
    └── Order Pod
```

### Marathi-English Understanding

Company मध्ये एक Manager असतो.

Manager सांगतो:

> "मला 3 Backend instances चालू पाहिजेत."

Employees actual काम करतात.

Kubernetes मध्ये:

```text
Control Plane
     ↓
"3 Backend Pods पाहिजेत"

Worker Nodes
     ↓
Actually run the Pods
```

---

# 3. Kubernetes Control Plane

### Short Definition — English

The Control Plane manages the overall state and operations of the Kubernetes cluster.

### Marathi-English

Control Plane म्हणजे Kubernetes चा **main brain**.

तो continuously cluster बद्दल माहिती ठेवतो:

* किती Pods पाहिजेत?
* कोणते Pods running आहेत?
* कोणत्या Node वर workload आहे?
* नवीन Pod कुठे schedule करायचा?
* काही Pod/Node problem मध्ये आहे का?

Main components:

```text
Control Plane
│
├── kube-apiserver
├── etcd
├── kube-scheduler
└── kube-controller-manager
```

---

# 4. kube-apiserver

### Short Definition — English

The API Server is the main API endpoint through which users and Kubernetes components communicate with the cluster.

### Marathi-English

API Server म्हणजे company चा **Reception Counter**.

आपण Kubernetes च्या प्रत्येक internal component ला direct command देत नाही.

Example:

```bash
kubectl get pods
```

Flow:

```text
DevOps Engineer
       ↓
     kubectl
       ↓
  API Server
       ↓
 Kubernetes Cluster
```

API Server:

* Request receive करतो
* Authentication/Authorization handle करतो
* Request validate करतो
* Kubernetes resources ची API provide करतो

### Easy Memory

> **API Server = Kubernetes चा main entry point**

Your notes correctly identify the API Server as the entry point for cluster commands and communication.

---

# 5. etcd

### Short Definition — English

`etcd` is the key-value store used to persist Kubernetes cluster state.

### Marathi-English

`etcd` म्हणजे Kubernetes चा **important database/register**.

Kubernetes ला cluster ची state store करायची असते.

For example:

```text
Deployment → backend
Replicas   → 3
Service    → backend-service
Node       → worker-node-1
```

Concept:

```text
Kubernetes
    ↓
   etcd
    ↓
Cluster State
```

### Real-Life Example

Manager कडे company चा register आहे:

```text
Team Members Required = 5
Team Members Present  = 5
```

Manager त्या information वरून situation समजतो.

Similarly:

> **etcd = Kubernetes cluster state store**

Important:

> etcd application containers run करत नाही.

Your notes identify etcd as the store for cluster state/configuration.

---

# 6. kube-scheduler

### Short Definition — English

The Scheduler selects a suitable Worker Node for Pods that need to be scheduled.

### Marathi-English

Scheduler म्हणजे **काम वाटणारा Manager**.

Suppose:

```text
Worker Node 1 → CPU 90%
Worker Node 2 → CPU 30%
Worker Node 3 → CPU 50%
```

New Pod आला.

Scheduler available nodes आणि scheduling rules पाहून suitable Node select करतो.

```text
             New Pod
                ↓
            Scheduler
                ↓
        Suitable Worker Node
```

### Important

Scheduler स्वतः Pod run करत नाही.

तो mainly decide करतो:

> **"हा Pod कुठल्या Node वर run करायचा?"**

### Real-Life Example

तीन employees आहेत:

```text
Employee A → Busy
Employee B → Free
Employee C → Medium workload
```

New काम आलं.

Manager बोलेल:

> "हे काम Employee B ला द्या."

Same concept:

```text
New Pod
   ↓
Scheduler
   ↓
Suitable Node
```

Your notes correctly describe the Scheduler as selecting a Worker Node based on resources and policies.

---

# 7. kube-controller-manager

### Short Definition — English

The Controller Manager runs controllers that continuously reconcile the desired state with the current state.

### Marathi-English

Controller Manager म्हणजे **Supervisor**.

Suppose आपण सांगितलं:

```text
मला 3 Pods पाहिजेत.
```

So:

```text
Desired State = 3
```

Everything is fine:

```text
Current State = 3
```

But one Pod crash झाला:

```text
Desired State = 3
Current State = 2
```

Controller difference detect करतो.

```text
Desired = 3
Current = 2
     ↓
Controller
     ↓
Kubernetes works to create replacement
     ↓
Current = 3
```

### Real-Life Example

Manager म्हणतो:

> "माझ्या team मध्ये 5 people पाहिजेत."

एक employee absent झाला.

```text
Required = 5
Available = 4
```

Supervisor problem notice करून replacement arrange करण्याची process सुरू करतो.

### Easy Memory

> **Controller = Desired State maintain करणारा Supervisor**

Your original notes mention that the Controller Manager watches cluster state and ensures it matches the desired state.

---

# 8. Worker Node

### Short Definition — English

A Worker Node is a machine where Kubernetes runs application workloads.

### Marathi-English

Worker Node म्हणजे **actual काम करणारी machine**.

```text
Worker Node
│
├── kubelet
├── kube-proxy
├── Container Runtime
└── Pods
```

Example:

```text
AWS EC2
Azure VM
On-Prem Server
```

हे Worker Node म्हणून वापरले जाऊ शकतात.

Simple:

> **Control Plane decides → Worker Node executes**

Your notes describe Worker Nodes as the place where applications run.

---

# 9. kubelet

### Short Definition — English

kubelet is the Kubernetes agent that runs on a Worker Node and manages Pods assigned to that node.

### Marathi-English

kubelet म्हणजे Worker Node चा **responsible Kubernetes agent**.

Suppose Scheduler ने decide केलं:

```text
Backend Pod → Worker Node 2
```

Worker Node 2 वर kubelet आहे.

kubelet ensure करतो की required Pod specification प्रमाणे running आहे.

```text
Control Plane
      ↓
API Server
      ↓
Worker Node 2
      ↓
kubelet
      ↓
Container Runtime
      ↓
Pod
```

### Easy Memory

> **kubelet = Worker Node चा Kubernetes agent**

---

# 10. Container Runtime

### Short Definition — English

The Container Runtime is responsible for running containers on a Worker Node.

Common examples:

```text
containerd
CRI-O
```

Flow:

```text
kubelet
   ↓
Container Runtime
   ↓
Container
   ↓
Application
```

### Marathi-English

Container Runtime म्हणजे **actual container चालवणारा software**.

कुबेरनेटिसला container run करण्यासाठी runtime लागतो.

Important:

> Modern Kubernetes मध्ये Docker Engine mandatory container runtime नाही.

---

# 11. kube-proxy

### Short Definition — English

kube-proxy helps implement Kubernetes Service networking and traffic forwarding on nodes.

### Marathi-English

kube-proxy म्हणजे Worker Node मधला **networking helper**.

Suppose Backend चे 3 Pods आहेत:

```text
Backend Pod 1
Backend Pod 2
Backend Pod 3
```

Service त्यांना stable access देते.

```text
             Backend Service
               /    |    \
              ↓     ↓     ↓
            Pod 1  Pod 2  Pod 3
```

kube-proxy node-level Service networking/traffic forwarding rules implement करण्यात help करतो.

### Easy Memory

> **kube-proxy = Service networking helper**

---

# 12. Pod

### Short Definition — English

A Pod is the smallest deployable unit in Kubernetes.

### Marathi-English

Pod म्हणजे Kubernetes मध्ये application workload run करण्याचा **basic unit**.

Usually:

```text
Pod
└── Application Container
```

Example:

```text
Worker Node
    ↓
   Pod
    ↓
Container
    ↓
Application
```

एक Pod मध्ये multiple closely related containers सुद्धा असू शकतात.

```text
Pod
├── Main Container
└── Sidecar Container
```

---

# 13. IMPORTANT — Actual Production Deployment Workflow

This is the workflow you should remember as a **DevOps Engineer**.

Suppose developer ने application तयार केली:

```text
MyShop Backend
```

Developer code GitHub मध्ये push करतो.

```text
Developer
    ↓
GitHub
```

CI/CD pipeline starts:

```text
GitHub
   ↓
CI/CD
   ↓
Build
   ↓
Test
   ↓
Docker Image
   ↓
Container Registry
```

Example registry:

```text
Amazon ECR
Docker Hub
Azure Container Registry
```

Then deployment:

```text
Container Registry
        ↓
Kubernetes
        ↓
Deployment
        ↓
ReplicaSet
        ↓
Pods
```

### Complete DevOps Workflow

```text
Developer
    ↓
Git Push
    ↓
GitHub
    ↓
CI/CD Pipeline
    ↓
Build & Test
    ↓
Docker Image
    ↓
Container Registry
    ↓
kubectl / Helm
    ↓
Kubernetes API Server
    ↓
Deployment
    ↓
ReplicaSet
    ↓
Scheduler
    ↓
Worker Node
    ↓
kubelet
    ↓
Container Runtime
    ↓
Pod
    ↓
Application
```

### Marathi-English

Developer code GitHub वर push करतो.

CI/CD pipeline:

> "Code build करा → test करा → Docker image तयार करा."

Then image registry मध्ये push होते.

```text
GitHub
 ↓
CI/CD
 ↓
Docker Image
 ↓
ECR / Docker Hub
```

DevOps Engineer Kubernetes मध्ये deployment update करतो.

Kubernetes मग:

```text
API Server
   ↓
Deployment
   ↓
ReplicaSet
   ↓
Scheduler
   ↓
Worker Node
   ↓
kubelet
   ↓
Runtime
   ↓
Pod
```

अशा प्रकारे नवीन application version run होते.

---

# 14. Actual User Request Workflow

Now suppose application successfully deployed आहे.

User browser मध्ये type करतो:

```text
https://www.myshop.com
```

Actual production traffic flow can look like:

```text
                         USER
                           │
                           ↓
                          DNS
                           │
                           ↓
                    Cloud Load Balancer
                           │
                           ↓
                  Ingress Controller
                           │
                           ↓
                  Kubernetes Service
                           │
                 ┌─────────┼─────────┐
                 ↓         ↓         ↓
               Pod 1     Pod 2     Pod 3
                 │         │         │
                 └─────────┼─────────┘
                           ↓
                      Application
```

### Marathi-English

User website open करतो:

```text
User
 ↓
DNS
```

DNS domain ला appropriate endpoint कडे resolve करतो.

Then:

```text
DNS
 ↓
Load Balancer
```

Load Balancer traffic Kubernetes entry point कडे पाठवतो.

जर Ingress वापरला असेल:

```text
Load Balancer
 ↓
Ingress Controller
```

Ingress decide करतो:

> "हा request कोणत्या Service कडे जायचा?"

Then:

```text
Ingress
 ↓
Backend Service
```

Service backend चे available Pods identify करते.

```text
Backend Service
   ↓
Pod 1
Pod 2
Pod 3
```

Finally request application container पर्यंत पोहोचते.

---

# 15. Very Important — Two Different Flows

हे दोन flows confuse करू नका.

## A. Management / Deployment Flow

```text
DevOps Engineer
      ↓
kubectl / Helm
      ↓
API Server
      ↓
Deployment
      ↓
ReplicaSet
      ↓
Scheduler
      ↓
Worker Node
      ↓
kubelet
      ↓
Container Runtime
      ↓
Pod
```

### Marathi-English

> हा flow **application deploy/manage करण्यासाठी** आहे.

---

## B. User Traffic Flow

```text
User
 ↓
DNS
 ↓
Load Balancer
 ↓
Ingress
 ↓
Service
 ↓
Pod
 ↓
Container
 ↓
Application
```

### Marathi-English

> हा flow **actual customer request application पर्यंत पोहोचवण्यासाठी** आहे.

### ⭐ Important

**API Server normal user HTTP request च्या middle मध्ये नसतो.**

API Server Kubernetes management/control operations साठी आहे.

---

# 16. What Happens When a Pod Crashes?

Suppose application has:

```text
3 Pods
```

Current:

```text
Pod 1 ✅
Pod 2 ❌
Pod 3 ✅
```

So:

```text
Desired = 3
Current = 2
```

Controller notices the difference.

```text
Controller
    ↓
Reconciliation
    ↓
Replacement Pod needed
    ↓
Scheduler
    ↓
Suitable Worker Node
    ↓
kubelet
    ↓
Container Runtime
    ↓
New Pod
```

Final:

```text
Pod 1 ✅
Pod 2 ✅
Pod 3 ✅
```

### Marathi-English

Manager म्हणतो:

> "मला 3 employees पाहिजेत."

एक employee गेल्यावर:

```text
Required = 3
Available = 2
```

Supervisor replacement arrange करण्याची process सुरू करतो.

Kubernetes मध्ये Controller desired state maintain करण्यासाठी काम करतो.

---

# 17. What Happens When Worker Node Fails?

Suppose:

```text
Worker Node 1 ❌
```

Node 1 वर:

```text
Pod A
Pod B
```

होते.

Kubernetes node problem detect करतो.

Controller-managed workloads साठी replacement Pods healthy nodes वर schedule होऊ शकतात, जर resources आणि scheduling rules allow करत असतील.

```text
Worker Node 1 ❌
       ↓
Pods unavailable
       ↓
Controller
       ↓
Replacement Pods
       ↓
Scheduler
       ↓
Healthy Worker Node
       ↓
New Pods
```

### Marathi-English

Simple:

> Node down झाला म्हणून application permanently down व्हायला नको; Kubernetes workload configuration आणि available capacity नुसार replacement Pods healthy Nodes वर आणू शकतो.

---

# 18. Full Production Scenario

Let's put everything together.

Suppose:

```text
Company: MyShop
Application: Online Shopping
```

Architecture:

```text
                    INTERNET
                       │
                       ↓
                     USERS
                       │
                       ↓
                      DNS
                       │
                       ↓
               Cloud Load Balancer
                       │
                       ↓
               Ingress Controller
                       │
                       ↓
               Frontend Service
                       │
              ┌────────┼────────┐
              ↓        ↓        ↓
           Pod 1     Pod 2     Pod 3
              │        │        │
              └────────┼────────┘
                       ↓
                 Frontend App
                       │
                       ↓
                Backend Service
                       │
              ┌────────┼────────┐
              ↓        ↓        ↓
           Pod 1     Pod 2     Pod 3
              │        │        │
              └────────┼────────┘
                       ↓
                   Backend
                       │
                       ↓
                    Database
```

Behind this application:

```text
                 CONTROL PLANE
                      │
       ┌──────────────┼──────────────┐
       │              │              │
  API Server         etcd       Controllers
                       │
                   Scheduler
                       │
          ┌────────────┴────────────┐
          ↓                         ↓
      Worker Node 1            Worker Node 2
          │                         │
       kubelet                   kubelet
       runtime                   runtime
          │                         │
         Pods                      Pods
```

---

# 19. Architecture Components — Quick Revision

| Component              | Short English Definition           | Marathi-English                            |
| ---------------------- | ---------------------------------- | ------------------------------------------ |
| **Control Plane**      | Manages the cluster                | Kubernetes चा Brain                        |
| **API Server**         | Main Kubernetes API endpoint       | Reception / Main Entry Point               |
| **etcd**               | Stores cluster state               | Kubernetes चा Database/Register            |
| **Scheduler**          | Selects Node for Pods              | Pod कुठे run करायचा ते ठरवतो               |
| **Controller Manager** | Reconciles desired/current state   | Supervisor / State maintain करतो           |
| **Worker Node**        | Runs application workloads         | Actual काम करणारी machine                  |
| **kubelet**            | Manages Pods on Node               | Node चा Kubernetes agent                   |
| **kube-proxy**         | Helps implement Service networking | Networking helper                          |
| **Container Runtime**  | Runs containers                    | Container चालवणारा engine/software         |
| **Pod**                | Smallest deployable unit           | Application workload चा basic unit         |
| **Service**            | Stable network endpoint for Pods   | Pods साठी stable networking                |
| **Ingress**            | Routes external HTTP/HTTPS traffic | Traffic कुठल्या Service कडे जायचा ते ठरवतो |

---

# 20. Easy Memory Trick

## Control Plane

```text
A → API Server
E → etcd
S → Scheduler
C → Controller Manager
```

**A-E-S-C**

---

## Worker Node

```text
K → kubelet
K → kube-proxy
R → Container Runtime
```

**K-K-R**

---

# 21. One-Minute Revision

```text
                 KUBERNETES
                    CLUSTER
                      │
          ┌───────────┴───────────┐
          │                       │
    CONTROL PLANE             WORKER NODE
      "BRAIN"                    "WORK"
          │                       │
     API Server                kubelet
     etcd                      kube-proxy
     Scheduler                 Runtime
     Controller                   │
          │                      Pod
          │                       │
          └──── Manages ──────────┘
                                  ↓
                              Container
                                  ↓
                              Application
```

### Management Flow

```text
DevOps
 ↓
kubectl
 ↓
API Server
 ↓
Deployment
 ↓
ReplicaSet
 ↓
Scheduler
 ↓
Worker Node
 ↓
kubelet
 ↓
Runtime
 ↓
Pod
```

### User Flow

```text
User
 ↓
DNS
 ↓
Load Balancer
 ↓
Ingress
 ↓
Service
 ↓
Pod
 ↓
Container
 ↓
Application
```

### ⭐ Final Concept

> **Control Plane decides and manages.**

> **Scheduler selects where the Pod should run.**

> **kubelet ensures the Pod runs on that Node.**

> **Container Runtime runs the container.**

> **Service provides stable access to Pods.**

> **Ingress routes external HTTP/HTTPS traffic.**

> **Controllers continuously work to keep the actual state aligned with the desired state.**

This is the **actual architecture/workflow you should understand as a DevOps Engineer**, rather than just memorizing the component names.
