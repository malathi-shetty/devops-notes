# Day 66 Master Revision Guide (Part 1)

# Terraform + VPC + EKS Foundation

---

# 1. What Was the Goal of Day 66?

Build a production-style AWS infrastructure using Terraform and deploy an EKS cluster on top of it.

Final Architecture:

```text
Internet
    │
    ▼
Internet Gateway
    │
    ▼
Public Subnets
    │
    ├── NAT Gateway
    │
    ▼
Private Subnets
    │
    ▼
EKS Cluster
    │
    ▼
Worker Nodes
    │
    ▼
Pods
```

---

# 2. Why Terraform?

Terraform allows Infrastructure as Code (IaC).

Instead of manually creating:

```text
VPC
Subnets
Route Tables
NAT Gateway
EKS Cluster
Node Groups
IAM Roles
```

We define them in code.

Example:

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```

Benefits:

* Repeatable
* Version controlled
* Automated
* Consistent

---

# 3. Terraform Workflow

Always remember:

```text
Write Code
    ↓
terraform fmt
    ↓
terraform validate
    ↓
terraform plan
    ↓
terraform apply
```

---

# 4. Terraform Providers

Providers connect Terraform to cloud platforms.

Example:

```hcl
provider "aws" {
  region = "us-east-1"
}
```

Provider acts as:

```text
Terraform ↔ AWS API
```

Without provider:

```text
Terraform doesn't know where to create resources.
```

---

# 5. Variables

Instead of hardcoding:

❌

```hcl
region = "us-east-1"
```

Use:

```hcl
variable "region" {}

provider "aws" {
 region = var.region
}
```

Benefits:

```text
Reusable
Flexible
Environment Independent
```

---

# 6. Variable Types

```hcl
string
number
bool
list
map
object
```

Examples:

```hcl
type = string
```

```hcl
type = list(string)
```

```hcl
type = map(string)
```

Interview Question:

**Difference between list and map?**

List:

```text
Ordered
Index Based
```

Map:

```text
Key Value Pair
```

---

# 7. Terraform Files

Common structure:

```text
terraform-project/

├── main.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
├── provider.tf
└── modules/
```

Purpose:

### main.tf

Resources

### variables.tf

Inputs

### outputs.tf

Outputs

### terraform.tfvars

Values

### provider.tf

Provider Configuration

---

# 8. What is a VPC?

VPC = Virtual Private Cloud

Think of it as:

```text
Your own private AWS network.
```

Example:

```text
VPC
CIDR: 10.0.0.0/16
```

Contains:

```text
Subnets
Route Tables
Security Groups
NACLs
Internet Gateway
```

---

# 9. CIDR Basics

CIDR:

```text
10.0.0.0/16
```

Means:

```text
Network Range
```

More hosts:

```text
/16
```

Less hosts:

```text
/24
```

Interview:

```text
/16 = 65,536 IPs
/24 = 256 IPs
```

---

# 10. Public Subnet

Public subnet has route:

```text
0.0.0.0/0
     ↓
Internet Gateway
```

Resources:

```text
ALB
Bastion Host
NAT Gateway
```

Can reach internet directly.

---

# 11. Private Subnet

Private subnet:

```text
No direct Internet Gateway route
```

Resources:

```text
EKS Worker Nodes
Databases
Internal Services
```

More secure.

---

# 12. Internet Gateway (IGW)

Purpose:

```text
Connect VPC to Internet
```

Flow:

```text
EC2
 ↓
Subnet
 ↓
Route Table
 ↓
IGW
 ↓
Internet
```

---

# 13. NAT Gateway

Big interview topic.

Purpose:

```text
Private subnet resources
can access internet
without being publicly accessible.
```

Flow:

```text
Private EC2
     ↓
NAT Gateway
     ↓
Internet
```

Use Cases:

```text
Install packages
Pull Docker Images
Download Updates
```

---

# 14. Route Tables

Traffic controller.

Example:

```text
Destination      Target

10.0.0.0/16      Local
0.0.0.0/0        IGW
```

Private Route Table:

```text
Destination      Target

10.0.0.0/16      Local
0.0.0.0/0        NAT
```

---

# 15. Why EKS Needs Private Subnets

Best practice:

```text
Control Plane Managed by AWS
Worker Nodes in Private Subnets
```

Benefits:

```text
Reduced attack surface
Better security
Production Ready
```

---

# 16. Terraform Modules

Module = Reusable Terraform code.

Instead of:

```text
Copy Paste
Copy Paste
Copy Paste
```

Create:

```text
module "vpc"
module "eks"
module "security"
```

Benefits:

```text
Reusable
Maintainable
Cleaner Code
```

---

# 17. AWS VPC Module

Very commonly used module:

```text
terraform-aws-modules/vpc/aws
```

Handles:

```text
VPC
Subnets
NAT
Route Tables
IGW
```

Automatically.

---

# 18. EKS Module

Popular module:

```text
terraform-aws-modules/eks/aws
```

Creates:

```text
Cluster
Node Groups
IAM
Networking
```

with fewer lines of code.

---

# Interview Questions

### What is Terraform?

Infrastructure as Code tool used to provision and manage infrastructure through code.

---

### Why use variables?

To make configurations reusable and avoid hardcoding.

---

### Difference between Public and Private Subnet?

Public:

```text
Internet Accessible
```

Private:

```text
No Direct Internet Access
```

---

### Why NAT Gateway?

Allows outbound internet access from private resources without exposing them publicly.

---

### What is a Module?

Reusable collection of Terraform resources.

---

### Why EKS nodes in private subnet?

Security and production best practices.

---

# 1-Minute Revision

```text
Terraform = IaC

Provider = Connects Terraform to AWS

Variable = Reusable input

VPC = Private AWS Network

CIDR = IP Range

Public Subnet = Internet Accessible

Private Subnet = Internal Only

IGW = Internet Access

NAT = Outbound Internet for Private Resources

Route Table = Traffic Rules

Module = Reusable Terraform Code

VPC Module = Creates Networking

EKS Module = Creates Kubernetes Cluster
```

***

# EKS Internals + IAM + Node Groups + kubectl + Deployments + Services

---

# 19. What is EKS?

EKS = Elastic Kubernetes Service

AWS Managed Kubernetes.

Instead of managing:

```text
Kubernetes Master Nodes
etcd
API Server
Scheduler
Controller Manager
```

AWS manages them.

You manage:

```text
Worker Nodes
Pods
Applications
```

---

# 20. EKS Architecture

```text
                 AWS Managed

           ┌─────────────────┐
           │ Control Plane   │
           │ API Server      │
           │ Scheduler       │
           │ Controller      │
           │ etcd            │
           └────────┬────────┘
                    │
                    │
      ┌─────────────┴─────────────┐
      │                           │
      ▼                           ▼

 Worker Node 1              Worker Node 2
      │                           │
      ▼                           ▼

    Pods                        Pods
```

Interview Question:

Who manages the Control Plane?

Answer:

```text
AWS
```

---

# 21. Control Plane

Brain of Kubernetes.

Contains:

```text
API Server
Scheduler
Controller Manager
etcd
```

Responsibilities:

```text
Accept Requests
Schedule Pods
Maintain Desired State
Store Cluster Data
```

---

# 22. Worker Node

Actual EC2 instance running containers.

Contains:

```text
kubelet
container runtime
kube-proxy
pods
```

Think:

```text
Control Plane = Brain

Worker Node = Muscle
```

---

# 23. What is a Pod?

Smallest deployable Kubernetes unit.

Example:

```text
1 Container
```

or

```text
Multiple Containers
```

inside one pod.

Architecture:

```text
Pod
 │
 └── Container
```

---

# 24. Why Not Run Containers Directly?

Kubernetes manages:

```text
Scaling
Recovery
Scheduling
Networking
```

instead of manually managing containers.

---

# 25. Managed Node Group

AWS feature.

Instead of manually creating EC2 instances:

```text
AWS creates
AWS updates
AWS replaces
AWS scales
```

worker nodes.

Terraform Example:

```hcl
eks_managed_node_groups
```

Interview:

Difference?

Self Managed:

```text
You manage nodes
```

Managed:

```text
AWS manages nodes
```

---

# 26. Node Group Architecture

```text
EKS Cluster

      │

      ▼

Managed Node Group

      │

 ┌────┴────┐

 ▼         ▼

EC2       EC2

 ▼         ▼

Pods      Pods
```

---

# 27. IAM Role for Cluster

Cluster requires AWS permissions.

Example:

```text
Create Load Balancer
Read Resources
Manage Networking
```

Without IAM:

```text
Access Denied
```

---

# 28. IAM Role for Worker Nodes

Worker nodes also need permissions.

Examples:

```text
Pull Images
Join Cluster
Write Logs
Access AWS Services
```

---

# 29. Access Entry

New EKS Authentication Method.

Old:

```text
aws-auth ConfigMap
```

New:

```text
Access Entries
```

Purpose:

```text
Grant access to cluster
```

Example:

```text
IAM User
      ↓
Access Entry
      ↓
EKS Cluster
```

---

# 30. Why Access Entries?

Advantages:

```text
Simpler
AWS Native
Easier Management
```

---

# 31. kubeconfig

Configuration file used by kubectl.

Contains:

```text
Cluster Endpoint
Certificate
Authentication Info
```

Location:

```text
~/.kube/config
```

Think:

```text
kubectl password book
```

---

# 32. Update kubeconfig

Important command:

```bash
aws eks update-kubeconfig \
--region us-east-1 \
--name my-cluster
```

Purpose:

```text
Connect local machine to EKS cluster
```

---

# 33. kubectl

Kubernetes CLI Tool.

Used to:

```text
Deploy
Delete
Inspect
Troubleshoot
```

Resources.

---

# 34. Important kubectl Commands

Check Nodes:

```bash
kubectl get nodes
```

Check Pods:

```bash
kubectl get pods
```

All Namespaces:

```bash
kubectl get pods -A
```

Services:

```bash
kubectl get svc
```

Deployments:

```bash
kubectl get deployments
```

---

# 35. Verify Cluster Connection

```bash
kubectl get nodes
```

Successful:

```text
STATUS = Ready
```

Example:

```text
NAME          STATUS

ip-10-0-1     Ready
ip-10-0-2     Ready
```

---

# 36. Kubernetes Deployment

Deployment manages Pods.

Example:

```text
Deployment
      │
      ▼
 ReplicaSet
      │
      ▼
   Pods
```

Benefits:

```text
Scaling
Self Healing
Rolling Updates
```

---

# 37. Deployment YAML

Example:

```yaml
apiVersion: apps/v1
kind: Deployment

spec:
  replicas: 2
```

Meaning:

```text
Keep 2 pods running
```

Always.

---

# 38. Self-Healing

Example:

```text
Pod Dies
   ↓
Deployment Detects
   ↓
Creates New Pod
```

Automatically.

Interview Favorite.

---

# 39. Scaling

Current:

```text
Replicas = 2
```

Increase:

```text
Replicas = 5
```

Kubernetes creates:

```text
3 Additional Pods
```

---

# 40. Service

Pods are temporary.

IP changes frequently.

Service provides:

```text
Stable Endpoint
```

Architecture:

```text
Service
   │
   ▼
 Pods
```

---

# 41. Types of Services

Main Types:

```text
ClusterIP
NodePort
LoadBalancer
```

---

# 42. ClusterIP

Default.

```text
Internal Access Only
```

Used for:

```text
Pod-to-Pod Communication
```

---

# 43. NodePort

Exposes application through:

```text
NodeIP:Port
```

Example:

```text
10.0.1.10:30001
```

---

# 44. LoadBalancer Service

Most common on AWS.

Flow:

```text
Internet
    │
    ▼
AWS Load Balancer
    │
    ▼
Service
    │
    ▼
Pods
```

---

# 45. Why LoadBalancer?

Advantages:

```text
External Access
High Availability
Traffic Distribution
```

---

# 46. Application Deployment Flow

```text
Deployment YAML
       │
       ▼
Deployment
       │
       ▼
ReplicaSet
       │
       ▼
Pods
       │
       ▼
Service
       │
       ▼
Load Balancer
       │
       ▼
Users
```

---

# 47. Common EKS Verification Commands

Nodes:

```bash
kubectl get nodes
```

Pods:

```bash
kubectl get pods
```

Services:

```bash
kubectl get svc
```

Describe Pod:

```bash
kubectl describe pod pod-name
```

Logs:

```bash
kubectl logs pod-name
```

Namespaces:

```bash
kubectl get ns
```

---

# 48. EKS Interview Questions

### What is EKS?

Managed Kubernetes Service on AWS.

---

### Who manages Control Plane?

AWS.

---

### What is a Worker Node?

EC2 instance running pods.

---

### What is a Pod?

Smallest deployable Kubernetes unit.

---

### What is kubeconfig?

Configuration file used by kubectl to connect to cluster.

---

### Why update kubeconfig?

To connect local machine with EKS.

---

### Difference Between Deployment and Pod?

Pod:

```text
Single Running Unit
```

Deployment:

```text
Manages Pods
Provides Scaling and Self-Healing
```

---

### Why Service?

Provides stable endpoint to pods.

---

### Difference Between ClusterIP and LoadBalancer?

ClusterIP:

```text
Internal Access
```

LoadBalancer:

```text
External Access
```

---

# Future Malathi (6-Month Revision)

If you forget everything, remember:

```text
EKS = Managed Kubernetes

AWS manages:
Control Plane

We manage:
Nodes
Pods
Applications

Deployment:
Keeps Pods Running

Service:
Provides Stable Access

LoadBalancer:
Exposes App to Internet

kubeconfig:
Connects kubectl to Cluster

kubectl get nodes:
First command after cluster creation

Managed Node Group:
AWS manages worker nodes

Access Entry:
Controls who can access EKS
```

***



# EKS Networking + IAM + Security Groups + NACLs + Pod Networking

---

# 49. Complete EKS Network Architecture

```text
Internet
    │
    ▼
Internet Gateway
    │
    ▼
Public Subnets
    │
    ├── NAT Gateway
    │
    ▼
Private Subnets
    │
    ├── EKS Worker Node
    │
    │      ▼
    │    Pod
    │
    └── EKS Worker Node
           ▼
         Pod

AWS Managed Control Plane
```

---

# 50. Why Put EKS Nodes in Private Subnets?

Imagine:

```text
Public EC2
     ↓
Internet Can Reach It
```

Security Risk.

Instead:

```text
Worker Node
      ↓
Private Subnet
```

Benefits:

```text
Not directly reachable
Reduced attack surface
Production best practice
```

---

# 51. How Private Nodes Access Internet?

Private subnets cannot access internet directly.

Need:

```text
NAT Gateway
```

Flow:

```text
Worker Node
     │
     ▼
NAT Gateway
     │
     ▼
Internet
```

Use Cases:

```text
Docker image pulls
OS updates
Package downloads
```

---

# 52. What Happens During Docker Pull?

Example:

```text
Pod Needs nginx Image
```

Flow:

```text
Worker Node
      │
      ▼
Private Route Table
      │
      ▼
NAT Gateway
      │
      ▼
Docker Hub / ECR
```

Without NAT:

```text
Image Pull Fails
```

---

# 53. IAM Overview

IAM = Identity and Access Management

Controls:

```text
Who?
Can do What?
On Which Resource?
```

Formula:

```text
User/Role
      +
Policy
      =
Permission
```

---

# 54. Why EKS Needs IAM?

Cluster performs AWS actions.

Examples:

```text
Create Load Balancer
Manage Networking
Read Security Groups
Manage ENIs
```

Without IAM:

```text
Access Denied
```

---

# 55. EKS Cluster IAM Role

Used by:

```text
Control Plane
```

Allows:

```text
Communicate with AWS Services
Create Resources
Manage Networking
```

---

# 56. Worker Node IAM Role

Used by:

```text
EC2 Worker Nodes
```

Allows:

```text
Join Cluster
Pull Images
Send Logs
Communicate with EKS
```

---

# 57. Common Managed Policies

### AmazonEKSClusterPolicy

Used by:

```text
EKS Cluster
```

---

### AmazonEKSWorkerNodePolicy

Used by:

```text
Worker Nodes
```

---

### AmazonEC2ContainerRegistryReadOnly

Allows:

```text
Pull Images From ECR
```

---

# 58. Security Groups

Security Group = Virtual Firewall

Controls:

```text
Inbound Traffic
Outbound Traffic
```

Think:

```text
Security Guard At Building Entrance
```

---

# 59. Security Group Flow

```text
Internet
    │
    ▼
Security Group
    │
    ▼
EC2 Instance
```

Traffic checked before entering.

---

# 60. Stateful Nature

Security Groups are:

```text
STATEFUL
```

Meaning:

```text
Request Allowed
Response Automatically Allowed
```

Example:

```text
Laptop → EC2

Reply Automatically Allowed
```

No extra rule needed.

---

# 61. Security Group Example

Allow SSH:

```text
Type: SSH

Port: 22

Source:
My IP
```

Allow HTTP:

```text
Port 80
```

Allow HTTPS:

```text
Port 443
```

---

# 62. Security Groups in EKS

Usually:

```text
Cluster SG

Node SG

Load Balancer SG
```

Each has separate responsibilities.

---

# 63. Security Group Communication

Example:

```text
Load Balancer SG
        │
        ▼
Node SG
        │
        ▼
Pods
```

Only approved traffic allowed.

---

# 64. NACL

NACL = Network Access Control List

Works at:

```text
Subnet Level
```

---

# 65. Security Group vs NACL

| Security Group   | NACL         |
| ---------------- | ------------ |
| Instance Level   | Subnet Level |
| Stateful         | Stateless    |
| Allow Rules Only | Allow + Deny |
| Easier           | More Complex |

Interview Favorite.

---

# 66. Stateful vs Stateless

Security Group:

```text
Request Allowed
Response Automatically Allowed
```

NACL:

```text
Request Allowed

Need Separate Rule For Response
```

---

# 67. EKS Pod Networking

One of the most important concepts.

In Kubernetes:

```text
Every Pod Gets Its Own IP
```

Example:

```text
Pod A = 10.0.1.5

Pod B = 10.0.1.6
```

---

# 68. How Do Pods Get IPs?

AWS VPC CNI Plugin.

It assigns:

```text
Real VPC IP Addresses
```

to pods.

---

# 69. What is VPC CNI?

CNI:

```text
Container Network Interface
```

AWS plugin responsible for:

```text
Pod Networking
IP Allocation
ENI Management
```

---

# 70. ENI

Elastic Network Interface.

Think:

```text
Virtual Network Card
```

attached to EC2.

---

# 71. ENI Architecture

```text
EC2 Worker Node
      │
      ▼
ENI
      │
      ▼
Pod IPs
```

---

# 72. Pod-to-Pod Communication

```text
Pod A
  │
  ▼
Pod B
```

Direct communication.

No NAT required.

Kubernetes networking rule:

```text
Every Pod Can Reach Every Pod
```

---

# 73. Pod-to-Service Flow

```text
Pod
 │
 ▼
Service
 │
 ▼
Other Pods
```

Service acts as stable endpoint.

---

# 74. External User to Pod Flow

This is a VERY common interview question.

```text
User
 │
 ▼
AWS Load Balancer
 │
 ▼
Service
 │
 ▼
Pod
```

---

# 75. Complete Request Flow

```text
Browser

   │

   ▼

Load Balancer

   │

   ▼

Node

   │

   ▼

Pod

   │

   ▼

Application
```

Response follows same path backwards.

---

# 76. Internal Cluster Communication

```text
Frontend Pod
      │
      ▼
Backend Service
      │
      ▼
Backend Pod
```

This stays inside cluster.

No internet involved.

---

# 77. Why Services Exist?

Pods are temporary.

Example:

```text
Pod Dies
```

New Pod Created:

```text
Different IP
```

Service solves this.

Provides:

```text
Stable DNS
Stable Endpoint
```

---

# 78. DNS in Kubernetes

Every Service gets DNS.

Example:

```text
backend-service
```

Frontend can call:

```text
http://backend-service
```

instead of IP.

---

# 79. Common EKS Networking Interview Questions

### Why Private Subnets for Worker Nodes?

Security.

---

### Why NAT Gateway?

Internet access for private resources.

---

### What is VPC CNI?

AWS networking plugin that assigns VPC IPs to pods.

---

### What is ENI?

Elastic Network Interface.

Virtual network card attached to EC2.

---

### Difference Between Security Group and NACL?

Security Group:

```text
Stateful
Instance Level
```

NACL:

```text
Stateless
Subnet Level
```

---

### Can Pods Communicate Directly?

Yes.

Kubernetes networking model:

```text
All Pods Can Reach All Pods
```

---

### Why Service?

Stable endpoint for pods.

---

### Request Flow from User to Pod?

```text
User
 ↓
Load Balancer
 ↓
Service
 ↓
Pod
```

---

# Future Malathi (6-Month Revision)

If you remember only one page:

```text
Worker Nodes → Private Subnets

NAT Gateway →
Internet Access For Private Resources

IAM →
Permissions

Security Group →
Instance Firewall (Stateful)

NACL →
Subnet Firewall (Stateless)

VPC CNI →
Assigns IPs To Pods

ENI →
Virtual Network Card

Service →
Stable Endpoint

Load Balancer →
Exposes Application

Traffic Flow:

User
 ↓
Load Balancer
 ↓
Service
 ↓
Pod
```


***

# EKS Authentication + Access Entries + Troubleshooting + Terraform Cleanup

This section contains many real-world interview questions and production issues.

---

# 80. Authentication vs Authorization

Many candidates confuse these.

### Authentication

Answers:

```text
Who are you?
```

Example:

```text
IAM User
IAM Role
AWS SSO User
```

---

### Authorization

Answers:

```text
What are you allowed to do?
```

Example:

```text
View Pods
Create Deployments
Delete Services
```

---

# 81. EKS Authentication Flow

```text
User
  │
  ▼
IAM Identity
  │
  ▼
AWS Authentication
  │
  ▼
EKS Cluster
```

First EKS verifies identity.

Then permissions are checked.

---

# 82. Old Method: aws-auth ConfigMap

Previously:

```text
IAM User
     │
     ▼
aws-auth ConfigMap
     │
     ▼
EKS Access
```

Problems:

```text
Hard to manage
Manual updates
Error-prone
```

---

# 83. New Method: Access Entries

AWS now recommends:

```text
Access Entry
```

Architecture:

```text
IAM User
     │
     ▼
Access Entry
     │
     ▼
EKS Cluster
```

---

# 84. Why Access Entries?

Benefits:

```text
AWS Native
Easier Management
Better Visibility
Cleaner Access Control
```

---

# 85. Authentication Modes

EKS supports:

```text
CONFIG_MAP
API
API_AND_CONFIG_MAP
```

---

### CONFIG_MAP

Uses:

```text
aws-auth ConfigMap
```

Old approach.

---

### API

Uses:

```text
Access Entries Only
```

Modern approach.

---

### API_AND_CONFIG_MAP

Supports both.

Useful during migration.

---

# 86. Access Policy

Access Entry defines:

```text
Who Can Access
```

Policy defines:

```text
What They Can Do
```

Example:

```text
Administrator
Developer
Read Only
```

---

# 87. kubeconfig Authentication

When running:

```bash
kubectl get nodes
```

Flow:

```text
kubectl
   │
   ▼
kubeconfig
   │
   ▼
AWS IAM
   │
   ▼
EKS
```

---

# 88. Most Important Verification Command

```bash
kubectl get nodes
```

If output appears:

```text
Ready
```

Cluster access works.

---

# 89. What Happens When a Node Joins EKS?

```text
EC2 Instance
      │
      ▼
Installs kubelet
      │
      ▼
Contacts Control Plane
      │
      ▼
Registers Itself
      │
      ▼
Ready
```

---

# 90. Node States

Common states:

```text
Ready
NotReady
SchedulingDisabled
```

---

### Ready

Healthy.

Can run pods.

---

### NotReady

Problem exists.

Node cannot host workloads.

---

# 91. Pod Lifecycle

```text
Pending
   │
   ▼
ContainerCreating
   │
   ▼
Running
   │
   ▼
Succeeded / Failed
```

---

# 92. Pending Pods

Meaning:

```text
Pod Not Scheduled Yet
```

Possible Causes:

```text
No Available Nodes
Insufficient CPU
Insufficient Memory
Taints
Node Problems
```

---

# 93. Troubleshooting Pending Pods

Check:

```bash
kubectl describe pod pod-name
```

Look at:

```text
Events Section
```

Usually tells exact reason.

---

# 94. CrashLoopBackOff

Very common interview question.

Meaning:

```text
Container Starts
Container Crashes
Kubernetes Restarts
Repeats Forever
```

---

# 95. Common CrashLoopBackOff Causes

```text
Application Bug
Wrong Environment Variable
Database Unreachable
Wrong Startup Command
Missing Secret
```

---

# 96. Check Pod Logs

Command:

```bash
kubectl logs pod-name
```

Usually first troubleshooting step.

---

# 97. ImagePullBackOff

Meaning:

```text
Pod Cannot Download Image
```

---

# 98. Common Causes

```text
Wrong Image Name
Wrong Tag
Private Repository
Permission Issue
No Internet Access
```

---

# 99. Example

Wrong:

```yaml
image: nginx123
```

Kubernetes:

```text
Image Not Found
```

Result:

```text
ImagePullBackOff
```

---

# 100. Node Not Ready

Common Causes:

```text
Network Issue
Kubelet Failure
Resource Exhaustion
AWS Issue
```

---

# 101. Check Nodes

```bash
kubectl get nodes
```

---

# 102. Detailed Node Information

```bash
kubectl describe node node-name
```

Shows:

```text
Conditions
Events
Resources
```

---

# 103. Check Everything

Pods:

```bash
kubectl get pods -A
```

Services:

```bash
kubectl get svc
```

Deployments:

```bash
kubectl get deploy
```

Nodes:

```bash
kubectl get nodes
```

---

# 104. Describe Resource

Golden troubleshooting command:

```bash
kubectl describe
```

Examples:

```bash
kubectl describe pod mypod
kubectl describe node mynode
kubectl describe svc myservice
```

---

# 105. Event Investigation

Always check:

```text
Events:
```

section.

Most root causes appear there.

---

# 106. Terraform Destroy

Removes infrastructure.

Command:

```bash
terraform destroy
```

Flow:

```text
Terraform State
      │
      ▼
AWS Resources
      │
      ▼
Deletion
```

---

# 107. Why Destroy?

Avoid:

```text
Unnecessary AWS Costs
Unused Resources
```

---

# 108. Destroy Best Practice

Before:

```bash
terraform destroy
```

Run:

```bash
terraform plan -destroy
```

Preview deletion.

---

# 109. Terraform State Importance

Terraform tracks:

```text
What Exists
What Changed
What Must Be Deleted
```

Without state:

```text
Terraform Loses Resource Tracking
```

---

# 110. Cleanup Sequence

Good practice:

```text
Delete Workloads
Delete Cluster
Delete Networking
```

instead of random deletion.

---

# 111. Common Terraform Errors

### Resource Already Exists

Reason:

```text
Manual Resource Creation
State Drift
```

---

### Access Denied

Reason:

```text
IAM Permission Missing
```

---

### Dependency Error

Reason:

```text
Resource Still In Use
```

---

# 112. State Drift

Definition:

```text
Terraform State
≠
Actual AWS Environment
```

Example:

```text
Terraform Created VPC

Someone Deleted VPC Manually
```

Now:

```text
State Incorrect
```

---

# 113. Fix State Drift

Commands:

```bash
terraform refresh
```

or

```bash
terraform import
```

depending on situation.

---

# 114. Real Production Scenario

Problem:

```text
Application Not Reachable
```

Investigation:

```text
Load Balancer?
Service?
Pod?
Container?
```

Flow:

```text
User
 ↓
Load Balancer
 ↓
Service
 ↓
Pod
 ↓
Container
```

Check layer by layer.

---

# 115. Interview Scenario

Question:

```text
Pods Running
But Website Not Opening
```

Approach:

```text
Check Service
Check Endpoints
Check Load Balancer
Check Security Groups
Check Application Logs
```

---

# 116. Interview Scenario

Question:

```text
Deployment Says 3 Replicas
Only 1 Running
```

Check:

```bash
kubectl describe deployment
kubectl get pods
kubectl describe pod
```

Likely:

```text
Resource Shortage
Image Issue
CrashLoopBackOff
```

---

# 117. Golden Troubleshooting Workflow

```text
1. Check Nodes

kubectl get nodes

2. Check Pods

kubectl get pods -A

3. Describe Resource

kubectl describe

4. Check Logs

kubectl logs

5. Check Events

Events Section

6. Verify Networking

Service
Load Balancer
Security Group
```

---

# Top Interview Questions

### Difference Between Authentication and Authorization?

Authentication:

```text
Who Are You?
```

Authorization:

```text
What Can You Do?
```

---

### What Replaced aws-auth ConfigMap?

```text
Access Entries
```

---

### What Causes CrashLoopBackOff?

Container repeatedly crashes after starting.

---

### What Causes ImagePullBackOff?

Image download failure.

---

### First Command After Cluster Creation?

```bash
kubectl get nodes
```

---

### Most Useful Troubleshooting Command?

```bash
kubectl describe
```

---

### What is State Drift?

Terraform state differs from actual infrastructure.

---

# Future Malathi (6-Month Revision)

Remember this page:

```text
Authentication =
Who Are You?

Authorization =
What Can You Do?

Access Entries =
Modern EKS Access Control

Pending =
Scheduling Problem

CrashLoopBackOff =
Application Crashing

ImagePullBackOff =
Image Download Failure

kubectl describe =
Best Troubleshooting Command

kubectl logs =
Application Errors

terraform destroy =
Cleanup Infrastructure

State Drift =
Terraform State ≠ AWS Reality
```

***

# Terraform Registry + Modules + Best Practices + Interview Master Sheet

This is the final revision section that ties together everything from Day 66.

---

# 118. Terraform Registry

Terraform Registry is a repository of reusable Terraform modules.

Think:

```text
GitHub for Terraform Modules
```

Instead of creating everything from scratch:

```text
VPC
Subnets
NAT
EKS
IAM
```

you can use pre-built modules.

---

# 119. Popular Registry Modules

### VPC Module

```text
terraform-aws-modules/vpc/aws
```

Creates:

```text
VPC
Subnets
NAT Gateway
Route Tables
Internet Gateway
```

---

### EKS Module

```text
terraform-aws-modules/eks/aws
```

Creates:

```text
Cluster
Node Groups
IAM
Networking Components
```

---

# 120. Why Use Modules?

Without modules:

```text
1000+ Lines
Complex Maintenance
More Errors
```

With modules:

```text
Less Code
Faster Deployment
Best Practices Included
```

---

# 121. Module Structure

```text
modules/

├── vpc
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf

├── eks
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
```

---

# 122. Module Invocation

Example:

```hcl
module "vpc" {
 source = "./modules/vpc"
}
```

Terraform:

```text
Reads Module
Creates Resources
Returns Outputs
```

---

# 123. Input Variables

Used to customize module behavior.

Example:

```hcl
module "vpc" {
 cidr = "10.0.0.0/16"
}
```

Input:

```text
CIDR Block
```

---

# 124. Outputs

Used to expose values.

Example:

```hcl
output "vpc_id" {
 value = aws_vpc.main.id
}
```

Returns:

```text
vpc-123456
```

---

# 125. Module Reusability

Same module can create:

```text
Development Environment
Testing Environment
Production Environment
```

Only inputs change.

---

# 126. Production Environment Example

```text
Dev
 └── Small Nodes

QA
 └── Medium Nodes

Prod
 └── Large Nodes
```

Same codebase.

Different variable values.

---

# 127. Why Organizations Love Modules

Benefits:

```text
Consistency
Standardization
Governance
Reduced Errors
```

---

# 128. Infrastructure Reusability

Bad:

```text
Copy
Paste
Modify
Repeat
```

Good:

```text
Reusable Modules
```

---

# 129. Terraform Dependency Graph

Terraform automatically calculates:

```text
What To Create First
What Depends On What
```

Example:

```text
VPC
 ↓
Subnet
 ↓
EKS
```

Terraform understands sequence.

---

# 130. Terraform Graph Thinking

Terraform creates:

```text
Dependency Graph
```

Example:

```text
VPC
 │
 ▼
Subnet
 │
 ▼
Node Group
 │
 ▼
Pods
```

---

# 131. Infrastructure Lifecycle

Terraform manages:

```text
Create
Update
Delete
```

Commands:

```bash
terraform apply
terraform apply
terraform destroy
```

---

# 132. Desired State Concept

Terraform always tries to reach:

```text
Desired State
```

Example:

Code:

```text
3 EC2 Instances
```

Reality:

```text
2 EC2 Instances
```

Terraform creates:

```text
1 Missing Instance
```

---

# 133. Declarative vs Imperative

### Imperative

Tell system HOW.

```text
Create VPC
Create Subnet
Create Route Table
```

---

### Declarative

Tell system WHAT.

```text
I Need A VPC
I Need A Cluster
```

Terraform figures out HOW.

---

# 134. Why DevOps Teams Prefer Declarative?

Benefits:

```text
Simpler
Repeatable
Less Error-Prone
```

---

# 135. Production EKS Architecture

```text
Internet
    │
    ▼
Application Load Balancer
    │
    ▼
Public Subnets
    │
    ▼
Private Subnets
    │
    ▼
Managed Node Groups
    │
    ▼
Pods

AWS Managed Control Plane
```

---

# 136. Complete Request Journey

Interview Favorite.

```text
User

 ↓

DNS

 ↓

Load Balancer

 ↓

Service

 ↓

Pod

 ↓

Container

 ↓

Application
```

Response:

```text
Application
 ↓
Container
 ↓
Pod
 ↓
Service
 ↓
Load Balancer
 ↓
User
```

---

# 137. Complete EKS Creation Flow

```text
Terraform Apply

      │

      ▼

VPC Created

      │

      ▼

Subnets Created

      │

      ▼

IAM Roles Created

      │

      ▼

EKS Cluster Created

      │

      ▼

Node Group Created

      │

      ▼

Nodes Join Cluster

      │

      ▼

kubectl Connects

      │

      ▼

Applications Deployed
```

---

# 138. Most Important Day 66 Commands

Terraform:

```bash
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
terraform destroy
```

---

AWS CLI:

```bash
aws configure

aws sts get-caller-identity

aws eks update-kubeconfig \
--region us-east-1 \
--name cluster-name
```

---

kubectl:

```bash
kubectl get nodes

kubectl get pods

kubectl get svc

kubectl get deploy

kubectl describe pod pod-name

kubectl describe node node-name

kubectl logs pod-name
```

---

# 139. Most Common Day 66 Mistakes

### Forgetting kubeconfig update

Result:

```text
Cannot Connect To Cluster
```

---

### Missing IAM Permissions

Result:

```text
Access Denied
```

---

### Wrong Region

Result:

```text
Cluster Not Found
```

---

### No NAT Gateway

Result:

```text
Image Pull Failures
```

---

### Wrong Security Group

Result:

```text
Application Not Reachable
```

---

# 140. Top 30 Rapid-Fire Interview Questions

### What is EKS?

Managed Kubernetes Service.

### Who manages Control Plane?

AWS.

### Who manages Pods?

You.

### What is kubeconfig?

Cluster connection configuration.

### What is kubectl?

Kubernetes CLI.

### What is a Pod?

Smallest deployable Kubernetes unit.

### What is Deployment?

Manages Pods.

### What is Service?

Stable endpoint.

### What is LoadBalancer?

External access.

### What is Node Group?

Collection of worker nodes.

### Why private subnets?

Security.

### Why NAT Gateway?

Outbound internet access.

### What is VPC CNI?

Pod networking plugin.

### What is ENI?

Virtual network card.

### What is IAM?

Identity and Access Management.

### Authentication?

Who are you?

### Authorization?

What can you do?

### Access Entry?

Modern EKS access control.

### CrashLoopBackOff?

Container repeatedly crashes.

### ImagePullBackOff?

Image download failure.

### Pending Pod?

Scheduling problem.

### Security Group?

Stateful firewall.

### NACL?

Stateless firewall.

### State Drift?

State ≠ Reality.

### Terraform Module?

Reusable code.

### Terraform Registry?

Public module repository.

### Terraform State?

Resource tracking database.

### Most useful troubleshooting command?

```bash
kubectl describe
```

### Most useful log command?

```bash
kubectl logs
```

### First command after EKS creation?

```bash
kubectl get nodes
```

---

# Final 1-Page Revision Sheet

```text
Terraform = Infrastructure as Code

Provider = Terraform ↔ AWS

Variable = Reusable Input

Module = Reusable Terraform Code

Registry = Public Module Store

VPC = Private AWS Network

Public Subnet = Internet Accessible

Private Subnet = Internal

IGW = Internet Access

NAT = Outbound Internet

Route Table = Traffic Rules

EKS = Managed Kubernetes

Control Plane = AWS Managed

Worker Nodes = EC2 Instances

Node Group = Group Of Workers

Pod = Smallest Deployable Unit

Deployment = Manages Pods

Service = Stable Endpoint

LoadBalancer = Internet Access

VPC CNI = Pod Networking

ENI = Virtual NIC

IAM = Permissions

Authentication = Who Are You?

Authorization = What Can You Do?

Access Entry = Cluster Access

Security Group = Stateful Firewall

NACL = Stateless Firewall

Pending = Scheduling Issue

CrashLoopBackOff = App Crash

ImagePullBackOff = Image Download Failure

kubectl describe = Best Troubleshooting Tool

kubectl logs = Application Logs

terraform destroy = Cleanup

State Drift = State ≠ Reality
```

### Future Malathi (Remember Only This)

```text
Terraform builds infrastructure.

VPC provides networking.

EKS provides Kubernetes.

AWS manages Control Plane.

We manage Nodes, Pods, Applications.

Deployment keeps pods running.

Service provides stable access.

LoadBalancer exposes apps.

IAM controls permissions.

Access Entries control cluster access.

Security Groups secure resources.

NAT enables internet for private resources.

kubectl describe + kubectl logs solve most problems.

User
 ↓
Load Balancer
 ↓
Service
 ↓
Pod
 ↓
Application
```

***

# 141. What Happens During `terraform init`?

Most people memorize:

```bash
terraform init
```

but don't understand it.

During init Terraform:

```text
1. Reads configuration files

2. Downloads providers

3. Downloads modules

4. Creates .terraform folder

5. Creates lock file
```

Architecture:

```text
terraform init

      │

      ▼

Download AWS Provider

      │

      ▼

Download Kubernetes Provider

      │

      ▼

Download EKS Module

      │

      ▼

Download VPC Module
```

---

# 142. What Happens During `terraform plan`?

Terraform compares:

```text
Desired State
        VS
Current State
```

Example:

```text
Code:
3 EC2

AWS:
0 EC2
```

Plan:

```text
+ Create 3 EC2
```

No resources are created yet.

---

# 143. What Happens During `terraform apply`?

This is where infrastructure is actually built.

Flow:

```text
Terraform Code
      │
      ▼
Dependency Graph
      │
      ▼
AWS API Calls
      │
      ▼
Resource Creation
```

---

# 144. Terraform Dependency Graph

A major concept from the original notes.

Example:

```text
VPC
 │
 ▼
Subnets
 │
 ▼
EKS Cluster
 │
 ▼
Node Group
```

Terraform automatically knows order.

You don't tell Terraform:

```text
Create VPC first
```

Terraform calculates it.

---

# 145. Why Terraform Created ~58 Resources

A common surprise.

You wrote:

```hcl
module "eks"
```

but Terraform created dozens of resources.

Because EKS module internally creates:

```text
IAM Roles
Security Groups
Launch Templates
Node Groups
KMS Keys
Policies
Attachments
Networking Components
```

---

# 146. Terraform Registry

The original file emphasizes this.

Instead of writing:

```hcl
500 lines of VPC code
```

you use:

```hcl
terraform-aws-modules/vpc/aws
```

and get:

```text
VPC
Subnets
Route Tables
IGW
NAT
```

automatically.

---

# 147. Why Companies Love Registry Modules

Benefits:

```text
Battle Tested

Community Maintained

Production Ready

Less Code

Faster Delivery
```

---

# 148. What Is an API?

The original journal explicitly explains this.

API:

```text
Application Programming Interface
```

Think:

```text
Waiter in a Restaurant
```

Example:

```text
Terraform
    │
    ▼
AWS API
    │
    ▼
AWS Creates Resource
```

---

# 149. Provider Concept (Very Important)

Provider acts as translator.

Example:

```text
Terraform
     │
     ▼
AWS Provider
     │
     ▼
AWS API
```

Without provider:

```text
Terraform can't talk to AWS.
```

---

# 150. Why EKS Needed Multiple Availability Zones

The file stresses this.

EKS Best Practice:

```text
AZ-1
AZ-2
```

Example:

```text
us-east-1a
us-east-1b
```

If one AZ fails:

```text
Application survives
```

---

# 151. Why NAT Gateway Costs Money

The journal repeatedly mentions cost awareness.

NAT Gateway:

```text
Hourly Cost
+
Data Processing Cost
```

Even if cluster is idle:

```text
NAT still costs money
```

This is why cleanup mattered.

---

# 152. Real Cost Components Created

The notes specifically call these out.

Resources that incur cost:

```text
NAT Gateway

EKS Control Plane

EC2 Worker Nodes

Elastic IP

Load Balancer
```

---

# 153. Why Terraform Destroy Was Mandatory

Without destroy:

```text
Cluster Continues Running

Nodes Continue Running

NAT Continues Running

Bill Continues Growing
```

---

# 154. Why Kubernetes Resources Should Be Deleted First

Original workflow:

Step 1:

```bash
kubectl delete
```

Step 2:

```bash
terraform destroy
```

Reason:

```text
Load Balancer cleanup becomes easier.

Dependency issues reduce.
```

---

# 155. Control Plane vs Data Plane

A concept strongly emphasized.

### Control Plane

Managed by AWS.

Contains:

```text
API Server
Scheduler
Controller Manager
etcd
```

---

### Data Plane

Managed by you.

Contains:

```text
Worker Nodes
Pods
Applications
```

---

# 156. What Is kubelet?

One of the missing concepts.

kubelet runs on every node.

Responsibilities:

```text
Talks to API Server

Starts Containers

Monitors Pods

Reports Health
```

---

# 157. Why Port 10250 Appeared

The file discusses this.

Port:

```text
10250
```

Used by:

```text
kubelet
```

Purpose:

```text
Node Management Communication
```

---

# 158. What Is kube-proxy?

Runs on every worker node.

Responsible for:

```text
Service Networking

Traffic Routing

Load Distribution
```

Flow:

```text
Service
    │
    ▼
kube-proxy
    │
    ▼
Correct Pod
```

---

# 159. What Is CoreDNS?

System component inside Kubernetes.

Purpose:

```text
DNS Resolution
```

Example:

Instead of:

```text
10.0.1.25
```

Applications use:

```text
backend-service
```

CoreDNS resolves it.

---

# 160. What Is aws-node?

One of the kube-system pods.

Responsible for:

```text
AWS VPC CNI

Pod Networking

IP Assignment
```

Without aws-node:

```text
Pods cannot get IPs.
```

---

# 161. Why Nodes Showed `<none>` Under Roles

The original notes explain this.

Command:

```bash
kubectl get nodes
```

Output:

```text
ROLES
<none>
```

Many people think it's broken.

It's normal.

Reason:

```text
Managed Node Groups
don't automatically assign
Kubernetes node roles.
```

Cluster is healthy.

---

# 162. Why We Used EKS Instead of KIND

Original comparison.

### KIND

```text
Local Machine

Learning

No AWS Cost
```

---

### EKS

```text
Real Cloud

Production Grade

AWS Managed
```

---

# 163. Why Port Forward Worked

A great troubleshooting lesson.

Command:

```bash
kubectl port-forward
```

Worked because:

```text
Traffic bypassed
Load Balancer
Security Groups
External Networking
```

Meaning:

```text
Application was healthy.
```

Problem existed elsewhere.

---

# 164. LoadBalancer Debugging Logic

One of the best lessons in the file.

When application doesn't open:

Check:

```text
Load Balancer

Service

Endpoints

Pods

Container
```

Never guess.

Follow the path.

---

# 165. Real DevOps Workflow Practiced

The journal highlights this.

You didn't just create EKS.

You practiced:

```text
Infrastructure Provisioning

Cloud Networking

IAM

Kubernetes

Troubleshooting

Verification

Cleanup

Cost Management
```

---

# Most Important Hidden Lesson From Day 66

The file repeats this idea in different ways:

```text
Terraform builds infrastructure.

AWS provides cloud resources.

EKS provides Kubernetes.

kubectl manages workloads.

Troubleshooting follows the request path.

Cleanup prevents unnecessary cost.
```

***

