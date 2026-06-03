# StatefulSets
kubectl get sts

# Watch StatefulSet Pods
kubectl get pods -l app=web -w

# Check PVCs
kubectl get pvc

# Scale StatefulSet
kubectl scale statefulset web --replicas=5

# Delete StatefulSet
kubectl delete statefulset web

# Delete Headless Service
kubectl delete svc web

# DNS Testing
kubectl run -it --rm dns-test --image=busybox -- sh

# Inside BusyBox
nslookup web-0.web.default.svc.cluster.local

# Check Pod IPs
kubectl get pods -o wide
