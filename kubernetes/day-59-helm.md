## Day 59 – Helm: Kubernetes Package Manager

# A. Detailed Notes (Part 1)

---

# 1. What We Learned

Day 59 introduced the following Helm concepts:

* Helm
* Helm Repositories
* Charts
* Releases
* Values Files
* Upgrades
* Rollbacks
* Custom Charts
* Go Templates
* Helm Architecture
* Helm Workflow

Helm is the most commonly used package manager for Kubernetes and is one of the most important tools for DevOps Engineers working with Kubernetes.

---

# 2. Why Helm Exists

Before Helm, deploying applications in Kubernetes required manually managing many YAML files.

Example:

```yaml
deployment.yaml
service.yaml
configmap.yaml
secret.yaml
pvc.yaml
ingress.yaml
hpa.yaml
```

A small application may require:

* 1 Deployment
* 2 Services
* 1 ConfigMap
* 1 Secret
* 1 PVC
* 1 Ingress

Total:

```text
7+ YAML files
```

A microservices environment can easily contain:

```text
20+
50+
100+
Kubernetes YAML files
```

For 20 microservices:

```text
140+ YAML files
```

Managing all these files manually becomes difficult and error-prone.

Example:

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f configmap.yaml
kubectl apply -f secret.yaml
kubectl apply -f ingress.yaml
```

Helm solves this problem by packaging Kubernetes manifests into reusable packages called Charts.

Think of Helm like:

```text
Ubuntu     → apt
Python     → pip
NodeJS     → npm
Kubernetes → Helm
```

One command:

```bash
helm install my-nginx bitnami/nginx
```

can create an entire application stack automatically.

---

# 3. Helm = Apt for Kubernetes

Ubuntu:

```bash
apt install nginx
```

Kubernetes:

```bash
helm install my-nginx bitnami/nginx
```

Same idea.

Helm packages Kubernetes resources and makes deployments easier, repeatable, and version-controlled.

---

# 4. Helm Core Concepts

Helm revolves around three major concepts:

```text
Chart
Release
Repository
```

---

## Chart

A Chart is a package containing Kubernetes templates and configuration values.

A Chart typically contains:

```text
Deployments
Services
ConfigMaps
Secrets
PVCs
Ingress
```

Examples:

```bash
bitnami/nginx
bitnami/mysql
```

Think:

```text
Docker Image = Package for Containers

Helm Chart = Package for Kubernetes Resources
```

A Chart includes:

* Deployment templates
* Service templates
* ConfigMaps
* Ingress definitions
* Default configuration values

---

## Release

A Release is a deployed instance of a Chart running inside a Kubernetes cluster.

Example:

```bash
helm install my-nginx bitnami/nginx
```

Here:

```text
Chart   = bitnami/nginx
Release = my-nginx
```

Multiple releases can be created from the same chart.

Example:

```bash
helm install app1 bitnami/nginx

helm install app2 bitnami/nginx

helm install app3 bitnami/nginx
```

Result:

```text
Three Releases

app1
app2
app3

One Chart

bitnami/nginx
```

---

## Repository

A Repository stores Helm Charts.

Example:

```text
https://charts.bitnami.com/bitnami
```

Popular repositories:

* Bitnami
* Prometheus Community
* Grafana
* Elastic

Similar to:

```text
APT Repository
Docker Hub
NPM Registry
```

Repositories allow Helm to download charts when required.

---

# 5. Helm Architecture

Helm Architecture is a very common interview topic.

```text
Helm Client
      ↓
Kubernetes API Server
      ↓
Cluster
```

Interview Question:

```text
Explain Helm Architecture
```

Important:

```text
Helm v2 → Used Tiller

Helm v3 → No Tiller

Helm v4 → No Tiller
```

Helm now communicates directly with the Kubernetes API Server.

---

# 6. Helm Workflow

Another very common interview topic.

```text
Developer
    ↓
Creates Chart
    ↓
Pushes to Repository
    ↓
User installs Chart
    ↓
Helm creates Release
    ↓
Kubernetes resources created
```

Lifecycle:

```text
Developer
    ↓
Chart
    ↓
Repository
    ↓
helm install
    ↓
Release
    ↓
helm upgrade
    ↓
Revision
    ↓
helm rollback
```

Interview Question:

```text
Explain Helm Lifecycle
```

---

# 7. What Happens Internally During helm install?

Very common interview question.

Command:

```bash
helm install my-nginx bitnami/nginx
```

Internally Helm performs the following steps:

1. Downloads the Chart
2. Reads values.yaml
3. Merges user overrides
4. Renders templates
5. Generates final Kubernetes YAML
6. Sends manifests to Kubernetes API Server
7. Stores Release metadata
8. Creates Revision 1

Flow:

```text
Repository
    ↓
Chart Downloaded
    ↓
Templates Rendered
    ↓
Values Injected
    ↓
Kubernetes YAML Generated
    ↓
Resources Created
```

---

# 8. Helm Does Not Create Magic

Helm is not a replacement for Kubernetes.

Helm eventually creates normal Kubernetes resources.

Examples:

```text
Deployment
ReplicaSet
Pods
Services
ConfigMaps
Secrets
PVCs
Ingress
```

Helm acts as an abstraction layer over Kubernetes YAML.

---

# 9. Kubernetes YAML vs Helm

Excellent interview topic.

| Kubernetes YAML    | Helm             |
| ------------------ | ---------------- |
| Static             | Dynamic          |
| Manual             | Automated        |
| Many files         | One package      |
| Manual rollback    | helm rollback    |
| No version history | Revision history |
| Repetitive         | Reusable         |
| Hard to maintain   | Easier to manage |

Benefits of Helm:

```text
Reusable Deployments
Versioning
Rollback Support
Templating
Less YAML
Automated Upgrades
Environment-Specific Configuration
```

---

# 10. Installing Helm

Initially installed:

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

Output:

```text
helm installed into /usr/local/bin/helm
```

Check version:

```bash
helm version
```

Initial Output:

```text
v3.21.0
```

---

## Why We Upgraded

Observation:

```text
Latest Helm = v4.1.3
Installed   = v3.21.0
```

So Helm was upgraded.

Commands:

```bash
curl -LO https://get.helm.sh/helm-v4.1.3-linux-amd64.tar.gz

tar -zxvf helm-v4.1.3-linux-amd64.tar.gz

sudo mv linux-amd64/helm /usr/local/bin/helm
```

Verify:

```bash
helm version
```

Output:

```text
Version: v4.1.3
```

or

```text
v4.1.3
```

---

## Helm 3 vs Helm 4

| Helm 3                  | Helm 4           |
| ----------------------- | ---------------- |
| Mature                  | New              |
| Industry Standard       | Emerging         |
| Most Tutorials Use      | Latest Version   |
| Stable Production Usage | Future Direction |

Important:

```text
Core Helm concepts remain unchanged.
```

---

## Lab Version Used

```text
Lab completed using Helm v4.1.3
```

---

## Verify Helm Environment

Command:

```bash
helm env
```

Important environment locations:

```text
HELM_CACHE_HOME
HELM_CONFIG_HOME
HELM_DATA_HOME
HELM_REPOSITORY_CACHE
HELM_REPOSITORY_CONFIG
```

These store:

```text
Downloaded Charts
Repository Information
Helm Configuration
Cached Data
```



***

# 11. Adding a Helm Repository

Before installing charts, Helm must know where charts are stored.

Repositories store Helm Charts.

Example:

```text
Bitnami Repository
```

URL:

```text
https://charts.bitnami.com/bitnami
```

---

## Common Beginner Mistake

Initially:

```bash
bitnami/nginx
```

Error:

```text
No such file or directory
```

Reason:

```text
bitnami/nginx is NOT a command.

It is a chart name.
```

---

Another error:

```bash
helm install my-nginx bitnami/nginx
```

Output:

```text
repo bitnami not found
```

Reason:

```text
Repository not added yet.
```

---

## Fix

Add repository:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

Update repository metadata:

```bash
helm repo update
```

Output:

```text
Successfully got an update from the "bitnami" chart repository
```

---

## Verify Repository

```bash
helm repo list
```

---

# 12. Searching Helm Charts

Search for NGINX:

```bash
helm search repo nginx
```

Search all Bitnami charts:

```bash
helm search repo bitnami
```

Count charts:

```bash
helm search repo bitnami | wc -l
```

Output may be:

```text
145+
```

or

```text
3000+ chart versions
```

Actual count changes over time.

---

# 13. Minikube Cluster Problem

After adding repository:

```bash
helm install my-nginx bitnami/nginx
```

Failed.

Error:

```text
cluster unreachable
```

or

```text
localhost:8080 connection refused
```

---

## Reason

Helm communicates with Kubernetes.

No cluster running:

```text
No Deployment
No Pods
No Services
```

---

## Verify Cluster

```bash
minikube status
```

Output:

```text
host: Stopped

apiserver: Stopped
```

---

## Check Kubernetes Context

```bash
kubectl config current-context
```

---

## Fix

Start cluster:

```bash
minikube start
```

---

# 14. Disk Full Problem

Minikube still failed.

Error:

```text
No space left on device
```

---

## Verify Disk Usage

```bash
df -h
```

Output:

```text
/ dev / root

6.7G
6.6G
0
100%
```

Root filesystem completely full.

---

## Root Cause

Minikube Docker driver downloaded:

```text
KIC Base Image
Kubernetes Preload Image
Container Images
```

which consumed most of the EC2 disk.

---

## Investigation Commands

```bash
df -h

du -sh /*

sudo du -sh /*
```

Found heavy storage usage from:

```text
Docker Images
Minikube Cache
Containers
Volumes
```

---

## Cleanup Performed

Delete Minikube:

```bash
minikube delete
```

Remove Minikube cache:

```bash
rm -rf ~/.minikube/cache
```

Remove Docker images:

```bash
docker system prune -af
```

Remove Docker volumes:

```bash
docker volume prune -f
```

Clean apt cache:

```bash
sudo apt clean
```

Clean logs:

```bash
sudo journalctl --vacuum-size=100M
```

---

## Result

Before:

```text
100% disk used
```

After:

```text
45% disk used

3.7GB free
```

or

```text
3.7GB recovered
```

---

# 15. Start Minikube Again

Started with smaller resources:

```bash
minikube start --driver=docker --memory=2048 --cpus=2
```

Success:

```text
kubectl configured to use minikube
```

---

## Verify Node

```bash
kubectl get nodes
```

Output:

```text
minikube Ready
```

Node must be:

```text
Ready
```

before Helm deployments.

---

# 16. Installing NGINX Chart

After fixing Minikube:

```bash
helm install my-nginx bitnami/nginx
```

Success.

---

## Verify Resources

```bash
kubectl get all
```

Output:

```text
1 Pod

1 Deployment

1 ReplicaSet

1 Service
```

---

## Verify Helm

```bash
helm list
```

```bash
helm status my-nginx
```

```bash
helm get manifest my-nginx
```

---

## Observations

Pods:

```text
1 Running
```

Service Type:

```text
LoadBalancer
```

Output:

```text
service/my-nginx LoadBalancer
```

---

# 17. Helm Inspection Commands

Useful commands after installation:

```bash
helm list
```

List releases.

---

```bash
helm status my-nginx
```

View release status.

---

```bash
helm get manifest my-nginx
```

View generated Kubernetes manifests.

---

```bash
helm get all my-nginx
```

Get complete release information.

---

```bash
helm get notes my-nginx
```

Show release notes.

---

```bash
helm get hooks my-nginx
```

Show hooks.

---

# 18. Understanding values.yaml

Every chart contains:

```yaml
values.yaml
```

Purpose:

```text
Default Configuration File
```

---

## View Default Values

```bash
helm show values bitnami/nginx
```

---

Example:

```yaml
replicaCount: 1
```

These values are used unless overridden.

---

# 19. values.yaml vs custom-values.yaml

Very common interview question.

---

## values.yaml

Packaged inside chart.

Default values.

Example:

```yaml
replicaCount: 1
```

---

## custom-values.yaml

User-provided override file.

Example:

```yaml
replicaCount: 3
```

Install:

```bash
helm install app ./chart -f custom-values.yaml
```

---

Comparison:

| values.yaml           | custom-values.yaml            |
| --------------------- | ----------------------------- |
| Default configuration | User overrides                |
| Inside chart          | Outside chart                 |
| Shipped with chart    | Created by user               |
| Base settings         | Environment-specific settings |

---

# 20. Override Using --set

Quick way to change values.

Example:

```bash
helm install nginx-nodeport bitnami/nginx \
--set replicaCount=3 \
--set service.type=NodePort
```

Result:

```text
3 Replicas

NodePort Service
```

---

# 21. Using custom-values.yaml

Created:

```yaml
replicaCount: 3

service:
  type: NodePort

resources:
  requests:
    cpu: 100m
    memory: 128Mi

  limits:
    cpu: 200m
    memory: 256Mi
```

---

## Meaning of Each Section

### replicaCount

```yaml
replicaCount: 3
```

Creates:

```text
3 Pod Replicas
```

---

### service.type

```yaml
service:
  type: NodePort
```

Creates:

```text
NodePort Service
```

instead of:

```text
LoadBalancer
```

---

### resources.requests

```yaml
requests:
  cpu: 100m
  memory: 128Mi
```

Meaning:

```text
Minimum resources guaranteed to container
```

---

### resources.limits

```yaml
limits:
  cpu: 200m
  memory: 256Mi
```

Meaning:

```text
Maximum resources container can consume
```

---

## Install Using Values File

```bash
helm install nginx-values bitnami/nginx \
-f custom-values.yaml
```

---

## Verify Values

```bash
helm get values nginx-values
```

Output:

```text
replicaCount: 3

service.type: NodePort
```

---

Verify deployment:

```bash
kubectl get deployment nginx-values
```

Output:

```text
3/3 Ready
```

or

```text
3 replicas running
```

---

Verify service:

```bash
kubectl get svc nginx-values
```

Output:

```text
NodePort
```

---

# 22. Why helm get values Showed null

Common confusion.

Command:

```bash
helm get values my-release
```

Output:

```text
USER-SUPPLIED VALUES:

null
```

---

## Reason

No values supplied during installation.

Example:

```bash
helm install my-release ./my-app
```

used only:

```text
Default values from values.yaml
```

No overrides.

---

## View All Values

```bash
helm get values my-release --all
```

Shows:

```text
Default Values + User Overrides
```

***





# 23. Upgrade and Rollback

One of Helm's biggest advantages is the ability to upgrade and rollback applications easily.

---

## Install → Upgrade → Rollback Lifecycle

### Install

Creates a release.

```bash
helm install my-nginx bitnami/nginx
```

Creates:

```text
Revision 1
```

---

### Upgrade

Updates an existing release.

```bash
helm upgrade my-nginx bitnami/nginx \
--set replicaCount=5
```

Creates:

```text
Revision 2
```

---

### Rollback

Returns application to a previous revision.

```bash
helm rollback my-nginx 1
```

Creates:

```text
Revision 3
```

Important:

```text
Rollback creates a NEW revision.

Rollback never overwrites history.
```

---

# 24. Upgrade Example

Increase replicas:

```bash
helm upgrade my-nginx bitnami/nginx \
--set replicaCount=5
```

Verify:

```bash
kubectl get deployment my-nginx
```

Output:

```text
5/5 Ready
```

or

```text
5 replicas running
```

Task passed.

---

## Check History

```bash
helm history my-nginx
```

Output:

```text
Revision 1
Revision 2
```

or

```text
Revision 1 Install
Revision 2 Upgrade
```

---

# 25. Rollback Example

Rollback:

```bash
helm rollback my-nginx 1
```

Success.

Check again:

```bash
helm history my-nginx
```

Output:

```text
Revision 1 Install

Revision 2 Upgrade

Revision 3 Rollback
```

---

## Important Learning

Rollback creates:

```text
NEW REVISION
```

Helm never deletes history.

Total revisions:

```text
3
```

---

# Real Production Benefit

Imagine:

```text
Version 2 deployed

Application broken

Customers affected
```

Fast recovery:

```bash
helm history my-app

helm rollback my-app 1
```

Application restored within seconds.

---

# 26. Creating Our Own Chart

Generate a custom chart:

```bash
helm create my-app
```

Generated structure:

```text
my-app/
│
├── Chart.yaml
├── values.yaml
├── charts/
├── templates/
└── _helpers.tpl
```

or

```text
my-app/
├── Chart.yaml
├── values.yaml
├── charts/
└── templates/
    ├── deployment.yaml
    ├── service.yaml
    ├── ingress.yaml
    └── ...
```

---

# 27. Chart Structure

## Chart.yaml

Contains chart metadata.

Example:

```yaml
apiVersion: v2
name: my-app
version: 0.1.0
```

or

```yaml
name: my-app

version: 0.1.0
```

Purpose:

```text
Chart Metadata
```

---

## values.yaml

Stores default configuration.

Example:

```yaml
replicaCount: 3

image:
  repository: nginx
  tag: "1.25"
```

Purpose:

```text
Default Configuration Values
```

---

## templates/

Contains Kubernetes templates.

Examples:

```text
deployment.yaml
service.yaml
ingress.yaml
```

Purpose:

```text
Reusable Kubernetes Manifest Templates
```

---

## charts/

Stores chart dependencies.

Purpose:

```text
Dependent Charts
```

---

## _helpers.tpl

Contains reusable helper functions.

Purpose:

```text
Reusable Template Logic
```

---

# 28. Go Templating

Helm uses:

```text
Go Template Engine
```

to dynamically generate Kubernetes YAML.

---

## Reading Values

values.yaml

```yaml
replicaCount: 3
```

Template:

```yaml
replicas: {{ .Values.replicaCount }}
```

Generated YAML:

```yaml
replicas: 3
```

---

## Chart Metadata

Template:

```yaml
{{ .Chart.Name }}
```

Returns:

```text
my-app
```

---

## Release Name

Template:

```yaml
{{ .Release.Name }}
```

Returns:

```text
my-release
```

---

## Namespace

Template:

```yaml
{{ .Release.Namespace }}
```

Returns current namespace.

---

## Chart Version

Template:

```yaml
{{ .Chart.Version }}
```

Returns chart version.

---

## Common Template Variables

| Template                   | Description        |
| -------------------------- | ------------------ |
| `{{ .Values.key }}`        | Access values.yaml |
| `{{ .Chart.Name }}`        | Chart name         |
| `{{ .Release.Name }}`      | Release name       |
| `{{ .Release.Namespace }}` | Namespace          |
| `{{ .Chart.Version }}`     | Chart version      |

---

# 29. Linting Charts

Validate chart:

```bash
helm lint my-app
```

Checks:

```text
Syntax
Chart Structure
Best Practices
Template Errors
```

Purpose:

```text
Validate Before Deployment
```

---

# 30. Template Rendering

Preview manifests without installation.

```bash
helm template my-release ./my-app
```

Equivalent to:

```text
Show Generated YAML

DO NOT Install
```

Benefits:

```text
Debug Templates

Verify Configuration

Inspect Generated YAML
```

Very useful troubleshooting tool.

---

# 31. Custom Chart Installation

Install local chart:

```bash
helm install my-release ./my-app
```

---

## Verification

```bash
kubectl get deployment my-release-my-app
```

Expected:

```text
3 replicas running
```

or

```text
3/3 Ready
```

---

# 32. Real Lab Problem — ImagePullBackOff

Custom chart installation failed.

Pods:

```text
ImagePullBackOff

ErrImagePull
```

---

Describe pod:

```bash
kubectl describe pod <pod>
```

Error:

```text
Failed to pull image

No space left on device
```

---

# Root Cause Analysis

Host:

```text
3.7GB free
```

Minikube:

```text
118MB free
```

Check:

```bash
minikube ssh -- df -h
```

Output:

```text
99% used
```

or

```text
overlay 99%
```

Important lesson:

```text
Host Disk Free ≠ Minikube Disk Free
```

---

# Fix

Delete cluster:

```bash
minikube delete
```

Cleanup:

```bash
docker system prune -af
```

Restart:

```bash
minikube start --memory=2048 --cpus=2
```

Reinstall chart.

Success.

---

# Verification

Pods:

```text
Running
Running
Running
```

Deployment:

```text
3/3 Ready
```

Task passed.

---

# 33. Upgrade Custom Chart

Upgrade:

```bash
helm upgrade my-release ./my-app \
--set replicaCount=5
```

Verify:

```bash
kubectl get deployment my-release-my-app
```

Output:

```text
5/5 Ready
```

or

```text
5 replicas running
```

---

# Task Verification

Before:

```text
3 replicas
```

After:

```text
5 replicas
```

SUCCESS.

---

# 34. Dependency Management

Real-world charts often depend on other charts.

Example:

```yaml
dependencies:
  - name: mysql
    version: "9.x.x"
    repository: https://charts.bitnami.com/bitnami
```

Commands:

```bash
helm dependency update
```

```bash
helm dependency build
```

Purpose:

```text
Download and Manage Dependencies
```

---

# 35. Helm Plugins

List plugins:

```bash
helm plugin list
```

Popular plugin:

```text
helm-diff
```

Used before upgrades.

Example:

```bash
helm diff upgrade my-release bitnami/nginx
```

Purpose:

```text
Preview Changes Before Upgrade
```

---

# 36. Packaging Charts

Package custom chart:

```bash
helm package my-app
```

Output:

```text
my-app-0.1.0.tgz
```

Purpose:

```text
Portable Chart Package
```

---

# 37. Publishing Charts

Push package:

```bash
helm push my-app-0.1.0.tgz <repository>
```

Enterprise teams commonly publish charts internally.

---

# 38. Additional Useful Commands

```bash
helm show chart
```

Show chart metadata.

---

```bash
helm show readme
```

Show chart documentation.

---

```bash
helm show all
```

Show complete chart information.

---

```bash
helm get all
```

Show complete release information.

---

```bash
helm get notes
```

Show release notes.

---

```bash
helm get hooks
```

Show hooks.

---

```bash
helm env
```

Show Helm environment configuration.

---

```bash
helm plugin list
```

List installed plugins.

---

# 39. Cleanup

Remove releases:

```bash
helm uninstall my-release

helm uninstall my-nginx

helm uninstall nginx-nodeport

helm uninstall nginx-values
```

Alternative:

```bash
helm uninstall my-release --keep-history
```

---

## Verify

```bash
helm list
```

Output:

```text
No releases found
```

or

```text
No releases
```

Task completed.

---

# 40. Day 59 Actual Lab Journey

## Step 1

Installed Helm.

Version:

```text
v4.1.3
```

---

## Step 2

Tried:

```bash
helm install my-nginx bitnami/nginx
```

Failed:

```text
repo bitnami not found
```

Fixed repository.

---

## Step 3

Tried installation again.

Failed:

```text
cluster unreachable
```

Minikube stopped.

---

## Step 4

Started Minikube.

Failed:

```text
no space left on device
```

Disk full.

---

## Step 5

Cleaned:

```bash
docker system prune -af
```

Deleted Minikube.

Recreated cluster.

---

## Step 6

Installed Bitnami NGINX.

Success.

---

## Step 7

Created:

```text
custom-values.yaml
```

Installed NodePort release.

Success.

---

## Step 8

Upgraded release.

Replica count:

```text
1 → 5
```

Success.

---

## Step 9

Rolled back release.

Revision:

```text
3
```

Success.

---

## Step 10

Created custom chart.

Pods failed.

Reason:

```text
ImagePullBackOff
```

Actually:

```text
No space left on device
```

inside Minikube.

---

## Step 11

Deleted cluster.

Recreated cluster.

Installed chart again.

Success.

---

## Step 12

Upgraded custom chart.

Replicas:

```text
3 → 5
```

Success.

---

# 41. Production Scenario Interview Question

### Scenario

Application upgraded using Helm.

After deployment:

```text
CrashLoopBackOff

Users cannot access application
```

Fastest recovery?

Answer:

```bash
helm history my-app

helm rollback my-app <previous_revision>
```

Reason:

```text
Rollback restores last known working version quickly.
```

---

# 42. Real-World Usage

Helm is used in almost every Kubernetes production environment.

Examples:

```text
Prometheus
Grafana
NGINX Ingress Controller
ArgoCD
Jenkins
HashiCorp Vault
Elasticsearch
Kafka
RabbitMQ
Redis
PostgreSQL
WordPress
Cert Manager
```

Production teams rarely deploy these tools using raw YAML.

Instead:

```bash
helm install prometheus prometheus-community/prometheus

helm install grafana grafana/grafana

helm install ingress-nginx ingress-nginx/ingress-nginx
```

Production example:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami

helm install prod-nginx bitnami/nginx \
-f production-values.yaml
```

If deployment breaks:

```bash
helm rollback prod-nginx 1
```

---

# Key Learning

```text
Helm = Kubernetes Package Manager

Chart = Package

Release = Installed Chart

Repository = Collection of Charts

values.yaml = Default Configuration

custom-values.yaml = User Overrides

helm install = Create Release

helm upgrade = Update Release

helm rollback = Restore Previous Revision

helm history = Show Revisions

helm lint = Validate Chart

helm template = Render YAML Without Installing

Rollback Creates NEW Revision

Helm Uses Go Templates

Helm Simplifies Kubernetes Application Deployment

Helm Is One Of The Most Important DevOps Skills
```

***




# Helm Fundamentals

### What is Helm?

Helm is the package manager for Kubernetes used to package, install, upgrade, rollback, and manage Kubernetes applications.

Think:

```text
Ubuntu     → apt
Python     → pip
NodeJS     → npm
Kubernetes → Helm
```

---

### Why is Helm used?

Helm helps solve problems such as:

```text
Too many YAML files

Manual deployments

Manual updates

Manual rollbacks

Environment-specific configuration

Application versioning
```

Benefits:

```text
Reusable Deployments

Version Control

Rollback Support

Templating

Less YAML

Easy Upgrades
```

---

### What problem does Helm solve?

Without Helm:

```bash
kubectl apply -f deployment.yaml

kubectl apply -f service.yaml

kubectl apply -f configmap.yaml

kubectl apply -f ingress.yaml
```

With Helm:

```bash
helm install my-app bitnami/nginx
```

One command deploys the complete application stack.

---

# Core Concepts

### What is a Chart?

A Chart is a package containing Kubernetes templates and configuration.

Contains:

```text
Deployment

Service

ConfigMap

Secret

PVC

Ingress

values.yaml

templates/
```

Example:

```bash
bitnami/nginx
```

---

### What is a Release?

A Release is a deployed instance of a Chart.

Example:

```bash
helm install my-nginx bitnami/nginx
```

Here:

```text
Chart   = bitnami/nginx

Release = my-nginx
```

---

### What is a Repository?

A Repository stores Helm Charts.

Example:

```text
https://charts.bitnami.com/bitnami
```

Popular repositories:

```text
Bitnami

Prometheus Community

Grafana

Elastic
```

---

### Difference Between Chart and Release?

```text
Chart   = Blueprint

Release = Running Instance
```

Example:

```bash
helm install app1 bitnami/nginx

helm install app2 bitnami/nginx
```

Result:

```text
One Chart

Two Releases
```

---

### Can multiple releases use the same chart?

Yes.

Example:

```bash
helm install app1 bitnami/nginx

helm install app2 bitnami/nginx

helm install app3 bitnami/nginx
```

Result:

```text
Three Releases

One Chart
```

---

# Helm Architecture

### Explain Helm Architecture

```text
Helm Client
      ↓
Kubernetes API Server
      ↓
Cluster
```

Helm communicates directly with Kubernetes API Server.

---

### What was Tiller?

Tiller was the server-side component used in Helm v2.

```text
Helm v2 → Tiller

Helm v3 → No Tiller

Helm v4 → No Tiller
```

---

### Why was Tiller removed?

Reasons:

```text
Security

Complexity

Direct API communication became possible
```

---

# Helm Workflow

### Explain Helm Lifecycle

```text
Developer
    ↓
Creates Chart
    ↓
Pushes to Repository
    ↓
helm install
    ↓
Release Created
    ↓
helm upgrade
    ↓
Revision Created
    ↓
helm rollback
```

---

### What happens during helm install?

Command:

```bash
helm install my-nginx bitnami/nginx
```

Helm:

```text
1. Downloads Chart

2. Reads values.yaml

3. Merges User Overrides

4. Renders Templates

5. Generates Kubernetes YAML

6. Sends YAML to API Server

7. Stores Release Metadata

8. Creates Revision 1
```

---

# Helm Repositories

### How do you add a repository?

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

---

### How do you update repositories?

```bash
helm repo update
```

---

### How do you list repositories?

```bash
helm repo list
```

---

### How do you search charts?

```bash
helm search repo nginx
```

or

```bash
helm search repo bitnami
```

---

# values.yaml

### What is values.yaml?

Default configuration file packaged inside a chart.

Example:

```yaml
replicaCount: 1
```

---

### What is custom-values.yaml?

User-specific override file.

Example:

```yaml
replicaCount: 3
```

---

### Difference Between values.yaml and custom-values.yaml?

| values.yaml           | custom-values.yaml            |
| --------------------- | ----------------------------- |
| Default configuration | User override                 |
| Inside chart          | External file                 |
| Packaged with chart   | Created by user               |
| Base settings         | Environment-specific settings |

---

### How do you override values quickly?

Using:

```bash
--set
```

Example:

```bash
helm install nginx bitnami/nginx \
--set replicaCount=3
```

---

### How do you install using a values file?

```bash
helm install nginx bitnami/nginx \
-f custom-values.yaml
```

---

### Why did helm get values show null?

Command:

```bash
helm get values my-release
```

Output:

```text
USER-SUPPLIED VALUES:

null
```

Reason:

```text
No user overrides supplied.
```

Only defaults from values.yaml were used.

---

### How do you see all values?

```bash
helm get values my-release --all
```

---

# Upgrade and Rollback

### What is helm upgrade?

Updates an existing release.

Example:

```bash
helm upgrade my-nginx bitnami/nginx \
--set replicaCount=5
```

---

### What is helm rollback?

Restores a previous release revision.

Example:

```bash
helm rollback my-nginx 1
```

---

### Does rollback overwrite history?

No.

Important:

```text
Rollback creates a NEW revision.
```

---

### What is helm history?

Shows release revisions.

Example:

```bash
helm history my-nginx
```

Output:

```text
Revision 1 Install

Revision 2 Upgrade

Revision 3 Rollback
```

---

### Difference Between Install, Upgrade and Rollback?

#### Install

```bash
helm install
```

Creates:

```text
Revision 1
```

---

#### Upgrade

```bash
helm upgrade
```

Creates:

```text
Revision 2
```

---

#### Rollback

```bash
helm rollback
```

Creates:

```text
Revision 3
```

Rollback creates a new revision.

---

# Custom Charts

### How do you create a chart?

```bash
helm create my-app
```

---

### What files are generated?

```text
Chart.yaml

values.yaml

templates/

charts/

_helpers.tpl
```

---

### What is Chart.yaml?

Stores chart metadata.

Example:

```yaml
apiVersion: v2

name: my-app

version: 0.1.0
```

---

### What is templates/?

Contains Kubernetes manifest templates.

Examples:

```text
deployment.yaml

service.yaml

ingress.yaml
```

---

### What is _helpers.tpl?

Contains reusable helper templates.

---

# Go Templates

### What template language does Helm use?

```text
Go Templates
```

---

### How do you access values.yaml?

```yaml
{{ .Values.replicaCount }}
```

---

### How do you get chart name?

```yaml
{{ .Chart.Name }}
```

---

### How do you get release name?

```yaml
{{ .Release.Name }}
```

---

### How do you get namespace?

```yaml
{{ .Release.Namespace }}
```

---

### How do you get chart version?

```yaml
{{ .Chart.Version }}
```

---

# Validation and Debugging

### What does helm lint do?

Validates:

```text
Chart Syntax

Chart Structure

Best Practices

Template Issues
```

Command:

```bash
helm lint my-app
```

---

### What does helm template do?

Generates YAML without installing.

```bash
helm template my-release ./my-app
```

Useful for debugging.

---

### Why use helm template?

```text
Preview Generated YAML

Validate Configuration

Debug Templates
```

---

# Dependency Management

### What are chart dependencies?

Charts can depend on other charts.

Example:

```yaml
dependencies:
  - name: mysql
    version: "9.x.x"
    repository: https://charts.bitnami.com/bitnami
```

---

### How do you download dependencies?

```bash
helm dependency update
```

---

### How do you build dependencies?

```bash
helm dependency build
```

---

# Plugins

### How do you list plugins?

```bash
helm plugin list
```

---

### Popular Helm plugin?

```text
helm-diff
```

---

### What does helm-diff do?

Shows changes before upgrade.

Example:

```bash
helm diff upgrade my-release bitnami/nginx
```

---

# Packaging & Publishing

### How do you package a chart?

```bash
helm package my-app
```

Output:

```text
my-app-0.1.0.tgz
```

---

### How do you publish a chart?

```bash
helm push my-app-0.1.0.tgz <repository>
```

---

# Real Production Scenario Questions

### Scenario: Deployment Failed After Upgrade

Symptoms:

```text
CrashLoopBackOff

Application unavailable
```

Fastest recovery?

```bash
helm history my-app

helm rollback my-app <previous_revision>
```

---

### Scenario: repo bitnami not found

Reason:

```text
Repository not added
```

Fix:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami

helm repo update
```

---

### Scenario: cluster unreachable

Reason:

```text
Kubernetes cluster not running
```

Fix:

```bash
minikube start
```

---

### Scenario: ImagePullBackOff

Check:

```bash
kubectl describe pod POD_NAME
```

Possible reasons:

```text
Invalid image

Registry issue

No space left on device
```

---

### Scenario: No space left on device

Check:

```bash
df -h

minikube ssh -- df -h
```

Fix:

```bash
docker system prune -af

minikube delete

rm -rf ~/.minikube/cache
```

---

### Scenario: cannot re-use a name that is still in use

Fix:

```bash
helm uninstall RELEASE_NAME
```

or

```bash
helm upgrade RELEASE_NAME
```

---

# Most Important Interview Takeaways

```text
Helm = Kubernetes Package Manager

Chart = Package

Release = Installed Chart

Repository = Collection of Charts

values.yaml = Default Configuration

custom-values.yaml = User Overrides

helm install = Create Release

helm upgrade = Update Release

helm rollback = Restore Previous Revision

helm history = Show Revisions

Rollback Creates NEW Revision

helm lint = Validation

helm template = Preview YAML

Helm Uses Go Templates

Helm Simplifies Kubernetes Deployments
```

***





# A. TROUBLESHOOTING GUIDE

---

# Problem 1 – repo bitnami not found

### Error

```text
repo bitnami not found
```

---

## When Did It Happen?

During:

```bash
helm install my-nginx bitnami/nginx
```

---

## Root Cause

Repository was never added.

Helm knew:

```text
Release Name
Chart Name
```

but did not know:

```text
Where to download chart from
```

---

## Fix

Add repository:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

Update repository metadata:

```bash
helm repo update
```

Verify:

```bash
helm repo list
```

---

## Lesson Learned

```text
Chart Name ≠ Repository

Repository must be added before chart installation.
```

---

# Problem 2 – bitnami/nginx Command Error

### Mistake

Executed:

```bash
bitnami/nginx
```

Error:

```text
No such file or directory
```

---

## Root Cause

```text
bitnami/nginx is not a Linux command.

It is a Helm chart name.
```

---

## Correct Usage

```bash
helm install my-nginx bitnami/nginx
```

---

## Lesson Learned

```text
Chart names must be used with Helm commands.
```

---

# Problem 3 – Cluster Unreachable

### Error

```text
cluster unreachable
```

or

```text
localhost:8080 connection refused
```

---

## When Did It Happen?

After:

```bash
helm install my-nginx bitnami/nginx
```

---

## Root Cause

Kubernetes cluster was stopped.

Helm communicates with:

```text
Kubernetes API Server
```

If API Server is unavailable:

```text
Deployment Fails
```

---

## Verify

Check Minikube:

```bash
minikube status
```

Output:

```text
host: Stopped

apiserver: Stopped
```

---

Check Context:

```bash
kubectl config current-context
```

---

Check Cluster:

```bash
kubectl cluster-info
```

---

Check Nodes:

```bash
kubectl get nodes
```

Expected:

```text
Ready
```

---

## Fix

Start Minikube:

```bash
minikube start
```

or

```bash
minikube start --driver=docker
```

---

## Lesson Learned

```text
Helm requires a running Kubernetes cluster.
```

---

# Problem 4 – EC2 Root Disk Full

### Error

```text
No space left on device
```

---

## Verify

```bash
df -h
```

Output:

```text
/ dev / root

6.7G
6.6G
0
100%
```

or

```text
100% used
```

---

## Root Cause

Minikube Docker Driver downloaded:

```text
KIC Base Image

Kubernetes Preload Image

Container Images

Docker Layers
```

which consumed most of the 8GB EC2 disk.

---

## Investigation Commands

Check disk:

```bash
df -h
```

Find large directories:

```bash
du -sh /*
```

Detailed:

```bash
sudo du -sh /*
```

---

## Cleanup Performed

Delete Minikube:

```bash
minikube delete
```

Remove Minikube cache:

```bash
rm -rf ~/.minikube/cache
```

Remove Docker images:

```bash
docker system prune -af
```

Remove volumes:

```bash
docker volume prune -f
```

Clean apt cache:

```bash
sudo apt clean
```

Clean logs:

```bash
sudo journalctl --vacuum-size=100M
```

---

## Result

Before:

```text
100% full
```

After:

```text
45% used

3.7GB free
```

---

## Lesson Learned

```text
Disk space is one of the most common Minikube problems.
```

---

# Problem 5 – Minikube Internal Storage Full

One of the most important Day 59 learnings.

---

### Symptoms

Pods failed.

Status:

```text
ImagePullBackOff

ErrImagePull
```

---

## Verify

Describe pod:

```bash
kubectl describe pod POD_NAME
```

Error:

```text
failed to register layer

no space left on device
```

---

## Confusing Observation

Host machine:

```text
3.7GB free
```

Pods still failed.

---

## Root Cause

Host storage and Minikube storage are different.

Check Minikube filesystem:

```bash
minikube ssh -- df -h
```

Output:

```text
overlay 99%
```

or

```text
99% used
```

---

## Important Learning

```text
Host Disk Free
≠
Minikube Disk Free
```

---

## Fix

Delete cluster:

```bash
minikube delete
```

Cleanup:

```bash
docker system prune -af
```

Recreate:

```bash
minikube start --driver=docker
```

or

```bash
minikube start --memory=2048 --cpus=2
```

---

## Result

Pods became:

```text
Running
Running
Running
```

Deployment:

```text
3/3 Ready
```

---

# Problem 6 – ImagePullBackOff

### Error

```text
ImagePullBackOff
```

or

```text
ErrImagePull
```

---

## Check

```bash
kubectl describe pod POD_NAME
```

---

## Possible Causes

```text
Invalid Image Name

Registry Issue

Authentication Issue

No Space Left On Device
```

---

## Actual Cause in Lab

```text
No space left on device
```

inside Minikube.

---

## Fix

```bash
minikube delete

docker system prune -af

minikube start
```

---

# Problem 7 – helm get values Shows null

### Command

```bash
helm get values my-release
```

Output:

```text
USER-SUPPLIED VALUES:

null
```

---

## Root Cause

Installed using:

```bash
helm install my-release ./my-app
```

No overrides supplied.

Only:

```text
values.yaml
```

was used.

---

## Fix

View all values:

```bash
helm get values my-release --all
```

---

## Lesson Learned

```text
helm get values

Shows only user overrides.
```

---

# Problem 8 – Cannot Re-use a Name That Is Still In Use

### Error

```text
cannot re-use a name that is still in use
```

---

## Root Cause

Release already exists.

Example:

```bash
helm install my-nginx bitnami/nginx
```

run again.

---

## Fix Option 1

Delete release:

```bash
helm uninstall my-nginx
```

---

## Fix Option 2

Upgrade release:

```bash
helm upgrade my-nginx bitnami/nginx
```

---

# Problem 9 – helm install Fails

### Checks

Verify nodes:

```bash
kubectl get nodes
```

---

Verify cluster:

```bash
kubectl cluster-info
```

---

Expected:

```text
Node Status = Ready
```

---

## Lesson Learned

Always verify cluster health before troubleshooting Helm.

---

# B. COMPLETE REAL LAB STORY

---

# Step 1 – Installed Helm

Installed Helm.

Initially:

```bash
helm version
```

Output:

```text
v3.21.0
```

---

# Step 2 – Upgraded Helm

Observed:

```text
Latest Helm = v4.1.3
```

Upgraded:

```bash
curl -LO https://get.helm.sh/helm-v4.1.3-linux-amd64.tar.gz

tar -zxvf helm-v4.1.3-linux-amd64.tar.gz

sudo mv linux-amd64/helm /usr/local/bin/helm
```

Verify:

```bash
helm version
```

Output:

```text
v4.1.3
```

---

# Step 3 – First Installation Attempt

Tried:

```bash
helm install my-nginx bitnami/nginx
```

Failed:

```text
repo bitnami not found
```

---

# Step 4 – Added Repository

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami

helm repo update
```

Success.

---

# Step 5 – Second Installation Attempt

Again:

```bash
helm install my-nginx bitnami/nginx
```

Failed:

```text
cluster unreachable
```

---

# Step 6 – Investigated Cluster

```bash
minikube status
```

Output:

```text
host: Stopped

apiserver: Stopped
```

---

# Step 7 – Started Minikube

Attempted:

```bash
minikube start
```

Failed.

Error:

```text
No space left on device
```

---

# Step 8 – Investigated Storage

Checked:

```bash
df -h
```

Output:

```text
100% used
```

---

# Step 9 – Cleanup

Executed:

```bash
minikube delete

rm -rf ~/.minikube/cache

docker system prune -af

docker volume prune -f

sudo apt clean
```

Recovered:

```text
3.7GB free
```

---

# Step 10 – Started Minikube Successfully

```bash
minikube start --driver=docker --memory=2048 --cpus=2
```

Success.

---

# Step 11 – Installed Bitnami NGINX

```bash
helm install my-nginx bitnami/nginx
```

Success.

Resources created:

```text
Pod

Deployment

ReplicaSet

Service
```

---

# Step 12 – Customized Deployment

Installed:

```bash
helm install nginx-nodeport bitnami/nginx \
--set replicaCount=3 \
--set service.type=NodePort
```

Success.

---

# Step 13 – Used custom-values.yaml

Created:

```yaml
replicaCount: 3

service:
  type: NodePort
```

Installed:

```bash
helm install nginx-values bitnami/nginx \
-f custom-values.yaml
```

Success.

---

# Step 14 – Upgraded Release

```bash
helm upgrade my-nginx bitnami/nginx \
--set replicaCount=5
```

Result:

```text
5 Replicas
```

---

# Step 15 – Rollback

```bash
helm rollback my-nginx 1
```

Result:

```text
Revision 3 Created
```

Important:

```text
Rollback creates a NEW revision.
```

---

# Step 16 – Created Custom Chart

```bash
helm create my-app
```

Generated chart structure.

---

# Step 17 – Installed Custom Chart

```bash
helm install my-release ./my-app
```

Pods failed.

---

# Step 18 – Investigated Pods

```bash
kubectl describe pod POD_NAME
```

Found:

```text
ImagePullBackOff
```

Actual issue:

```text
No space left on device
```

inside Minikube.

---

# Step 19 – Recreated Cluster

```bash
minikube delete

docker system prune -af

minikube start
```

---

# Step 20 – Reinstalled Custom Chart

Success.

Pods:

```text
3 Running
```

Deployment:

```text
3/3 Ready
```

---

# Step 21 – Upgraded Custom Chart

```bash
helm upgrade my-release ./my-app \
--set replicaCount=5
```

Result:

```text
5/5 Ready
```

Task passed.

---

# Final Day 59 Lessons

```text
Helm Simplifies Kubernetes Deployments

Charts Package Kubernetes Resources

Releases Are Running Chart Instances

Repositories Store Charts

values.yaml Stores Defaults

custom-values.yaml Stores Overrides

helm install Creates Releases

helm upgrade Creates New Revision

helm rollback Creates New Revision

helm history Shows Revision History

Helm Uses Go Templates

helm lint Validates Charts

helm template Renders YAML Without Installing

Disk Space Issues Are Common In Minikube

Host Storage And Minikube Storage Must Be Checked Separately

Helm Is A Critical Skill For DevOps Engineers
```

***





# 1-Minute Revision Sheet

```text id="frwkp6"
Helm = Kubernetes Package Manager

Chart = Kubernetes Package

Release = Installed Chart

Repository = Collection Of Charts

values.yaml = Default Configuration

custom-values.yaml = User Overrides

templates/ = Kubernetes Templates

Chart.yaml = Metadata

charts/ = Dependencies

_helpers.tpl = Reusable Template Functions

helm install = Create Release

helm upgrade = Update Release

helm rollback = Restore Previous Revision

helm history = Show Revision History

helm lint = Validate Chart

helm template = Render YAML Without Installing

Helm Uses Go Templates

Rollback Creates NEW Revision

Helm Communicates Directly With Kubernetes API Server

Helm v2 = Tiller

Helm v3 = No Tiller

Helm v4 = No Tiller
```

---

# Helm Architecture Revision

```text id="j1ghd2"
Developer
    ↓
Creates Chart
    ↓
Pushes To Repository
    ↓
User Installs Chart
    ↓
Helm Creates Release
    ↓
Templates Rendered
    ↓
Kubernetes Resources Created
```

---

## Internal Workflow

```text id="6fq3rj"
1. Download Chart

2. Read values.yaml

3. Merge User Overrides

4. Render Templates

5. Generate YAML

6. Send To Kubernetes API Server

7. Store Release Metadata

8. Create Revision
```

---

# Kubernetes YAML vs Helm

| Kubernetes YAML         | Helm              |
| ----------------------- | ----------------- |
| Static                  | Dynamic           |
| Hardcoded               | Parameterized     |
| Manual Updates          | Automated Updates |
| Manual Rollback         | helm rollback     |
| Many YAML Files         | One Package       |
| No Version History      | Revision History  |
| Difficult Reuse         | Easy Reuse        |
| Repeated Configurations | Templating        |

---

# Helm Objects Revision

## Chart

```text id="gw5kgv"
Blueprint

Package

Template Collection
```

Example:

```text id="k8owu7"
bitnami/nginx
```

---

## Release

```text id="b1g2o5"
Running Instance Of Chart
```

Example:

```bash id="yd5wo0"
helm install my-nginx bitnami/nginx
```

```text id="1nxxgn"
Release = my-nginx
```

---

## Repository

```text id="mkhhwy"
Storage Location For Charts
```

Example:

```text id="fpk7cw"
Bitnami Repository
```

---

# Chart Structure Revision

```text id="5wzhs7"
my-app/
│
├── Chart.yaml
├── values.yaml
├── charts/
├── templates/
└── _helpers.tpl
```

---

## Chart.yaml

Stores:

```text id="cw6j8v"
Chart Metadata

Name

Version

Description
```

Example:

```yaml id="lwl4f7"
apiVersion: v2

name: my-app

version: 0.1.0
```

---

## values.yaml

Stores:

```text id="4vcrlp"
Default Configuration
```

Example:

```yaml id="7ft3qd"
replicaCount: 1
```

---

## templates/

Stores:

```text id="b79v5y"
Deployment

Service

Ingress

ConfigMap

Secret
```

templates.

---

## charts/

Stores:

```text id="uznxp3"
Dependencies
```

---

## _helpers.tpl

Stores:

```text id="a65r0s"
Reusable Functions

Reusable Template Logic
```

---

# Go Template Revision

## Read Values

```yaml id="wv2nmr"
{{ .Values.replicaCount }}
```

---

## Chart Name

```yaml id="95k5oz"
{{ .Chart.Name }}
```

---

## Release Name

```yaml id="o8uwkh"
{{ .Release.Name }}
```

---

## Namespace

```yaml id="3ntc55"
{{ .Release.Namespace }}
```

---

## Chart Version

```yaml id="nrk55z"
{{ .Chart.Version }}
```

---

# Install → Upgrade → Rollback

```text id="ib3lyz"
Install
   ↓
Revision 1

Upgrade
   ↓
Revision 2

Rollback
   ↓
Revision 3
```

---

## Important

```text id="jgt4vx"
Rollback Does NOT Overwrite History

Rollback Creates NEW Revision
```

---

# values.yaml vs custom-values.yaml

| values.yaml         | custom-values.yaml   |
| ------------------- | -------------------- |
| Default Values      | Override Values      |
| Inside Chart        | User Created         |
| Packaged With Chart | External File        |
| Base Configuration  | Environment Specific |

---

# Resource Requests vs Limits

## Requests

```yaml id="o84slf"
requests:
  cpu: 100m
  memory: 128Mi
```

Meaning:

```text id="jlwm7u"
Minimum Guaranteed Resources
```

---

## Limits

```yaml id="i4z5ae"
limits:
  cpu: 200m
  memory: 256Mi
```

Meaning:

```text id="oaqe3c"
Maximum Resources Allowed
```

---

# Helm Commands Cheat Sheet

---

## Repository Commands

Add Repository:

```bash id="r1vk7j"
helm repo add bitnami https://charts.bitnami.com/bitnami
```

Update Repository:

```bash id="c2vkme"
helm repo update
```

List Repositories:

```bash id="9q9h97"
helm repo list
```

Search Charts:

```bash id="n9i7va"
helm search repo nginx
```

```bash id="hqzk74"
helm search repo bitnami
```

---

# Installation Commands

Install Chart:

```bash id="8z9n5h"
helm install my-nginx bitnami/nginx
```

Install Using Values File:

```bash id="4mjlwm"
helm install nginx-values bitnami/nginx \
-f custom-values.yaml
```

Install Using --set:

```bash id="wlr9n2"
helm install nginx-nodeport bitnami/nginx \
--set replicaCount=3 \
--set service.type=NodePort
```

---

# Upgrade Commands

```bash id="5v1r2p"
helm upgrade my-nginx bitnami/nginx \
--set replicaCount=5
```

---

# Rollback Commands

Show History:

```bash id="r33jj3"
helm history my-nginx
```

Rollback:

```bash id="amqjlwm"
helm rollback my-nginx 1
```

---

# Inspection Commands

List Releases:

```bash id="c09vut"
helm list
```

Status:

```bash id="pmyi5d"
helm status my-nginx
```

Manifest:

```bash id="q0e3lh"
helm get manifest my-nginx
```

All Information:

```bash id="chhz4u"
helm get all my-nginx
```

Values:

```bash id="nhf8v4"
helm get values my-nginx
```

All Values:

```bash id="0mjlwm"
helm get values my-nginx --all
```

Notes:

```bash id="v2qngn"
helm get notes my-nginx
```

Hooks:

```bash id="jlwm51"
helm get hooks my-nginx
```

---

# Chart Development Commands

Create Chart:

```bash id="7nvjlwm"
helm create my-app
```

Validate Chart:

```bash id="pj4k7n"
helm lint my-app
```

Render Templates:

```bash id="3zdfg9"
helm template my-release ./my-app
```

Package Chart:

```bash id="94jlwm"
helm package my-app
```

---

# Dependency Commands

Update Dependencies:

```bash id="z13o0p"
helm dependency update
```

Build Dependencies:

```bash id="z8nkr2"
helm dependency build
```

---

# Plugin Commands

List Plugins:

```bash id="pv5u8j"
helm plugin list
```

Diff Upgrade:

```bash id="djlwm2"
helm diff upgrade my-release bitnami/nginx
```

---

# Cleanup Commands

Delete Release:

```bash id="7gkw0p"
helm uninstall my-nginx
```

Keep History:

```bash id="g7jlwm"
helm uninstall my-nginx --keep-history
```

---

# Troubleshooting Cheat Sheet

| Error                   | Cause                  | Fix                  |
| ----------------------- | ---------------------- | -------------------- |
| repo bitnami not found  | Repository missing     | helm repo add        |
| cluster unreachable     | Cluster stopped        | minikube start       |
| localhost:8080 refused  | API Server unavailable | Start cluster        |
| ImagePullBackOff        | Image/Storage issue    | Describe pod         |
| No space left on device | Disk full              | Cleanup              |
| cannot re-use a name    | Release exists         | uninstall or upgrade |
| helm get values = null  | No overrides           | use --all            |

---

# Real Day 59 Lab Journey (Ultra Short)

```text id="s1i5vc"
Install Helm
      ↓
Repo Not Found
      ↓
Added Bitnami Repo
      ↓
Cluster Unreachable
      ↓
Started Minikube
      ↓
Disk Full
      ↓
Cleanup Performed
      ↓
Minikube Started
      ↓
Installed NGINX Chart
      ↓
Created custom-values.yaml
      ↓
Upgrade Tested
      ↓
Rollback Tested
      ↓
Created Custom Chart
      ↓
ImagePullBackOff
      ↓
Minikube Storage Full
      ↓
Cluster Recreated
      ↓
Chart Installed Successfully
      ↓
Upgrade Successful
```

---

# Top 15 Interview Questions (Last Minute)

```text id="mjlwm8"
1. What is Helm?

2. Why is Helm used?

3. What is a Chart?

4. What is a Release?

5. What is a Repository?

6. Difference between Chart and Release?

7. What is values.yaml?

8. Difference between values.yaml and custom-values.yaml?

9. What is helm install?

10. What is helm upgrade?

11. What is helm rollback?

12. Why does rollback create a new revision?

13. What is helm lint?

14. What is helm template?

15. Explain Helm architecture and workflow.
```

---

# Final Exam / Interview Summary

```text id="c7t9xq"
Helm Is The Package Manager For Kubernetes

Chart = Package

Release = Installed Chart

Repository = Chart Storage

values.yaml = Defaults

custom-values.yaml = Overrides

Helm Uses Go Templates

helm install Creates Release

helm upgrade Creates New Revision

helm rollback Creates New Revision

helm history Shows Revisions

helm lint Validates Charts

helm template Generates YAML Without Installation

Helm Greatly Simplifies Kubernetes Deployments

Helm Is One Of The Most Important Kubernetes And DevOps Interview Topics
```








