# Day 56 – Kubernetes StatefulSets

---

# A. Detailed Notes (Full Explanation)

## What Problem Does StatefulSet Solve?

Before StatefulSets existed, Kubernetes primarily used Deployments.

Deployments work perfectly for **stateless applications** like:

* Nginx
* React applications
* APIs
* Microservices

These applications do not care:

* Which pod serves the request
* What the pod name is
* Whether the pod restarts with a new identity

Example:

```text
nginx-deploy-5cf8dc6bc5-f5fpb
nginx-deploy-5cf8dc6bc5-snh72
nginx-deploy-5cf8dc6bc5-zkh7p
```

If one pod dies:

```bash
kubectl delete pod nginx-deploy-5cf8dc6bc5-f5fpb
```

Kubernetes creates:

```text
nginx-deploy-5cf8dc6bc5-abc99
```

New name.

New identity.

For Nginx, no problem.

For a database cluster, this is a huge problem.

---

## Why Random Pod Names Are Bad For Databases

Databases need stable identity.

Examples:

### MySQL Replication

```text
mysql-primary
mysql-replica-1
mysql-replica-2
```

Replicas must know exactly who the primary server is.

---

### PostgreSQL Clusters

Nodes identify each other using fixed hostnames.

---

### Kafka Brokers

Each broker has a unique broker ID.

Changing identities causes cluster instability.

---

### Problems Caused by Random Names

* Replication breaks
* Leader election fails
* Cluster membership changes
* Sharding fails
* Clients cannot target specific nodes
* Recovery becomes difficult

---

## Solution: StatefulSet

StatefulSet provides:

### Stable Identity

Pods are predictable:

```text
web-0
web-1
web-2
```

Not random.

---

### Stable Storage

Each pod gets its own PVC.

Example:

```text
web-data-web-0
web-data-web-1
web-data-web-2
```

---

### Stable Network Identity

Each pod gets a DNS name.

Example:

```text
web-0.web.default.svc.cluster.local
web-1.web.default.svc.cluster.local
web-2.web.default.svc.cluster.local
```

---

### Ordered Deployment

Pods start sequentially.

```text
web-0
↓
web-1
↓
web-2
```

---

### Ordered Termination

Pods terminate in reverse order.

```text
web-2
↓
web-1
↓
web-0
```

---

## Sticky Identity (Important Interview Concept)

StatefulSet provides **sticky identity**.

Even if a pod is rescheduled to another node, it keeps:

* The same pod name
* The same DNS identity
* The same associated storage

Example:

```text
web-0
```

may move to another node, but it still remains:

```text
web-0
```

and continues using its original PVC.

---

## StatefulSet Controller Responsibilities

The StatefulSet Controller continuously ensures:

* Correct number of replicas
* Correct pod ordering
* Correct pod identity
* Correct PVC association

If a pod is deleted:

```text
web-0
```

the controller recreates:

```text
web-0
```

with the same identity and storage association.

---

# StatefulSet Architecture

## Why StatefulSets?

Deployments are designed for stateless workloads.

Problems for databases:

* Random pod names
* No stable identity
* No ordered startup
* No dedicated storage

StatefulSets solve these by providing:

* Stable pod names
* Ordered deployment
* Stable DNS
* Dedicated persistent storage

---

## StatefulSet Architecture Diagram

```text
Headless Service (web)
          │
 ┌────────┼────────┐
 │        │        │
web-0   web-1   web-2
 │        │        │
PVC      PVC      PVC
 │        │        │
PV       PV       PV
```

---

## Why StatefulSet Needs a Headless Service

StatefulSets require a Headless Service.

Normal Services:

```text
Service
   ↓
Load Balancing
   ↓
Pod
```

Requests are distributed.

---

StatefulSets need direct pod access.

Example:

```text
web-0
web-1
web-2
```

Each pod must be reachable individually.

---

### How Headless Service Works

A Headless Service does not receive a ClusterIP.

Instead:

```text
DNS
 ↓
Returns Individual Pod IPs
```

rather than:

```text
DNS
 ↓
Returns Service IP
```

This allows pod-specific DNS records.

Example:

```text
web-0.web.default.svc.cluster.local
web-1.web.default.svc.cluster.local
web-2.web.default.svc.cluster.local
```

Each pod can be contacted directly.

---

# Deployment vs StatefulSet

| Feature        | Deployment       | StatefulSet   |
| -------------- | ---------------- | ------------- |
| Pod Names      | Random           | Stable        |
| Startup Order  | Parallel         | Sequential    |
| Shutdown Order | Random           | Reverse       |
| Storage        | Shared/Ephemeral | Dedicated PVC |
| DNS            | Dynamic          | Stable        |
| Identity       | No               | Yes           |
| Best For       | Stateless Apps   | Databases     |

---

## Key Difference

Deployment:

```text
Pods are interchangeable.
```

StatefulSet:

```text
Pods are identifiable.
```

---

## Stable DNS vs Dynamic Pod IP

### Important Interview Point

For StatefulSets:

```text
DNS name is stable
Pod IP is not guaranteed stable
```

Do NOT assume:

```text
StatefulSet = Fixed Pod IP
```

This is incorrect.

A pod may restart and receive a new IP.

However:

```text
web-0.web.default.svc.cluster.local
```

remains the same.

Applications should use DNS names, not Pod IPs.

---

## Storage and StorageClass

StatefulSets commonly use:

```yaml
volumeClaimTemplates:
```

to automatically create one PVC per pod.

When PVCs are created, Kubernetes uses:

* The default StorageClass

or

* A specified StorageClass

to dynamically provision Persistent Volumes (PVs).

Flow:

```text
StatefulSet
      ↓
volumeClaimTemplates
      ↓
PVC Created
      ↓
StorageClass
      ↓
PV Provisioned
```

This allows each pod to receive dedicated persistent storage.

---

## StatefulSet Guarantees

| Guarantee          | Meaning                 |
| ------------------ | ----------------------- |
| Stable Identity    | Pod name remains same   |
| Stable Storage     | Same PVC reused         |
| Stable DNS         | Same hostname           |
| Ordered Deployment | web-0 before web-1      |
| Ordered Scaling    | Sequential scaling      |
| Ordered Updates    | Reverse rolling updates |

---

## Important Safety Feature

Deleting StatefulSet:

```text
Deletes Pods
```

but

```text
Does NOT delete PVCs
```

to prevent accidental data loss.

---

## Real-World StatefulSet Use Cases

### MySQL / PostgreSQL

```text
mysql-0
mysql-1
mysql-2
```

---

### Kafka

```text
kafka-0
kafka-1
kafka-2
```

---

### Elasticsearch

```text
es-0
es-1
es-2
```

---

### ZooKeeper

```text
zk-0
zk-1
zk-2
```

---

## When Should StatefulSets Be Used?

Examples:

* MySQL
* PostgreSQL
* Kafka
* MongoDB
* Cassandra
* ZooKeeper

These applications require:

* Stable identity
* Persistent storage
* Stable DNS
* Ordered startup/shutdown

---

## Can StatefulSet Work Without Persistent Storage?

Yes.

Stable identity and ordered operations can still be useful even without PVCs.

StatefulSets are primarily about:

* Identity
* Ordering
* Stable networking

Persistent storage is commonly used, but not mandatory.

---

## Common StatefulSet Mistakes

* Forgetting to create Headless Service
* serviceName not matching Service name
* Missing volumeClaimTemplates
* Expecting PVCs to be deleted automatically
* Using StatefulSet for stateless applications

---

## Important Memory Line

```text
Deployment = Stateless + Interchangeable Pods

StatefulSet = Stable Identity + Stable DNS + Persistent Storage + Ordered Operations
```

This single line explains almost the entire purpose of StatefulSets.

---

# B. Hands-On Labs (Tasks 1–7)

---

# Task 1 – Deployment Experiment

## Goal

Observe how Deployments provide stateless, replaceable pods rather than stable identities.

---

## Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```

---

## Apply Deployment

```bash
kubectl apply -f deployment.yaml
```

---

## Check Pods

```bash
kubectl get pods
```

Output:

```text
nginx-deploy-xxxxx
nginx-deploy-yyyyy
nginx-deploy-zzzzz
```

Notice:

* Names contain random suffixes
* Pods are interchangeable
* No stable identity exists

---

## Delete One Pod

```bash
kubectl delete pod <pod-name>
```

New pod created:

```text
nginx-deploy-abcde
```

Observation:

* Old pod disappears
* New pod receives a different name
* New identity is created

### Conclusion

Deployments do not provide stable identity.

---

# Task 2 – Headless Service

## Why Is It Required?

StatefulSets require a Headless Service.

A normal Service performs load balancing:

```text
Service
   ↓
Load Balancing
   ↓
Pod
```

Requests are distributed among pods.

---

Stateful applications need direct pod access.

Example:

```text
web-0
web-1
web-2
```

Each pod must be reachable individually.

---

## Headless Service YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web
spec:
  clusterIP: None
  selector:
    app: web
  ports:
  - port: 80
```

Apply:

```bash
kubectl apply -f headless-service.yaml
```

---

## Verify Service

```bash
kubectl get svc
```

Output:

```text
CLUSTER-IP: None
```

Important:

```yaml
clusterIP: None
```

creates a Headless Service.

---

## What Does Headless Service Do?

Instead of:

```text
DNS
 ↓
Service IP
 ↓
Random Pod
```

It provides:

```text
DNS
 ↓
Individual Pod IPs
```

Result:

```text
web-0.web.default.svc.cluster.local
web-1.web.default.svc.cluster.local
web-2.web.default.svc.cluster.local
```

Each pod gets its own DNS record.

---

# Task 3 – Create StatefulSet

## StatefulSet YAML

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web

spec:
  serviceName: web
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
      - name: nginx
        image: nginx

        volumeMounts:
        - name: web-data
          mountPath: /usr/share/nginx/html

  volumeClaimTemplates:
  - metadata:
      name: web-data

    spec:
      accessModes:
      - ReadWriteOnce

      resources:
        requests:
          storage: 100Mi
```

---

## Apply StatefulSet

```bash
kubectl apply -f statefulset.yaml
```

---

## Watch Pod Creation

```bash
kubectl get pods -l app=web -w
```

Observed:

```text
web-0
web-1
web-2
```

Created sequentially.

---

## Ordered Creation

StatefulSet follows:

```text
web-0
↓ Ready

web-1
↓ Ready

web-2
```

The next pod is not created until the previous pod becomes Ready.

---

## serviceName Purpose

```yaml
serviceName: web
```

links the StatefulSet to the Headless Service.

This is required for stable DNS generation.

---

## PVC Creation

### PVC Naming Pattern

```text
<volumeClaimTemplate-name>-<pod-name>
```

Example:

```text
web-data-web-0
web-data-web-1
web-data-web-2
```

---

## Verify PVCs

```bash
kubectl get pvc
```

Output:

```text
web-data-web-0
web-data-web-1
web-data-web-2
```

PVC naming pattern:

```text
<template-name>-<pod-name>
```

Example:

```text
web-data-web-0
```

---

## What Happened Internally?

```text
StatefulSet
      ↓
volumeClaimTemplates
      ↓
PVC Created
      ↓
StorageClass
      ↓
PV Provisioned
```

Each pod receives dedicated storage.

---

# Task 4 – Stable DNS Verification

## DNS Format

```text
<pod-name>.<service-name>.<namespace>.svc.cluster.local
```

Example:

```text
web-0.web.default.svc.cluster.local
```

---

## Get Pod IPs

```bash
kubectl get pods -o wide
```

Output:

```text
web-0 → 10.244.0.11
web-1 → 10.244.0.13
web-2 → 10.244.0.15
```

---

## Run BusyBox

```bash
kubectl run -it --rm dns-test --image=busybox -- sh
```

Inside BusyBox:

```bash
nslookup web-0.web.default.svc.cluster.local
```

Output:

```text
10.244.0.11
```

Similarly:

```text
web-1 → 10.244.0.13
web-2 → 10.244.0.15
```

---

## Verification

```text
DNS IP = Pod IP
```

Perfect match.

---

## What Did We Prove?

### Stable DNS

```text
web-0.web.default.svc.cluster.local
```

always points to:

```text
web-0
```

---

### Direct Pod Access

No load balancing.

Direct pod communication.

---

### Stable Identity

Perfect for databases.

---

## Important Note

For StatefulSets:

```text
DNS name is stable
Pod IP is not guaranteed stable
```

Use:

```text
Stable DNS Name
Dynamic Pod IP
```

Applications should use DNS names rather than Pod IPs.

---

# Task 5 – Persistent Storage Verification

## Write Data

```bash
kubectl exec web-0 -- sh -c \
"echo 'Data from web-0' > /usr/share/nginx/html/index.html"
```

---

## Verify Data

```bash
kubectl exec web-0 -- cat /usr/share/nginx/html/index.html
```

Output:

```text
Data from web-0
```

---

## Delete Pod

```bash
kubectl delete pod web-0
```

Wait for recreation.

---

## Check Pods

```bash
kubectl get pods
```

New pod:

```text
web-0
```

Same name.

---

## Verify Data Again

```bash
kubectl exec web-0 -- cat /usr/share/nginx/html/index.html
```

Output:

```text
Data from web-0
```

Data survived.

---

## Why?

Kubernetes deleted:

```text
Pod
```

but not:

```text
PVC
```

StatefulSet reattached:

```text
web-data-web-0
```

to:

```text
web-0
```

again.

---

## What Did We Prove?

StatefulSet guarantees:

* Stable identity
* Stable storage
* PVC reuse after recreation
* Data persistence

---

# Task 6 – Ordered Scaling

## Scale Up

```bash
kubectl scale statefulset web --replicas=5
```

Creation order:

```text
web-3
web-4
```

Sequential.

---

### Ordered Scaling Behavior

```text
web-0
↓ Ready

web-1
↓ Ready

web-2
↓ Ready

web-3
↓ Ready

web-4
```

Each pod waits for the previous pod.

---

## Scale Down

```bash
kubectl scale statefulset web --replicas=3
```

Deletion order:

```text
web-4
web-3
```

Reverse order.

---

## Verify PVCs

```bash
kubectl get pvc
```

Output:

```text
web-data-web-0
web-data-web-1
web-data-web-2
web-data-web-3
web-data-web-4
```

All PVCs remain.

---

## Why Keep PVCs?

Data protection.

If scaled back up:

```text
web-3
web-4
```

reuse their old storage.

---

## Interview Question

### Are PVCs deleted when StatefulSet scales down?

No.

PVCs remain.

Storage is preserved.

---

# Task 7 – Cleanup

## Delete StatefulSet

```bash
kubectl delete statefulset web
```

---

## Delete Service

```bash
kubectl delete service web
```

---

## Verify PVCs

```bash
kubectl get pvc
```

PVCs still exist.

---

## Delete PVCs Manually

```bash
kubectl delete pvc web-data-web-0 web-data-web-1 web-data-web-2 web-data-web-3 web-data-web-4
```

---

## Important Safety Feature

Deleting StatefulSet:

```text
Deletes Pods
```

but

```text
Does NOT delete PVCs
```

This prevents accidental data loss.

---

## Lab Summary

### What We Proved

#### Deployment

```text
Pods are replaceable.
```

---

#### StatefulSet

```text
Pods are identifiable.
```

---

### Verified Features

✅ Stable Pod Names

```text
web-0
web-1
web-2
```

✅ Stable DNS

```text
web-0.web.default.svc.cluster.local
```

✅ Dedicated PVC

```text
web-data-web-0
```

✅ Ordered Creation

```text
0 → 1 → 2
```

✅ Ordered Deletion

```text
2 → 1 → 0
```

✅ Data survives pod recreation

✅ PVC survives scaling down

✅ PVC survives StatefulSet deletion

---

### Final Lab Memory Line

```text
Delete Pod
    ↓
Same Name
    ↓
Same PVC
    ↓
Same Data
```

This behavior is the core reason StatefulSets are used for databases and clustered applications.

---

# C. Interview Questions & Answers

---

### What is a StatefulSet?

A Kubernetes workload for stateful applications requiring stable identity, storage, and network identity.

---

### When should StatefulSets be used?

Examples:

* MySQL
* PostgreSQL
* Kafka
* MongoDB
* Cassandra
* ZooKeeper

These applications require:

* Stable identity
* Persistent storage
* Stable DNS
* Ordered startup/shutdown

---

### Difference between Deployment and StatefulSet?

Deployment:

```text
Stateless
```

StatefulSet:

```text
Stateful
```

---

### Why does StatefulSet need a Headless Service?

To create individual DNS records for each pod.

Without a Headless Service, StatefulSet pods cannot receive stable DNS names.

---

### What is a Headless Service?

A Service with:

```yaml
clusterIP: None
```

Characteristics:

* No load balancing
* No ClusterIP
* Direct DNS resolution
* Returns pod IPs instead of a Service IP

---

### What is the DNS format of StatefulSet Pods?

```text
<pod-name>.<service-name>.<namespace>.svc.cluster.local
```

Example:

```text
web-0.web.default.svc.cluster.local
```

---

### What is volumeClaimTemplates?

A template used to automatically create one PVC per StatefulSet pod.

PVC naming pattern:

```text
<template-name>-<pod-name>
```

Example:

```text
web-data-web-0
```

---

### What happens if a StatefulSet pod is deleted?

* Pod recreated
* Same name
* Same PVC attached
* Data preserved

---

### Are PVCs deleted when StatefulSet scales down?

No.

PVCs remain.

---

### Are PVCs deleted when StatefulSet is deleted?

No.

PVCs must be deleted manually.

---

### What order do StatefulSet pods start?

```text
0 → 1 → 2
```

---

### What order do StatefulSet pods terminate?

```text
2 → 1 → 0
```

---

### Why are StatefulSets important for databases?

Because databases require:

* Stable identity
* Persistent storage
* Stable DNS
* Ordered startup/shutdown

---

### Why doesn't a Deployment use stable pod names?

Because Deployment pods are designed to be:

* Disposable
* Replaceable
* Interchangeable

Stateful applications require:

* Identity
* Ordering
* Persistent storage

Therefore Kubernetes created StatefulSets.

---

### Why not run MySQL in a Deployment?

Deployment pods are replaceable and have no stable identity.

MySQL replicas need:

* Stable hostname
* Stable storage
* Stable network identity

StatefulSet provides all three.

---

### What is the purpose of serviceName in a StatefulSet?

Example:

```yaml
serviceName: web
```

Purpose:

* Links StatefulSet to Headless Service
* Used for stable DNS generation
* Required for pod-specific DNS records

---

### Can StatefulSet work without persistent storage?

Yes.

Stable identity and ordered operations can still be useful without PVCs.

StatefulSet is primarily about:

* Identity
* Ordering
* Stable networking

Persistent storage is optional.

---

### What happens if web-1 is not Ready?

Using default:

```text
OrderedReady
```

policy:

```text
web-2
```

will not be created until:

```text
web-1
```

becomes Ready.

---

### What is the default podManagementPolicy?

```text
OrderedReady
```

---

### Can StatefulSet pods move to another node?

Yes.

Identity remains the same.

Pod IP may change.

DNS remains stable.

Example:

```text
web-0
```

remains:

```text
web-0
```

even after rescheduling.

---

### What is Sticky Identity?

Sticky Identity means a StatefulSet pod keeps:

* Same pod name
* Same DNS identity
* Same associated storage

even if recreated or rescheduled.

---

### What is the StatefulSet Controller responsible for?

The StatefulSet Controller continuously ensures:

* Correct number of replicas
* Correct pod ordering
* Correct pod identity
* Correct PVC association

---

# D. 1-Minute Revision Sheet

---

## StatefulSet Purpose

Run stateful applications in Kubernetes.

---

## Key Features

### Stable Pod Names

```text
web-0
web-1
web-2
```

---

### Stable DNS

```text
web-0.web.default.svc.cluster.local
```

---

### Dedicated PVC

```text
web-data-web-0
```

---

### Ordered Creation

```text
0 → 1 → 2
```

---

### Ordered Deletion

```text
2 → 1 → 0
```

---

### Stable Identity

Sticky pod identity is maintained.

---

### Stable Storage

Same PVC reused after recreation.

---

### Data Survives Pod Recreation

Delete Pod:

```text
Pod Deleted
```

Result:

```text
Same Name
Same PVC
Same Data
```

---

## Headless Service

```yaml
clusterIP: None
```

Required by StatefulSet.

---

## DNS Pattern

```text
<pod>.<service>.<namespace>.svc.cluster.local
```

Example:

```text
web-0.web.default.svc.cluster.local
```

---

## PVC Pattern

```text
<template-name>-<pod-name>
```

Example:

```text
web-data-web-0
```

---

## Storage Provisioning Flow

```text
StatefulSet
      ↓
volumeClaimTemplates
      ↓
PVC
      ↓
StorageClass
      ↓
PV
```

---

# E. Quick Revision Table – One-Liners & Quick Facts

| Concept              | Quick Fact                              |
| -------------------- | --------------------------------------- |
| StatefulSet          | Used for stateful applications          |
| Deployment           | Used for stateless applications         |
| Pod Identity         | Stable in StatefulSet                   |
| Pod Naming           | web-0, web-1, web-2                     |
| Service Required     | Headless Service                        |
| Headless Service     | `clusterIP: None`                       |
| DNS Pattern          | pod.service.namespace.svc.cluster.local |
| Storage              | One PVC per pod                         |
| PVC Creation         | via volumeClaimTemplates                |
| Storage Provisioning | Via StorageClass                        |
| Pod Startup          | Ordered                                 |
| Pod Shutdown         | Reverse ordered                         |
| Scale Down           | PVCs remain                             |
| Delete StatefulSet   | PVCs remain                             |
| Delete Pod           | Data survives                           |
| Sticky Identity      | Same name + DNS + PVC                   |
| Best Use Cases       | MySQL, PostgreSQL, Kafka, MongoDB       |
| Main Benefit         | Identity + DNS + Persistent Storage     |

---

# F. Advanced Topics

---

## podManagementPolicy

### OrderedReady (Default)

Pods are created sequentially.

Example:

```text
web-0
↓ Ready

web-1
↓ Ready

web-2
```

Safer for databases.

---

### Parallel

Pods are created simultaneously.

Example:

```text
web-0
web-1
web-2
```

No readiness dependency.

Faster but less controlled.

---

## Update Strategies

### RollingUpdate

Default strategy.

Pods updated automatically.

Example:

```text
web-2
↓
web-1
↓
web-0
```

Controlled update process.

---

### OnDelete

Pods updated only after manual deletion.

Administrator controls update timing.

---

# G. Troubleshooting

---

## Pods Stuck in Pending

Check:

```bash
kubectl get pvc

kubectl describe pvc

kubectl get storageclass
```

Common causes:

* No StorageClass
* Storage unavailable
* PVC not bound

---

## DNS Resolution Failing

Check:

```bash
kubectl get svc web

kubectl get pods -n kube-system
```

Verify:

* Headless Service exists
* CoreDNS is running
* serviceName matches Service name

---

## Data Not Persisting

Check:

```bash
kubectl get pvc

kubectl describe pod web-0
```

Verify:

* PVC exists
* PVC bound successfully
* Volume mounted correctly

---

## Pod Not Starting In Order

Check:

```bash
kubectl describe pod web-0

kubectl describe pod web-1
```

Remember:

```text
web-1
```

must become Ready before:

```text
web-2
```

is created when using:

```text
OrderedReady
```

---

# H. Common StatefulSet Mistakes

* Forgetting to create Headless Service
* serviceName not matching Service name
* Missing volumeClaimTemplates
* Expecting PVCs to be deleted automatically
* Assuming StatefulSet provides fixed Pod IPs
* Using Pod IPs instead of DNS names
* Using StatefulSet for stateless applications

---

# I. Quick Comparison Tables

---

## 1. Deployment vs StatefulSet

| Feature           | Deployment                 | StatefulSet               |
| ----------------- | -------------------------- | ------------------------- |
| Purpose           | Stateless applications     | Stateful applications     |
| Pod Names         | Random (`app-xyz-abc`)     | Stable (`app-0`, `app-1`) |
| Identity          | No fixed identity          | Fixed identity            |
| Pod Creation      | Parallel                   | Ordered                   |
| Pod Deletion      | Any order                  | Reverse order             |
| Storage           | Shared PVC / Ephemeral     | Dedicated PVC per Pod     |
| DNS               | No stable DNS              | Stable DNS                |
| Scaling           | Fast                       | Ordered                   |
| Typical Use Cases | Nginx, APIs, Frontend Apps | MySQL, PostgreSQL, Kafka  |
| Pod Replacement   | New random name            | Same pod name restored    |

### Memory Trick

```text
Deployment = Replaceable Pods

StatefulSet = Identifiable Pods
```

---

## 2. Stateless vs Stateful Applications

| Feature               | Stateless App             | Stateful App             |
| --------------------- | ------------------------- | ------------------------ |
| Stores Data           | No                        | Yes                      |
| Pod Identity Required | No                        | Yes                      |
| Pod Restart Impact    | Minimal                   | Critical                 |
| Storage Requirement   | Optional                  | Mandatory                |
| Examples              | Nginx, React, Node.js API | MySQL, PostgreSQL, Kafka |
| Scaling               | Easy                      | Controlled               |

---

## 3. Normal Service vs Headless Service

| Feature                 | Normal Service | Headless Service     |
| ----------------------- | -------------- | -------------------- |
| clusterIP               | Assigned       | None                 |
| Load Balancing          | Yes            | No                   |
| DNS Result              | Service IP     | Individual Pod IPs   |
| Pod Access              | Indirect       | Direct               |
| StatefulSet Requirement | No             | Yes                  |
| Usage                   | Web Apps       | Databases & Clusters |

### Example

Normal Service:

```text
web-service
      ↓
Service IP
      ↓
Random Pod
```

Headless Service:

```text
web-0.web.default.svc.cluster.local
web-1.web.default.svc.cluster.local
web-2.web.default.svc.cluster.local
```

Direct Pod Access.

---

## 4. Deployment Storage vs StatefulSet Storage

| Feature          | Deployment      | StatefulSet                        |
| ---------------- | --------------- | ---------------------------------- |
| PVC Sharing      | Possible        | Not Recommended                    |
| Per-Pod Storage  | No              | Yes                                |
| Storage Identity | Not tied to pod | Tied to pod                        |
| PVC Creation     | Manual          | Automatic via volumeClaimTemplates |
| Data Persistence | Depends         | Guaranteed per pod                 |

---

## 5. Pod Naming Difference

### Deployment

```text
nginx-deploy-5cf8dc6bc5-f5fpb
nginx-deploy-5cf8dc6bc5-snh72
nginx-deploy-5cf8dc6bc5-zkh7p
```

Delete one:

```text
nginx-deploy-5cf8dc6bc5-abcde
```

New random name.

---

### StatefulSet

```text
web-0
web-1
web-2
```

Delete:

```text
web-0
```

Result:

```text
web-0
```

Same name restored.

---

## 6. Startup Order Difference

### Deployment

```text
pod-1
pod-2
pod-3
```

All create simultaneously.

---

### StatefulSet

```text
web-0
↓ Ready

web-1
↓ Ready

web-2
```

Strict sequence.

---

## 7. Shutdown Order Difference

### Deployment

```text
Random
```

Any pod can terminate first.

---

### StatefulSet

```text
web-2
↓
web-1
↓
web-0
```

Reverse order.

---

## 8. DNS Behavior

### Deployment

```text
service-name.default.svc.cluster.local
```

Resolves to Service IP.

No pod-specific DNS.

---

### StatefulSet

```text
web-0.web.default.svc.cluster.local

web-1.web.default.svc.cluster.local

web-2.web.default.svc.cluster.local
```

Each pod gets its own DNS record.

---

## 9. Scaling Behavior

| Operation            | Deployment | StatefulSet     |
| -------------------- | ---------- | --------------- |
| Scale Up             | Parallel   | Ordered         |
| Scale Down           | Any Pod    | Reverse Order   |
| Storage Preservation | Depends    | Preserved       |
| PVC Deletion         | Possible   | Never Automatic |

---

## 10. Pod Deletion Behavior

### Deployment

Delete pod:

```bash
kubectl delete pod nginx-abc
```

New pod:

```text
nginx-xyz
```

New identity.

---

### StatefulSet

Delete pod:

```bash
kubectl delete pod web-0
```

New pod:

```text
web-0
```

Same identity.

---

## 11. Data Persistence

| Action              | Deployment            | StatefulSet   |
| ------------------- | --------------------- | ------------- |
| Pod Deleted         | Data may be lost      | Data retained |
| PVC Deleted         | Data lost             | Data lost     |
| Scale Down          | Storage may disappear | PVC retained  |
| StatefulSet Deleted | N/A                   | PVC retained  |

---

## 12. StatefulSet vs ReplicaSet

| Feature            | ReplicaSet     | StatefulSet   |
| ------------------ | -------------- | ------------- |
| Stable Names       | No             | Yes           |
| Stable Storage     | No             | Yes           |
| Stable DNS         | No             | Yes           |
| Ordered Operations | No             | Yes           |
| Use Case           | Stateless Pods | Stateful Pods |

---

## 13. OrderedReady vs Parallel

| Feature           | OrderedReady | Parallel     |
| ----------------- | ------------ | ------------ |
| Creation          | Sequential   | Simultaneous |
| Safety            | High         | Medium       |
| Default           | Yes          | No           |
| Database Friendly | Yes          | No           |
| Speed             | Slower       | Faster       |

---

## 14. RollingUpdate vs OnDelete

| Feature            | RollingUpdate | OnDelete |
| ------------------ | ------------- | -------- |
| Update Trigger     | Automatic     | Manual   |
| Downtime Risk      | Low           | Depends  |
| Default            | Yes           | No       |
| Operational Effort | Low           | High     |

---

## 15. Most Important Table

| Requirement     | Deployment | StatefulSet |
| --------------- | ---------- | ----------- |
| Web Application | ✅          | ❌           |
| REST API        | ✅          | ❌           |
| Frontend App    | ✅          | ❌           |
| MySQL           | ❌          | ✅           |
| PostgreSQL      | ❌          | ✅           |
| MongoDB         | ❌          | ✅           |
| Kafka           | ❌          | ✅           |
| Elasticsearch   | ❌          | ✅           |
| ZooKeeper       | ❌          | ✅           |

---

# Final Memory Lines

```text
Deployment = Pods are interchangeable.

StatefulSet = Pods have identity, storage, and stable networking.
```

---

```text
Delete Pod
    ↓
Same Name
    ↓
Same PVC
    ↓
Same Data
```

---

```text
Stable DNS Name
Dynamic Pod IP
```

---

```text
Deployment = Replaceable Pods

StatefulSet = Identifiable Pods
```

---

```text
Deployment = Stateless + Interchangeable Pods

StatefulSet = Stable Identity + Stable DNS + Persistent Storage + Ordered Operations
```
