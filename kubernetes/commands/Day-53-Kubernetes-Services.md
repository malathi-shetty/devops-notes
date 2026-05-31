**ALL Kubernetes + system commands used today (Day 53 Services lab)**

---

#  1. Deployment Commands

```bash
kubectl apply -f app-deployment.yaml
kubectl get pods -o wide
kubectl delete -f app-deployment.yaml
```

---

#  2. ClusterIP Service Commands

```bash
kubectl apply -f clusterip-service.yaml
kubectl get services
kubectl get svc
kubectl get endpoints web-app-clusterip
```

### Test inside cluster (BusyBox pod)

```bash
kubectl run test-client --image=busybox:latest --rm -it --restart=Never -- sh

wget -qO- http://web-app-clusterip
wget -qO- http://web-app-clusterip.default.svc.cluster.local
nslookup web-app-clusterip
exit
```

---

#  3. DNS Test Pod

```bash
kubectl run dns-test --image=busybox:latest --rm -it --restart=Never -- sh
```

Inside pod:

```bash
wget -qO- http://web-app-clusterip
nslookup web-app-clusterip
```

---

#  4. NodePort Service Commands

```bash
kubectl apply -f nodeport-service.yaml
kubectl get svc
kubectl get endpoints web-app-nodeport
kubectl get nodes -o wide
```

### External test

```bash
curl http://<NodeIP>:30080
curl http://localhost:30080
```

---

#  5. LoadBalancer Service Commands

```bash
kubectl apply -f loadbalancer-service.yaml
kubectl get svc
kubectl describe service web-app-loadbalancer
```

---

#  6. Service Inspection Commands

```bash
kubectl get services
kubectl get svc -o wide
kubectl describe service web-app-clusterip
kubectl describe service web-app-nodeport
kubectl describe service web-app-loadbalancer
```

---

#  7. Endpoints Commands

```bash
kubectl get endpoints
kubectl get endpoints web-app-nodeport
kubectl describe endpoints web-app-nodeport
```

---

#  8. Debugging / Network Commands (IMPORTANT)

```bash
kubectl port-forward service/web-app-nodeport 8080:80

curl http://localhost:8080
```

---

### Node debugging (you used Docker + exec)

```bash
docker ps
docker exec -it devops-cluster-control-plane bash
```

Inside container:

```bash
curl http://172.18.0.2:30080
curl http://localhost:30080
```

---

#  9. Network / Port Checks

```bash
sudo ss -tulpn | grep 8080
sudo ufw status
```

---

#  10. Final Cleanup Commands

```bash
kubectl delete -f clusterip-service.yaml
kubectl delete -f nodeport-service.yaml
kubectl delete -f loadbalancer-service.yaml
kubectl get pods
kubectl get services
```

