Yes. Your current version has **a lot of duplication** because the same commands appear 2–3 times:

* `kubectl get deployments -n dev`
* `kubectl get deployment nginx-deployment -n dev`
* `kubectl get rs -n dev`
* `kubectl get pods -n dev -w`
* `kubectl rollout status`
* `kubectl rollout history`
* `kubectl describe deployment ... | grep Image`

are all repeated later.

For GitHub notes, it's cleaner to keep **one command → one explanation**.

---

# Day 52 - Kubernetes Deployments

## Create Deployment

```bash
kubectl apply -f nginx-deployment.yaml
```

### Explanation

Creates a Deployment from the YAML manifest.

### Internal Flow

```text
Deployment
     ↓
ReplicaSet
     ↓
Pods
```

---

# 📦 Deployments

## List Deployments

```bash
kubectl get deployments -n dev
```

### Explanation

Displays all Deployments running in the `dev` namespace.

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

Displays details for a specific Deployment.

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
* image version
* events
* conditions

### Common Usage

```text
Used to troubleshoot Deployment issues.
```

---

## View Deployment YAML

```bash
kubectl get deployment nginx-deployment -n dev -o yaml
```

### Explanation

Displays the live Deployment manifest stored in Kubernetes.

### Useful For

* debugging
* backup
* understanding Deployment structure
* comparing desired vs actual state

---

## Explain Deployment Schema

```bash
kubectl explain deployment
```

### Explanation

Shows Deployment API schema and field information.

### Examples

```bash
kubectl explain deployment.spec
kubectl explain deployment.spec.template
```

---

# 📑 ReplicaSets

## List ReplicaSets

```bash
kubectl get rs -n dev
```

### Explanation

Displays ReplicaSets managed by Deployments.

### Purpose

```text
ReplicaSets ensure the desired number
of Pod replicas are always running.
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

Continuously watches Pod status changes.

### Useful For

* self-healing
* scaling
* rollouts
* debugging

---

# 🔄 Self-Healing

## Delete a Deployment Pod

```bash
kubectl delete pod <pod-name> -n dev
```

### Example

```bash
kubectl delete pod nginx-deployment-68cd4c497b-77cgz -n dev
```

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

Increases the number of Pod replicas.

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

Reduces the number of Pod replicas.

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

## Update Deployment Image

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.25 -n dev
```

### Explanation

Updates the container image used by the Deployment.

### Result

```text
Triggers a Rolling Update.
```

---

## Watch Rollout Status

```bash
kubectl rollout status deployment/nginx-deployment -n dev
```

### Explanation

Displays rollout progress until the update completes.

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

Shows Deployment revision history.

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

Rolls the Deployment back to the previous revision.

### Example

```text
nginx:1.25
      ↓
Rollback
      ↓
nginx:1.24
```

---

## Verify Active Image

```bash
kubectl describe deployment nginx-deployment -n dev | grep Image
```

### Example Output

```text
Image: nginx:1.24
```

### Explanation

Confirms the image version currently used by the Deployment.

---

# 📊 Events

## View Namespace Events

```bash
kubectl get events -n dev --sort-by=.metadata.creationTimestamp
```

### Explanation

Displays namespace events in chronological order.

### Useful For

* Pod creation
* scheduling
* image pulling
* scaling
* rollout troubleshooting

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

Deletes the Deployment and its managed ReplicaSets and Pods.

---

## Delete Application Pods

```bash
kubectl delete pod nginx-dev -n dev
kubectl delete pod nginx-staging -n staging
```

### Explanation

Deletes standalone Pods created for testing.

---

## Delete Namespaces

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

# 🌍 Verification

## List Namespaces

```bash
kubectl get namespaces
```

### Explanation

Verifies namespace cleanup.

---

## View All Pods

```bash
kubectl get pods -A
```

### Explanation

Displays Pods across all namespaces.

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

This version preserves all Day 52 learning and commands, removes duplicates, and keeps the same structure/style as your Day 50–51 notes for GitHub.
