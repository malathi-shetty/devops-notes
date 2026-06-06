
#  COMMANDS USED (END-TO-END FLOW)

## 🔹 Namespace & Setup

```bash
# Create a dedicated namespace for the capstone project
kubectl create namespace capstone

# Set current kubectl context to use capstone namespace by default
kubectl config set-context --current --namespace=capstone

# Verify namespace creation and view all namespaces
kubectl get ns
```

---

## 🔹 Apply All YAMLs

```bash
# Create Secret containing MySQL credentials
kubectl apply -f mysql-secret.yaml

# Create Headless Service for StatefulSet DNS resolution
kubectl apply -f mysql-headless-service.yaml

# Deploy MySQL StatefulSet with persistent storage
kubectl apply -f mysql-statefulset.yaml

# Create WordPress configuration values
kubectl apply -f wordpress-configmap.yaml

# Deploy WordPress application pods
kubectl apply -f wordpress-deployment.yaml

# Expose WordPress using NodePort Service
kubectl apply -f wordpress-service.yaml

# Enable Horizontal Pod Autoscaling
kubectl apply -f wordpress-hpa.yaml
```

---

## 🔹 Verify Resources

```bash
# Display all resources in capstone namespace
kubectl get all -n capstone

# Display pod details including IPs and node placement
kubectl get pods -n capstone -o wide

# Verify services and exposed ports
kubectl get svc -n capstone

# Verify Persistent Volume Claims
kubectl get pvc -n capstone

# Verify Secrets
kubectl get secret -n capstone

# Verify ConfigMaps
kubectl get configmap -n capstone

# Verify HPA status and scaling metrics
kubectl get hpa -n capstone
```

---

## 🔹 Inspect Resource Definitions

```bash
# View complete Deployment YAML
kubectl get deployment wordpress -o yaml

# View complete StatefulSet YAML
kubectl get statefulset mysql -o yaml

# View complete Service YAML
kubectl get svc wordpress -o yaml

# View complete HPA YAML
kubectl get hpa wordpress-hpa -o yaml

# View complete PVC YAML
kubectl get pvc -o yaml
```

---

## 🔹 Describe Resources

```bash
# Show detailed Deployment information and events
kubectl describe deployment wordpress

# Show detailed StatefulSet information and events
kubectl describe statefulset mysql

# Show detailed Service information
kubectl describe svc wordpress

# Show detailed HPA information
kubectl describe hpa wordpress-hpa

# Show detailed PVC information
kubectl describe pvc
```

---

## 🔹 Debug Commands

```bash
# Display detailed MySQL pod information
kubectl describe pod mysql-0 -n capstone

# View MySQL container logs
kubectl logs mysql-0 -n capstone

# Display detailed WordPress pod information
kubectl describe pod <wordpress-pod> -n capstone

# View WordPress container logs
kubectl logs <wordpress-pod> -n capstone

# Verify Service-to-Pod endpoint mapping
kubectl get endpoints wordpress -n capstone
```

---

## 🔹 Monitor Resources

```bash
# Continuously watch pods
kubectl get pods -n capstone -w

# Continuously watch services
kubectl get svc -n capstone -w

# Continuously watch HPA changes
kubectl get hpa -n capstone -w
```

---

## 🔹 Access Application

```bash
# Verify WordPress Service details
kubectl get svc -n capstone

# Open WordPress using Minikube service URL
minikube service wordpress -n capstone
```

OR

```bash
# Forward local port 8080 to WordPress service port 80
kubectl port-forward svc/wordpress 8080:80 -n capstone --address 0.0.0.0
```

---

## 🔹 Self-Healing Test

```bash
# Delete MySQL pod and test StatefulSet recovery
kubectl delete pod mysql-0 -n capstone

# Delete WordPress pod and test Deployment recovery
kubectl delete pod <wordpress-pod> -n capstone

# Watch Kubernetes automatically recreate pods
kubectl get pods -n capstone -w
```

---

## 🔹 Scaling Test

```bash
# Scale WordPress Deployment manually to 4 replicas
kubectl scale deployment wordpress --replicas=4

# Verify scaling result
kubectl get pods

# Scale WordPress Deployment back to 2 replicas
kubectl scale deployment wordpress --replicas=2
```

---

## 🔹 Persistence Validation

```bash
# Connect to MySQL pod
kubectl exec -it mysql-0 -- bash

# Access MySQL shell
mysql -u root -p

# Verify database contents
SHOW DATABASES;
```

---

## 🔹 Cleanup Commands

```bash
# Delete WordPress Deployment
kubectl delete deployment wordpress

# Delete WordPress Service
kubectl delete svc wordpress

# Delete HPA
kubectl delete hpa wordpress-hpa

# Delete StatefulSet
kubectl delete statefulset mysql

# Delete Headless Service
kubectl delete svc mysql

# Delete Namespace and all contained resources
kubectl delete namespace capstone
```

---

## 🔹 Helm Commands (Comparison)

```bash
# Add Bitnami Helm repository
helm repo add bitnami https://charts.bitnami.com/bitnami

# Update local Helm repository cache
helm repo update

# Search for WordPress chart
helm search repo wordpress

# Install WordPress Helm chart
helm install wp-helm bitnami/wordpress -n helm-wp --create-namespace

# List installed Helm releases
helm list -A

# View Helm release status
helm status wp-helm -n helm-wp

# View all resources created by Helm
kubectl get all -n helm-wp

# View Helm-generated values
helm get values wp-helm -n helm-wp

# Upgrade Helm release using custom values
helm upgrade wp-helm bitnami/wordpress -f values.yaml -n helm-wp

# Uninstall Helm release
helm uninstall wp-helm -n helm-wp

# Delete Helm namespace
kubectl delete namespace helm-wp
```

---

#   Memory Flow

```text
Create Namespace
        ↓
Create Secret
        ↓
Create Headless Service
        ↓
Deploy MySQL StatefulSet
        ↓
Create ConfigMap
        ↓
Deploy WordPress
        ↓
Expose Service
        ↓
Configure HPA
        ↓
Verify Resources
        ↓
Access Application
        ↓
Test Self-Healing
        ↓
Test Persistence
        ↓
Compare with Helm
```

This command section is now complete and matches the same level of detail as your YAML explanations.
