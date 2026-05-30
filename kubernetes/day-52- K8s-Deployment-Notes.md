
# 1. What is a Kubernetes Deployment?
  
#  Explanation

Imagine you own a restaurant.

You need:

* 3 chefs working all the time
* If one chef leaves → replace him
* Upgrade all chefs from Recipe V1 to Recipe V2
* Roll back if customers complain

Instead of managing chefs manually, you hire a manager.

```text id="lygl6e"
Manager = Deployment

Chefs = Pods
```

Manager's responsibilities:

```text id="q9bct8"
Keep enough chefs working

Replace missing chefs

Upgrade chefs safely

Rollback bad upgrades
```

This manager is Kubernetes Deployment.

---

#  Technical Definition

A Deployment is a Kubernetes controller used to manage stateless applications.

It provides:

```text id="esr7go"
Pod Creation

Scaling

Self Healing

Rolling Updates

Rollbacks

Version History
```

by managing ReplicaSets, which in turn manage Pods.

---

# Note:

> A Deployment is a Kubernetes controller that manages ReplicaSets and Pods. It enables declarative application deployment, scaling, self-healing, rolling updates, rollback, and version management for stateless workloads.

---

#  Why Deployment Exists

---

## Life Without Deployment

```text id="r99c2z"
Create Pod
     ↓

Application Running
     ↓

Pod Dies
     ↓

Application Down
     ↓

Manual Recreation
```

Problems:

```text id="ptptkp"
No Self Healing

No Scaling

No Rolling Updates

No Rollback

No Version History

High Operational Risk
```

---

## Life With Deployment

```text id="vvr7sy"
Create Deployment
        ↓

Deployment Creates Pods
        ↓

Pod Dies
        ↓

Replacement Pod Created
        ↓

Application Continues Running
```

Benefits:

```text id="9p4uhs"
Self Healing

Scaling

High Availability

Zero-Downtime Updates

Rollback Support
```

---

#  Pod vs Deployment

This is where most beginners get confused.

---

## Pod Mental Model

Kubernetes asks:

```text id="2svvli"
1. Which API?

2. What resource?

3. What is its name?

4. What should it run?
```

---

### Pod YAML

```yaml
apiVersion: v1                   # Which API should understand this object?

kind: Pod                        # What resource should Kubernetes create?

metadata:                        # Identity section

  name: nginx-pod                # Pod name

spec:                            # Desired state

  containers:                    # Containers inside Pod

  - name: nginx                  # Container name

    image: nginx:latest          # Docker image to run
```

---

### Read Pod YAML Like English

```text id="m4px4u"
Use v1 API.

Create a Pod.

Call it nginx-pod.

Run nginx container using nginx:latest image.
```

---

### Pod Ownership Tree

```text id="j76bhf"
Pod
│
├── apiVersion
├── kind
│
├── metadata
│    └── name
│
└── spec
     │
     └── containers
          │
          ├── name
          └── image
```

---

## Why Pod Is Not Enough

Problem:

```text id="3hgmgi"
Pod Dies
   ↓

Application Down
```

Production requires:

```text id="eh0m3x"
Self Healing

Scaling

Updates

Rollback
```

Therefore:

```text id="1p4j9s"
Pod
 ↓

Deployment
```

---

## Pod vs Deployment Comparison

| Feature            | Pod  | Deployment  |
| ------------------ | ---- | ----------- |
| Runs Container     | ✅    | ✅           |
| Self Healing       | ❌    | ✅           |
| Scaling            | ❌    | ✅           |
| Rolling Update     | ❌    | ✅           |
| Rollback           | ❌    | ✅           |
| Version History    | ❌    | ✅           |
| Creates ReplicaSet | ❌    | ✅           |
| Production Usage   | Rare | Very Common |

---

# Memory Formula

### Pod

```text id="0ys40m"
API
→ KIND
→ METADATA
→ CONTAINERS
```

### Deployment

```text id="a6g7m5"
API
→ KIND
→ METADATA
→ REPLICAS
→ SELECTOR
→ TEMPLATE
→ CONTAINERS
```

---

#  Separation Model

One of the most important concepts.

Many think:

```text id="9f4onl"
Deployment creates Pods.
```

Technically incomplete.

---

## Real Separation Of Responsibilities

```text id="rj99x4"
Deployment
     ↓
Manages ReplicaSets

ReplicaSet
     ↓
Maintains Pod Count

Scheduler
     ↓
Assigns Pods To Nodes

Kubelet
     ↓
Runs Containers

Container Runtime
     ↓
Executes Containers
```

---

## Who Does What?

| Component         | Responsibility       |
| ----------------- | -------------------- |
| Deployment        | Desired State        |
| ReplicaSet        | Pod Count            |
| Scheduler         | Node Selection       |
| Kubelet           | Container Management |
| Container Runtime | Run Containers       |

---

#  Relationship Thinking
---

## Relationship Map

```text id="dfzv8z"
Deployment
      ↓ creates

ReplicaSet
      ↓ creates

Pods
      ↓ contain

Containers
```

---

## Networking Relationship

```text id="r6m55g"
Service
     ↓ routes traffic

Pods

Deployment
     ↓ creates

Pods
```

---

## Complete Relationship Diagram

```text id="6vl83t"
Deployment
     │
     ▼

ReplicaSet
     │
     ▼

Pods
     │
     ▼

Containers

Service
     │
     ▼

Pods
```

---

#  Kubernetes Questions Framework

When Kubernetes reads Deployment YAML:

```text id="d2cew9"
1. Which API?

2. What resource?

3. What is its name?

4. How many Pods?

5. Which Pods belong to me?

6. What should each Pod look like?

7. Which container should run?
```

---

#  Deployment Manifest Skeleton

Think of every Deployment as:

```text id="g7kt0u"
Identity Card
      +
Deployment Rules
      +
Pod Blueprint
      +
Container Instructions
```

---

## Four Top-Level Fields

### 1. apiVersion

```yaml
apiVersion: apps/v1
```

Meaning:

```text id="3m6ew8"
Which Kubernetes API should understand this object?
```

---

### 2. kind

```yaml
kind: Deployment
```

Meaning:

```text id="2w3rfh"
What resource should Kubernetes create?
```

---

### 3. metadata

```yaml
metadata:
```

Meaning:

```text id="dbj6wj"
Who am I?
```

Usually contains:

```text id="e5z7h7"
name

labels

namespace

annotations
```

---

### 4. spec

```yaml
spec:
```

Meaning:

```text id="nnu8yq"
What do I want?
```

Usually contains:

```text id="sd6u7h"
replicas

selector

template

strategy
```

---

#  Full Deployment YAML (Every Line Explained)

```yaml
apiVersion: apps/v1             # Which Kubernetes API should understand this object?

kind: Deployment                # What resource should Kubernetes create?

metadata:                       # Identity Deployment

  name: nginx-deployment        # Deployment name

spec:                           # Desired state / Deployment rules

  replicas: 3                   # Keep 3 Pods running

  selector:                     # Which Pods belong to this Deployment?

    matchLabels:                # Match Pods using labels

      app: nginx                # Manage Pods having app=nginx

  template:                     # Blueprint used to create future Pods

    metadata:                   # Metadata of future Pods

      labels:                   # Labels attached to future Pods

        app: nginx              # Every Pod gets app=nginx label

    spec:                       # Pod specification starts here

      containers:               # Containers inside future Pod

      - name: nginx             # Container name

        image: nginx:latest     # Docker image to pull and run
```

---

## Read Deployment YAML Like English

```text id="w86y22"
Use apps/v1 API.

Create a Deployment.

Call it nginx-deployment.

Keep 3 Pods running.

Manage Pods having app=nginx.

Use this template when creating Pods.

Give every Pod label app=nginx.

Inside each Pod run nginx container.

Pull nginx:latest image.
```

---

# — Ownership Tree (Indentation Mastery)

Never memorize spaces.

Memorize ownership.

```text id="wnlt4j"
Deployment
│
├── apiVersion
├── kind
│
├── metadata
│    └── name
│
└── spec
     │
     ├── replicas
     │
     ├── selector
     │    └── matchLabels
     │          └── app
     │
     └── template
          │
          ├── metadata
          │    └── labels
          │          └── app
          │
          └── spec
               │
               └── containers
                    │
                    ├── name
                    └── image
```

---

## Parent → Child Examples

### Example 1

```yaml
metadata:
  name: nginx-deployment
```

Means:

```text id="xkhrvw"
metadata
   └── name
```

---

### Example 2

```yaml
selector:
  matchLabels:
    app: nginx
```

Means:

```text id="3b8ivn"
selector
   └── matchLabels
         └── app
```

---

### Example 3

```yaml
template:
  metadata:
    labels:
      app: nginx
```

Means:

```text id="c1y7vs"
template
   └── metadata
         └── labels
               └── app
```

---

## One-Line Meaning Of Every Section

```text id="w7i7s8"
apiVersion → Which API?

kind → What resource?

metadata → Who am I?

spec → What do I want?

replicas → How many Pods?

selector → Which Pods belong to me?

template → What should Pods look like?

containers → What should run inside Pods?
```

# Kubernetes Deployment Notes

---

#  The Most Important Deployment Concept

Most Deployment confusion comes from one thing:

```yaml
template:
```

Many beginners think everything inside a Deployment YAML belongs to the Deployment.

That is wrong.

---

## Mental Model

Think:

```text
Deployment
     ↓

Deployment Rules
     +

Pod Template
```

Deployment contains:

```text
replicas
selector
strategy
```

Pod Template contains:

```text
metadata
spec
containers
volumes
env
probes
```

---

## Golden Rule

Everything below:

```yaml
template:
```

belongs to the FUTURE POD.

NOT the Deployment.

---

## Visual Representation

```text
Deployment
│
├── Deployment Settings
│      ├── replicas
│      ├── selector
│      └── strategy
│
└── Pod Blueprint
       │
       ├── Pod Metadata
       ├── Pod Spec
       └── Containers
```

---

## Trap

> Where does the Pod configuration start?

Correct answer:

```text
template:
```

Everything under template belongs to future Pods.

---

#  Why Are There Two metadata Sections?

This is one of the most common Kubernetes questions.

---

## Deployment Metadata

```yaml
metadata:
  name: nginx-deployment
```

Belongs to:

```text
Deployment Object
```

Purpose:

```text
Deployment Identity
```

---

### Visual

```text
Deployment
│
└── metadata
      └── name=nginx-deployment
```

---

## Pod Metadata

```yaml
template:
  metadata:
    labels:
      app: nginx
```

Belongs to:

```text
Future Pods
```

Purpose:

```text
Pod Identity
```

---

### Visual

```text
Future Pod
│
└── metadata
      └── labels
             └── app=nginx
```

---

## Why Kubernetes Needs Both

Because Deployment and Pod are different objects.

```text
Deployment
     ↓ creates

Pods
```

Each object needs its own identity.

---

## Real Example

```text
Deployment Name

nginx-deployment

Pod Names

nginx-6d4cf56d9-xp7mq

nginx-6d4cf56d9-z9trn

nginx-6d4cf56d9-gsh4x
```

One Deployment.

Multiple Pods.

Different identities.

---

#  Selector vs Labels

This is probably the MOST IMPORTANT Deployment YAML relationship.

---

## Labels

Labels are attached to Pods.

Example:

```yaml
labels:
  app: nginx
```

Meaning:

```text
This Pod belongs to nginx application.
```

---

## Selector

Selector tells Deployment:

```text
Which Pods belong to me?
```

Example:

```yaml
selector:
  matchLabels:
    app: nginx
```

---

## Relationship

```text
Selector
     ↓ matches

Labels
```

---

## Correct Example

```yaml
selector:
  matchLabels:
    app: nginx

template:
  metadata:
    labels:
      app: nginx
```

---

### Visual

```text
Deployment
      │

Selector
app=nginx
      │

Matches
      │

Pod Label
app=nginx
```

Works correctly.

---

## Wrong Example

```yaml
selector:
  matchLabels:
    app: frontend

template:
  metadata:
    labels:
      app: backend
```

---

### Visual

```text
Selector
app=frontend

Looking For

app=frontend

Found

app=backend
```

No match.

---

## Result

Deployment cannot properly manage Pods.

---

## Trap

Question:

> Can selector and labels be different?

Answer:

```text
No.

Deployment selector must match Pod labels.
```

---

#  Architecture

---

## High-Level Architecture

```text
Developer
     │

kubectl apply
     │

API Server
     │

Deployment
     │

ReplicaSet
     │

Pods
     │

Containers
```

---

## Real Kubernetes Architecture

```text
                Control Plane

 ┌─────────────────────────────────┐
 │                                 │
 │ API Server                      │
 │ Controller Manager              │
 │ Scheduler                       │
 │ etcd                            │
 │                                 │
 └─────────────────────────────────┘
                 │
                 │
                 ▼

              Worker Node

 ┌─────────────────────────────────┐
 │                                 │
 │ Kubelet                         │
 │ Container Runtime               │
 │ Pods                            │
 │                                 │
 └─────────────────────────────────┘
```

---

## Deployment Architecture

```text
Deployment
      ↓

ReplicaSet
      ↓

Pods
      ↓

Containers
```

---

#  Control Flow

Control Flow = What happens after kubectl apply?

---

## Complete Flow

```text
Developer
     ↓

kubectl apply
     ↓

API Server
     ↓

etcd
     ↓

Deployment Controller
     ↓

ReplicaSet
     ↓

Pods
     ↓

Scheduler
     ↓

Node
     ↓

Kubelet
     ↓

Container Runtime
     ↓

Application Running
```

---

## Component Responsibilities

| Component             | Responsibility      |
| --------------------- | ------------------- |
| kubectl               | Sends request       |
| API Server            | Entry point         |
| etcd                  | Stores state        |
| Deployment Controller | Watches Deployment  |
| ReplicaSet            | Maintains Pod count |
| Scheduler             | Chooses Node        |
| Kubelet               | Starts Containers   |
| Runtime               | Runs Containers     |

---

#  Runtime Lifecycle

This explains HOW a Deployment actually becomes a running application.

---

## Runtime Lifecycle

```text
Deployment Created
        ↓

ReplicaSet Created
        ↓

Pod Created
        ↓

Scheduler Picks Node
        ↓

Pod Assigned To Node
        ↓

Kubelet Receives Pod
        ↓

Image Pulled
        ↓

Container Created
        ↓

Container Started
        ↓

Application Running
```

---

## Real Example

```text
Deployment

nginx-deployment
```

Desired:

```text
3 Pods
```

Actual Flow:

```text
Deployment
     ↓

ReplicaSet
     ↓

Pod1
Pod2
Pod3
     ↓

Node Assignment
     ↓

Container Startup
     ↓

Application Available
```

---

#  Who Creates? Who Monitors? Who Restarts? Who Deletes?

These are common questions.

---

## Who Creates Deployment?

```text
Developer

DevOps Engineer

GitHub Actions

Jenkins

ArgoCD

FluxCD
```

---

## Who Monitors Deployment?

```text
Deployment Controller

Controller Manager

Prometheus

Grafana

SRE Team
```

---

## Who Restarts Crashed Containers?

Trap.

Many people answer:

```text
Deployment
```

Wrong.

Correct:

```text
Kubelet
```

---

### Container Crash Flow

```text
Container Crash
       ↓

Kubelet Detects Failure
       ↓

Container Restarted
```

---

## Who Creates New Pod If Pod Is Lost?

```text
ReplicaSet
```

Flow:

```text
Pod Deleted
      ↓

ReplicaSet Notices

Desired = 3
Current = 2

ReplicaSet Creates New Pod
```

---

## Who Deletes Deployment?

```bash
kubectl delete deployment nginx-deployment
```

---

### Deletion Flow

```text
Deployment Deleted
        ↓

ReplicaSet Deleted
        ↓

Pods Deleted
```

---

#  Failure Map

---

# Golden Rule

Most Kubernetes failures happen at only 3 layers.

```text
1. YAML Layer

2. Scheduling Layer

3. Runtime Layer
```

---

# Layer 1 — YAML Failure

Manifest itself is wrong.

---

## Example

```yaml
image: nginxxxx
```

---

Result

```text
ImagePullBackOff
```

---

## Example

```yaml
selector:
  app: nginx
```

Wrong schema.

---

Result

```text
Validation Error
```

---

## Symptoms

```text
kubectl apply fails

Validation errors

Image pull errors
```

---

## Commands

```bash
kubectl apply -f deployment.yaml

kubectl describe pod pod-name
```

---

# Layer 2 — Scheduling Failure

Manifest valid.

Pod created.

Scheduler cannot place Pod.

---

## Example Causes

```text
Insufficient CPU

Insufficient Memory

Node Taints

Affinity Rules

Node Selector Mismatch
```

---

## Result

```text
Pending
```

---

## Flow

```text
Deployment
     ↓

ReplicaSet
     ↓

Pod Created
     ↓

Scheduler Cannot Find Node
     ↓

Pending
```

---

## Commands

```bash
kubectl describe pod pod-name
```

Look for:

```text
Events
```

---

# Layer 3 — Runtime Failure

Pod scheduled successfully.

Container crashes.

---

## Example

```yaml
command: ["wrong-command"]
```

---

## Result

```text
CrashLoopBackOff
```

---

## Flow

```text
Pod Running
      ↓

Container Starts
      ↓

Application Crashes
      ↓

Restart
      ↓

Crash Again
      ↓

CrashLoopBackOff
```

---

## Commands

```bash
kubectl logs pod-name

kubectl describe pod pod-name
```

---

# Failure Map Summary

| Layer      | Problem      | Symptom          |
| ---------- | ------------ | ---------------- |
| YAML       | Bad manifest | Validation Error |
| YAML       | Wrong image  | ImagePullBackOff |
| Scheduling | No resources | Pending          |
| Scheduling | Taints       | Pending          |
| Runtime    | Bad command  | CrashLoopBackOff |
| Runtime    | App crash    | CrashLoopBackOff |

---

## Thinking

When something breaks:

Never start with:

```text
Deployment is broken
```

Start with:

```text
Which layer failed?

YAML?

Scheduling?

Runtime?
```

That mindset dramatically speeds up troubleshooting.

#  Troubleshooting Methodology

Most troubleshoot randomly.

Some follow a fixed path.

---

# Golden Troubleshooting Flow

```text
Deployment
     ↓

ReplicaSet
     ↓

Pod
     ↓

Node
     ↓

Container
     ↓

Application
     ↓

Logs
```

Never jump directly to logs.

Always move layer by layer.

---

## Troubleshooting Flow Diagram

```text
User Complaint
      ↓

Application Down
      ↓

Check Deployment
      ↓

Check ReplicaSet
      ↓

Check Pod
      ↓

Check Events
      ↓

Check Node
      ↓

Check Container
      ↓

Check Logs
```

---

# Step 1 — Check Deployment

```bash
kubectl get deployment

kubectl describe deployment nginx-deployment
```

Verify:

```text
Desired Replicas

Available Replicas

Conditions

Events
```

---

# Step 2 — Check ReplicaSet

```bash
kubectl get rs

kubectl describe rs
```

Verify:

```text
Replica Count

Pod Creation Errors

Ownership
```

---

# Step 3 — Check Pods

```bash
kubectl get pods

kubectl describe pod pod-name
```

Look for:

```text
Pending

ImagePullBackOff

CrashLoopBackOff

Evicted
```

---

# Step 4 — Check Events

Most Kubernetes problems are visible in Events.

```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

Common clues:

```text
FailedScheduling

FailedMount

FailedPullImage

BackOff
```

---

# Step 5 — Check Logs

```bash
kubectl logs pod-name
```

Multi-container pod:

```bash
kubectl logs pod-name -c container-name
```

---

# Troubleshooting Decision Tree

```text
kubectl get pods
         │
         │
         ├── Pending
         │       ↓
         │   Scheduling Issue
         │
         ├── ImagePullBackOff
         │       ↓
         │   Image Issue
         │
         ├── CrashLoopBackOff
         │       ↓
         │   Application Issue
         │
         └── Running
                 ↓
             Check Logs
```

---

#  kubectl Deep Dive


---

# View Deployments

```bash
kubectl get deployments
```

or

```bash
kubectl get deploy
```

---

# Detailed Information

```bash
kubectl describe deployment nginx-deployment
```

Shows:

```text
Replicas

Strategy

Events

Conditions
```

---

# Create Deployment

```bash
kubectl apply -f deployment.yaml
```

---

# Delete Deployment

```bash
kubectl delete deployment nginx-deployment
```

Flow:

```text
Deployment Deleted
       ↓

ReplicaSet Deleted
       ↓

Pods Deleted
```

---

# Scale Deployment

Current:

```text
3 Pods
```

Scale:

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

Result:

```text
3 Pods
   ↓

5 Pods
```

---

# Edit Deployment

```bash
kubectl edit deployment nginx-deployment
```

Opens editor.

Useful for quick production changes.

---

# Deployment History

```bash
kubectl rollout history deployment nginx-deployment
```

Shows:

```text
Revision 1

Revision 2

Revision 3
```

---

# Check Rollout Status

```bash
kubectl rollout status deployment nginx-deployment
```

Used during releases.

---

# Rollback Deployment

```bash
kubectl rollout undo deployment nginx-deployment
```

Flow:

```text
Version 2
     ↓

Problem Found
     ↓

Rollback
     ↓

Version 1
```

---

# Restart Deployment

```bash
kubectl rollout restart deployment nginx-deployment
```

Creates fresh Pods.

---

# Most Important Deployment Commands

| Command                 | Purpose          |
| ----------------------- | ---------------- |
| kubectl get deploy      | List Deployments |
| kubectl describe deploy | Details          |
| kubectl apply -f        | Create/Update    |
| kubectl delete deploy   | Delete           |
| kubectl scale           | Scale            |
| kubectl rollout status  | Release Status   |
| kubectl rollout history | History          |
| kubectl rollout undo    | Rollback         |
| kubectl rollout restart | Restart          |

---

#  Production Mapping

How Deployments are actually used in production.

---

# Small Lab

```text
1 Application

1 Deployment

1 Service
```

---

# Real Company

```text
Frontend
     ↓
Deployment

Backend
     ↓
Deployment

Auth Service
     ↓
Deployment

Payment Service
     ↓
Deployment

Notification Service
     ↓
Deployment
```

---

# Production Architecture

```text
Internet
     ↓

Load Balancer
     ↓

Service
     ↓

Deployment
     ↓

ReplicaSet
     ↓

Pods
```

---

# Example E-Commerce Application

```text
Frontend Deployment

Product Deployment

Cart Deployment

Payment Deployment

User Deployment

Email Deployment
```

Every microservice usually has its own Deployment.

---

#  Real Production Scenario

This is how Deployments are used every day.

---

# Day 1

Developer releases:

```text
Version 1
```

Deployment:

```yaml
replicas: 3
```

Result:

```text
Pod1

Pod2

Pod3
```

---

# Day 5

One Pod crashes.

```text
Pod2 Dies
```

ReplicaSet notices:

```text
Desired = 3

Current = 2
```

Creates replacement:

```text
Pod4 Created
```

Users unaffected.

---

# Day 10

New version released.

```text
v1
 ↓

v2
```

Deployment performs:

```text
Rolling Update
```

---

# Rolling Update Flow

```text
Old Pod
Old Pod
Old Pod

      ↓

New Pod
Old Pod
Old Pod

      ↓

New Pod
New Pod
Old Pod

      ↓

New Pod
New Pod
New Pod
```

No downtime.

---

# Day 11

Bug discovered.

Rollback:

```bash
kubectl rollout undo deployment nginx-deployment
```

Flow:

```text
Version 2
      ↓

Rollback
      ↓

Version 1
```

---

#  Mindset

---

# Junior Engineer Thinks

```text
Deployment creates Pods.
```

---

# Intermediate Engineer Thinks

```text
Deployment
     ↓

ReplicaSet
     ↓

Pods
```

---

# Thinks

```text
Deployment
     ↓

ReplicaSet
     ↓

Pods
     ↓

Scheduler
     ↓

Node
     ↓

Kubelet
     ↓

Container Runtime
     ↓

Application
```

---

# Architect Thinks

```text
Deployment
     ↓

Availability

Scalability

Reliability

Upgrade Strategy

Disaster Recovery

Observability
```

---

# Question

Instead of asking:

```text
How many Pods?
```

Ask:

```text
What happens when one Pod dies?

What happens during upgrades?

What happens if a node fails?

How fast can we recover?
```

---

#  Common  Traps

---

## Trap 1

Question:

> Does Deployment create Pods directly?

Wrong:

```text
Yes
```

Correct:

```text
Deployment
      ↓

ReplicaSet
      ↓

Pods
```

---

## Trap 2

Question:

> Who maintains replica count?

Correct:

```text
ReplicaSet
```

---

## Trap 3

Question:

> Who restarts crashed containers?

Correct:

```text
Kubelet
```

---

## Trap 4

Question:

> Can Deployment manage databases?

Answer:

```text
Possible

But StatefulSet is preferred.
```

---

## Trap 5

Question:

> What matches Pods to Deployment?

Answer:

```text
Selector + Labels
```

---

## Trap 6

Question:

> What is template?

Answer:

```text
Blueprint used to create Pods.
```

---

## Trap 7

Question:

> Why are there two metadata sections?

Answer:

```text
One belongs to Deployment.

One belongs to Pods.
```

---

#  Comparison Tables

---

# Pod vs ReplicaSet vs Deployment

| Feature             | Pod  | ReplicaSet | Deployment  |
| ------------------- | ---- | ---------- | ----------- |
| Runs Containers     | ✅    | ✅          | ✅           |
| Maintains Pod Count | ❌    | ✅          | ✅           |
| Self Healing        | ❌    | ✅          | ✅           |
| Scaling             | ❌    | ✅          | ✅           |
| Rolling Update      | ❌    | ❌          | ✅           |
| Rollback            | ❌    | ❌          | ✅           |
| Version History     | ❌    | ❌          | ✅           |
| Production Usage    | Rare | Rare       | Very Common |

---

# Responsibility Comparison

| Component  | Responsibility      |
| ---------- | ------------------- |
| Deployment | Desired State       |
| ReplicaSet | Pod Count           |
| Scheduler  | Node Placement      |
| Kubelet    | Container Lifecycle |
| Runtime    | Execute Containers  |

---

#  Mental Model Diagram

This is the entire Deployment system in one view.

```text
Developer
     │

kubectl apply
     │

API Server
     │

etcd
     │

Deployment
     │

ReplicaSet
     │

Pods
     │

Scheduler
     │

Node
     │

Kubelet
     │

Container Runtime
     │

Application

────────────────────────────

Failures

1. YAML

2. Scheduling

3. Runtime

────────────────────────────

Recovery

Deployment
      ↓

ReplicaSet
      ↓

New Pod

────────────────────────────

Updates

Deployment
      ↓

Rolling Update
      ↓

Rollback
```

---

# Second Revision Sheet

```text
DEPLOYMENT

Purpose:
Manage stateless applications.

Provides:

• Scaling
• Self Healing
• Rolling Updates
• Rollback
• Version History

Flow:

Deployment
      ↓
ReplicaSet
      ↓
Pods
      ↓
Containers

Golden Rule:

Deployment manages ReplicaSets.

ReplicaSets manage Pods.

Scheduler places Pods.

Kubelet manages Containers.

Three Failure Layers:

1. YAML
2. Scheduling
3. Runtime

Most Important YAML:

apiVersion
kind
metadata
spec
  replicas
  selector
  template
    metadata
    spec
      containers

Most Important Trap:

Deployment does NOT create Pods directly.

Deployment
      ↓
ReplicaSet
      ↓
Pods
```

