#  1. PROJECT OVERVIEW

## What We Built

We deployed a **real production-style web application** on Kubernetes:

### Frontend

 WordPress

### Backend Database

 MySQL

The project combines multiple Kubernetes concepts into a single working application.

---

## Kubernetes Concepts Used

✔ Namespace

✔ Deployment

✔ StatefulSet

✔ Services

✔ ConfigMap

✔ Secret

✔ Persistent Volume Claim (PVC)

✔ Liveness Probe

✔ Readiness Probe

✔ Horizontal Pod Autoscaler (HPA)

✔ Resource Requests & Limits

✔ Helm

✔ Self-Healing

✔ Persistence

---

## Real-World Problems Kubernetes Solves

In production environments:

* Applications must scale automatically
* Failed containers must recover automatically
* Databases must not lose data
* Services must communicate reliably
* Deployments must be repeatable and automated

Kubernetes provides:

✔ Scaling

✔ High Availability

✔ Self-Healing

✔ Service Discovery

✔ Persistent Storage

✔ Automation

---

#  2. COMPLETE ARCHITECTURE

## Manual YAML (Capstone)

```text
User Browser
      │
      ▼
NodePort Service (30080)
      │
      ▼
WordPress Deployment
      │
      ▼
WordPress Pods
      │
      ▼
MySQL Service
(ClusterIP / Headless)
      │
      ▼
MySQL StatefulSet
(mysql-0)
      │
      ▼
Persistent Volume Claim
(PVC)
```

---

## Helm Architecture

```text
User Browser
      │
      ▼
LoadBalancer / Service
      │
      ▼
WordPress Pod
(Helm Managed)
      │
      ▼
MariaDB StatefulSet
      │
      ▼
PVC (Auto Created)
      │
      ▼
Secrets (Auto Generated)
```

---

# 3. END-TO-END REQUEST FLOW

## What Happens When a User Opens WordPress?

```text
Browser
   │
   ▼
NodePort Service (30080)
   │
   ▼
WordPress Service
   │
   ▼
WordPress Pod
   │
   ├── Reads ConfigMap
   │
   ├── Reads Secret
   │
   ▼
MySQL Service
   │
   ▼
MySQL StatefulSet
(mysql-0)
   │
   ▼
PVC Storage
   │
   ▼
Database Data Returned
   │
   ▼
Response Sent To Browser
```

---

## Simplified Flow

```text
User
  ↓
WordPress
  ↓
MySQL
  ↓
PVC
```

---

#  4. COMPONENTS USED (IMPORTANT)

---

## 4.1 Namespace

### Purpose

Logical isolation of resources.

### Used

```bash
capstone
helm-wp
```

### Benefits

* Keeps resources separated
* Easier management
* Prevents conflicts

---

## 4.2 Pods

### Purpose

Smallest deployable Kubernetes unit.

### Used

* WordPress Pods
* MySQL Pod

### Responsibilities

* Run containers
* Execute application workload

---

## 4.3 Deployment (WordPress)

### Purpose

Manage stateless applications.

### Why WordPress Uses Deployment

Because WordPress is:

* Stateless
* Easily scalable
* Replaceable

### Benefits

✔ Replica management

✔ Rolling updates

✔ Self-healing

✔ Auto recreation

---

## 4.4 StatefulSet (MySQL)

### Purpose

Manage stateful applications.

### Why MySQL Uses StatefulSet

Because databases require:

* Stable identity
* Stable storage
* Ordered startup
* Ordered shutdown

### Example

```text
mysql-0
```

Unlike Deployment pods:

```text
wordpress-7c89f9d8-xk4rt
```

StatefulSet pod names stay consistent.

### Benefits

✔ Persistent identity

✔ Persistent storage

✔ Database-friendly

---

## 4.5 Services

Services provide stable networking.

### NodePort

Purpose:

External access.

Example:

```text
30080
```

Used For:

WordPress browser access.

---

### ClusterIP

Purpose:

Internal communication.

Used For:

WordPress → MySQL communication.

---

### Headless Service

Purpose:

DNS resolution for StatefulSets.

Example:

```text
mysql-0.mysql.capstone.svc.cluster.local
```

Benefits:

* Stable DNS
* Direct Pod communication

---

## 4.6 ConfigMap

### Purpose

Store non-sensitive configuration.

### Examples

* Database host
* Database name
* Application configuration

### Benefits

Keeps configuration separate from code.

---

## 4.7 Secret

### Purpose

Store sensitive data.

### Examples

* Database username
* Database password

### Benefits

* Secure storage
* Base64 encoded
* Separate from application code

---

## 4.8 Persistent Volume Claim (PVC)

### Purpose

Provide persistent storage.

### Why Needed

Pods are ephemeral.

Without PVC:

```text
Pod Deleted
     ↓
Data Lost
```

With PVC:

```text
Pod Deleted
     ↓
PVC Remains
     ↓
Data Preserved
```

### Benefits

✔ Persistent database storage

✔ Survives pod restarts

✔ Survives pod recreation

---

## 4.9 Horizontal Pod Autoscaler (HPA)

### Purpose

Automatically scale pods.

### Configuration Used

```text
Min Pods: 2
Max Pods: 10
CPU Target: 50%
```

### Benefits

* Handles traffic spikes
* Saves resources
* Improves availability

---

## 4.10 Probes

### Liveness Probe

Purpose:

Checks whether application is alive.

If failed:

```text
Restart Pod
```

---

### Readiness Probe

Purpose:

Checks whether application can serve traffic.

If failed:

```text
Remove Pod From Service
```

Traffic is not sent until ready.

---

## 4.11 Helm

### Purpose

Kubernetes package manager.

### Helm Automatically Creates

* Deployments
* StatefulSets
* Services
* PVCs
* Secrets
* ConfigMaps

### Benefits

✔ Faster deployment

✔ Reusable templates

✔ Easier upgrades

✔ Easier rollbacks

***

#  5. LAB EXECUTION FLOW (What Actually Happened)

This section documents the actual sequence followed during the capstone implementation.

---

## 5.1 Namespace Creation

### Command

```bash
kubectl create namespace capstone
```

### Purpose

Create an isolated environment for the project.

### Result

All project resources were deployed inside:

```text
capstone
```

Benefits:

* Resource isolation
* Easier cleanup
* Better organization

---

## 5.2 MySQL Deployment (Backend)

### Components Created

✔ Secret

✔ Headless Service

✔ StatefulSet

✔ PVC

---

### StatefulSet Created

```text
mysql-0
```

The database was deployed as a StatefulSet because it requires:

* Stable hostname
* Stable storage
* Ordered startup

---

### DNS Generated

```text
mysql-0.mysql.capstone.svc.cluster.local
```

WordPress uses this DNS to connect to MySQL.

---

### PVC Attached

Storage remained attached even after pod recreation.

---

## 5.3 WordPress Deployment (Frontend)

### Components Created

✔ Deployment

✔ ConfigMap

✔ Secret

✔ NodePort Service

✔ Probes

---

### Replica Count

```text
2 Pods
```

Purpose:

* High availability
* Better fault tolerance

---

### ConfigMap Used For

```text
Database Host
Database Name
```

---

### Secret Used For

```text
Database Username
Database Password
```

---

## 5.4 Service Exposure

WordPress was exposed externally.

### NodePort

```text
30080
```

Expected Access:

```text
http://<EC2-PUBLIC-IP>:30080
```

---

#  6. WORDPRESS INSTALLATION LIFECYCLE

One important learning:

Kubernetes deploys infrastructure.

WordPress still requires application-level setup.

---

## Initial Setup Screen

After opening WordPress:

```text
/wp-admin/install.php
```

We manually entered:

* Site Title
* Username
* Password
* Email Address

---

## What Happened Internally

```text
WordPress
      ↓
Connects To MySQL
      ↓
Creates Tables
      ↓
Stores Initial Configuration
      ↓
Site Becomes Ready
```

---

## Key Learning

Kubernetes deploys:

* Pods
* Services
* Storage

But application initialization may still be required.

---

#  7. SELF-HEALING VALIDATION

One of the most important Kubernetes features tested.

---

## 7.1 WordPress Self-Healing Test

### Command

```bash
kubectl delete pod wordpress-xxx
```

---

### Expected Result

Deployment detects:

```text
Desired Pods = 2
Running Pods = 1
```

---

### Action Taken

ReplicaSet automatically creates a replacement pod.

---

### Outcome

✔ New pod created

✔ Application remained available

✔ Desired state maintained

---

## Internal Flow

```text
Pod Deleted
      ↓
Deployment Detects Drift
      ↓
ReplicaSet Creates New Pod
      ↓
Application Restored
```

---

## 7.2 MySQL Self-Healing Test

### Command

```bash
kubectl delete pod mysql-0
```

---

### Expected Result

StatefulSet recreates:

```text
mysql-0
```

with same identity.

---

### Outcome

✔ mysql-0 recreated

✔ Same PVC attached

✔ Data preserved

---

## Internal Flow

```text
mysql-0 Deleted
        ↓
StatefulSet Detects Missing Pod
        ↓
mysql-0 Recreated
        ↓
PVC Reattached
        ↓
Database Restored
```

---

#  8. PERSISTENCE VALIDATION

Persistence is the reason databases use StatefulSets + PVCs.

---

## Experiment Performed

### Step 1

Create content inside WordPress.

Example:

```text
Blog Post
```

---

### Step 2

Delete MySQL pod.

```bash
kubectl delete pod mysql-0
```

---

### Step 3

Wait for recreation.

StatefulSet recreates:

```text
mysql-0
```

---

### Step 4

Open WordPress again.

---

## Result

✔ Blog post still existed

✔ Database unchanged

✔ No data loss

---

## Why Data Survived

Because data was stored in:

```text
PVC
```

not inside the pod filesystem.

---

## Without PVC

```text
Pod Deleted
      ↓
Storage Deleted
      ↓
Database Lost
```

---

## With PVC

```text
Pod Deleted
      ↓
PVC Remains
      ↓
Pod Recreated
      ↓
PVC Reattached
      ↓
Data Preserved
```

---

# KEY LEARNING FROM SELF-HEALING + PERSISTENCE

## Deployment Guarantees

```text
Application Availability
```

by recreating missing pods.

---

## StatefulSet Guarantees

```text
Stable Identity
Stable Storage
```

for databases.

---

## PVC Guarantees

```text
Data Durability
```

across pod restarts and recreation.

---



### How did you verify self-healing?

> I manually deleted both WordPress and MySQL pods. Kubernetes automatically recreated them using Deployment and StatefulSet controllers. The WordPress application remained functional and MySQL data remained intact because the PVC was reattached after pod recreation.

***

#  9. HPA (Horizontal Pod Autoscaler)

HPA automatically adjusts the number of pods based on resource utilization.

---

## Purpose

Instead of manually scaling:

```bash
kubectl scale deployment wordpress --replicas=5
```

Kubernetes can automatically scale workloads.

---

## Configuration Used

```text
Minimum Pods : 2
Maximum Pods : 10
Target CPU   : 50%
```

---

## How HPA Works

```text
CPU Usage Increases
         ↓
HPA Detects Load
         ↓
Additional Pods Created
         ↓
Traffic Distributed
```

---

## When Traffic Drops

```text
CPU Usage Drops
        ↓
HPA Detects Lower Load
        ↓
Extra Pods Removed
        ↓
Resources Saved
```

---

## Benefits

✔ Better performance

✔ Better availability

✔ Cost optimization

✔ Automatic scaling

---

# 10. HPA ISSUE FACED IN LAB

One of the most important troubleshooting learnings.

---

## Command Executed

```bash
kubectl top nodes
```

---

## Error Received

```text
Metrics API not available
```

---

## Meaning

Kubernetes was unable to collect CPU metrics.

HPA requires metrics to make scaling decisions.

Without metrics:

```text
HPA Exists
      ↓
But Cannot Scale Correctly
```

---

## Root Cause

Metrics Server was not installed.

---

## Architecture Dependency

```text
Node Metrics
      ↓
Metrics Server
      ↓
Metrics API
      ↓
HPA
      ↓
Scaling Decisions
```

---

##  Question

### Why was HPA not working?

> HPA depends on the Metrics API. In our environment the metrics-server was not installed, so Kubernetes could not collect CPU metrics and HPA could not make scaling decisions.

---

# 11. REAL ISSUES FACED DURING LAB

These are valuable because they reflect actual troubleshooting experience.

---

# Issue 1: curl Failed

### Command

```bash
curl localhost:30080
```

---

## Result

Failed to connect.

---

## Why?

NodePort works on the Node IP.

Not necessarily on localhost.

---

## Correct Access

```text
http://<EC2-PUBLIC-IP>:30080
```

---

## Learning

NodePort exposes services on the node network interface.

---

# Issue 2: NodePort Access Confusion

Initially:

```text
Application Running
Service Running
But Browser Access Failed
```

---

## Checks Required

### Service

```bash
kubectl get svc
```

### Pods

```bash
kubectl get pods
```

### Endpoints

```bash
kubectl get endpoints
```

---

## Learning

Always verify:

```text
Service
     ↓
Endpoints
     ↓
Pods
```

---

# Issue 3: Minikube Service Error

### Command

```bash
minikube service wordpress --url
```

---

## Error

```text
Service not found
```

---

## Cause

Wrong namespace.

---

## Fix

```bash
minikube service wordpress -n capstone --url
```

---

## Learning

Namespace mistakes are very common.

---

# Issue 4: Port Forward Failure

### Command

```bash
kubectl port-forward svc/wordpress 8080:80 -n capstone
```

---

## Error

```text
bind: address already in use
```

---

## Meaning

Another process was already using port:

```text
8080
```

---

## Fix

Use another port.

Example:

```bash
kubectl port-forward svc/wordpress 9090:80 -n capstone
```

or terminate the conflicting process.

---

## Learning

Port conflicts are common during debugging.

---

# Issue 5: Metrics API Error

### Error

```text
Metrics API not available
```

---

## Root Cause

Metrics Server missing.

---

## Fix

Install metrics-server.

---

## Learning

HPA depends on Metrics API.

---

# Issue 6: Service Not Reachable

Possible reasons:

### Wrong Selector

Service labels do not match pod labels.

---

### Wrong Port

Service targetPort mismatch.

---

### Pod Not Ready

Readiness probe failing.

---

### Firewall/Security Group

AWS Security Group blocking traffic.

---

## Learning

Networking issues are often configuration issues.

---

# Issue 7: Helm LoadBalancer Pending

Observed during Helm deployment.

---

## Status

```text
EXTERNAL-IP = <pending>
```

---

## Why?

No cloud load balancer available.

Common in:

* Minikube
* Local clusters
* Basic EC2 setups

---

## Learning

LoadBalancer services require cloud-provider integration.

---

# Issue 8: MariaDB Init Delay

Observed after Helm installation.

---

## Status

```text
Init:0/1
```

---

## Meaning

Initialization containers still running.

---

## Learning

Stateful workloads often take longer to start.

---

#  12. TROUBLESHOOTING CHEAT SHEET

---

## Cluster Overview

```bash
kubectl get all -n capstone
```

---

## Detailed Resource View

```bash
kubectl get all,pvc,secret,configmap,hpa -n capstone -o wide
```

---

## Pod Investigation

```bash
kubectl describe pod <pod>
```

---

## Pod Logs

```bash
kubectl logs <pod>
```

---

## Service Verification

```bash
kubectl get svc
```

---

## Endpoint Verification

```bash
kubectl get endpoints
```

---

## PVC Verification

```bash
kubectl get pvc
```

---

## Events

```bash
kubectl get events
```

---

## HPA Verification

```bash
kubectl get hpa
```

---

## Metrics Check

```bash
kubectl top nodes
kubectl top pods
```

---

## Namespace Verification

```bash
kubectl get ns
```

---

## Port Forward

```bash
kubectl port-forward svc/wordpress 8080:80 -n capstone
```

---

## Helm Resources

```bash
helm list -n helm-wp
```

---

## Helm Status

```bash
helm status wp-helm -n helm-wp
```

---

#  TROUBLESHOOTING FLOW (MEMORIZE)

```text
Application Not Working
          ↓
Check Pods
          ↓
Check Logs
          ↓
Check Services
          ↓
Check Endpoints
          ↓
Check Events
          ↓
Check Networking
          ↓
Find Root Cause
```

---

#  BIGGEST LEARNING FROM TROUBLESHOOTING

Most Kubernetes problems are NOT Kubernetes failures.

They are usually:

* YAML mistakes
* Label mismatches
* Namespace mistakes
* Port mismatches
* Missing dependencies
* Storage configuration issues
* Networking configuration issues

***

#  13. HELM DEPLOYMENT (AUTOMATION APPROACH)

After completing the manual Kubernetes deployment, we deployed the same application using Helm.

---

## What is Helm?

Helm is the package manager for Kubernetes.

Think of Helm as:

```text
apt  → Ubuntu Packages

yum  → RHEL Packages

helm → Kubernetes Packages
```

---

## Why Helm Exists

Without Helm:

```text
Many YAML Files
        ↓
Manual Configuration
        ↓
Manual Updates
        ↓
Manual Rollbacks
```

---

With Helm:

```text
Single Command
       ↓
Complete Application
       ↓
Auto-Created Resources
```

---

## Helm Installation Command

```bash
helm install wp-helm bitnami/wordpress -n helm-wp
```

---

## What Helm Automatically Created

### WordPress Deployment

```text
WordPress Pod
```

---

### MariaDB StatefulSet

```text
mariadb-0
```

---

### Services

```text
ClusterIP
Headless Service
LoadBalancer
```

---

### Persistent Storage

```text
PVCs
```

---

### Secrets

```text
Database Passwords
Root Passwords
Application Credentials
```

---

### Other Resources

```text
ConfigMaps
ServiceAccounts
Deployments
StatefulSets
```

---

## Helm Architecture

```text
User
 ↓
Service
 ↓
WordPress Pod
 ↓
MariaDB StatefulSet
 ↓
PVC
 ↓
Storage
```

---

## Benefits of Helm

✔ Faster deployments

✔ Reusable templates

✔ Easier upgrades

✔ Easier rollbacks

✔ Standardized deployments

✔ Production friendly

---

# 🔍 14. HELM OBSERVATIONS FROM LAB

These are actual behaviors observed during deployment.

---

## Observation 1: MariaDB Init Delay

Observed:

```text
Init:0/1
```

---

### Meaning

Initialization container still running.

---

### Learning

Stateful applications often take longer to initialize.

---

## Observation 2: LoadBalancer Pending

Observed:

```text
EXTERNAL-IP = <pending>
```

---

### Reason

No cloud load balancer available.

Common in:

* Minikube
* Local Kubernetes
* Basic EC2 clusters

---

### Learning

LoadBalancer services require cloud integration.

---

## Observation 3: Auto-Created Secrets

Helm automatically generated:

```text
Database Passwords
Root Passwords
WordPress Credentials
```

---

### Learning

Helm reduces manual secret management.

---

## Observation 4: Auto-Created PVCs

Helm automatically provisioned storage.

---

### Learning

Storage management becomes simpler.

---

## Observation 5: Bitnami Warnings

Observed warnings related to:

```text
Rolling Tags
Image Versions
```

---

### Learning

Always pin production images to specific versions.

Avoid:

```text
latest
```

Use:

```text
specific versions
```

---

#  15. MANUAL YAML vs HELM

One of the most important  topics.

---

## High-Level Comparison

| Feature               | Manual YAML | Helm      |
| --------------------- | ----------- | --------- |
| Control               | High        | Medium    |
| Visibility            | High        | Medium    |
| Speed                 | Slow        | Fast      |
| Learning Value        | High        | Medium    |
| Debugging             | Easier      | Harder    |
| Production Deployment | Good        | Excellent |
| Automation            | Low         | High      |
| Reusability           | Low         | High      |

---

## Resource Comparison

| Resource     | Manual (Capstone) | Helm |
| ------------ | ----------------- | ---- |
| Pods         | 3                 | 2    |
| Deployments  | 1                 | 1    |
| StatefulSets | 1                 | 1    |
| Services     | 2                 | 3    |
| PVCs         | 1                 | 2    |
| Secrets      | 1                 | 3    |
| ConfigMaps   | 1                 | 1    |
| HPA          | 1                 | 0    |

---

## Which Gives More Control?

### Answer

 Manual YAML

Reason:

* Every resource explicitly defined
* Easier debugging
* Better understanding

---

## Which Is Faster?

### Answer

 Helm

Reason:

```text
One Command
      ↓
Entire Application
```

---

##  Answer

### Helm vs YAML?

> YAML provides full control over every Kubernetes resource, while Helm provides automation through reusable templates. YAML is better for learning and debugging, whereas Helm is better for rapid and standardized deployments.

---

#  16.  QUESTIONS & ANSWERS

---

## What is Kubernetes?

Kubernetes is a container orchestration platform used to deploy, scale, manage, and monitor containerized applications.

---

## Why Deployment for WordPress?

Because WordPress is stateless and can be scaled horizontally.

---

## Why StatefulSet for MySQL?

Because databases require:

* Stable identity
* Stable storage
* Ordered startup
* Persistent data

---

## Why PVC?

To ensure data survives pod restarts and recreation.

---

## What is NodePort?

A service type that exposes an application outside the cluster using a port on every node.

---

## What is ClusterIP?

The default Kubernetes service used for internal communication.

---

## What is a Headless Service?

A service without a cluster IP that provides direct DNS resolution to StatefulSet pods.

---

## Why ConfigMap?

To store non-sensitive configuration separately from application code.

---

## Why Secret?

To securely store sensitive information like usernames and passwords.

---

## What is HPA?

Horizontal Pod Autoscaler automatically scales pods based on resource usage.

---

## Why Did HPA Fail?

Because Metrics Server was not installed and Metrics API was unavailable.

---

## What is Self-Healing?

Kubernetes automatically recreates failed or deleted pods to maintain the desired state.

---

## What Did Helm Do?

Helm automatically created deployments, services, StatefulSets, PVCs, secrets, and configuration resources from templates.

---

#  17. 1-MINUTE REVISION SHEET

## Core Mapping

```text
WordPress  → Deployment

MySQL      → StatefulSet

NodePort   → External Access

ClusterIP  → Internal Access

Headless   → StatefulSet DNS

PVC        → Persistent Storage

ConfigMap  → Configuration

Secret     → Credentials

HPA        → Auto Scaling

Helm       → Automation
```

---

## Core Flow

```text
User
 ↓
NodePort
 ↓
WordPress
 ↓
MySQL
 ↓
PVC
```

---

## Core Behaviors

```text
Pod Deleted
      ↓
Recreated
```

```text
DB Pod Deleted
      ↓
PVC Reattached
      ↓
Data Preserved
```

---

#  FUTURE-ME MEMORY TRICK

If you forget Day 60 completely, remember:

```text
Deployment
     ↓
Stateless

StatefulSet
     ↓
Database

PVC
     ↓
Persistence

Service
     ↓
Networking

HPA
     ↓
Scaling

Helm
     ↓
Automation
```

---

#  18. "I WISH I KNEW THIS EARLIER"

---

## Kubernetes Is Not Hard

Most problems come from:

* YAML mistakes
* Wrong labels
* Wrong namespaces
* Wrong ports

Not Kubernetes itself.

---

## Namespace Mistakes Cause Huge Confusion

Always verify:

```bash
kubectl get ns
```

and

```bash
kubectl config view
```

---

## Metrics Server Is Required For HPA

Without Metrics Server:

```text
HPA Exists
     ↓
Cannot Scale Properly
```

---

## Stateful Apps Need Special Handling

Never treat databases like stateless applications.

Use:

```text
StatefulSet
     +
PVC
```

---

## Helm Saves Time But Hides Complexity

Manual YAML teaches Kubernetes.

Helm accelerates Kubernetes.

You should know both.

---

## Networking Causes Most Problems

Usually the issue is:

```text
Service
     ↓
Selector
     ↓
Endpoint
     ↓
Pod
```

not the application itself.

---

#  FINAL SUMMARY

This capstone project taught:

✔ Kubernetes Architecture

✔ Deployments

✔ StatefulSets

✔ Services

✔ ConfigMaps

✔ Secrets

✔ Persistent Storage

✔ PVCs

✔ Self-Healing

✔ HPA

✔ Troubleshooting

✔ Helm

✔ Real Production Patterns

✔ Manual vs Automated Deployments

---



> "I deployed a complete WordPress and MySQL application on Kubernetes using Deployments, StatefulSets, Services, PVCs, Secrets, ConfigMaps, HPA, and Helm. I validated self-healing and persistence by deleting pods, troubleshot networking and metrics issues, and compared manual Kubernetes deployments with Helm-based automation."


***

  

##  Project Overview

This project demonstrates deployment of a real-world application using Kubernetes.

Application Stack:

- WordPress (Frontend)
- MySQL (Backend Database)

The project covers:

- Deployments
- StatefulSets
- Services
- ConfigMaps
- Secrets
- Persistent Volumes
- HPA
- Helm
- Self-Healing
- Troubleshooting

---

##  Architecture

User Browser
↓
NodePort Service
↓
WordPress Deployment
↓
WordPress Pods
↓
MySQL Service
↓
MySQL StatefulSet
↓
PVC Storage

---

##  Kubernetes Resources Used

| Resource | Purpose |
|-----------|----------|
| Namespace | Resource Isolation |
| Deployment | WordPress |
| StatefulSet | MySQL |
| NodePort | External Access |
| ClusterIP | Internal Communication |
| Headless Service | StatefulSet DNS |
| ConfigMap | Configuration |
| Secret | Credentials |
| PVC | Persistent Storage |
| HPA | Auto Scaling |
| Helm | Automation |

---

##  Self-Healing Validation

### WordPress

kubectl delete pod wordpress-xxx

Result:

- Pod recreated automatically

### MySQL

kubectl delete pod mysql-0

Result:

- Pod recreated
- PVC reattached
- Data preserved

---

##  Persistence Validation

Created WordPress content.

Deleted MySQL pod.

Verified:

- Database remained intact
- Content remained available
- PVC preserved storage

---

##  HPA

Configuration:

- Min Pods: 2
- Max Pods: 10
- CPU Target: 50%

---

##  Helm Deployment

helm install wp-helm bitnami/wordpress -n helm-wp

Helm automatically created:

- Deployment
- StatefulSet
- Services
- Secrets
- PVCs

---

## Troubleshooting

Issues encountered:

- NodePort access issue
- Port-forward conflict
- Metrics API unavailable
- Helm LoadBalancer pending
- MariaDB init delay

---

##  Key Learnings

- StatefulSet for databases
- Deployment for stateless apps
- PVC for persistence
- Services for networking
- Helm for automation
- Debugging is a critical Kubernetes skill

---

##  Final Outcome

Successfully deployed and validated a production-style WordPress + MySQL application using Kubernetes and Helm while testing persistence, self-healing, networking, and scaling concepts.
```

---

#  20.  SPEAKING SCRIPT

---

## Opening

> I deployed a complete WordPress and MySQL application on Kubernetes using both manual YAML manifests and Helm charts to understand production deployment patterns.

---

## Architecture

> The frontend WordPress application runs as a Deployment because it is stateless. The backend MySQL database runs as a StatefulSet because it requires persistent storage and stable identity.

---

## Request Flow

> User traffic enters through a NodePort service, reaches the WordPress pods, which then connect to MySQL through an internal service. MySQL stores data in a PVC-backed volume.

---

## Kubernetes Resources Used

> I used Deployments, StatefulSets, Services, ConfigMaps, Secrets, PVCs, HPA, Liveness and Readiness Probes, and Helm.

---

## Self-Healing

> I manually deleted WordPress and MySQL pods. Kubernetes automatically recreated them using Deployment and StatefulSet controllers.

---

## Persistence

> Even after deleting the MySQL pod, all blog data remained intact because the PVC was reattached to the recreated pod.

---

## HPA

> I configured HPA with a CPU target of 50%, minimum 2 pods, and maximum 10 pods. However, scaling did not function because Metrics Server was not installed.

---

## Helm

> Helm simplified deployment by automatically generating Deployments, StatefulSets, Services, PVCs, and Secrets from templates.

---

## Manual vs Helm

> Manual YAML provides maximum control and better learning. Helm provides faster deployments and standardization.

---

## Troubleshooting

> I encountered NodePort access issues, namespace mistakes, port-forward conflicts, Metrics API errors, and Helm LoadBalancer limitations, which helped me understand real Kubernetes debugging.

---

## Closing

> This project helped me understand Kubernetes networking, storage, scaling, self-healing, persistence, and deployment automation in a production-style environment.

---

#  21. ULTRA-FAST REVISION PAGE (30-SECOND RECALL)

```text
WordPress  → Deployment

MySQL      → StatefulSet

NodePort   → External Access

ClusterIP  → Internal Access

Headless   → Stateful DNS

ConfigMap  → Configuration

Secret     → Credentials

PVC        → Persistence

HPA        → Scaling

Helm       → Automation
```

---

## Core Flow

```text
User
 ↓
NodePort
 ↓
WordPress
 ↓
MySQL
 ↓
PVC
```

---

## Golden  Line

```text
Deployment = Stateless

StatefulSet = Stateful

PVC = Persistence

Service = Networking

HPA = Scaling

Helm = Automation
```

---

#  DAY 60 FINAL TAKEAWAY

If future-you forgets everything, remember:

```text
User
 ↓
NodePort
 ↓
WordPress (Deployment)
 ↓
MySQL (StatefulSet)
 ↓
PVC

Self-Healing
+
Persistence
+
Scaling
+
Automation
=
Kubernetes Capstone
```

***



## 1. Architecture Diagram Variations

We covered:

```text
User
 ↓
NodePort
 ↓
WordPress
 ↓
MySQL
 ↓
PVC
```

But many capstone notes also contain:

### Resource Relationship Diagram

```text
Namespace
│
├── ConfigMap
├── Secret
│
├── WordPress Deployment
│      │
│      └── Pods
│
├── NodePort Service
│
├── MySQL StatefulSet
│      │
│      └── mysql-0
│
└── PVC
```

---

### Networking Architecture

```text
Browser
   │
   ▼
NodePort Service
   │
   ▼
WordPress Service
   │
   ▼
WordPress Pods
   │
   ▼
MySQL Service
   │
   ▼
mysql-0
```

---

### DNS Resolution Flow

```text
WordPress Pod
      │
      ▼
mysql-0.mysql.capstone.svc.cluster.local
      │
      ▼
Headless Service
      │
      ▼
mysql-0
```

---

#  2. StatefulSet Deep Dive

We covered the basics, but often missing:

## Ordered Startup

```text
mysql-0
   ↓
mysql-1
   ↓
mysql-2
```

---

## Ordered Shutdown

```text
mysql-2
   ↓
mysql-1
   ↓
mysql-0
```

---

## Stable Identity

```text
Deployment:
wordpress-x8as7

Restart
↓

wordpress-k29fd
```

Changes.

---

```text
StatefulSet:
mysql-0

Restart
↓

mysql-0
```

Remains same.

---

#  3. Service Type Comparison

Very common  section.

| Service      | Access         |
| ------------ | -------------- |
| ClusterIP    | Internal       |
| NodePort     | External       |
| LoadBalancer | Cloud External |
| Headless     | Direct Pod DNS |

---

## Memory Trick

```text
ClusterIP
   ↓
Inside Cluster

NodePort
   ↓
Outside Cluster

LoadBalancer
   ↓
Cloud Traffic

Headless
   ↓
Stateful DNS
```

---

#  4. ConfigMap vs Secret

Very frequently appears separately.

| Feature        | ConfigMap | Secret |
| -------------- | --------- | ------ |
| Sensitive Data | No        | Yes    |
| Passwords      | No        | Yes    |
| Configuration  | Yes       | No     |
| Base64 Encoded | No        | Yes    |

---

#  5. Deployment vs StatefulSet

We touched it, but not as a dedicated  table.

| Feature        | Deployment | StatefulSet |
| -------------- | ---------- | ----------- |
| Stateless Apps | Yes        | No          |
| Databases      | No         | Yes         |
| Stable Name    | No         | Yes         |
| Stable Storage | No         | Yes         |
| Scaling        | Easy       | Controlled  |
| Example        | WordPress  | MySQL       |

---

#  6. HPA Lifecycle

Often shown separately.

```text
CPU > 50%
      ↓
Metrics Server
      ↓
HPA
      ↓
Create Pods
```

---

```text
CPU < 50%
      ↓
HPA
      ↓
Remove Pods
```

---

#  7. Probe Comparison

You mentioned probes, but a dedicated comparison table is useful.

| Probe           | Purpose              |
| --------------- | -------------------- |
| Startup Probe   | Application Started? |
| Liveness Probe  | Application Alive?   |
| Readiness Probe | Ready For Traffic?   |

---

### Flow

```text
Container Starts
        ↓
Startup Probe
        ↓
Liveness Probe
        ↓
Readiness Probe
```

---

#  8. PVC Lifecycle Diagram

Very commonly forgotten.

```text
Application
      │
      ▼
PVC
      │
      ▼
PV
      │
      ▼
Storage
```

---

### Deletion Scenario

```text
Pod Deleted
      ↓
PVC Remains
      ↓
New Pod
      ↓
PVC Reattached
```

---

#  9. Manual YAML Deployment Lifecycle

```text
Write YAML
      ↓
kubectl apply
      ↓
API Server
      ↓
Scheduler
      ↓
Node
      ↓
Pod Running
```

---

#  10. Helm Lifecycle

```text
helm install
      ↓
Chart
      ↓
Templates
      ↓
Rendered YAML
      ↓
Kubernetes API
      ↓
Resources Created
```

---

#  11. Production Readiness Section

Very often present in capstone summaries.

### For Production

Add:

* Ingress
* TLS/HTTPS
* Monitoring
* Logging
* Backup Strategy
* Multi-Node Cluster
* CI/CD Pipeline

---

#  12. Complete Kubernetes Resource Mapping

```text
Namespace
│
├── Deployment
│
├── StatefulSet
│
├── Service
│
├── ConfigMap
│
├── Secret
│
├── PVC
│
├── HPA
│
└── Pods
```

***



# 22. DEPLOYMENT vs STATEFULSET (MASTER COMPARISON)

| Feature          | Deployment   | StatefulSet |
| ---------------- | ------------ | ----------- |
| Application Type | Stateless    | Stateful    |
| Pod Names        | Random       | Stable      |
| Storage          | Temporary    | Persistent  |
| Scaling          | Easy         | Controlled  |
| Startup Order    | No Guarantee | Ordered     |
| Shutdown Order   | No Guarantee | Ordered     |
| Identity         | Dynamic      | Fixed       |
| Example          | WordPress    | MySQL       |

---

## Visual Comparison

### Deployment

```text
wordpress-abc123

Delete
  ↓

wordpress-xyz456
```

Pod identity changes.

---

### StatefulSet

```text
mysql-0

Delete
  ↓

mysql-0
```

Identity remains the same.

---

#  Answer

> Deployments are used for stateless workloads where pod identity does not matter. StatefulSets are used for stateful workloads like databases where stable identity and persistent storage are required.

---

# 23. CONFIGMAP vs SECRET

| Feature     | ConfigMap     | Secret         |
| ----------- | ------------- | -------------- |
| Stores      | Configuration | Sensitive Data |
| Passwords   | No            | Yes            |
| API Keys    | No            | Yes            |
| DB Host     | Yes           | No             |
| DB Password | No            | Yes            |
| Encoding    | Plain Text    | Base64         |

---

## Examples

### ConfigMap

```yaml
DB_HOST=mysql
DB_NAME=wordpress
```

---

### Secret

```yaml
DB_USER=admin
DB_PASSWORD=MyPassword
```

---

## Memory Trick

```text
ConfigMap
     ↓
Configuration

Secret
     ↓
Credentials
```

---

# 24. SERVICE TYPES COMPARISON

| Service Type | Access         |
| ------------ | -------------- |
| ClusterIP    | Internal       |
| NodePort     | External       |
| LoadBalancer | Cloud External |
| Headless     | Direct Pod DNS |

---

## ClusterIP

```text
Pod
 ↓
Service
 ↓
Pod
```

Internal communication.

---

## NodePort

```text
Browser
 ↓
NodeIP:30080
 ↓
Service
 ↓
Pod
```

External access.

---

## LoadBalancer

```text
Internet
 ↓
Cloud Load Balancer
 ↓
Service
 ↓
Pods
```

Production environments.

---

## Headless

```text
WordPress
     ↓
mysql-0.mysql.default.svc.cluster.local
```

Used by StatefulSets.

---

# 25. PROBE COMPARISON

| Probe           | Purpose                |
| --------------- | ---------------------- |
| Startup Probe   | Has app started?       |
| Liveness Probe  | Is app alive?          |
| Readiness Probe | Can app serve traffic? |

---

## Lifecycle

```text
Container Starts
        ↓
Startup Probe
        ↓
Liveness Probe
        ↓
Readiness Probe
```

---

## Failure Behavior

### Startup Probe Fails

```text
Application Still Starting
```

---

### Liveness Probe Fails

```text
Restart Container
```

---

### Readiness Probe Fails

```text
Remove From Service
```

---

# 26. PVC COMPLETE LIFECYCLE

## Architecture

```text
Application
      ↓
PVC
      ↓
PV
      ↓
Physical Storage
```

---

## Persistence Flow

```text
Pod Running
     ↓
Data Written
     ↓
PVC Stores Data
     ↓
Pod Deleted
     ↓
PVC Remains
     ↓
New Pod
     ↓
PVC Attached
     ↓
Data Available
```

---

# 27. KUBERNETES SELF-HEALING INTERNALLY



```bash
kubectl delete pod
```



---

## Deployment Recovery

```text
Pod Deleted
      ↓
Deployment Notices Drift
      ↓
ReplicaSet Triggered
      ↓
New Pod Created
      ↓
Desired State Restored
```

---

## StatefulSet Recovery

```text
mysql-0 Deleted
       ↓
StatefulSet Detects Missing Pod
       ↓
mysql-0 Recreated
       ↓
PVC Reattached
       ↓
Data Preserved
```

---

# 28. HPA INTERNAL WORKFLOW

```text
CPU Usage Rises
       ↓
Kubelet Collects Metrics
       ↓
Metrics Server
       ↓
Metrics API
       ↓
HPA
       ↓
Scale Up
```

---

## Scale Down

```text
CPU Usage Drops
       ↓
Metrics API
       ↓
HPA
       ↓
Scale Down
```

---

# 29. HELM INTERNAL WORKFLOW

```text
helm install
      ↓
Download Chart
      ↓
Read values.yaml
      ↓
Render Templates
      ↓
Generate YAML
      ↓
Send To API Server
      ↓
Resources Created
```

---

## Helm Advantages

```text
Reuse
Automation
Consistency
Rollback
Versioning
```

---

# 30. PRODUCTION IMPROVEMENTS



### "What would you improve in production?"

Answer:

* Add Ingress
* Configure TLS/HTTPS
* Add Monitoring
* Add Logging
* Add Alerts
* Configure Backups
* Use Multi-Node Cluster
* Implement CI/CD
* Add Network Policies
* Add Resource Quotas

---

# 31. COMPLETE RESOURCE RELATIONSHIP DIAGRAM

```text
Namespace
│
├── ConfigMap
│
├── Secret
│
├── WordPress Deployment
│     │
│     └── Pods
│
├── NodePort Service
│
├── MySQL StatefulSet
│     │
│     └── mysql-0
│
├── PVC
│
├── HPA
│
└── Services
```

---

# 32. FINAL 10-SECOND REVISION

```text
WordPress
     ↓
Deployment

MySQL
     ↓
StatefulSet

Storage
     ↓
PVC

Networking
     ↓
Services

Configuration
     ↓
ConfigMap

Credentials
     ↓
Secret

Scaling
     ↓
HPA

Automation
     ↓
Helm

Recovery
     ↓
Self-Healing
```

***




# 📚 APPENDIX A – COMPLETE ARCHITECTURE DIAGRAMS

#  A.1 CAPSTONE ARCHITECTURE (MANUAL YAML – FULL CONTROL)

```text
┌────────────────────────────────────────────────────────────────────┐
│                        capstone namespace                          │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                  WordPress Deployment                         │ │
│  │                                                              │ │
│  │   ┌──────────────┐   ┌──────────────┐                        │ │
│  │   │ wordpress-1  │   │ wordpress-2  │ (ReplicaSet)          │ │
│  │   │ (Pod)        │   │ (Pod)        │                       │ │
│  │   └──────────────┘   └──────────────┘                       │ │
│  │            │                 │                               │ │
│  │            └───────┬─────────┘                               │ │
│  │                    │                                         │ │
│  │          ┌─────────▼─────────┐                               │ │
│  │          │ WordPress Service │                               │ │
│  │          │ NodePort: 30080   │                               │ │
│  │          └─────────┬─────────┘                               │ │
│  │                    │                                         │ │
│  │          ┌─────────▼─────────┐                               │ │
│  │          │ HPA (Autoscaler)  │                               │ │
│  │          │ min:2 max:10      │                               │ │
│  │          │ CPU:50%           │                               │ │
│  │          └───────────────────┘                               │ │
│  └──────────────────────────────────────────────────────────────┘ │
│                             │                                     │
│                             ▼                                     │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                 MySQL StatefulSet                            │ │
│  │                                                              │ │
│  │          ┌──────────────┐                                    │ │
│  │          │ mysql-0      │                                    │ │
│  │          │ (Pod)        │                                    │ │
│  │          └──────┬───────┘                                    │ │
│  │                 │                                            │ │
│  │          ┌──────▼──────┐                                     │ │
│  │          │ PVC         │                                     │ │
│  │          │ 1Gi Storage │                                     │ │
│  │          └─────────────┘                                     │ │
│  │                                                              │ │
│  │          ┌─────────────┐                                     │ │
│  │          │ Headless    │                                     │ │
│  │          │ Service     │                                     │ │
│  │          │ Port 3306   │                                     │ │
│  │          └─────────────┘                                     │ │
│  └──────────────────────────────────────────────────────────────┘ │
│                                                                    │
│  Supporting Resources:                                             │
│  • Secret                                                          │
│  • ConfigMap                                                       │
│  • PVC                                                             │
│  • Deployment                                                      │
│  • StatefulSet                                                     │
│  • NodePort                                                        │
│  • ClusterIP                                                       │
│  • HPA                                                             │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

---

#  A.2 HELM ARCHITECTURE (BITNAMI)

```text
┌────────────────────────────────────────────────────────────────────┐
│                         helm-wp namespace                         │
├────────────────────────────────────────────────────────────────────┤
│                                                                    │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                WordPress (Helm Chart)                        │ │
│  │                                                              │ │
│  │         ┌─────────────────────────────┐                      │ │
│  │         │ WordPress Pod               │                      │ │
│  │         │ Deployment (Auto)           │                      │ │
│  │         └────────────┬────────────────┘                      │ │
│  │                      │                                       │ │
│  │         ┌────────────▼─────────────┐                         │ │
│  │         │ ClusterIP/LoadBalancer  │                         │ │
│  │         │ Service                 │                         │ │
│  │         └────────────┬────────────┘                         │ │
│  └──────────────────────────────────────────────────────────────┘ │
│                            │                                      │
│                            ▼                                      │
│  ┌──────────────────────────────────────────────────────────────┐ │
│  │                   MariaDB StatefulSet                        │ │
│  │                                                              │ │
│  │          ┌──────────────┐                                    │ │
│  │          │ mariadb-0    │                                    │ │
│  │          └──────┬───────┘                                    │ │
│  │                 │                                            │ │
│  │          ┌──────▼──────┐                                     │ │
│  │          │ PVC         │                                     │ │
│  │          │ Auto Create │                                     │ │
│  │          └─────────────┘                                     │ │
│  └──────────────────────────────────────────────────────────────┘ │
│                                                                    │
│  Auto Generated Resources:                                         │
│  • Secrets                                                         │
│  • ConfigMaps                                                      │
│  • PVCs                                                            │
│  • ServiceAccounts                                                 │
│  • Deployment                                                      │
│  • StatefulSet                                                     │
│  • ClusterIP                                                       │
│  • Headless Service                                                │
│  • LoadBalancer                                                    │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

---

# 📊 APPENDIX B – RESOURCE COMPARISON

| Resource Type | Manual YAML | Helm |
| ------------- | ----------- | ---- |
| Pods          | 3           | 2    |
| Deployments   | 1           | 1    |
| StatefulSets  | 1           | 1    |
| ReplicaSets   | 1           | 1    |
| Services      | 2           | 3    |
| HPA           | 1           | 0    |
| ConfigMaps    | 1           | 2    |
| Secrets       | 1           | 3    |
| PVCs          | 1           | 2    |

---

# 🔥 APPENDIX C – KEY OBSERVATIONS

## Manual YAML

### Advantages

* Full control
* Easier debugging
* Better Kubernetes understanding
* Explicit resource creation

### Disadvantages

* More YAML
* Slower deployment
* More maintenance

---

## Helm

### Advantages

* Faster deployment
* Reusable charts
* Standardization
* Easier upgrades
* Easier rollback

### Disadvantages

* Hidden templates
* Less visibility
* Harder troubleshooting

---

#  APPENDIX D – WHICH GIVES MORE CONTROL?

## Answer

 Manual YAML

Because:

* You define every resource.
* You control scaling.
* You control networking.
* You control storage.
* You control security.

Nothing is abstracted.

---

## Helm

You control only what is exposed through:

```text
values.yaml
```

Everything else is generated from templates.

---



> The Helm deployment creates more resources automatically because it uses templates and defaults. The manual YAML deployment provides greater visibility and control because every Kubernetes object is explicitly defined. Therefore, Helm is better for automation and speed, while manual YAML is better for learning, debugging, and fine-grained control.


***

# 🧾 1. COMMANDS USED (END-TO-END FLOW)

## 🔹 Namespace & Setup

```bash
# Create a dedicated namespace for the capstone project
kubectl create namespace capstone

# Switch current kubectl context to capstone namespace
kubectl config set-context --current --namespace=capstone

# Verify namespace creation
kubectl get ns
```

---

## 🔹 Apply All YAMLs

```bash
# Create MySQL credentials Secret
kubectl apply -f mysql-secret.yaml

# Create Headless Service required by StatefulSet DNS
kubectl apply -f mysql-headless-service.yaml

# Deploy MySQL StatefulSet with persistent storage
kubectl apply -f mysql-statefulset.yaml

# Create WordPress configuration values
kubectl apply -f wordpress-configmap.yaml

# Deploy WordPress application pods
kubectl apply -f wordpress-deployment.yaml

# Expose WordPress using NodePort service
kubectl apply -f wordpress-service.yaml

# Enable Horizontal Pod Autoscaler
kubectl apply -f wordpress-hpa.yaml
```

---

## 🔹 Verify Resources

```bash
# Display all resources inside capstone namespace
kubectl get all -n capstone

# Show pod details including node placement and IPs
kubectl get pods -n capstone -o wide

# Verify services and exposed ports
kubectl get svc -n capstone

# Verify Persistent Volume Claims
kubectl get pvc -n capstone

# Verify secrets were created
kubectl get secret -n capstone

# Verify ConfigMaps were created
kubectl get configmap -n capstone

# Verify HPA status
kubectl get hpa -n capstone
```

---

## 🔹 Debug Commands

```bash
# Show complete MySQL pod information and events
kubectl describe pod mysql-0 -n capstone

# View MySQL container logs
kubectl logs mysql-0 -n capstone

# Show WordPress pod information
kubectl describe pod <wordpress-pod> -n capstone

# View WordPress logs
kubectl logs <wordpress-pod> -n capstone

# Verify service-to-pod endpoint mapping
kubectl get endpoints wordpress -n capstone
```

---

## 🔹 Access Application

```bash
# Verify NodePort service details
kubectl get svc -n capstone

# Open application using Minikube service URL
minikube service wordpress -n capstone
```

OR

```bash
# Forward local port 8080 to WordPress service port 80
kubectl port-forward svc/wordpress 8080:80 -n capstone --address 0.0.0.0
```

---

## 🔹 Self-Healing Test

```bash
# Delete MySQL pod to test StatefulSet recovery
kubectl delete pod mysql-0 -n capstone

# Delete WordPress pod to test Deployment recovery
kubectl delete pod <wordpress-pod> -n capstone

# Watch Kubernetes recreate missing pods
kubectl get pods -n capstone -w
```

---

## 🔹 Helm Commands (Comparison)

```bash
# Add Bitnami Helm repository
helm repo add bitnami https://charts.bitnami.com/bitnami

# Update local Helm repository cache
helm repo update

# Deploy WordPress Helm chart into helm-wp namespace
helm install wp-helm bitnami/wordpress -n helm-wp --create-namespace

# Verify Helm-created resources
kubectl get all -n helm-wp

# Remove Helm release
helm uninstall wp-helm -n helm-wp

# Delete Helm namespace
kubectl delete namespace helm-wp
```

---

#  mysql-secret.yaml (Line-by-Line)

```yaml
# Kubernetes API version for Secret resource
apiVersion: v1

# Resource type
kind: Secret

# Resource metadata
metadata:

  # Secret name
  name: mysql-secret

  # Namespace where Secret will be created
  namespace: capstone

# Generic secret type
type: Opaque

# Store values as plain text and Kubernetes converts to base64
stringData:

  # MySQL root password
  MYSQL_ROOT_PASSWORD: rootpass

  # Database name to create
  MYSQL_DATABASE: wordpress

  # Application database username
  MYSQL_USER: wordpress

  # Application database password
  MYSQL_PASSWORD: wordpress123
```

---

#  mysql-headless-service.yaml (Line-by-Line)

```yaml
# Kubernetes core API version
apiVersion: v1

# Create a Service resource
kind: Service

# Metadata section
metadata:

  # Service name
  name: mysql

  # Namespace
  namespace: capstone

# Service specification
spec:

  # Headless Service (no ClusterIP assigned)
  clusterIP: None

  # Match pods having label app=mysql
  selector:
    app: mysql

  # Expose MySQL port
  ports:

    # Service port
    - port: 3306

      # Target container port
      targetPort: 3306
```

---

#  mysql-statefulset.yaml (Most Important)

```yaml
# Apps API group
apiVersion: apps/v1

# Stateful application controller
kind: StatefulSet

# Metadata
metadata:

  # StatefulSet name
  name: mysql

  # Namespace
  namespace: capstone

# StatefulSet configuration
spec:

  # Associated Headless Service
  serviceName: mysql

  # Number of MySQL replicas
  replicas: 1

  # Label selector
  selector:
    matchLabels:
      app: mysql

  # Pod template
  template:

    metadata:
      labels:
        app: mysql

    spec:

      containers:

      # MySQL container
      - name: mysql

        # MySQL image version
        image: mysql:8.0

        # Expose database port
        ports:
        - containerPort: 3306

        # Load environment variables from Secret
        envFrom:
        - secretRef:
            name: mysql-secret

        # Mount persistent storage
        volumeMounts:
        - name: mysql-data

          # MySQL data directory
          mountPath: /var/lib/mysql

  # Create PVC automatically
  volumeClaimTemplates:

  - metadata:

      # PVC name
      name: mysql-data

    spec:

      # Single node write access
      accessModes: ["ReadWriteOnce"]

      resources:
        requests:

          # Requested storage size
          storage: 1Gi
```

***


#  wordpress-configmap.yaml (Line-by-Line)

```yaml
# Kubernetes core API version
apiVersion: v1

# Resource type
kind: ConfigMap

# Metadata section
metadata:

  # ConfigMap name
  name: wordpress-config

  # Namespace where ConfigMap will be created
  namespace: capstone

# Key-value configuration data
data:

  # DNS name of MySQL StatefulSet pod
  WORDPRESS_DB_HOST: mysql-0.mysql.capstone.svc.cluster.local

  # Database name WordPress should connect to
  WORDPRESS_DB_NAME: wordpress
```

---

#  wordpress-deployment.yaml (Line-by-Line)

```yaml
# Apps API group
apiVersion: apps/v1

# Deployment controller
kind: Deployment

# Metadata section
metadata:

  # Deployment name
  name: wordpress

  # Namespace
  namespace: capstone

# Deployment specification
spec:

  # Number of WordPress replicas
  replicas: 2

  # Deployment manages pods with this label
  selector:
    matchLabels:
      app: wordpress

  # Pod template
  template:

    metadata:

      # Label assigned to pods
      labels:
        app: wordpress

    spec:

      containers:

      # WordPress container
      - name: wordpress

        # WordPress image
        image: wordpress:latest

        # Expose container web port
        ports:

        # HTTP port used by WordPress
        - containerPort: 80
```

---

#  wordpress-service.yaml (Line-by-Line)

```yaml
# Kubernetes core API version
apiVersion: v1

# Resource type
kind: Service

# Metadata section
metadata:

  # Service name
  name: wordpress

  # Namespace
  namespace: capstone

# Service specification
spec:

  # Expose service outside cluster
  type: NodePort

  # Forward traffic to WordPress pods
  selector:
    app: wordpress

  # Port configuration
  ports:

    # Service port
    - port: 80

      # Container port
      targetPort: 80

      # External node port
      nodePort: 30080
```

---

#  wordpress-hpa.yaml (Line-by-Line)

```yaml
# Autoscaling API version
apiVersion: autoscaling/v2

# Horizontal Pod Autoscaler resource
kind: HorizontalPodAutoscaler

# Metadata section
metadata:

  # HPA name
  name: wordpress-hpa

  # Namespace
  namespace: capstone

# HPA configuration
spec:

  # Resource to scale
  scaleTargetRef:

    # API group of target resource
    apiVersion: apps/v1

    # Resource type
    kind: Deployment

    # Deployment name
    name: wordpress

  # Minimum number of pods
  minReplicas: 2

  # Maximum number of pods
  maxReplicas: 10

  # Scaling metric
  metrics:

  # Resource-based metric
  - type: Resource

    resource:

      # Monitor CPU usage
      name: cpu

      target:

        # Percentage-based scaling
        type: Utilization

        # Target CPU utilization percentage
        averageUtilization: 50
```

---

#  nginx-hpa.yaml (OPTIONAL EQUIVALENT)

```yaml
# Autoscaling API version
apiVersion: autoscaling/v2

# Horizontal Pod Autoscaler
kind: HorizontalPodAutoscaler

# Metadata section
metadata:

  # HPA name
  name: nginx-hpa

# HPA configuration
spec:

  # Deployment to scale
  scaleTargetRef:

    # API version
    apiVersion: apps/v1

    # Resource type
    kind: Deployment

    # Target deployment
    name: nginx

  # Minimum replicas
  minReplicas: 2

  # Maximum replicas
  maxReplicas: 5

  # Scaling metric definition
  metrics:

  - type: Resource

    resource:

      # CPU-based scaling
      name: cpu

      target:

        # Percentage utilization target
        type: Utilization

        # Desired CPU utilization
        averageUtilization: 50
```

---

#  Helm values.yaml Override (Bitnami WordPress)

```yaml
# Default WordPress admin username
wordpressUsername: admin

# Default WordPress admin password
wordpressPassword: admin123

# MariaDB configuration
mariadb:

  # Authentication settings
  auth:

    # MariaDB root password
    rootPassword: rootpass

    # Database name to create
    database: wordpress

    # Application database username
    username: wordpress

    # Application database password
    password: wordpress123

# WordPress service configuration
service:

  # Expose service using LoadBalancer
  type: LoadBalancer
```

---

#  Quick  Memory Trick

```text
mysql-secret.yaml
        ↓
Stores Credentials

mysql-headless-service.yaml
        ↓
Provides Stable DNS

mysql-statefulset.yaml
        ↓
Runs MySQL + PVC

wordpress-configmap.yaml
        ↓
Stores Configuration

wordpress-deployment.yaml
        ↓
Runs WordPress Pods

wordpress-service.yaml
        ↓
Exposes WordPress

wordpress-hpa.yaml
        ↓
Auto Scales WordPress

values.yaml
        ↓
Customizes Helm Deployment
```

***



#  What Happens Internally When You Run

```bash
kubectl delete pod nginx-pod
```

---

# Step 1: kubectl Sends Request to API Server

```text
kubectl delete pod nginx-pod
          │
          ▼
      API Server
```

* `kubectl` does not delete the pod directly.
* It sends a REST API request to the Kubernetes API Server.
* API Server updates the desired state in etcd.

---

# Step 2: Pod Marked for Deletion

```text
API Server
     │
     ▼
etcd

Pod Status:
Terminating
```

* Pod is not removed immediately.
* Kubernetes first marks it as:

```text
STATUS = Terminating
```

* Deletion timestamp is added.

---

# Step 3: Kubelet Receives Deletion Event

```text
API Server
      │
      ▼
Kubelet
      │
      ▼
Node
```

Kubelet running on that node notices:

```text
This pod must be stopped
```

---

# Step 4: Container Runtime Stops Containers

```text
Kubelet
     │
     ▼
Container Runtime
(Containerd/Docker)
     │
     ▼
Stop Container
```

Kubelet tells the container runtime:

```text
Stop the container gracefully
```

SIGTERM signal is sent.

---

# Step 5: Grace Period

Default:

```text
30 seconds
```

```yaml
terminationGracePeriodSeconds: 30
```

Flow:

```text
SIGTERM
    │
    ▼
Application cleans up
    │
    ▼
Exit gracefully
```

Examples:

* Close DB connections
* Save data
* Finish requests
* Flush logs

---

# Step 6: Force Kill (If Needed)

If application ignores SIGTERM:

```text
30 Seconds Expired
         │
         ▼
SIGKILL
```

Container is forcibly terminated.

---

# Step 7: Pod Object Removed

```text
etcd
  │
  ▼
Pod Deleted
```

Now pod disappears from:

```bash
kubectl get pods
```

---

#  What Happens Next Depends on Controller

---

# Case 1: Standalone Pod

You created:

```yaml
kind: Pod
```

Then:

```bash
kubectl delete pod nginx
```

Result:

```text
Pod Deleted
     │
     ▼
Gone Forever
```

No recreation.

---

# Case 2: Deployment Pod

Suppose:

```yaml
Deployment
Replicas = 2
```

Current:

```text
wordpress-abc
wordpress-def
```

Delete one:

```bash
kubectl delete pod wordpress-abc
```

Internal flow:

```text
Pod Deleted
      │
      ▼
ReplicaSet Notices
      │
      ▼
Desired = 2
Current = 1
      │
      ▼
Create New Pod
      │
      ▼
wordpress-xyz
```

---

## Diagram

```text
wordpress-abc
      │
      ▼
Deleted
      │
      ▼
ReplicaSet
      │
      ▼
New Pod Created
      │
      ▼
wordpress-xyz
```

This is Kubernetes Self-Healing.

---

# Case 3: StatefulSet Pod

Current:

```text
mysql-0
```

Delete:

```bash
kubectl delete pod mysql-0
```

Internal flow:

```text
mysql-0 Deleted
        │
        ▼
StatefulSet Detects Missing Pod
        │
        ▼
Recreate Same Pod
        │
        ▼
mysql-0
```

Notice:

```text
Same Name
Same Identity
Same PVC
```

---

## Diagram

```text
mysql-0
    │
    ▼
Deleted
    │
    ▼
StatefulSet
    │
    ▼
mysql-0 Recreated
    │
    ▼
PVC Reattached
```

Data remains intact.

---

# Case 4: Job Pod

```text
Job
 │
 ▼
Pod
```

Delete pod:

```bash
kubectl delete pod job-pod
```

Job sees work incomplete:

```text
Job Controller
      │
      ▼
Create New Pod
```

---

# Complete Internal Flow

```text
kubectl delete pod
          │
          ▼
API Server
          │
          ▼
etcd Updated
          │
          ▼
Pod Marked Terminating
          │
          ▼
Kubelet Receives Event
          │
          ▼
SIGTERM Sent
          │
          ▼
Graceful Shutdown
          │
          ▼
Container Stopped
          │
          ▼
Pod Removed
          │
          ▼
Controller Checks Desired State
          │
          ├── No Controller
          │       ▼
          │    Gone Forever
          │
          ├── Deployment
          │       ▼
          │    New Pod Created
          │
          ├── StatefulSet
          │       ▼
          │    Same Pod Recreated
          │
          └── Job
                  ▼
             New Pod Created
```



> When `kubectl delete pod` is executed, the API Server marks the pod for deletion in etcd. The Kubelet receives the event and gracefully stops the container using SIGTERM. After the grace period, the pod is removed. If the pod is managed by a Deployment, ReplicaSet, StatefulSet, or Job, the corresponding controller detects the missing pod and recreates it to maintain the desired state. This behavior is Kubernetes self-healing.

