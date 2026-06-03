# Day 56 – StatefulSets Commands Cheat Sheet

---

# Task 1: Understand the Problem

## Create Deployment

```bash
kubectl apply -f deployment.yaml
```

or

```bash
kubectl create deployment nginx-deploy --image=nginx --replicas=3
```

## Check Pods

```bash
kubectl get pods
```

## Watch Pods

```bash
kubectl get pods -w
```

## Delete One Pod

```bash
kubectl delete pod <pod-name>
```

Example:

```bash
kubectl delete pod nginx-deploy-5cf8dc6bc5-f5fpb
```

## Verify New Pod Name

```bash
kubectl get pods
```

## Delete Deployment

```bash
kubectl delete deployment nginx-deploy
```

---

# Task 2: Create Headless Service

## Apply Headless Service

```bash
kubectl apply -f headless-service.yaml
```

## Verify Service

```bash
kubectl get svc
```

or

```bash
kubectl get service
```

## Describe Service

```bash
kubectl describe svc web
```

---

# Task 3: Create StatefulSet

## Apply StatefulSet

```bash
kubectl apply -f statefulset.yaml
```

## Watch StatefulSet Pods

```bash
kubectl get pods -l app=web -w
```

## View Pods

```bash
kubectl get pods -l app=web
```

## Check StatefulSets

```bash
kubectl get sts
```

## Describe StatefulSet

```bash
kubectl describe sts web
```

## Check PVCs

```bash
kubectl get pvc
```

## Check PVs

```bash
kubectl get pv
```

---

# Task 4: Stable Network Identity

## Get Pod IPs

```bash
kubectl get pods -o wide
```

## Launch BusyBox Pod

```bash
kubectl run -it --rm dns-test --image=busybox -- sh
```

Inside BusyBox:

## DNS Lookup web-0

```bash
nslookup web-0.web.default.svc.cluster.local
```

## DNS Lookup web-1

```bash
nslookup web-1.web.default.svc.cluster.local
```

## DNS Lookup web-2

```bash
nslookup web-2.web.default.svc.cluster.local
```

## Exit BusyBox

```bash
exit
```

---

# Task 5: Stable Storage

## Write Data

```bash
kubectl exec web-0 -- sh -c "echo 'Data from web-0' > /usr/share/nginx/html/index.html"
```

## Verify Data

```bash
kubectl exec web-0 -- cat /usr/share/nginx/html/index.html
```

## Delete Pod

```bash
kubectl delete pod web-0
```

## Check Pod Recreation

```bash
kubectl get pods
```

or

```bash
kubectl get pods -w
```

## Verify Data After Recreation

```bash
kubectl exec web-0 -- cat /usr/share/nginx/html/index.html
```

---

# Task 6: Ordered Scaling

## Scale Up

```bash
kubectl scale statefulset web --replicas=5
```

## Watch Scaling

```bash
kubectl get pods -l app=web -w
```

## Scale Down

```bash
kubectl scale statefulset web --replicas=3
```

## Check Remaining Pods

```bash
kubectl get pods
```

## Verify PVCs

```bash
kubectl get pvc
```

---

# Task 7: Cleanup

## Delete StatefulSet

```bash
kubectl delete statefulset web
```

## Delete Headless Service

```bash
kubectl delete service web
```

## Verify PVCs Still Exist

```bash
kubectl get pvc
```

## Delete PVCs Individually

```bash
kubectl delete pvc web-data-web-0 web-data-web-1 web-data-web-2 web-data-web-3 web-data-web-4
```

## Verify Cleanup

```bash
kubectl get pvc
```

```bash
kubectl get pods
```

```bash
kubectl get svc
```

---

# Useful Troubleshooting Commands

## Describe Pod

```bash
kubectl describe pod web-0
```

## Describe PVC

```bash
kubectl describe pvc web-data-web-0
```

## Describe PV

```bash
kubectl describe pv
```

## Check Events

```bash
kubectl get events
```

## Check Storage Classes

```bash
kubectl get storageclass
```

## Check Node Resources

```bash
kubectl describe nodes
```

---

# StatefulSet Management Commands

## Get StatefulSets

```bash
kubectl get sts
```

## Describe StatefulSet

```bash
kubectl describe sts web
```

## Rollout Status

```bash
kubectl rollout status statefulset/web
```

## Rollout History

```bash
kubectl rollout history statefulset/web
```

## Undo Rollout

```bash
kubectl rollout undo statefulset/web
```

---

# Most Important Commands (Interview Favorites)

```bash
kubectl get sts

kubectl get pods -l app=web -w

kubectl get pvc

kubectl get pods -o wide

kubectl scale statefulset web --replicas=5

kubectl delete pod web-0

kubectl exec web-0 -- cat /usr/share/nginx/html/index.html

kubectl run -it --rm dns-test --image=busybox -- sh

nslookup web-0.web.default.svc.cluster.local

kubectl delete statefulset web
```

## One-Line Summary

```text
Create Deployment → Observe Random Pods →
Create Headless Service →
Create StatefulSet →
Verify DNS →
Verify Storage Persistence →
Scale Up/Down →
Cleanup PVCs Manually
```

***





```bash
# StatefulSets
kubectl get sts
kubectl describe sts web

# Apply Resources
kubectl apply -f headless-service.yaml
kubectl apply -f statefulset.yaml

# Watch StatefulSet Pods
kubectl get pods -l app=web -w

# View Pods
kubectl get pods
kubectl get pods -o wide

# Check Storage
kubectl get pvc
kubectl describe pvc web-data-web-0

# Scale StatefulSet
kubectl scale statefulset web --replicas=5
kubectl scale statefulset web --replicas=3

# StatefulSet Rollout
kubectl rollout status statefulset/web

# DNS Testing
kubectl run -it --rm dns-test --image=busybox -- sh

# Inside BusyBox
nslookup web-0.web.default.svc.cluster.local
nslookup web-1.web.default.svc.cluster.local
nslookup web-2.web.default.svc.cluster.local

# Verify Persistent Storage
kubectl exec web-0 -- sh -c "echo 'Data from web-0' > /usr/share/nginx/html/index.html"

kubectl exec web-0 -- cat /usr/share/nginx/html/index.html

# Delete Pod and Verify Persistence
kubectl delete pod web-0

kubectl exec web-0 -- cat /usr/share/nginx/html/index.html

# Cleanup
kubectl delete statefulset web
kubectl delete svc web

# Delete PVCs Manually
kubectl delete pvc web-data-web-0 web-data-web-1 web-data-web-2 web-data-web-3 web-data-web-4
```

---

## If you want a very short interview revision sheet

```bash
kubectl get sts
kubectl describe sts web
kubectl get pods -l app=web -w
kubectl get pvc
kubectl get pods -o wide

kubectl scale sts web --replicas=5
kubectl scale sts web --replicas=3

kubectl delete pod web-0

kubectl exec web-0 -- cat /usr/share/nginx/html/index.html

kubectl run -it --rm dns-test --image=busybox -- sh
nslookup web-0.web.default.svc.cluster.local

kubectl delete statefulset web
kubectl delete svc web
kubectl delete pvc <pvc-name>
```



```bash
kubectl apply -f statefulset.yaml
kubectl apply -f headless-service.yaml

kubectl get pvc

kubectl delete pod web-0

kubectl exec web-0 -- cat /usr/share/nginx/html/index.html

kubectl scale statefulset web --replicas=3
```

These are important because they demonstrate the **three core StatefulSet concepts**:

1. **Stable Identity** → `nslookup web-0...`
2. **Persistent Storage** → `kubectl exec ...`, `kubectl delete pod web-0`
3. **Ordered Scaling** → `kubectl scale ...`

