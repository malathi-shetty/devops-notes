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

# Section: Why StatefulSets?

## Why StatefulSets?

Deployments are designed for stateless workloads.

Problems for databases:

- Random pod names
- No stable identity
- No ordered startup
- No dedicated storage

StatefulSets solve these by providing:

- Stable pod names
- Ordered deployment
- Stable DNS
- Dedicated persistent storage

---

# Task 1 – Deployment Experiment

Deployment YAML:

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

Apply:

```bash
kubectl apply -f deployment.yaml
```

Check pods:

```bash
kubectl get pods
```

Output:

```text
nginx-deploy-xxxxx
nginx-deploy-yyyyy
nginx-deploy-zzzzz
```

Delete one:

```bash
kubectl delete pod <pod-name>
```

New pod created with different name.

Observation:

Deployments do not provide stable identity.

---

# Task 2 – Headless Service

StatefulSets require a Headless Service.

---

## Why?

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

Headless Service YAML:

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

Verify:

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

# Task 3 – StatefulSet

YAML:

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

Apply:

```bash
kubectl apply -f statefulset.yaml
```

Watch:

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

## PVC Creation

Check:

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

# Task 4 – Stable DNS

DNS Format:

```text
<pod-name>.<service-name>.<namespace>.svc.cluster.local
```

Example:

```text
web-0.web.default.svc.cluster.local
```

---

Get Pod IPs:

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

Run BusyBox:

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

Verification:

DNS IP = Pod IP

Perfect match.

---

## What Did We Prove?

StatefulSet gives:

### Stable DNS

```text
web-0.web.default.svc.cluster.local
```

always points to

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

# Task 5 – Persistent Storage

Write data:

```bash
kubectl exec web-0 -- sh -c \
"echo 'Data from web-0' > /usr/share/nginx/html/index.html"
```

Verify:

```bash
kubectl exec web-0 -- cat /usr/share/nginx/html/index.html
```

Output:

```text
Data from web-0
```

---

Delete Pod:

```bash
kubectl delete pod web-0
```

Wait for recreation.

Check:

```bash
kubectl get pods
```

New pod:

```text
web-0
```

Same name.

---

Verify Data:

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

but not

```text
PVC
```

Reattached:

```text
web-data-web-0
```

to

```text
web-0
```

again.

---

# Task 6 – Ordered Scaling

Scale Up:

```bash
kubectl scale statefulset web --replicas=5
```

Creation Order:

```text
web-3
web-4
```

Sequential.

---

Scale Down:

```bash
kubectl scale statefulset web --replicas=3
```

Deletion Order:

```text
web-4
web-3
```

Reverse order.

---

Check PVCs:

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

All remain.

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

# Task 7 – Cleanup

Delete StatefulSet:

```bash
kubectl delete statefulset web
```

Delete Service:

```bash
kubectl delete service web
```

Check PVC:

```bash
kubectl get pvc
```

PVCs still exist.

Delete manually:

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

to prevent accidental data loss.

---

# B.  Notes (Questions & Answers)

### What is a StatefulSet?

A Kubernetes workload for stateful applications requiring stable identity, storage, and network identity.

---

### When should StatefulSets be used?

* MySQL
* PostgreSQL
* Kafka
* MongoDB
* Cassandra
* Zookeeper

---

### Difference between Deployment and StatefulSet?

Deployment = Stateless

StatefulSet = Stateful

---

### Why does StatefulSet need a Headless Service?

To create individual DNS records for each pod.

---

### What is a Headless Service?

A Service with:

```yaml
clusterIP: None
```

No load balancing.

Direct DNS resolution.

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

# C. 1-Minute Revision Sheet

### StatefulSet Purpose

Run stateful applications in Kubernetes.

---

### Key Features

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

---

### Headless Service

```yaml
clusterIP: None
```

Required by StatefulSet.

---

### DNS Pattern

```text
<pod>.<service>.<namespace>.svc.cluster.local
```

---

### PVC Pattern

```text
<template-name>-<pod-name>
```

Example:

```text
web-data-web-0
```

---

# D. Quick Revision Table – One-Liners & Quick Facts

| Concept            | Quick Fact                              |
| ------------------ | --------------------------------------- |
| StatefulSet        | Used for stateful applications          |
| Deployment         | Used for stateless applications         |
| Pod Identity       | Stable in StatefulSet                   |
| Pod Naming         | web-0, web-1, web-2                     |
| Service Required   | Headless Service                        |
| Headless Service   | `clusterIP: None`                       |
| DNS Pattern        | pod.service.namespace.svc.cluster.local |
| Storage            | One PVC per pod                         |
| PVC Creation       | via volumeClaimTemplates                |
| Pod Startup        | Ordered                                 |
| Pod Shutdown       | Reverse ordered                         |
| Scale Down         | PVCs remain                             |
| Delete StatefulSet | PVCs remain                             |
| Delete Pod         | Data survives                           |
| Best Use Cases     | MySQL, PostgreSQL, Kafka, MongoDB       |
| Main Benefit       | Identity + DNS + Persistent Storage     |

---

## Final Memory Line

**Deployment = Stateless + Interchangeable Pods**

**StatefulSet = Stable Identity + Stable DNS + Persistent Storage + Ordered Operations**

This single line explains almost the entire purpose of StatefulSets. 

---

## Common StatefulSet Mistakes
- Forgetting to create Headless Service
- serviceName not matching Service name
- Missing volumeClaimTemplates
- Expecting PVCs to be deleted automatically
- Using StatefulSet for stateless applications

---

### Section: Real-World Use Cases

## StatefulSet Use Cases
```bash
### MySQL / PostgreSQL

mysql-0
mysql-1
mysql-2

### Kafka

kafka-0
kafka-1
kafka-2

### Elasticsearch

es-0
es-1
es-2

### Zookeeper

zk-0
zk-1
zk-2
```

---

### Section: Advanced Topics

```bash
## Advanced StatefulSet Concepts

### podManagementPolicy

OrderedReady (Default)

Parallel

### Update Strategies

RollingUpdate

OnDelete
```

### Section: Troubleshooting
```bash
## Troubleshooting

### Pods stuck in Pending

Check:

kubectl get pvc

kubectl describe pvc

kubectl get storageclass

### DNS Resolution Failing

Check:

kubectl get svc web

kubectl get pods -n kube-system

### Data Not Persisting

Check:

kubectl get pvc

kubectl describe pod web-0
```

## Note:

For StatefulSets:

- DNS name is stable
- Pod IP is not guaranteed stable

Use:
```bash
Stable DNS Name
Dynamic Pod IP
```

---


# StatefulSet Quick Comparison Tables

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

Delete `web-0`

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

DNS:

```text
service-name.default.svc.cluster.local
```

Resolves to Service IP.

No pod-specific DNS.

---

### StatefulSet

DNS:

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

## 15. Most Important  Table

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

# Final  One-Liner

```text
Deployment = Pods are interchangeable.

StatefulSet = Pods have identity, storage, and stable networking.
```

