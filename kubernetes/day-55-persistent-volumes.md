# Day 55 – Persistent Volumes (PV) and Persistent Volume Claims (PVC)

---

# A. Detailed Notes (Full Explanation)

## PV vs PVC Analogy

| Component    | Real World Analogy        |
| ------------ | ------------------------- |
| PV           | Parking Space             |
| PVC          | Parking Ticket            |
| Pod          | Car                       |
| StorageClass | Parking Management System |

Flow:
```bash
Pod
 ↓
PVC
 ↓
PV
 ↓
Actual Storage
```

## Why Do We Need Persistent Storage?

Containers are **ephemeral**.

That means:

* A container can be stopped, deleted, or recreated at any time.
* Anything stored inside the container filesystem disappears when the Pod is deleted.
* This is fine for stateless applications but not for:

  * Databases
  * User uploads
  * Logs
  * Application state
  * Configuration files

Example:

```text
Pod Deleted
    ↓
Container Deleted
    ↓
Filesystem Deleted
    ↓
Data Lost
```

To solve this problem, Kubernetes provides:

* PersistentVolume (PV)
* PersistentVolumeClaim (PVC)
* StorageClass

These allow storage to exist independently of Pods.

---

# Task 1: Demonstrating Data Loss with emptyDir

## What is emptyDir?

`emptyDir` is a temporary volume created when a Pod starts.

Characteristics:

* Created when Pod starts
* Deleted when Pod is deleted
* Shared between containers in the same Pod
* Not persistent

---

## Manifest

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: emptydir-demo
spec:
  containers:
    - name: writer
      image: busybox
      command: ["/bin/sh", "-c"]
      args:
        - while true; do
            date >> /data/message.txt;
            sleep 5;
          done
      volumeMounts:
        - name: data-volume
          mountPath: /data

  volumes:
    - name: data-volume
      emptyDir: {}
```

Apply:

```bash
kubectl apply -f emptydir-pod.yaml
```

Verify:

```bash
kubectl exec -it emptydir-demo -- cat /data/message.txt
```

Output:

```text
Tue Jun 2 ...
Tue Jun 2 ...
Tue Jun 2 ...
```

---

## Delete Pod

```bash
kubectl delete pod emptydir-demo
```

Recreate:

```bash
kubectl apply -f emptydir-pod.yaml
```

Check again:

```bash
kubectl exec -it emptydir-demo -- cat /data/message.txt
```

---

## Observation

Timestamp is different.

Old data is gone.

---

## Why?

Because:

```text
emptyDir
    ↓
Exists only while Pod exists
    ↓
Pod Deleted
    ↓
Volume Deleted
    ↓
Data Lost
```

---

# Task 2: Persistent Volume (PV)

## What is a PV?

A PersistentVolume is actual storage available to the cluster.

Think of it as:

```text
Hard Disk
Cloud Disk
NFS Share
hostPath
```

represented as a Kubernetes object.

---

## PV Characteristics
### Persistent Volume (PV)

A PV is:
* Cluster-wide resource
* Not namespaced
* Created manually or dynamically
* Has storage capacity
* Has access modes
* Has reclaim policy
* Represents actual storage
* Can exist without Pods
* Can exist without PVCs

Examples
```bash
hostPath
NFS
AWS EBS
Azure Disk
GCE Persistent Disk
```

## PV Lifecycle
```bash
Create PV
    ↓
Available
    ↓
Create PVC
    ↓
Bound
    ↓
Delete PVC
    ↓
Released
    ↓
Delete PV / Reclaim
```

---

## Manifest

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: manual-pv

spec:
  capacity:
    storage: 1Gi

  accessModes:
    - ReadWriteOnce

  persistentVolumeReclaimPolicy: Retain

  hostPath:
    path: /tmp/k8s-pv-data
```

Apply:

```bash
kubectl apply -f pv.yaml
```

Verify:

```bash
kubectl get pv
```

Output:

```text
NAME        STATUS
manual-pv   Available
```

---

## Why Available?

No PVC is using it.

Lifecycle:

```text
Available
    ↓
Bound
    ↓
Released
```

Meaning:

| Status    | Meaning        |
| --------- | -------------- |
| Available | Free           |
| Bound     | Claimed by PVC |
| Released  | PVC deleted    |

---

## hostPath

```yaml
hostPath:
  path: /tmp/k8s-pv-data
```

Stores data directly on node filesystem.

Good for learning.

Not recommended for production.

Why?

Because if Pod moves to another node:

```text
Node A
    ↓
Data Exists

Pod Moves

Node B
    ↓
Data Missing
```

---

# Access Modes

## ReadWriteOnce (RWO)

```text
Read + Write
Single Node
```

Used for:

* Databases
* Stateful applications

---

## ReadOnlyMany (ROX)

```text
Read Only
Multiple Nodes
```

Used for:

* Shared static content

---

## ReadWriteMany (RWX)

```text
Read + Write
Multiple Nodes
```

Used for:

* Shared filesystems
* NFS

---

# Task 3: Persistent Volume Claim (PVC)

## What is a PVC?

A PVC is a request for storage.

Developer says:

```text
I need:
500Mi
ReadWriteOnce
```

Kubernetes finds a matching PV.

---

### PVC Characteristics

A PVC is:
- Namespaced resource
- Request for storage
- Consumes PV resources
- Used by Pods

Example:
```yaml
resources:
  requests:
    storage: 500Mi
```
---

## Manifest

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc

spec:
  storageClassName: ""

  accessModes:
    - ReadWriteOnce

  resources:
    requests:
      storage: 500Mi
```

Important:

```yaml
storageClassName: ""
```

Disables dynamic provisioning.

Allows binding to manually created PV.

---

Apply:

```bash
kubectl apply -f pvc.yaml
```

Verify:

```bash
kubectl get pvc
```

Output:

```text
NAME     STATUS   VOLUME
my-pvc   Bound    manual-pv
```

---

Check PV:

```bash
kubectl get pv
```

Output:

```text
manual-pv   Bound
```

---

## Why Did Binding Happen?

PVC requested:

```text
500Mi
ReadWriteOnce
```

PV provided:

```text
1Gi
ReadWriteOnce
```

Kubernetes matched them.

---

## Relationship

```text
Pod
 ↓
PVC
 ↓
PV
 ↓
Storage
```

---

# Task 4: Use PVC Inside a Pod

## Manifest

First Pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pvc-demo

spec:
  containers:
  - name: app
    image: busybox
    command: ["/bin/sh", "-c"]
    args:
    - 'echo "First Pod: $(date)" >> /data/message.txt; sleep 3600'

    volumeMounts:
    - name: storage
      mountPath: /data

  volumes:
  - name: storage
    persistentVolumeClaim:
      claimName: my-pvc
```

Apply:

```bash
kubectl apply -f pvc-pod.yaml
```

Check:

```bash
kubectl exec -it pvc-demo -- cat /data/message.txt
```

Output:

```text
First Pod: Tue Jun 2 10:40:30 UTC 2026
```

---

## Delete Pod

```bash
kubectl delete pod pvc-demo
```

---

## Modify Manifest

```yaml
args:
- 'echo "Second Pod: $(date)" >> /data/message.txt; sleep 3600'
```

Apply again:

```bash
kubectl apply -f pvc-pod.yaml
```

Check:

```bash
kubectl exec -it pvc-demo -- cat /data/message.txt
```

Output:

```text
First Pod: Tue Jun 2 10:40:30 UTC 2026
Second Pod: Tue Jun 2 10:44:58 UTC 2026
```

---

## Why Did Data Persist?

Because:

```text
Pod Deleted
    ↓
PVC Remained
    ↓
PV Remained
    ↓
Data Remained
```

Unlike:

```text
emptyDir
    ↓
Pod Deleted
    ↓
Data Lost
```

---

# Task 5: StorageClass

## What is a StorageClass?

A StorageClass defines:

* Provisioner
* Reclaim Policy
* Binding Mode

Used for dynamic provisioning.

---

Your Cluster:

```bash
kubectl get storageclass
```

Output:

```text
standard (default)
```

---

Describe:

```bash
kubectl describe storageclass standard
```

Output:

```text
Provisioner: rancher.io/local-path
ReclaimPolicy: Delete
VolumeBindingMode: WaitForFirstConsumer
```

---

## Default StorageClass

```text
standard
```

---

## Provisioner

```text
rancher.io/local-path
```

Responsible for creating PVs automatically.

---

## Reclaim Policy

```text
Delete
```

Meaning:

```text
PVC Deleted
    ↓
PV Deleted
```

---

## Volume Binding Mode

```text
WaitForFirstConsumer
```

Meaning:

PVC alone is not enough.

Kubernetes waits until a Pod uses it.

Then storage gets provisioned.

---

# Task 6: Dynamic Provisioning

## PVC

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: dynamic-pvc

spec:
  storageClassName: standard

  accessModes:
    - ReadWriteOnce

  resources:
    requests:
      storage: 500Mi
```

Apply:

```bash
kubectl apply -f dynamic-pvc.yaml
```

Initially:

```text
Pending
```

---

Why?

Because:

```text
WaitForFirstConsumer
```

No Pod yet.

---

## Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dynamic-pod

spec:
  containers:
  - name: app
    image: busybox
    command: ["/bin/sh", "-c"]
    args:
    - 'echo "Dynamic PVC Test: $(date)" >> /data/message.txt; sleep 3600'

    volumeMounts:
    - name: storage
      mountPath: /data

  volumes:
  - name: storage
    persistentVolumeClaim:
      claimName: dynamic-pvc
```

Apply:

```bash
kubectl apply -f dynamic-pod.yaml
```

---

Now:

```bash
kubectl get pvc
```

Output:

```text
dynamic-pvc Bound
```

---

```bash
kubectl get pv
```

Output:

```text
manual-pv

pvc-ca95c210-6371-4036-ae08-26e4c8166f82
```

---

## What Happened?

StorageClass automatically:

1. Created PV
2. Bound PV
3. Attached storage

Developer only created PVC.

---

## Static vs Dynamic Provisioning

### Static

```text
Admin creates PV
      ↓
Developer creates PVC
      ↓
Bound
```

### Dynamic

```text
Developer creates PVC
      ↓
StorageClass creates PV
      ↓
Bound
```

---

# Task 7: Cleanup

Delete Pods:

```bash
kubectl delete pod pvc-demo
kubectl delete pod dynamic-pod
kubectl delete pod emptydir-demo
```

---

Delete PVCs:

```bash
kubectl delete pvc my-pvc
kubectl delete pvc dynamic-pvc
```

---

Check PVs:

```bash
kubectl get pv
```

Output:

```text
manual-pv Released
```

Dynamic PV disappeared.

---

Why?

### Dynamic PV

```text
Reclaim Policy = Delete
```

Automatically removed.

---

### Manual PV

```text
Reclaim Policy = Retain
```

Remained.

Status:

```text
Released
```

---

Delete remaining PV:

```bash
kubectl delete pv manual-pv
```

---

Verify:

```bash
kubectl get pv
```

Output:

```text
No resources found
```

---

# Static vs Dynamic Provisioning Comparison

| Feature             | Static    | Dynamic         |
| ------------------- | --------- | --------------- |
| PV Creation         | Manual    | Automatic       |
| Uses StorageClass   | No        | Yes             |
| Admin Work          | High      | Low             |
| Scalability         | Limited   | High            |
| Production Friendly | Less      | More            |
| Example             | manual-pv | pvc-ca95c210... |

---

# PV vs PVC

| Feature    | PV                 | PVC             |
| ---------- | ------------------ | --------------- |
| Meaning    | Actual Storage     | Storage Request |
| Scope      | Cluster-wide       | Namespaced      |
| Created By | Admin/StorageClass | Developer       |
| Example    | manual-pv          | my-pvc          |

---

# Access Modes

| Mode | Meaning                  |
| ---- | ------------------------ |
| RWO  | Read-write by one node   |
| ROX  | Read-only by many nodes  |
| RWX  | Read-write by many nodes |

---

# Reclaim Policies

| Policy | Behavior                 |
| ------ | ------------------------ |
| Delete | PV removed automatically |
| Retain | PV preserved             |

---

# B. Interview Notes (Questions & Answers)

### What is a Persistent Volume?

A Persistent Volume (PV) is a cluster-wide storage resource that exists independently of Pods.

---

### What is a Persistent Volume Claim?

A PVC is a request for storage made by a user or application.

---

### Difference between PV and PVC?

PV provides storage.

PVC consumes storage.

---

### Why use PVC instead of mounting PV directly?

PVC abstracts storage details and allows applications to remain portable.

---

### What happens to emptyDir when Pod is deleted?

The volume and all data are deleted.

---

### What is StorageClass?

A StorageClass defines how storage should be dynamically provisioned.

---

### What is Dynamic Provisioning?

Automatic creation of PVs when a PVC requests storage.

---

### What is Static Provisioning?

Manual creation of PVs before PVCs are created.

---

### What is RWO?

ReadWriteOnce.

One node can read/write.

---

### What is RWX?

ReadWriteMany.

Multiple nodes can read/write.

---

### What is ROX?

ReadOnlyMany.

Multiple nodes can read.

No writes.

---

### What is a reclaim policy?

Determines what happens to a PV after PVC deletion.

---

### Difference between Retain and Delete?

Retain:

```text
PVC Deleted
PV Remains
```

Delete:

```text
PVC Deleted
PV Deleted
```

---

### Explain PV lifecycle.

```text
Available
 ↓
Bound
 ↓
Released
```

---

### What is WaitForFirstConsumer?

Storage is provisioned only when a Pod actually uses the PVC.

---

### Why was dynamic-pvc initially Pending?

Because the StorageClass used:

```text
WaitForFirstConsumer
```

and no Pod was consuming it.

---

# C. 1-Minute Revision Sheet

```text
Containers = Ephemeral

emptyDir
---------
Pod deleted → Data lost

PV
--
Actual storage

PVC
---
Request for storage

Relationship
------------
Pod
 ↓
PVC
 ↓
PV
 ↓
Disk

Access Modes
------------
RWO = ReadWriteOnce
ROX = ReadOnlyMany
RWX = ReadWriteMany

PV Lifecycle
------------
Available
 ↓
Bound
 ↓
Released

Reclaim Policies
----------------
Delete → PV removed
Retain → PV kept

StorageClass
------------
Defines dynamic provisioning

Your Cluster
------------
Default SC = standard
Provisioner = rancher.io/local-path
ReclaimPolicy = Delete
VolumeBindingMode = WaitForFirstConsumer

Static Provisioning
-------------------
Create PV manually
Create PVC
Bind

Dynamic Provisioning
--------------------
Create PVC
StorageClass creates PV
Bind

Key Learning
------------
emptyDir → temporary
PV + PVC → persistent
Dynamic provisioning → automatic PV creation
```

***

You covered **about 95–98% of everything we discussed and did practically**. The notes are strong enough for revision and interview prep. However, for a true "master reference," I'd add these few items that came up during troubleshooting and learning:

---

# 1. Why Your PVC Stayed Pending Initially (Important Real-World Scenario)

You actually hit this issue during Task 3.

### What happened?

PVC:

```yaml
storageClassName: standard
```

PV:

```yaml
No StorageClass
```

Result:

```text
PVC = Pending
PV  = Available
```

### Why?

Kubernetes couldn't match:

```text
PVC wants StorageClass = standard
PV has StorageClass = none
```

### Fix

Use:

```yaml
storageClassName: ""
```

in PVC.

Then:

```text
PVC Bound → manual-pv
```

### Interview Question

**Q: PVC is stuck in Pending. What do you check?**

Answer:

* Capacity
* Access Modes
* StorageClass
* Available PVs
* Events (`kubectl describe pvc`)

---

# 2. The YAML Errors You Faced

This is valuable because interviewers often ask about Kubernetes troubleshooting.

### Error 1

```text
yaml: line 13: could not find expected ':'
```

Cause:

Incorrect YAML indentation/syntax.

Fix:

Validate indentation carefully.

---

### Error 2

```text
cannot unmarshal object into Go struct field Container.spec.containers.args of type string
```

Cause:

`args:` was written incorrectly.

Wrong:

```yaml
args:
  command: something
```

Correct:

```yaml
args:
- 'echo "Hello"; sleep 3600'
```

### Learning

Kubernetes YAML is extremely indentation-sensitive.

---

# 3. Why Dynamic PVC Stayed Pending

This happened in Task 6.

Initially:

```text
dynamic-pvc = Pending
```

Even though StorageClass existed.

### Why?

StorageClass:

```text
WaitForFirstConsumer
```

means:

```text
Create PVC
     ↓
Still Pending
     ↓
Create Pod
     ↓
PV Created
     ↓
PVC Bound
```

This is a common interview question.

---

# 4. Cluster Scope vs Namespace Scope

You mentioned it briefly in hints but it's worth keeping.

| Resource     | Scope        |
| ------------ | ------------ |
| PV           | Cluster-wide |
| PVC          | Namespaced   |
| Pod          | Namespaced   |
| Deployment   | Namespaced   |
| StorageClass | Cluster-wide |

Interviewers ask this frequently.

---

# 5. Complete Storage Flow

Add this diagram:

```text
Developer
    ↓
Create PVC
    ↓
StorageClass (optional)
    ↓
PV
    ↓
Physical Storage
    ↓
Pod mounts PVC
```

This is the complete picture.

---

# 6. Static Provisioning Workflow

```text
Admin creates PV
        ↓
Developer creates PVC
        ↓
PVC binds to PV
        ↓
Pod uses PVC
```

---

# 7. Dynamic Provisioning Workflow

```text
Developer creates PVC
        ↓
StorageClass
        ↓
PV automatically created
        ↓
PVC bound
        ↓
Pod uses PVC
```

---

# 8. Why hostPath is Not Production Ready

We discussed it but briefly.

### Problem

```text
Node A
   ↓
Data stored
```

Pod moves:

```text
Node B
```

Now:

```text
Data unavailable
```

Because storage exists only on Node A.

Production usually uses:

* EBS
* EFS
* NFS
* Ceph
* Longhorn
* Azure Disk
* GCP Persistent Disk

---


# Additional Notes & Troubleshooting (Important for Interviews)

---

# PVC Pending Troubleshooting

One of the most common issues when working with Persistent Volumes is a PVC that remains in the **Pending** state.

## Scenario We Faced

Initially:

```text
PVC = Pending
PV  = Available
```

Even though both resources existed.

### PVC

```yaml
storageClassName: standard
```

### PV

```yaml
No StorageClass defined
```

Result:

```text
PVC wants StorageClass = standard
PV has StorageClass = none
```

Kubernetes could not match them.

---

## Fix

Disable dynamic provisioning for the PVC:

```yaml
storageClassName: ""
```

Example:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  storageClassName: ""
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 500Mi
```

After applying:

```text
PVC = Bound
PV  = Bound
```

---

## Common Reasons for PVC Pending

| Cause                 | Explanation                                           |
| --------------------- | ----------------------------------------------------- |
| Capacity mismatch     | PVC requests more storage than PV provides            |
| Access mode mismatch  | PVC and PV access modes do not match                  |
| StorageClass mismatch | PVC requests a StorageClass that the PV does not have |
| No available PV       | No matching PV exists                                 |
| WaitForFirstConsumer  | Storage waits until a Pod uses the PVC                |

---

## Troubleshooting PVC Pending

Symptoms:
```bash
kubectl get pvc
```
```bash
STATUS = Pending
```
Check:

Check PVC
```bash
kubectl describe pvc my-pvc
```
Check PV
```bash
kubectl get pv
```
## Verify
- Capacity matches
- Access mode matches
- StorageClass matches

## Real Example From Your Lab

PVC stayed Pending because:
```bash
storageClassName: standard
```
was present.

PV had:
```bash
storageClassName: ""
```
Therefore Kubernetes couldn't bind them.

Fix:
```bash
storageClassName: ""
```
inside PVC.

Result:
```bash
PVC -> Bound
PV -> Bound
```


---

## Useful Troubleshooting Commands

Check PVC details:

```bash
kubectl describe pvc my-pvc
```

Check PV details:

```bash
kubectl describe pv manual-pv
```

Check StorageClasses:

```bash
kubectl get storageclass
```

Check Events:

```bash
kubectl describe pvc my-pvc
```

Events usually explain why the PVC is Pending.

---

# YAML Errors and Fixes

During Task 4, we encountered YAML errors.

---

## Error 1

```text
yaml: line 13: could not find expected ':'
```

### Cause

Invalid YAML syntax or indentation.

Example:

```yaml
args:
  echo hello
```

YAML expected a proper key-value structure.

---

### Fix

Use proper YAML formatting:

```yaml
args:
  - echo hello
```

---

## Error 2

```text
cannot unmarshal object into Go struct field Container.spec.containers.args of type string
```

### Cause

The `args` field was incorrectly formatted.

Wrong:

```yaml
args:
  command:
    echo hello
```

Kubernetes expects a string list, not an object.

---

### Correct Format

```yaml
args:
  - 'echo "Hello"; sleep 3600'
```

---

## YAML Best Practices

* Use spaces, not tabs
* Maintain proper indentation
* Validate YAML before applying
* Use `kubectl apply --dry-run=client -f file.yaml`

Example:

```bash
kubectl apply --dry-run=client -f pvc-pod.yaml
```

---

# Cluster-Scoped vs Namespaced Resources

Not all Kubernetes resources behave the same way.

Some belong to the entire cluster, while others belong to a namespace.

| Resource                    | Scope        |
| --------------------------- | ------------ |
| PersistentVolume (PV)       | Cluster-wide |
| StorageClass                | Cluster-wide |
| Node                        | Cluster-wide |
| Namespace                   | Cluster-wide |
| PersistentVolumeClaim (PVC) | Namespaced   |
| Pod                         | Namespaced   |
| Deployment                  | Namespaced   |
| Service                     | Namespaced   |

---

## Example

PVC:

```text
default/my-pvc
```

Contains a namespace.

PV:

```text
manual-pv
```

No namespace because it is cluster-scoped.

---

# Complete Kubernetes Storage Flow

Understanding the complete flow is important for interviews.

```text
Developer
    │
    ▼
Creates PVC
    │
    ▼
StorageClass (optional)
    │
    ▼
PV
    │
    ▼
Physical Storage
    │
    ▼
Pod Mounts PVC
```

---

## Real Example from This Lab

```text
pvc-demo
    │
    ▼
my-pvc
    │
    ▼
manual-pv
    │
    ▼
/tmp/k8s-pv-data
```

---

# Static Provisioning Workflow

Static provisioning means an administrator creates the storage first.

```text
Admin Creates PV
        │
        ▼
Developer Creates PVC
        │
        ▼
PVC Binds to PV
        │
        ▼
Pod Uses PVC
```

---

## Example from Lab

```text
manual-pv
     │
     ▼
my-pvc
     │
     ▼
pvc-demo
```

---

# Dynamic Provisioning Workflow

Dynamic provisioning means Kubernetes automatically creates storage when needed.

```text
Developer Creates PVC
        │
        ▼
StorageClass
        │
        ▼
PV Automatically Created
        │
        ▼
PVC Bound
        │
        ▼
Pod Uses Storage
```

---

## Example from Lab

```text
dynamic-pvc
      │
      ▼
StorageClass (standard)
      │
      ▼
pvc-ca95c210-6371-4036-ae08-26e4c8166f82
      │
      ▼
dynamic-pod
```

---

# Why hostPath Is Not Production Ready

For learning purposes we used:

## hostPath Limitation

```yaml
hostPath:
  path: /tmp/k8s-pv-data
```

This stores data directly on the Kubernetes node.

Good for:
```bash
Learning
Testing
Local Clusters
```
Not recommended for:
```bash
Production
Multi-node clusters
```
Reason:
```bash
Data exists only on one node.
```
If Pod moves to another node:
```bash
Data unavailable
```
---

## Problem Scenario

Node A:

```text
/tmp/k8s-pv-data
```

contains application data.

```text
Node A
   │
   ▼
Data Stored
```

---

Pod gets rescheduled:

```text
Node B
```

Now:

```text
Pod Running
   │
   ▼
No Data
```

because the storage exists only on Node A.

---

## Production Storage Options

Common production-grade storage solutions:

* AWS EBS
* AWS EFS
* Azure Disk
* Azure Files
* Google Persistent Disk
* NFS
* Ceph
* Longhorn
* OpenEBS

These allow data to remain available even when Pods move between nodes.

---

## Common Storage Backends

| Storage Type        | Supports                  |
| ------------------- | ------------------------- |
| hostPath            | Learning only             |
| NFS                 | Shared storage            |
| AWS EBS             | Single-node block storage |
| AWS EFS             | Shared RWX storage        |
| Azure Disk          | Block storage             |
| Azure Files         | Shared storage            |
| GCE Persistent Disk | GCP storage               |

---

## Volume Expansion

Some StorageClasses allow expansion.

Example:
```bash
allowVolumeExpansion: true
```
Then:
```bash
kubectl edit pvc my-pvc
```
Change:
```bash
500Mi
```
to
```bash
1Gi
```
without recreating PVC.

---

# Day 55 Final Learnings

By the end of this lab, we learned:

1. Containers are ephemeral.
2. `emptyDir` storage is temporary.
3. Data inside a Pod is lost when the Pod is deleted.
4. Persistent Volumes provide durable storage.
5. Persistent Volume Claims request storage.
6. Pods consume storage through PVCs.
7. Static provisioning requires manual PV creation.
8. Dynamic provisioning automatically creates PVs.
9. StorageClasses define how storage is provisioned.
10. `ReadWriteOnce`, `ReadOnlyMany`, and `ReadWriteMany` define access patterns.
11. `Retain` and `Delete` define reclaim behavior.
12. PV lifecycle is:

```text
Available
    ↓
Bound
    ↓
Released
```

13. `hostPath` is suitable for labs but not production.
14. PVC Pending issues are commonly caused by:

    * StorageClass mismatch
    * Capacity mismatch
    * Access mode mismatch
15. `WaitForFirstConsumer` delays provisioning until a Pod actually uses the PVC.

---

# Interview Gold Nuggets ⭐

If an interviewer asks:

**"What is the biggest lesson from PV/PVC?"**

Answer:

> Containers are ephemeral and should not store important data locally. Kubernetes solves this by separating storage from Pods using Persistent Volumes and Persistent Volume Claims, allowing data to survive Pod restarts, deletions, and recreations.

## Interview One-Liners
### Quick Facts
#### PV
```bash
Cluster-scoped resource
```
#### PVC
```bash
Namespace-scoped resource
```
#### StorageClass
```bash
Used for dynamic provisioning
```
#### Retain
```bash
Keep PV after PVC deletion
```
#### Delete
```bash
Delete PV after PVC deletion
```
#### RWO
```bash
Read-write by one node
```
#### ROX
```bash
Read-only by many nodes
```
#### RWX
```bash
Read-write by many nodes
```
