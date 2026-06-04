# Day 57 — Complete Commands Cheat Sheet

## Task 1 — Resource Requests & Limits

### Create Manifest

```bash
nano resource-demo.yaml
```

### Apply

```bash
kubectl apply -f resource-demo.yaml
```

### Watch Pod

```bash
kubectl get pods -w
```

### Verify Requests, Limits and QoS

```bash
kubectl describe pod resource-demo
```

### Useful Filters

```bash
kubectl describe pod resource-demo | grep -A5 Limits
```

```bash
kubectl describe pod resource-demo | grep -A5 Requests
```

```bash
kubectl describe pod resource-demo | grep QoS
```

---

## Task 2 — OOMKilled

### Create Manifest

```bash
nano oom-demo.yaml
```

### Apply

```bash
kubectl apply -f oom-demo.yaml
```

### Watch Pod

```bash
kubectl get pod oom-demo -w
```

### Check Status

```bash
kubectl get pod oom-demo
```

### Describe Pod

```bash
kubectl describe pod oom-demo
```

### Check Logs

```bash
kubectl logs oom-demo
```

### Find Exit Code

```bash
kubectl describe pod oom-demo | grep -A10 "Last State"
```

### Delete and Recreate (what you actually did)

```bash
kubectl delete pod oom-demo
```

```bash
kubectl apply -f oom-demo.yaml
```

---

## Task 3 — Pending Pod (Huge Resource Request)

### Create Manifest

```bash
nano huge-request.yaml
```

### Apply

```bash
kubectl apply -f huge-request.yaml
```

### Check Status

```bash
kubectl get pod huge-request
```

### Describe Pod

```bash
kubectl describe pod huge-request
```

### View Scheduler Events Only

```bash
kubectl describe pod huge-request | tail -20
```

### Expected Event

```text
Insufficient cpu
Insufficient memory
```

---

## Task 4 — Liveness Probe

### Create Manifest

```bash
nano liveness-demo.yaml
```

### Apply

```bash
kubectl apply -f liveness-demo.yaml
```

### Watch Restarts

```bash
kubectl get pod liveness-demo -w
```

### Check Restart Count

```bash
kubectl get pod liveness-demo
```

### Describe Pod

```bash
kubectl describe pod liveness-demo
```

### Look for Probe Failures

```bash
kubectl describe pod liveness-demo | grep -A5 Unhealthy
```

### Check Logs

```bash
kubectl logs liveness-demo
```

### Exec into Pod

```bash
kubectl exec -it liveness-demo -- sh
```

### Verify File

```bash
cat /tmp/healthy
```

---

## Task 5 — Readiness Probe

### Create Manifest

```bash
nano readiness-demo.yaml
```

### Apply

```bash
kubectl apply -f readiness-demo.yaml
```

### Watch Pod

```bash
kubectl get pod readiness-demo -w
```

### Expose Service

```bash
kubectl expose pod readiness-demo --port=80 --name=readiness-svc
```

### Check Service

```bash
kubectl get svc
```

### Check Endpoints

```bash
kubectl get endpoints readiness-svc
```

### Verify nginx Files

```bash
kubectl exec readiness-demo -- ls /usr/share/nginx/html
```

### Delete index.html

```bash
kubectl exec readiness-demo -- rm /usr/share/nginx/html/index.html
```

### Verify File Removed

```bash
kubectl exec readiness-demo -- ls /usr/share/nginx/html
```

### Check Ready State

```bash
kubectl get pod readiness-demo
```

### Check Endpoints Again

```bash
kubectl get endpoints readiness-svc
```

### Describe Pod

```bash
kubectl describe pod readiness-demo
```

### Verify Restart Count

```bash
kubectl get pod readiness-demo
```

Expected:

```text
READY = 0/1
ENDPOINTS = <none>
RESTARTS = 0
```

---

## Task 6 — Startup Probe

### Create Manifest

```bash
nano startup-demo.yaml
```

### Apply

```bash
kubectl apply -f startup-demo.yaml
```

### Watch Startup

```bash
kubectl get pod startup-demo -w
```

### Check Status

```bash
kubectl get pod startup-demo
```

### Describe Pod

```bash
kubectl describe pod startup-demo
```

### Look for Startup Probe

```bash
kubectl describe pod startup-demo | grep Startup
```

### Look for Liveness Probe

```bash
kubectl describe pod startup-demo | grep Liveness
```

### View Events

```bash
kubectl describe pod startup-demo
```

### Verify Startup File Inside Pod

```bash
kubectl exec startup-demo -- ls /tmp
```

```bash
kubectl exec startup-demo -- cat /tmp/started
```

---

# General Troubleshooting Commands

## Get All Pods

```bash
kubectl get pods
```

## Watch All Pods

```bash
kubectl get pods -w
```

## Wide Output

```bash
kubectl get pods -o wide
```

## Get Services

```bash
kubectl get svc
```

## Get Endpoints

```bash
kubectl get endpoints
```

## Describe Any Pod

```bash
kubectl describe pod <pod-name>
```

Example:

```bash
kubectl describe pod oom-demo
```

## View Events

```bash
kubectl get events
```

## Sort Events

```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

## Container Logs

```bash
kubectl logs <pod-name>
```

Example:

```bash
kubectl logs oom-demo
```

## Exec Into Container

```bash
kubectl exec -it <pod-name> -- sh
```

Example:

```bash
kubectl exec -it readiness-demo -- sh
```

---

# Task 7 — Cleanup

### Delete Individual Pods

```bash
kubectl delete pod resource-demo
```

```bash
kubectl delete pod oom-demo
```

```bash
kubectl delete pod huge-request
```

```bash
kubectl delete pod liveness-demo
```

```bash
kubectl delete pod readiness-demo
```

```bash
kubectl delete pod startup-demo
```

### Delete Service

```bash
kubectl delete svc readiness-svc
```

### Verify Cleanup

```bash
kubectl get pods
```

```bash
kubectl get svc
```

### Delete Everything at Once (Lab Shortcut)

```bash
kubectl delete pod resource-demo oom-demo huge-request liveness-demo readiness-demo startup-demo
```

```bash
kubectl delete svc readiness-svc
```

or

```bash
kubectl delete -f .
```

(if all manifests are in the same folder and only Day 57 resources exist there)

---

# Most Important Verification Answers

```text
Task 1 → QoS Class = Burstable

Task 2 → Exit Code = 137

Task 3 → Insufficient cpu, Insufficient memory

Task 4 → Restart Count = 13
         Liveness failure = Restart

Task 5 → Restart Count = 0
         Endpoint removed
         Readiness failure ≠ Restart

Task 6 → failureThreshold=2
         → Startup fails
         → Restart loop
         → CrashLoopBackOff
```
