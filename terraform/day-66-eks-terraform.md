# DAY 66 MASTER NOTES

# PART A – Assignment Overview (The Foundation)

---

# 1. What Was Day 66 About?

The official goal of Day 66 was:

> Provision a Kubernetes cluster on AWS using Amazon EKS and Terraform Modules, deploy an application, verify it works, and then clean up all resources.

At first glance this sounds simple:

```text
Create EKS
Deploy Nginx
Delete Everything
```

But the actual learning is much bigger.

Day 66 is the first day where we move from:

```text
Local Kubernetes
```

to

```text
Cloud Kubernetes
```

Before Day 66 we mostly worked with:

```text
Kind
Minikube
Docker Desktop Kubernetes
```

which run on our laptop.

Day 66 teaches how Kubernetes is deployed in a real cloud environment.

---

# 2. Why Is This Day Important?

Many beginners learn Kubernetes like this:

```text
kubectl create deployment nginx
kubectl get pods
kubectl get svc
```

and think:

```text
"I know Kubernetes."
```

But in reality, Kubernetes alone is not enough.

A production environment requires:

```text
Networking
Security
Identity Management
Load Balancing
Compute Resources
Automation
Monitoring
```

Day 66 introduces these concepts.

---

# 3. What Did We Actually Build?

Most people think:

```text
Terraform
    ↓
EKS Cluster
```

But we actually built:

```text
AWS Account
│
├── VPC
│
├── Public Subnets
│
├── Private Subnets
│
├── Internet Gateway
│
├── NAT Gateway
│
├── Route Tables
│
├── Security Groups
│
├── IAM Roles
│
├── KMS Encryption
│
├── EKS Control Plane
│
├── Managed Node Group
│
├── EC2 Worker Nodes
│
├── Kubernetes Pods
│
└── AWS Load Balancer
```

EKS is only one piece of the puzzle.

---

# 4. The Big Picture Architecture

The final architecture looked like this:

```text
                    Internet
                        │
                        ▼
                AWS Load Balancer
                        │
                        ▼
                 Public Subnets
                        │
                        ▼
                  NAT Gateway
                        │
                        ▼
                Private Subnets
                 │           │
                 ▼           ▼
              Node 1      Node 2
                 │           │
                 ▼           ▼
             Nginx Pods   Nginx Pods

          AWS Managed EKS Control Plane
```

Every component exists for a reason.

Day 66 teaches why.

---

# 5. What Problem Were We Solving?

Imagine manually creating:

```text
VPC
Subnets
NAT Gateway
Route Tables
IAM Roles
Security Groups
EKS Cluster
Worker Nodes
```

through the AWS Console.

This would require:

```text
Dozens of screens
Hundreds of clicks
Many chances for mistakes
```

Terraform solves this problem.

Instead of:

```text
Infrastructure in AWS Console
```

we store:

```text
Infrastructure in Code
```

This concept is called:

```text
Infrastructure as Code (IaC)
```

---

# 6. What Is Infrastructure as Code?

Infrastructure as Code means:

```text
Servers
Networks
Security
Cloud Resources
```

are described using code files.

Example:

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```

Instead of clicking buttons:

```text
Create VPC
Enter CIDR
Click Next
Click Finish
```

we write code.

Benefits:

```text
Repeatable
Version Controlled
Reviewable
Automated
Easy to Rebuild
```

---

# 7. Why Did We Use Terraform?

Terraform is one of the most popular IaC tools.

Think:

```text
Git manages source code.

Terraform manages infrastructure.
```

Terraform allows us to:

```text
Create Infrastructure
Update Infrastructure
Delete Infrastructure
```

using code.

Example:

```bash
terraform plan
terraform apply
terraform destroy
```

Three commands can manage an entire cloud environment.

---

# 8. Why Did We Use EKS?

EKS stands for:

```text
Elastic Kubernetes Service
```

It is AWS's managed Kubernetes service.

Without EKS, we would need to manage:

```text
API Server
etcd
Scheduler
Controller Manager
```

ourselves.

With EKS:

```text
AWS manages Kubernetes control plane.
```

We focus on:

```text
Applications
Pods
Services
Deployments
```

This reduces operational complexity.

---

# 9. What Is the Difference Between Local Kubernetes and EKS?

### Before Day 66

```text
Laptop
   │
   ▼
Kind / Minikube
   │
   ▼
Kubernetes
```

Everything runs locally.

Advantages:

```text
Free
Easy
Fast
```

Limitations:

```text
Not production-like
No AWS integration
No IAM
No real networking
```

---

### After Day 66

```text
AWS Cloud
   │
   ▼
EKS Cluster
   │
   ▼
Worker Nodes
   │
   ▼
Applications
```

Advantages:

```text
Scalable
Highly Available
Cloud Native
Production Ready
```

---

# 10. What Was the Workflow?

The Day 66 workflow was:

```text
Write Terraform Code
        │
        ▼
terraform init
        │
        ▼
terraform plan
        │
        ▼
terraform apply
        │
        ▼
Create EKS Cluster
        │
        ▼
Configure kubectl
        │
        ▼
Deploy Nginx
        │
        ▼
Verify Application
        │
        ▼
Delete Workloads
        │
        ▼
terraform destroy
```

This is a real-world Infrastructure Lifecycle.

---

# 11. What Were the Main Learning Areas?

Day 66 is actually several topics combined.

### Terraform

Learned:

```text
Providers
Modules
State
Plan
Apply
Destroy
```

---

### AWS

Learned:

```text
VPC
Subnets
NAT Gateway
IAM
Security Groups
KMS
```

---

### EKS

Learned:

```text
Control Plane
Node Groups
Worker Nodes
Authentication
Cluster Access
```

---

### Kubernetes

Learned:

```text
Deployments
Pods
Services
LoadBalancers
kubectl
```

---

# 12. What Was Successfully Achieved?

By the end of Day 66:

✅ Created AWS networking

✅ Created EKS cluster

✅ Created Managed Node Group

✅ Connected kubectl

✅ Verified worker nodes

✅ Verified system pods

✅ Deployed Nginx application

✅ Created LoadBalancer service

✅ Verified application access

✅ Destroyed infrastructure cleanly

---

# 13. The Biggest Mindset Shift

Before Day 66:

```text
Kubernetes = Pods + Deployments + Services
```

After Day 66:

```text
Production Kubernetes =
Kubernetes
+
Networking
+
IAM
+
Security
+
Load Balancing
+
Cloud Infrastructure
+
Automation
```

This is the biggest lesson.

---

# 14. Real DevOps Insight

Day 66 was not really about:

```text
Running Nginx
```

Anyone can run Nginx.

The real lesson was understanding how all of these work together:

```text
Terraform
     ↓
AWS
     ↓
VPC
     ↓
EKS
     ↓
Worker Nodes
     ↓
Pods
     ↓
Services
     ↓
Load Balancer
     ↓
Users
```

That chain is the foundation of modern cloud-native infrastructure.

---

# 15. One-Line Summary for Future Malathi

> Day 66 taught me how Terraform can automatically provision a complete AWS Kubernetes platform—including networking, security, IAM, EKS control plane, worker nodes, and load balancing—allowing applications to run in a production-style environment instead of a local Kubernetes cluster.

---

# 1-Minute Revision (Part A)

```text
Day 66 Goal:
Provision EKS using Terraform.

Major Components:
VPC
Subnets
NAT Gateway
IAM
Security Groups
KMS
EKS
Node Groups
Pods
Services
Load Balancer

Tools Used:
Terraform
AWS
kubectl
EKS

Workflow:
init
plan
apply
configure kubectl
deploy app
verify
destroy

Biggest Lesson:
Production Kubernetes is not just Kubernetes.
It is networking, security, IAM, infrastructure,
automation, and Kubernetes working together.
```

***

# DAY 66 MASTER NOTES

# PART B – Terraform Fundamentals (Deep Dive)

---

# 1. What Is Terraform?

Terraform is an **Infrastructure as Code (IaC)** tool created by HashiCorp.

Terraform allows us to create, modify, and delete infrastructure using code instead of manually clicking through cloud consoles.

Think:

```text
Application Code
        ↓
Creates Software

Terraform Code
        ↓
Creates Infrastructure
```

---

# 2. Why Was Terraform Created?

Before Terraform, infrastructure was usually created manually.

Example:

```text
Login AWS Console
    ↓
Create VPC
    ↓
Create Subnets
    ↓
Create Route Tables
    ↓
Create Security Groups
    ↓
Create EC2
    ↓
Create Load Balancer
```

Problems:

```text
Time-consuming
Human errors
Difficult to reproduce
Hard to track changes
No version control
```

---

# 3. The Real Problem Terraform Solves

Imagine your company has:

```text
Development Environment
Testing Environment
Staging Environment
Production Environment
```

Without Terraform:

```text
Create each environment manually
Hope configurations match
Pray nobody makes mistakes
```

With Terraform:

```text
Write Once
Deploy Anywhere
```

Same code can create identical environments.

---

# 4. What Is Infrastructure as Code (IaC)?

Infrastructure as Code means:

```text
Servers
Networks
Databases
Load Balancers
IAM Roles
Security Groups
```

are defined in code files.

Example:

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```

This code describes infrastructure.

Terraform converts it into real AWS resources.

---

# 5. Traditional Infrastructure vs IaC

### Traditional Method

```text
Engineer
   ↓
AWS Console
   ↓
Clicks Buttons
   ↓
Infrastructure Created
```

Problem:

```text
No documentation
No history
Not repeatable
```

---

### Infrastructure as Code

```text
Engineer
    ↓
Terraform Code
    ↓
Git Repository
    ↓
terraform apply
    ↓
Infrastructure Created
```

Benefits:

```text
Version Control
Automation
Repeatability
Collaboration
Disaster Recovery
```

---

# 6. Terraform's Core Philosophy

Terraform follows a concept called:

```text
Desired State
```

This is one of the most important ideas in DevOps.

---

Instead of saying:

```text
Create one VPC now
```

you describe:

```text
I want a VPC with CIDR 10.0.0.0/16
```

Terraform continuously compares:

```text
Current State
vs
Desired State
```

---

# 7. Understanding Desired State

Example:

Current AWS:

```text
No VPC Exists
```

Terraform Code:

```text
Need One VPC
```

Difference:

```text
Create VPC
```

---

Later:

Current AWS:

```text
VPC Exists
```

Terraform Code:

```text
Need One VPC
```

Difference:

```text
Nothing
```

Terraform reports:

```text
No changes.
```

---

# 8. How Terraform Works Internally

When we run Terraform:

```text
Terraform Code
      ↓
Terraform Engine
      ↓
Provider
      ↓
Cloud API
      ↓
Infrastructure Created
```

For AWS:

```text
Terraform
     ↓
AWS Provider
     ↓
AWS APIs
     ↓
AWS Resources
```

---

# 9. Terraform Architecture

Terraform consists of:

```text
Configuration Files
        ↓
State File
        ↓
Providers
        ↓
Resources
        ↓
Modules
```

These five pieces form Terraform's foundation.

---

# 10. What Is HCL?

Terraform uses:

```text
HCL
```

which stands for:

```text
HashiCorp Configuration Language
```

Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-12345"
  instance_type = "t2.micro"
}
```

HCL is designed to be:

```text
Readable
Simple
Human Friendly
```

---

# 11. What Is a Resource?

Resource = Anything Terraform Creates.

Examples:

```text
VPC
EC2
S3 Bucket
IAM Role
EKS Cluster
Security Group
```

Example:

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```

Meaning:

```text
Create one AWS VPC
```

---

# 12. Resource Anatomy

Example:

```hcl
resource "aws_vpc" "main" {
}
```

Breakdown:

```text
resource      → Terraform keyword

aws_vpc       → Resource type

main          → Local name
```

Think:

```text
resource "type" "nickname"
```

---

# 13. What Is a Provider?

Provider = Translator.

Terraform itself doesn't know AWS.

Terraform doesn't know Azure.

Terraform doesn't know Kubernetes.

Providers teach Terraform how to talk to them.

---

Example:

```hcl
provider "aws" {
  region = "us-west-2"
}
```

Meaning:

```text
Use AWS APIs
in us-west-2 region
```

---

# 14. Why Providers Matter

Without providers:

```text
Terraform cannot create anything.
```

Terraform only understands:

```text
Desired State
```

Provider understands:

```text
Cloud APIs
```

Provider acts as translator.

---

# 15. Terraform and AWS API Relationship

You write:

```hcl
resource "aws_vpc" "main" {}
```

Terraform does NOT create the VPC itself.

Instead:

```text
Terraform
      ↓
AWS Provider
      ↓
CreateVpc API
      ↓
AWS Creates VPC
```

Terraform is orchestrating API calls.

---

# 16. What Is an API?

API stands for:

```text
Application Programming Interface
```

Think:

```text
Remote Control
```

Instead of clicking buttons:

```text
Terraform sends requests
AWS responds
```

Everything in AWS Console eventually becomes API calls.

---

# 17. What Is a Terraform Module?

A Module is a reusable collection of Terraform code.

Think:

```text
Function in Programming
```

Example:

Without module:

```text
Create VPC
Create IGW
Create NAT
Create Route Tables
Create Subnets
```

Hundreds of lines.

---

With module:

```hcl
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
}
```

Done.

---

# 18. Why Modules Are Powerful

Modules provide:

```text
Reusability
Consistency
Best Practices
Faster Development
```

This is why we used:

```text
VPC Module
EKS Module
```

instead of writing everything ourselves.

---

# 19. Terraform Registry

Modules are often downloaded from:

```text
Terraform Registry
```

Think:

```text
GitHub for Terraform Modules
```

Popular examples:

```text
VPC Module
EKS Module
ALB Module
RDS Module
```

---

# 20. What Is State?

State is Terraform's memory.

File:

```text
terraform.tfstate
```

Terraform records:

```text
Resource IDs
ARNs
Relationships
Attributes
```

inside this file.

---

# 21. Why State Is Important

Suppose Terraform creates:

```text
VPC
EKS Cluster
Subnets
```

Later:

```bash
terraform destroy
```

Terraform must know:

```text
What did I create?
```

State provides that answer.

---

# 22. What Happens Without State?

Without state:

```text
Terraform becomes blind.
```

It cannot easily determine:

```text
What exists
What changed
What to destroy
```

State is critical.

---

# 23. Terraform Lifecycle

The normal lifecycle is:

```text
Write Code
      ↓
Init
      ↓
Plan
      ↓
Apply
      ↓
Modify
      ↓
Plan
      ↓
Apply
      ↓
Destroy
```

This cycle repeats throughout infrastructure management.

---

# 24. terraform init

Command:

```bash
terraform init
```

Purpose:

```text
Initialize Working Directory
```

Downloads:

```text
Providers
Modules
Plugins
```

Think:

```text
npm install
```

for Terraform.

---

# 25. terraform plan

Command:

```bash
terraform plan
```

Purpose:

```text
Preview Changes
```

Terraform compares:

```text
Current State
Desired State
```

and shows:

```text
What will happen
```

Example:

```text
58 to add
0 to change
0 to destroy
```

Nothing is created yet.

---

# 26. terraform apply

Command:

```bash
terraform apply
```

Purpose:

```text
Execute Changes
```

Terraform sends API calls to AWS.

Infrastructure becomes real.

Example:

```text
Create VPC
Create EKS
Create Node Group
Create IAM Roles
```

---

# 27. terraform destroy

Command:

```bash
terraform destroy
```

Purpose:

```text
Delete Infrastructure
```

Terraform reads state and removes resources.

Example:

```text
Delete Nodes
Delete Cluster
Delete NAT
Delete VPC
```

---

# 28. Why Day 66 Needed Terraform

Without Terraform:

```text
Hundreds of AWS Console clicks
```

With Terraform:

```text
terraform apply
```

created:

```text
58 AWS resources
```

automatically.

---

# 29. Terraform vs Kubernetes

Interesting observation:

Terraform manages:

```text
Infrastructure Desired State
```

Kubernetes manages:

```text
Application Desired State
```

Comparison:

```text
Terraform
      ↓
VPC
EKS
IAM
Nodes

Kubernetes
      ↓
Pods
Deployments
Services
```

Both use the same philosophy:

```text
Desired State Management
```

---

# 30. Biggest Lesson From Part B

Terraform is NOT:

```text
AWS Automation Tool
```

Terraform is:

```text
A Desired State Engine
```

that converts code into infrastructure through cloud APIs while tracking everything in state.

---

# Interview Questions

### What is Terraform?

Terraform is an Infrastructure as Code tool used to create, modify, and destroy infrastructure using code.

---

### What is a Provider?

A provider is a plugin that allows Terraform to communicate with cloud platforms such as AWS, Azure, GCP, or Kubernetes.

---

### What is State?

State is Terraform's memory file (`terraform.tfstate`) that stores information about created resources.

---

### Difference Between Plan and Apply?

```text
terraform plan
→ Preview

terraform apply
→ Execute
```

---

### What Is a Module?

A reusable collection of Terraform resources that can be used repeatedly across projects.

---

# 1-Minute Revision (Part B)

```text
Terraform = Infrastructure as Code

HCL = Terraform Language

Provider = Translator

Resource = Infrastructure Object

Module = Reusable Terraform Package

State = Terraform Memory

terraform init
→ Download dependencies

terraform plan
→ Preview changes

terraform apply
→ Create/modify infrastructure

terraform destroy
→ Delete infrastructure

Terraform manages:
Infrastructure Desired State

Kubernetes manages:
Application Desired State
```

***

# Day 66 – EKS with Terraform Modules

# Part C – EKS Cluster Deep Dive (Control Plane, Nodes, IAM, Networking)

This section explains **what actually got created**, **why 58+ resources appeared**, and **how AWS + Kubernetes work together internally**.

---

# What is EKS?

EKS stands for:

```text
Elastic Kubernetes Service
```

It is AWS's managed Kubernetes offering.

Without EKS:

```text
You manage:

API Server
etcd
Scheduler
Controller Manager
Certificates
Upgrades
Backups
High Availability
```

With EKS:

```text
AWS manages:

API Server
etcd
Scheduler
Controller Manager
Control Plane Upgrades
Control Plane Availability
```

You manage:

```text
Applications
Pods
Deployments
Services
Worker Nodes
```

---

# Understanding the Control Plane

The Control Plane is the brain of Kubernetes.

Components:

```text
API Server
Scheduler
Controller Manager
etcd
```

Architecture:

```text
                Control Plane
          (Managed by AWS EKS)

            ┌─────────────┐
            │ API Server  │
            └──────┬──────┘
                   │
            ┌──────▼──────┐
            │ Scheduler   │
            └──────┬──────┘
                   │
            ┌──────▼──────┐
            │ Controllers │
            └──────┬──────┘
                   │
            ┌──────▼──────┐
            │   etcd      │
            └─────────────┘
```

---

# API Server

Think of it as:

```text
Front Door of Kubernetes
```

Every request goes through API Server.

Examples:

```bash
kubectl get pods
kubectl get nodes
kubectl apply -f nginx.yaml
kubectl delete pod xyz
```

Flow:

```text
kubectl
    ↓
API Server
```

---

# Scheduler

Scheduler decides:

```text
Which node should run a pod?
```

Example:

```yaml
replicas: 3
```

Kubernetes must place:

```text
Pod 1
Pod 2
Pod 3
```

Scheduler chooses appropriate nodes.

---

# Controller Manager

Controller Manager continuously checks:

```text
Current State
vs
Desired State
```

Example:

Desired:

```text
3 Pods
```

Current:

```text
2 Pods
```

Controller immediately creates:

```text
1 New Pod
```

This is Kubernetes self-healing.

---

# etcd

etcd is Kubernetes database.

Stores:

```text
Pods
Deployments
Services
ConfigMaps
Secrets
Nodes
Cluster State
```

Think:

```text
Brain Memory
```

Everything Kubernetes knows is stored in etcd.

---

# What is a Worker Node?

Worker Node = EC2 Instance

Example:

```text
Node 1
Node 2
```

These are actual AWS virtual machines.

Pods run here.

Containers run here.

Applications run here.

---

# Control Plane vs Worker Nodes

```text
Control Plane
     ↓
Makes Decisions

Worker Nodes
     ↓
Run Workloads
```

Diagram:

```text
          EKS Control Plane
           (Managed AWS)
                  │
        ┌─────────┴─────────┐
        │                   │
        ▼                   ▼

      Node 1            Node 2
      EC2               EC2

        │                 │
      Pods              Pods
```

---

# What is a Managed Node Group?

Instead of manually creating EC2 instances:

```text
Create EC2
Install Kubernetes
Configure IAM
Join Cluster
Create Auto Scaling
```

AWS automates everything.

Terraform:

```hcl
eks_managed_node_groups = {
   terraweek_nodes = {
      desired_size = 2
   }
}
```

AWS handles:

```text
Node Creation
Node Replacement
Scaling
Updates
Health Monitoring
```

---

# What Happens If a Node Dies?

Suppose:

```text
Desired Nodes = 2
Current Nodes = 1
```

AWS detects mismatch.

AWS automatically launches:

```text
Replacement Node
```

Cluster returns to:

```text
2 Nodes
```

This is infrastructure self-healing.

---

# Launch Template

Terraform created:

```text
Launch Template
```

Purpose:

```text
Blueprint for New Nodes
```

Contains:

```text
AMI
Instance Type
Security Groups
Disk Size
User Data
```

Whenever AWS needs a new node:

```text
Read Launch Template
Create EC2
Join Cluster
```

---

# What is kubelet?

Every worker node runs:

```text
kubelet
```

Think:

```text
Node Manager
```

Flow:

```text
API Server
      ↓
kubelet
      ↓
Container Created
```

---

# What is kube-proxy?

kube-proxy handles networking.

Responsible for:

```text
Service Routing
Pod Connectivity
Traffic Forwarding
```

Without kube-proxy:

```text
Services break
```

---

# What is CoreDNS?

Pods rarely communicate using IP addresses.

Instead:

```text
nginx-service
mysql-service
backend-service
```

CoreDNS translates:

```text
Name → IP
```

Example:

```text
nginx-service
      ↓
10.0.4.123
```

Just like internet DNS.

---

# What is aws-node?

You saw:

```text
aws-node
```

inside kube-system namespace.

This is:

```text
AWS VPC CNI Plugin
```

Purpose:

```text
Assign VPC IPs to Pods
Enable Pod Networking
```

Without aws-node:

```text
Pods cannot communicate
```

---

# Why IAM is Required

Kubernetes alone cannot access AWS.

AWS requires permissions.

Those permissions are provided using:

```text
IAM Roles
```

---

# Cluster IAM Role

Used by:

```text
EKS Control Plane
```

Permissions:

```text
Manage Networking
Create ENIs
Communicate with AWS APIs
```

---

# Node IAM Role

Used by:

```text
Worker Nodes
```

Permissions:

```text
Join Cluster
Pull Images
Access ECR
Send Logs
Communicate with AWS
```

Without Node Role:

```text
Nodes cannot join EKS
```

---

# Security Groups

Security Groups are AWS firewalls.

Think:

```text
Traffic Rules
```

Every EC2 node has one.

---

# Important Ports

## Port 443

```text
HTTPS
```

Used for:

```text
kubectl → API Server
```

---

## Port 10250

Used by:

```text
kubelet
```

Needed for:

```text
kubectl logs
kubectl exec
kubectl describe
```

---

# Why Nodes Showed `<none>` Under ROLES

Output:

```text
NAME       STATUS   ROLES
node-1     Ready    <none>
```

Many beginners think:

```text
Cluster Broken
```

Wrong.

Modern Kubernetes uses:

```text
Labels
```

instead of role names.

Therefore:

```text
<none>
```

is completely normal.

---

# What Terraform Actually Created

Although Terraform code looked small:

```hcl
module "eks" {}
```

AWS created:

```text
EKS Cluster
IAM Roles
IAM Policies
Security Groups
Launch Template
Node Group
Auto Scaling Resources
KMS Key
CloudWatch Integration
Networking Components
Access Entries
```

This is why:

```text
58+ Resources
```

appeared during plan.

---

# Why EKS Takes 10–15 Minutes

Terraform creates resources in dependency order.

```text
VPC
 ↓
Subnets
 ↓
Security Groups
 ↓
IAM Roles
 ↓
KMS
 ↓
EKS Control Plane
 ↓
Node Group
 ↓
EC2 Nodes
 ↓
Cluster Ready
```

Many AWS services must become healthy before the next step starts.

---

# What We Learned From Part C

By the end of this section you should understand:

```text
✓ What EKS actually is
✓ Control Plane vs Worker Nodes
✓ Scheduler, API Server, Controller Manager
✓ etcd purpose
✓ Managed Node Groups
✓ Launch Templates
✓ kubelet
✓ kube-proxy
✓ CoreDNS
✓ aws-node CNI
✓ IAM Roles
✓ Security Groups
✓ Important Kubernetes ports
✓ Why 58 resources were created
✓ How EKS components interact
```

---

## 1-Minute Revision Sheet

```text
EKS = AWS Managed Kubernetes

Control Plane:
API Server
Scheduler
Controller Manager
etcd

Worker Nodes:
EC2 Instances running Pods

Managed Node Group:
AWS manages node lifecycle

kubelet:
Runs containers on nodes

kube-proxy:
Routes Service traffic

CoreDNS:
Service discovery

aws-node:
Provides Pod networking

IAM:
Allows EKS and Nodes to use AWS APIs

Security Groups:
Firewall for cluster resources

Launch Template:
Blueprint for future nodes

Terraform created:
VPC + IAM + SG + KMS + EKS + Node Group + EC2

Control Plane = Brain
Nodes = Workers
Pods = Applications
```

***

# Day 66 – EKS with Terraform Modules

# Part D – kubectl Authentication, Access Entries, Deployments, Services & Traffic Flow

This section covers what happened **after the cluster was created**.

Most learners think:

```text
terraform apply
      ↓
Cluster Ready
      ↓
Done
```

Actually the second half of the exercise begins here.

You must:

```text
Connect kubectl
Verify Cluster
Deploy Application
Expose Application
Troubleshoot Access
Understand Traffic Flow
```

---

# Connecting kubectl to EKS

After EKS creation:

```text
Cluster Exists
```

But:

```text
kubectl
```

still does not know:

```text
Cluster Endpoint
Authentication Method
Certificates
```

So we ran:

```bash
aws eks update-kubeconfig \
--name terraweek-eks \
--region us-west-2
```

---

# What is kubeconfig?

File:

```text
~/.kube/config
```

Think:

```text
Address Book for kubectl
```

It stores:

```text
Cluster Name
Cluster Endpoint
Certificates
Authentication Method
```

Without kubeconfig:

```text
kubectl is lost
```

---

# What Does update-kubeconfig Actually Do?

Many beginners think:

```text
It changes the cluster
```

Wrong.

It changes:

```text
Your Local Machine
```

Before:

```text
kubectl
      ↓
Doesn't know cluster
```

After:

```text
kubectl
      ↓
Knows where EKS exists
```

---

# Verifying Cluster Connectivity

First command:

```bash
kubectl cluster-info
```

Expected:

```text
Kubernetes control plane is running
```

---

# Verifying Nodes

Command:

```bash
kubectl get nodes
```

Expected:

```text
STATUS = Ready
```

Example:

```text
ip-10-0-1-xx    Ready
ip-10-0-2-xx    Ready
```

This proves:

```text
Nodes successfully joined cluster
```

---

# The Authentication Error We Encountered

Initially:

```bash
kubectl get nodes
```

returned:

```text
You must be logged in to the server
```

or

```text
the server has asked for credentials
```

---

# First Reaction

Most beginners think:

```text
EKS Broken
```

Wrong.

The cluster was healthy.

Problem was:

```text
Authentication
```

---

# Authentication vs Authorization

These are different concepts.

---

## Authentication

Question:

```text
Who are you?
```

AWS verifies identity.

Example:

```text
Malathi
```

Authentication successful.

---

## Authorization

Question:

```text
What are you allowed to do?
```

Even if AWS knows who you are:

```text
You may still have no permissions.
```

---

# Debugging Process

We checked:

```bash
aws sts get-caller-identity
```

Purpose:

```text
Verify AWS Login
```

Output:

```text
Account ID
User ARN
```

---

# Verifying Token Generation

Command:

```bash
aws eks get-token \
--cluster-name terraweek-eks
```

Purpose:

```text
Can AWS generate EKS token?
```

If token returns:

```text
Authentication Working
```

---

# Verifying kubectl Identity

Command:

```bash
kubectl auth whoami
```

Purpose:

```text
How Kubernetes sees me
```

---

# Verifying Permissions

Command:

```bash
kubectl auth can-i get nodes
```

Expected:

```text
yes
```

This confirms authorization.

---

# Root Cause

Most likely:

```text
Access propagation delay
```

AWS permissions needed time to propagate.

Eventually:

```bash
kubectl get nodes
```

worked successfully.

---

# Important Lesson

```text
Authentication Issue
≠
Cluster Issue
```

Always verify identity first.

---

# What Are EKS Access Entries?

Historically EKS used:

```text
aws-auth ConfigMap
```

Modern EKS uses:

```text
Access Entries
```

Command:

```bash
aws eks list-access-entries \
--cluster-name terraweek-eks
```

Output:

```text
arn:aws:iam::xxxx:user/malathi
```

---

# Why Access Entries Matter

Flow:

```text
IAM User
      ↓
Access Entry
      ↓
Kubernetes Access
```

Without Access Entry:

```text
kubectl commands fail
```

---

# Verifying System Components

Command:

```bash
kubectl get pods -A
```

Important namespace:

```text
kube-system
```

---

# aws-node

Purpose:

```text
AWS Pod Networking
```

Provides:

```text
Pod IPs
VPC Integration
```

---

# kube-proxy

Purpose:

```text
Traffic Routing
```

Routes traffic:

```text
Service
   ↓
Pod
```

---

# CoreDNS

Purpose:

```text
Name Resolution
```

Converts:

```text
nginx-service
```

into:

```text
10.x.x.x
```

---

# Deploying Nginx

File:

```yaml
nginx-deployment.yaml
```

Contained:

```yaml
Deployment
Service
```

---

# What is a Deployment?

Deployment manages Pods.

Example:

```yaml
replicas: 3
```

Means:

```text
Always maintain 3 pods
```

---

# Desired State Concept

Kubernetes works using:

```text
Desired State
```

You say:

```text
I want 3 Pods
```

Kubernetes continuously checks reality.

---

# Example

Desired:

```text
3 Pods
```

Current:

```text
2 Pods
```

Controller Manager immediately creates:

```text
1 New Pod
```

Back to:

```text
3 Pods
```

---

# Applying the Deployment

Command:

```bash
kubectl apply -f nginx-deployment.yaml
```

Result:

```text
Deployment Created
Pods Created
Service Created
```

---

# Verifying Deployment

Command:

```bash
kubectl get deployments
```

Output:

```text
3/3 Available
```

Meaning:

```text
All Pods Healthy
```

---

# Verifying Pods

Command:

```bash
kubectl get pods
```

Output:

```text
Running
Running
Running
```

---

# What is a Service?

Pods are temporary.

Today:

```text
10.0.1.23
```

Tomorrow:

```text
10.0.2.88
```

Pod IPs change.

Applications cannot depend on Pod IPs.

---

# Service Provides Stability

Instead of:

```text
Pod IP
```

Clients use:

```text
nginx-service
```

Service becomes permanent.

---

# Service Types

Three major types:

---

## ClusterIP

```text
Internal Only
```

Used for:

```text
Backend Services
Databases
Microservices
```

---

## NodePort

```text
Expose Service Through Node
```

Example:

```text
NodeIP:32735
```

---

## LoadBalancer

```text
Creates Cloud Load Balancer
```

Used for:

```text
Public Websites
Public APIs
```

---

# Service Used in Day 66

```yaml
type: LoadBalancer
```

This triggers AWS integration.

---

# What Happens Internally?

Kubernetes sees:

```yaml
type: LoadBalancer
```

Then requests AWS:

```text
Create Load Balancer
```

AWS automatically provisions:

```text
Classic Load Balancer
```

No console clicks required.

---

# Complete Traffic Flow

This is one of the most important interview concepts.

```text
Browser
   ↓
AWS Load Balancer
   ↓
NodePort
   ↓
Kubernetes Service
   ↓
Pod
   ↓
Nginx Container
```

---

# Expanded Traffic Flow

```text
User Browser
      ↓
ELB DNS Name
      ↓
AWS Load Balancer
      ↓
Worker Node
      ↓
NodePort
      ↓
Service
      ↓
Pod
      ↓
Container
      ↓
Nginx Response
```

---

# Why Port 32735 Appeared

You saw something like:

```text
80:32735/TCP
```

Meaning:

```text
Service Port = 80
NodePort = 32735
```

Traffic:

```text
ELB
 ↓
32735
 ↓
Pod Port 80
```

---

# Understanding Endpoints

Command:

```bash
kubectl get endpoints
```

Output:

```text
10.0.1.xx:80
10.0.2.xx:80
10.0.3.xx:80
```

Endpoints are:

```text
Actual Pod Addresses
```

Service forwards traffic to these endpoints.

---

# Why Port Forward Worked

Command:

```bash
kubectl port-forward svc/nginx-service 8080:80
```

Access:

```text
http://localhost:8080
```

Displayed:

```text
Welcome to nginx!
```

---

# Why Is Port Forward Important?

Port Forward bypasses:

```text
Load Balancer
Security Groups
Internet
AWS Networking
```

Path:

```text
Laptop
   ↓
kubectl
   ↓
Pod
```

---

# What Did Port Forward Prove?

It proved:

```text
Pods Healthy
Deployment Healthy
Service Healthy
Cluster Healthy
```

This is valuable troubleshooting evidence.

---

# Load Balancer Investigation

We verified:

```bash
kubectl describe svc nginx-service
kubectl get endpoints
aws elb describe-instance-health
```

Checked:

```text
Pods
Endpoints
Nodes
ELB
```

layer by layer.

---

# Real DevOps Troubleshooting Method

Never assume.

Check each layer separately:

```text
Application
      ↓
Pod
      ↓
Service
      ↓
Node
      ↓
Load Balancer
      ↓
Network
```

Find where failure begins.

---

# What We Learned From Part D

```text
✓ kubeconfig purpose
✓ update-kubeconfig
✓ Authentication vs Authorization
✓ Access Entries
✓ kubectl troubleshooting
✓ Deployment
✓ Desired State
✓ Pods
✓ Services
✓ ClusterIP
✓ NodePort
✓ LoadBalancer
✓ ELB creation
✓ Endpoints
✓ Port Forwarding
✓ Traffic Flow
✓ Real-world troubleshooting
```

---

# 1-Minute Revision Sheet

```text
update-kubeconfig:
Connects kubectl to EKS

Authentication:
Who are you?

Authorization:
What can you do?

Access Entry:
Maps IAM User to Kubernetes

Deployment:
Maintains desired number of pods

Pods:
Run containers

Service:
Stable endpoint for pods

ClusterIP:
Internal

NodePort:
Expose via node port

LoadBalancer:
Creates AWS ELB

Traffic Flow:
Browser → ELB → Node → Service → Pod → Container

Port Forward:
Laptop → kubectl → Pod

Port Forward Success:
Proves cluster and application are healthy

Troubleshooting:
Check layer by layer, never assume.
```

***

# Day 66 – EKS with Terraform Modules

# Part E – Terraform State, Destroy Process, Cost Management, Interview Notes & Final Revision

This is the final section of Day 66.

Most beginners think the exercise ends after:

```text
Nginx Running
```

A DevOps engineer knows the lifecycle is:

```text
Plan
 ↓
Create
 ↓
Verify
 ↓
Operate
 ↓
Troubleshoot
 ↓
Destroy
```

Infrastructure is not complete until it is cleaned up properly.

---

# Terraform State

One of the most important Terraform concepts.

When Terraform creates resources:

```bash
terraform apply
```

it generates:

```text
terraform.tfstate
```

---

# What is terraform.tfstate?

Think:

```text
Terraform Memory File
```

Terraform records:

```text
VPC ID
Subnet IDs
Route Tables
NAT Gateway
IAM Roles
Security Groups
EKS Cluster
Node Groups
```

Example:

```text
Terraform Creates:

VPC
 ↓
vpc-12345

Subnet
 ↓
subnet-67890

EKS
 ↓
eks-cluster-abc
```

All IDs are stored inside state.

---

# Why State Is Required

Later when you run:

```bash
terraform destroy
```

Terraform asks:

```text
What did I create?
```

Answer comes from:

```text
terraform.tfstate
```

Without state:

```text
Terraform forgets everything.
```

---

# Real Production State Storage

For learning:

```text
Local State
```

For companies:

```text
S3 Bucket
+
DynamoDB Lock
```

Architecture:

```text
Terraform
     ↓
S3 State File
     ↓
Shared Team Access
```

Benefits:

```text
Centralized
Versioned
Recoverable
Collaborative
```

---

# Terraform Destroy

Command:

```bash
terraform destroy
```

Purpose:

```text
Delete Infrastructure
```

Terraform reads:

```text
terraform.tfstate
```

and removes resources.

---

# Destroy Happens in Reverse Order

Creation:

```text
VPC
 ↓
Subnets
 ↓
EKS
 ↓
Nodes
 ↓
Load Balancer
```

Destroy:

```text
Load Balancer
 ↓
Nodes
 ↓
EKS
 ↓
Subnets
 ↓
VPC
```

Terraform automatically handles dependencies.

---

# Why Kubernetes Resources Must Be Deleted First

Very important lesson.

Many people try:

```bash
terraform destroy
```

immediately.

Sometimes it fails.

---

# Problem

If LoadBalancer Service still exists:

```text
AWS ELB Exists
```

ELB depends on:

```text
Subnets
Security Groups
VPC
```

Terraform cannot remove those resources.

---

# Correct Cleanup Order

Step 1

```bash
kubectl delete -f nginx-deployment.yaml
```

---

Step 2

Verify:

```bash
kubectl get all
```

Application resources removed.

---

Step 3

Run:

```bash
terraform destroy
```

Now AWS infrastructure can be deleted cleanly.

---

# Why Cleanup Matters

AWS resources continue charging.

Even if unused.

---

# Resources That Cost Money

## EKS Control Plane

Charges per cluster.

Even with:

```text
0 Pods
```

cost continues.

---

## Worker Nodes

EC2 instances charge while running.

```text
Node Running
=
Billing Running
```

---

## NAT Gateway

One of the most common surprise costs.

Charges:

```text
Per Hour
+
Per GB
```

Even with little traffic.

---

## Load Balancer

Charges while provisioned.

---

## Elastic IP

May incur charges if left unused.

---

# Cost Awareness Lesson

Many learners focus on:

```text
terraform apply
```

Professional engineers focus equally on:

```text
terraform destroy
```

---

# What We Actually Built

Many people say:

```text
I created an EKS Cluster
```

Technically true.

But incomplete.

---

Actual platform:

```text
AWS Account
│
├── VPC
│
├── Public Subnets
│
├── Private Subnets
│
├── Internet Gateway
│
├── NAT Gateway
│
├── Route Tables
│
├── Security Groups
│
├── IAM Roles
│
├── KMS Key
│
├── EKS Control Plane
│
├── Managed Node Group
│
├── Launch Template
│
├── EC2 Worker Nodes
│
├── Kubernetes Services
│
├── Kubernetes Deployments
│
└── AWS Load Balancer
```

This is a production-style platform.

---

# Terraform vs Kubernetes

One of the biggest Day 66 insights.

Terraform manages:

```text
Infrastructure Desired State
```

Kubernetes manages:

```text
Application Desired State
```

Comparison:

| Terraform              | Kubernetes           |
| ---------------------- | -------------------- |
| Manages Infrastructure | Manages Applications |
| Creates VPC            | Creates Pods         |
| Creates IAM            | Creates Services     |
| Creates EKS            | Creates Deployments  |
| terraform apply        | kubectl apply        |

---

# Desired State Concept

Terraform:

```text
I want:

1 VPC
4 Subnets
1 EKS Cluster
2 Nodes
```

Terraform makes reality match.

---

Kubernetes:

```text
I want:

3 Nginx Pods
```

Kubernetes makes reality match.

---

Same philosophy.

Different layers.

---

# Common Mistakes Beginners Make

## Mistake 1

```text
Forget terraform init
```

Error:

```text
Provider not found
```

Fix:

```bash
terraform init
```

---

## Mistake 2

Wrong AWS credentials.

Check:

```bash
aws sts get-caller-identity
```

---

## Mistake 3

Forget kubeconfig update.

Fix:

```bash
aws eks update-kubeconfig \
--name cluster-name
```

---

## Mistake 4

Ignore subnet tags.

Without:

```text
kubernetes.io/role/elb
```

LoadBalancer services may fail.

---

## Mistake 5

Delete cluster before application resources.

Always:

```text
Delete Kubernetes Resources
        ↓
Destroy Terraform
```

---

## Mistake 6

Forget cleanup.

Result:

```text
Unexpected AWS Bill
```

---

# Day 66 Interview Questions

## Q1. What is EKS?

Answer:

```text
Amazon EKS is a managed Kubernetes service where AWS manages the Kubernetes control plane while users manage worker nodes and applications.
```

---

## Q2. What is the Control Plane?

Answer:

```text
The control plane consists of API Server, Scheduler, Controller Manager and etcd. It manages the overall state of the Kubernetes cluster.
```

---

## Q3. Why are worker nodes placed in private subnets?

Answer:

```text
For security. Private subnets prevent direct internet access while still allowing outbound internet connectivity through a NAT Gateway.
```

---

## Q4. What is a NAT Gateway?

Answer:

```text
A NAT Gateway allows resources in private subnets to access the internet while preventing inbound internet connections.
```

---

## Q5. What is a Managed Node Group?

Answer:

```text
A Managed Node Group is an AWS-managed collection of worker nodes that handles scaling, upgrades, replacement and health monitoring automatically.
```

---

## Q6. What is kubeconfig?

Answer:

```text
kubeconfig is a configuration file used by kubectl to locate clusters, authenticate users and establish secure connections.
```

---

## Q7. What is the difference between Deployment and Pod?

Answer:

```text
A Pod runs containers directly.

A Deployment manages Pods, supports scaling, self-healing and rolling updates.
```

---

## Q8. What is a Service?

Answer:

```text
A Service provides a stable network endpoint for Pods whose IP addresses may change.
```

---

## Q9. What happens when Service type LoadBalancer is used?

Answer:

```text
Kubernetes requests the cloud provider to create an external load balancer and expose the application publicly.
```

---

## Q10. What is Terraform State?

Answer:

```text
Terraform State is Terraform's memory file that stores information about created resources and their relationships.
```

---

# Complete Day 66 Architecture

```text
                    Internet
                        │
                        ▼
               AWS Load Balancer
                        │
                        ▼
                Public Subnets
                        │
                        ▼
                  NAT Gateway
                        │
                        ▼
 ------------------------------------------------
 |                                              |
 ▼                                              ▼

Private Subnet A                        Private Subnet B

Worker Node 1                           Worker Node 2
   │                                         │
   ▼                                         ▼

Nginx Pod                              Nginx Pod
Nginx Pod                              Nginx Pod

                ▲
                │
                │
       EKS Control Plane
          (AWS Managed)
```

---

# Final Day 66 Learning Summary

Before Day 66:

```text
Kind
Minikube
Local Kubernetes
```

After Day 66:

```text
Terraform
VPC
Subnets
Internet Gateway
NAT Gateway
IAM
Security Groups
KMS
EKS
Managed Node Groups
Load Balancers
kubectl Authentication
Deployments
Services
Terraform State
Cleanup Lifecycle
```

---

# Final One-Line Memory

> Day 66 taught me that a production Kubernetes environment is not just Kubernetes—it is networking (VPC, subnets, NAT), security (IAM, Security Groups, KMS), compute (EC2 nodes), orchestration (EKS), application management (Deployments and Services), and automation (Terraform) all working together as a single platform that can be created and destroyed through Infrastructure as Code.

***

 **why companies use EKS instead of kind/minikube**.

# Local Kubernetes vs Amazon EKS

| Feature             | Local Kubernetes (Kind/Minikube) | Amazon EKS               |
| ------------------- | -------------------------------- | ------------------------ |
| Purpose             | Learning & Development           | Production Workloads     |
| Runs On             | Your Laptop                      | AWS Cloud                |
| Control Plane       | Runs Locally                     | Managed by AWS           |
| Worker Nodes        | Local VM/Containers              | EC2 Instances            |
| High Availability   | No                               | Yes                      |
| Multi-AZ Support    | No                               | Yes                      |
| IAM Integration     | No                               | Native AWS IAM           |
| Auto Scaling        | Limited                          | Full Auto Scaling        |
| Load Balancers      | Simulated                        | Real AWS ELB/ALB         |
| Storage             | Local Disk                       | EBS, EFS, S3 Integration |
| Networking          | Local Docker Network             | Real VPC/Subnets         |
| Security Groups     | No                               | Yes                      |
| NAT Gateway         | No                               | Yes                      |
| Cost                | Free                             | Paid                     |
| Team Usage          | Personal Learning                | Enterprise Teams         |
| Terraform Usage     | Optional                         | Common Practice          |
| Disaster Recovery   | Weak                             | Strong                   |
| Kubernetes Upgrades | Manual                           | Managed                  |
| Monitoring          | Manual Setup                     | CloudWatch Integration   |
| Production Ready    | No                               | Yes                      |

---

# Architecture Comparison

## 1. Local Kubernetes (Kind / Minikube)

```text
                 Your Laptop
+---------------------------------------+
|                                       |
|  Docker / VM                          |
|                                       |
|   +-----------------------+           |
|   | Kubernetes Cluster    |           |
|   |                       |           |
|   | Control Plane         |           |
|   | Worker Node(s)        |           |
|   | Pods                  |           |
|   +-----------------------+           |
|                                       |
+---------------------------------------+
```

Everything runs on:

```text
One Machine
One Network
One User
```

If laptop dies:

```text
Cluster dies
```

---

## 2. Amazon EKS

```text
                     Internet
                         |
                         |
                  AWS Load Balancer
                         |
 ------------------------------------------------
 |                                              |
 Public Subnet A                        Public Subnet B
 |                                              |
 NAT Gateway
 |
 ------------------------------------------------
 |                                              |
 Private Subnet A                      Private Subnet B
 |                                              |
 EC2 Worker Node 1                    EC2 Worker Node 2
 |                                              |
 Pods                                   Pods

 ------------------------------------------------
              AWS Managed Control Plane
 ------------------------------------------------
        API Server
        Scheduler
        Controller Manager
        etcd
```

---

# Resource Ownership

## Kind / Minikube

You manage:

```text
Laptop
Docker
Control Plane
Worker Nodes
Storage
Networking
Everything
```

---

## EKS

AWS manages:

```text
Control Plane
etcd
Scheduler
API Server
Availability
```

You manage:

```text
Applications
Pods
Services
Deployments
```

---

# Networking Comparison

## Local Kubernetes

```text
Browser
   |
localhost
   |
NodePort
   |
Pod
```

Usually accessed via:

```bash
kubectl port-forward
```

or

```bash
minikube service
```

---

## EKS

```text
Browser
   |
AWS Load Balancer
   |
Kubernetes Service
   |
Pods
```

Real public DNS:

```text
ae3b244e4a8e844dc832717e3825bdca.us-west-2.elb.amazonaws.com
```

which you saw in Day 66.

---

# Scaling Comparison

## Kind

```text
1 Laptop
   |
4 CPU
8 GB RAM
```

Maximum cluster size depends on your machine.

---

## EKS

```text
AWS Cloud
   |
10 Nodes
50 Nodes
100 Nodes
1000 Nodes
```

Can scale almost indefinitely.

---

# Security Comparison

## Local Kubernetes

```text
Basic Authentication
Local Network
```

Good for learning.

---

## EKS

```text
IAM
Security Groups
VPC
Private Subnets
KMS Encryption
CloudTrail
```

Enterprise-grade security.

---

# What You Learned on Day 50 vs Day 66

## Day 50 (Kind/Minikube)

You learned Kubernetes concepts:

```text
Pods
Deployments
Services
ConfigMaps
Volumes
Ingress
kubectl
```

Focus:

```text
How Kubernetes Works
```

---

## Day 66 (EKS)

You learned Infrastructure concepts:

```text
Terraform
VPC
Subnets
NAT Gateway
IAM Roles
Security Groups
EKS
Node Groups
Load Balancers
KMS
```

Focus:

```text
How Kubernetes Runs In Production
```

---

# Simple Memory Trick

Think:

```text
Day 50
=
Learning Kubernetes
```

```text
Day 66
=
Learning Cloud Kubernetes
```

Or even simpler:

```text
Kind/Minikube
=
Toy Car 🚗
```

```text
Amazon EKS
=
Real Car 🚙
```

Both teach driving, but only one is used on real roads. Day 66 was your first experience running Kubernetes the way infrastructure teams run it in production.


