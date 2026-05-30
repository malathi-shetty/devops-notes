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
