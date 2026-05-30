# Day 52 - Kubernetes Deployments

## Create Deployment

```bash
kubectl apply -f nginx-deployment.yaml
```

Creates a Deployment from YAML.

---

## View Deployments

```bash
kubectl get deployments -n dev
```

Shows:

- READY
- UP-TO-DATE
- AVAILABLE

---

## Describe Deployment

```bash
kubectl describe deployment nginx-deployment -n dev
```

Displays:

- replicas
- rollout strategy
- events
- conditions

---

## View ReplicaSets

```bash
kubectl get rs -n dev
```

Shows ReplicaSets managed by Deployment.

---

## View Pods

```bash
kubectl get pods -n dev
```

Lists Deployment-created Pods.

---

# Self-Healing

## Delete Pod

```bash
kubectl delete pod <pod-name> -n dev
```

Example:

```bash
kubectl delete pod nginx-deployment-68cd4c497b-77cgz -n dev
```

Deployment automatically creates replacement Pod.

---

## Watch New Pod

```bash
kubectl get pods -n dev -w
```

Observe new Pod creation.

---

# Scaling

## Scale Up

```bash
kubectl scale deployment nginx-deployment --replicas=5 -n dev
```

Creates additional Pods.

---

## Scale Down

```bash
kubectl scale deployment nginx-deployment --replicas=2 -n dev
```

Terminates extra Pods.

---

## Verify Deployment

```bash
kubectl get deployment nginx-deployment -n dev
```

---

# Rolling Updates

## Update Image

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.25 -n dev
```

Triggers rolling update.

---

## Watch Rollout

```bash
kubectl rollout status deployment/nginx-deployment -n dev
```

Shows rollout progress.

---

## Rollout History

```bash
kubectl rollout history deployment/nginx-deployment -n dev
```

Displays revisions.

---

## Rollback

```bash
kubectl rollout undo deployment/nginx-deployment -n dev
```

Restores previous version.

---

## Verify Image

```bash
kubectl describe deployment nginx-deployment -n dev | grep Image
```

Output:

```text
Image: nginx:1.24
```

---

# Cleanup

## Delete Deployment

```bash
kubectl delete deployment nginx-deployment -n dev
```

---

## Delete Pods

```bash
kubectl delete pod nginx-dev -n dev
kubectl delete pod nginx-staging -n staging
```

---

## Delete Namespaces

```bash
kubectl delete namespace dev staging production
```

Deletes all resources inside namespaces.

---

# Verification

## List Namespaces

```bash
kubectl get namespaces
```

---

## List All Pods

```bash
kubectl get pods -A
```

Verify resources are removed.

---

# 🚀 Kubernetes Deployments — Day 52 Commands Sheet

---

# 📦 Deployments

## List Deployments

```bash
kubectl get deployments -n dev
```

### Explanation

Displays all Deployments running inside the `dev` namespace.

### Shows

* READY
* UP-TO-DATE
* AVAILABLE
* AGE

### Example Output

```text
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           5m
```

---

## Get Specific Deployment

```bash
kubectl get deployment nginx-deployment -n dev
```

### Explanation

Displays information about a specific Deployment.

---

## Describe Deployment

```bash
kubectl describe deployment nginx-deployment -n dev
```

### Explanation

Displays complete Deployment information.

### Includes

* desired replicas
* available replicas
* rollout strategy
* events
* image version
* conditions

### Common Usage

```text
Used to troubleshoot Deployment issues.
```

---

# 📑 ReplicaSets

## List ReplicaSets

```bash
kubectl get rs -n dev
```

### Explanation

Displays ReplicaSets created and managed by Deployments.

### ReplicaSet Purpose

```text
Ensures the desired number of Pod replicas
are always running.
```

### Example Output

```text
NAME                          DESIRED   CURRENT   READY
nginx-deployment-68cd4c497b   3         3         3
```

---

# 📦 Deployment Pods

## List Pods

```bash
kubectl get pods -n dev
```

### Explanation

Shows Pods created by the Deployment.

---

## Watch Pods in Real-Time

```bash
kubectl get pods -n dev -w
```

### Explanation

Continuously watches Pod changes.

### Useful For

* scaling
* rollouts
* self-healing
* debugging

---

# 🔄 Self-Healing

## Delete Pod

```bash
kubectl delete pod <pod-name> -n dev
```

### Example

```bash
kubectl delete pod nginx-deployment-68cd4c497b-77cgz -n dev
```

### Explanation

Deletes a Deployment-managed Pod.

### What Happens Internally

```text
Pod deleted
      ↓
Replica count decreases
      ↓
Deployment detects mismatch
      ↓
ReplicaSet creates replacement Pod
```

### Key Observation

```text
Replacement Pod gets a different name.
```

---

# 📈 Scaling Deployments

## Scale Up

```bash
kubectl scale deployment nginx-deployment --replicas=5 -n dev
```

### Explanation

Increases Deployment replicas from current count to 5.

### Result

```text
Kubernetes creates additional Pods.
```

---

## Scale Down

```bash
kubectl scale deployment nginx-deployment --replicas=2 -n dev
```

### Explanation

Reduces Deployment replicas to 2.

### Result

```text
Extra Pods enter Terminating state
and are removed.
```

### Internal Flow

```text
Desired Replicas = 2
        ↓
Current Replicas = 5
        ↓
ReplicaSet removes 3 Pods
```

---

# 🚀 Rolling Updates

## Update Container Image

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.25 -n dev
```

### Explanation

Updates Deployment container image.

### Result

```text
Triggers Rolling Update.
```

---

## Watch Rollout Progress

```bash
kubectl rollout status deployment/nginx-deployment -n dev
```

### Explanation

Displays rollout progress until update completes.

### Example Output

```text
deployment "nginx-deployment" successfully rolled out
```

---

## View Rollout History

```bash
kubectl rollout history deployment/nginx-deployment -n dev
```

### Explanation

Shows Deployment revisions.

### Example Output

```text
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

---

# ⏪ Rollback

## Undo Previous Rollout

```bash
kubectl rollout undo deployment/nginx-deployment -n dev
```

### Explanation

Rolls Deployment back to previous revision.

### Example

```text
nginx:1.25
      ↓
Rollback
      ↓
nginx:1.24
```

---

## Verify Current Image

```bash
kubectl describe deployment nginx-deployment -n dev | grep Image
```

### Example Output

```text
Image: nginx:1.24
```

### Explanation

Confirms image version after rollback.

---

# 📊 Events

## View Namespace Events

```bash
kubectl get events -n dev --sort-by=.metadata.creationTimestamp
```

### Explanation

Displays events in chronological order.

### Useful For

* Pod creation
* scheduling
* image pulling
* scaling
* rollouts

### Common Events

```text
Scheduled
Pulling
Pulled
Created
Started
ScalingReplicaSet
SuccessfulCreate
```

---

# 🗑️ Cleanup

## Delete Deployment

```bash
kubectl delete deployment nginx-deployment -n dev
```

### Explanation

Deletes Deployment and its ReplicaSets/Pods.

---

## Delete Namespace

```bash
kubectl delete namespace dev staging production
```

### Explanation

Deletes namespaces and all resources inside them.

### Warning

```text
Namespace deletion removes everything
inside that namespace.
```

---

# 🌍 Cluster-Wide Verification

## View All Pods

```bash
kubectl get pods -A
```

### Explanation

Displays Pods across every namespace.

### Useful For

* cleanup verification
* cluster inspection
* troubleshooting

---

# 🧠 Key Deployment Insights

## Insight 1

```text
Deployment manages ReplicaSets.
ReplicaSets manage Pods.
```

---

## Insight 2

```text
Pods are disposable.
Deployments provide durability.
```

---

## Insight 3

```text
Rolling Updates provide
zero-downtime application upgrades.
```

---

## Insight 4

```text
Rollback allows quick recovery
from failed deployments.
```

---

# 🔥 Deployment Mental Model

```text
Deployment
      ↓
ReplicaSet
      ↓
Pods
      ↓
Containers
```

