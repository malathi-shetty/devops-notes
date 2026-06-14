# Day 67 – TerraWeek Capstone 

## Part A – Terraform Workspaces + Project Structure

---

# Why Day 67 Matters

Days 61–66 taught individual Terraform concepts:

| Day | Focus                                    |
| --- | ---------------------------------------- |
| 61  | Terraform basics, IaC, state             |
| 62  | Providers, resources, dependencies       |
| 63  | Variables, outputs, locals, data sources |
| 64  | Backend, locking, drift detection        |
| 65  | Modules and reusable infrastructure      |
| 66  | EKS and production-grade modules         |
| 67  | Bringing everything together             |

Day 67 is the first time we use:

* Custom Modules
* Workspaces
* Multiple Environments
* Shared Codebase
* Environment Isolation

in a single project.

This resembles how real DevOps teams manage:

* Development
* Staging
* Production

without maintaining three separate Terraform repositories.

---

# Task 1 – Understanding Terraform Workspaces

---

## Creating Workspaces

Initialize Terraform:

```bash
terraform init
```

Create workspaces:

```bash
terraform workspace new dev
terraform workspace new staging
terraform workspace new prod
```

Verify:

```bash
terraform workspace list
```

Example:

```text
default
dev
staging
* prod
```

Meaning:

```text
* = currently selected workspace
```

---

# What is a Workspace?

A workspace is an isolated Terraform state within the same Terraform codebase.

Think of it like:

```text
Same Terraform code
        │
        ├── dev state
        ├── staging state
        └── prod state
```

Terraform code remains identical.

Only the state changes.

---

# terraform.workspace

Terraform provides a built-in variable:

```hcl
terraform.workspace
```

which automatically returns the active workspace.

Example:

```hcl
locals {
  environment = terraform.workspace
}
```

If currently selected:

```bash
terraform workspace select dev
```

then:

```hcl
terraform.workspace
```

returns:

```text
dev
```

Switch:

```bash
terraform workspace select prod
```

returns:

```text
prod
```

No code modifications required.

---

# Real Usage of terraform.workspace

Example:

```hcl
resource "aws_instance" "server" {

  instance_type =
    terraform.workspace == "prod"
    ? "t3.small"
    : "t2.micro"

  tags = {
    Environment = terraform.workspace
  }
}
```

Result:

### Dev

```text
Instance Type = t2.micro
```

### Staging

```text
Instance Type = t2.micro
```

### Prod

```text
Instance Type = t3.small
```

Same code.

Different behavior.

---

# Where Does Terraform Store State?

This is one of the most important interview questions.

---

## Default Workspace

Uses:

```text
terraform.tfstate
```

Example:

```text
project/
│
└── terraform.tfstate
```

---

## Additional Workspaces

Terraform automatically creates:

```text
terraform.tfstate.d/
│
├── dev/
│   └── terraform.tfstate
│
├── staging/
│   └── terraform.tfstate
│
└── prod/
    └── terraform.tfstate
```

---

# State Mapping

| Workspace | State File                                    |
| --------- | --------------------------------------------- |
| default   | terraform.tfstate                             |
| dev       | terraform.tfstate.d/dev/terraform.tfstate     |
| staging   | terraform.tfstate.d/staging/terraform.tfstate |
| prod      | terraform.tfstate.d/prod/terraform.tfstate    |

Each workspace has its own state.

---

# Workspaces with S3 Backend

When using remote state:

```hcl
backend "s3" {
  bucket = "terraform-state"
}
```

Terraform automatically separates states.

Example:

```text
env:/dev/terraform.tfstate
env:/staging/terraform.tfstate
env:/prod/terraform.tfstate
```

Result:

```text
Same code
Different states
Different infrastructure
```

---

# Workspaces vs Separate Directories

This is a very common interview topic.

---

## Approach 1 – Workspaces

Structure:

```text
terraform-project/
│
├── main.tf
├── variables.tf
├── outputs.tf
└── workspaces
```

Deployment:

```bash
terraform workspace select dev
```

or

```bash
terraform workspace select prod
```

Advantages:

* Single codebase
* Less duplication
* Easier maintenance
* Faster updates
* Consistent environments

Disadvantages:

* Logical isolation only
* Requires careful environment configuration

---

## Approach 2 – Separate Directories

Structure:

```text
terraform-project/

├── dev/
│   └── main.tf

├── staging/
│   └── main.tf

└── prod/
    └── main.tf
```

Advantages:

* Strong isolation
* Easier access control
* Environment-specific customization

Disadvantages:

* Duplicate code
* More maintenance
* Harder consistency management

---

# Comparison Table

| Feature                 | Workspaces           | Separate Directories          |
| ----------------------- | -------------------- | ----------------------------- |
| Codebase                | Single               | Multiple                      |
| State Files             | Separate             | Separate                      |
| Duplication             | Minimal              | High                          |
| Maintenance             | Easy                 | More effort                   |
| Isolation               | Logical              | Physical                      |
| Environment Consistency | High                 | Medium                        |
| Best For                | Similar environments | Highly different environments |

---

# Important Interview Point

Workspaces isolate:

```text
Terraform State
```

Workspaces do NOT automatically isolate:

```text
Network Design
CIDR Planning
Security Design
Architecture
```

Example:

Bad:

```text
dev     -> 10.0.0.0/16
staging -> 10.0.0.0/16
prod    -> 10.0.0.0/16
```

Even though states are separate:

```text
Network CIDRs overlap
```

which is poor design.

Correct:

```text
dev     -> 10.0.0.0/16
staging -> 10.1.0.0/16
prod    -> 10.2.0.0/16
```

---

# Key Takeaways from Task 1

Remember these interview answers:

### Q: What does terraform.workspace return?

```text
The currently selected workspace name.
```

---

### Q: Why use workspaces?

```text
To manage multiple environments from a single Terraform codebase.
```

---

### Q: Does each workspace have its own state?

```text
Yes.
Each workspace maintains a separate state file.
```

---

### Q: Do workspaces isolate networking?

```text
No.
They isolate Terraform state only.
```

---

# Task 2 – Project Structure

---

# Final Repository Structure

```text
terraweek-capstone/

├── main.tf
├── variables.tf
├── outputs.tf
├── providers.tf
├── locals.tf

├── dev.tfvars
├── staging.tfvars
├── prod.tfvars

├── .gitignore

└── modules/

    ├── vpc/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf

    ├── security-group/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf

    └── ec2-instance/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

---

# Creating Structure

```bash
mkdir -p terraweek-capstone/modules/{vpc,security-group,ec2-instance}
```

Create root files:

```bash
touch main.tf
touch variables.tf
touch outputs.tf
touch providers.tf
touch locals.tf
```

Create tfvars:

```bash
touch dev.tfvars
touch staging.tfvars
touch prod.tfvars
```

Create module files:

```bash
touch modules/vpc/{main.tf,variables.tf,outputs.tf}

touch modules/security-group/{main.tf,variables.tf,outputs.tf}

touch modules/ec2-instance/{main.tf,variables.tf,outputs.tf}
```

---

# .gitignore

```gitignore
.terraform/
*.tfstate
*.tfstate.backup
*.tfvars
.terraform.lock.hcl
```

---

# Why Ignore These Files?

### .terraform/

Contains:

```text
Downloaded provider binaries
Temporary Terraform files
```

Never commit.

---

### *.tfstate

Contains:

```text
Infrastructure metadata
Resource IDs
Potential secrets
```

Never commit.

---

### *.tfstate.backup

Backup state files.

Not required in Git.

---

### *.tfvars

Usually contain:

```text
Passwords
Secrets
Environment-specific values
```

Should remain private.

---

### .terraform.lock.hcl

Some teams commit it.
Some teams ignore it.

For this project it was ignored.

---

# Root File Responsibilities

| File         | Purpose                        |
| ------------ | ------------------------------ |
| providers.tf | Provider and Terraform version |
| variables.tf | Input variables                |
| locals.tf    | Computed values                |
| main.tf      | Module orchestration           |
| outputs.tf   | Export values                  |

---

# Why This Structure Is Best Practice

### 1. Separation of Concerns

Each file does one job.

Easier to read.

Easier to debug.

---

### 2. Easier Maintenance

Instead of:

```text
main.tf
(1000+ lines)
```

you have:

```text
providers.tf
variables.tf
locals.tf
main.tf
outputs.tf
```

Much cleaner.

---

### 3. Reusable Modules

VPC module can be reused for:

```text
dev
staging
prod
future projects
```

without rewriting code.

---

### 4. Team Collaboration

Different engineers can work independently on:

```text
Networking
Security
Compute
```

without constantly modifying the same file.

---

### 5. Environment Isolation

Workspaces allow:

```text
dev
staging
prod
```

to share code but maintain separate infrastructure and state.

---

### 6. Production-Ready Design

This mirrors real Terraform repositories:

```text
Root Module
     │
     ├── Networking Module
     ├── Security Module
     └── Compute Module
```

which scales far better than keeping everything in a single file.

---

# Part A Summary

Learned:

### Terraform Workspaces

* Create isolated state files
* Use one codebase
* Use `terraform.workspace`
* Separate dev/staging/prod

### Project Structure

* Root module controls orchestration
* Child modules contain reusable infrastructure
* `.gitignore` protects state and secrets
* Separation of concerns improves maintainability

***




# Part B – Custom Modules (VPC, Security Group, EC2)

---

# Why Modules Exist

Before modules, Terraform projects often looked like:

```text
main.tf

VPC
Subnet
Internet Gateway
Route Tables
Security Groups
EC2
Load Balancer
RDS

1000+ lines
```

Problems:

* Difficult to maintain
* Difficult to debug
* Difficult to reuse
* Multiple engineers editing same file
* Large code reviews

Modules solve this problem.

---

# What is a Module?

A module is a reusable Terraform component.

Think of a module as:

```text
Mini Terraform Project
```

Each module has:

```text
Inputs
Resources
Outputs
```

Structure:

```text
Module

Inputs
  ↓

Resources

  ↓

Outputs
```

---

# Module Design Used in Day 67

Three modules were created:

```text
modules/

├── vpc/
├── security-group/
└── ec2-instance/
```

Each module follows:

```text
module-name/

├── variables.tf
├── main.tf
└── outputs.tf
```

This is the standard Terraform module structure.

---

# Why Three Separate Modules?

Each module has one responsibility.

| Module         | Responsibility |
| -------------- | -------------- |
| vpc            | Networking     |
| security-group | Security       |
| ec2-instance   | Compute        |

This follows:

```text
Single Responsibility Principle
```

One module = one job.

---

# Module 1 – VPC Module

---

# Purpose

Creates:

```text
VPC
Subnet
Internet Gateway
Route Table
Route Table Association
```

This module builds the networking foundation.

Everything else depends on it.

---

# VPC Module Structure

```text
modules/vpc/

├── variables.tf
├── main.tf
└── outputs.tf
```

---

# variables.tf

Inputs required by the module:

```hcl
variable "cidr" {}

variable "public_subnet_cidr" {}

variable "environment" {}

variable "project_name" {}
```

---

# Why These Inputs Exist

### cidr

Used to create:

```text
VPC CIDR Block
```

Example:

```text
10.0.0.0/16
```

---

### public_subnet_cidr

Used for:

```text
Public Subnet
```

Example:

```text
10.0.1.0/24
```

---

### environment

Used for naming:

```text
dev
staging
prod
```

---

### project_name

Used for tags and naming.

Example:

```text
terraweek
```

---

# main.tf

---

## VPC Resource

```hcl
resource "aws_vpc" "this" {

  cidr_block = var.cidr

  tags = {
    Name        = "${var.project_name}-${var.environment}-vpc"
    Environment = var.environment
    Project     = var.project_name
  }
}
```

Creates:

```text
terraweek-dev-vpc
terraweek-staging-vpc
terraweek-prod-vpc
```

depending on workspace.

---

# Public Subnet

```hcl
resource "aws_subnet" "public" {

  vpc_id = aws_vpc.this.id

  cidr_block = var.public_subnet_cidr

  map_public_ip_on_launch = true
}
```

---

# Why map_public_ip_on_launch?

Without it:

```text
EC2 launches
↓
No Public IP
↓
Cannot SSH
```

With it:

```text
EC2 launches
↓
Gets Public IP
↓
Accessible
```

---

# Internet Gateway

```hcl
resource "aws_internet_gateway" "igw" {

  vpc_id = aws_vpc.this.id
}
```

Purpose:

```text
Connect VPC to Internet
```

Without IGW:

```text
Internet
   X
VPC
```

No outbound connectivity.

---

# Route Table

```hcl
resource "aws_route_table" "public" {

  vpc_id = aws_vpc.this.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }
}
```

Meaning:

```text
All traffic
↓
Internet Gateway
↓
Internet
```

---

# Route Table Association

```hcl
resource "aws_route_table_association" "public" {

  subnet_id      = aws_subnet.public.id

  route_table_id = aws_route_table.public.id
}
```

Connects:

```text
Subnet
   ↓
Route Table
```

Without association:

```text
Subnet exists

but

cannot use routes
```

---

# VPC Architecture Created

```text
Internet
    │
    ▼
Internet Gateway
    │
    ▼
Route Table
    │
    ▼
Public Subnet
    │
    ▼
EC2
```

---

# outputs.tf

```hcl
output "vpc_id" {
  value = aws_vpc.this.id
}

output "subnet_id" {
  value = aws_subnet.public.id
}
```

Exports:

```text
VPC ID
Subnet ID
```

for use by other modules.

---

# Why Outputs Matter

Without outputs:

```text
Security Group Module

cannot know

which VPC was created
```

Outputs allow:

```text
VPC Module
    ↓
Outputs VPC ID
    ↓
Security Group Module
```

Module communication.

---

# Module 2 – Security Group Module

---

# Purpose

Creates:

```text
Security Group
Ingress Rules
Egress Rules
```

Responsible only for security.

---

# Structure

```text
modules/security-group/

├── variables.tf
├── main.tf
└── outputs.tf
```

---

# variables.tf

```hcl
variable "vpc_id" {}

variable "ingress_ports" {}

variable "environment" {}

variable "project_name" {}
```

---

# Why vpc_id?

Security Groups must belong to a VPC.

Terraform needs:

```text
Which VPC?
```

Answer comes from:

```text
module.vpc.vpc_id
```

---

# Dynamic Ingress Block

Most important concept in this module.

```hcl
dynamic "ingress" {

  for_each = var.ingress_ports

  content {

    from_port = ingress.value
    to_port   = ingress.value

    protocol = "tcp"

    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

---

# Why Dynamic Blocks?

Without dynamic blocks:

```hcl
ingress {
 port = 22
}

ingress {
 port = 80
}

ingress {
 port = 443
}
```

Repeated code.

---

Dynamic approach:

```hcl
ingress_ports = [22,80,443]
```

Terraform generates:

```text
Port 22
Port 80
Port 443
```

automatically.

---

# Example

Input:

```hcl
ingress_ports = [22,80]
```

Creates:

```text
SSH
HTTP
```

---

Input:

```hcl
ingress_ports = [22,80,443]
```

Creates:

```text
SSH
HTTP
HTTPS
```

---

# Egress Rule

```hcl
egress {

  from_port = 0
  to_port   = 0

  protocol = "-1"

  cidr_blocks = ["0.0.0.0/0"]
}
```

Meaning:

```text
Allow all outbound traffic
```

Common default configuration.

---

# Security Group Output

```hcl
output "sg_id" {

  value = aws_security_group.this.id
}
```

Exports:

```text
Security Group ID
```

for EC2 module.

---

# Security Group Architecture

```text
Security Group

Inbound
│
├── 22
├── 80
└── 443

Outbound
│
└── ALL
```

---

# Module 3 – EC2 Instance Module

---

# Purpose

Creates:

```text
EC2 Instance
```

Nothing else.

Pure compute module.

---

# Structure

```text
modules/ec2-instance/

├── variables.tf
├── main.tf
└── outputs.tf
```

---

# variables.tf

```hcl
variable "ami_id" {}

variable "instance_type" {}

variable "subnet_id" {}

variable "security_group_ids" {}

variable "environment" {}

variable "project_name" {}
```

---

# Why Each Variable Exists

### ami_id

Defines OS image.

Example:

```text
Amazon Linux
Ubuntu
RHEL
```

---

### instance_type

Defines server size.

Example:

```text
t2.micro
t2.small
t3.small
```

---

### subnet_id

Defines:

```text
Which subnet?
```

EC2 must be launched inside a subnet.

---

### security_group_ids

Defines:

```text
Which firewall rules?
```

---

# EC2 Resource

```hcl
resource "aws_instance" "this" {

  ami = var.ami_id

  instance_type = var.instance_type

  subnet_id = var.subnet_id

  vpc_security_group_ids =
    var.security_group_ids
}
```

---

# Dependency Chain

Terraform automatically understands:

```text
Create VPC
     ↓
Create Subnet
     ↓
Create Security Group
     ↓
Create EC2
```

because IDs are passed between modules.

---

# EC2 Tags

```hcl
tags = {

 Name =
 "${var.project_name}-${var.environment}-server"
}
```

Examples:

```text
terraweek-dev-server

terraweek-staging-server

terraweek-prod-server
```

---

# outputs.tf

```hcl
output "instance_id" {

  value = aws_instance.this.id
}
```

---

```hcl
output "public_ip" {

  value = aws_instance.this.public_ip
}
```

Exports:

```text
Instance ID
Public IP
```

---

# Complete Infrastructure Flow

```text
VPC Module
│
├── VPC
├── Subnet
├── Internet Gateway
└── Route Table

        ↓

Security Group Module
│
├── SSH
├── HTTP
└── HTTPS

        ↓

EC2 Module
│
└── EC2 Instance
```

---

# Why This Module Design Is Good

---

## Reusability

Can reuse:

```text
VPC Module
```

in another project.

No code rewrite.

---

## Maintainability

Bug in Security Group?

Fix:

```text
security-group module
```

only.

---

## Team Collaboration

Network Engineer:

```text
VPC Module
```

Security Engineer:

```text
Security Group Module
```

Platform Engineer:

```text
EC2 Module
```

Independent work.

---

## Testing

Modules can be tested individually.

Example:

```text
Test VPC Module

without

deploying EC2
```

---

# Interview Questions

### Q: Why use modules?

```text
To create reusable,
maintainable infrastructure
components.
```

---

### Q: What should a module contain?

```text
Inputs
Resources
Outputs
```

---

### Q: What is a good module design principle?

```text
One module
One responsibility
```

---

### Q: How do modules communicate?

```text
Using Outputs and Inputs.
```

Example:

```text
module.vpc.vpc_id
```

passed into:

```text
security-group module
```

---

# Part B Summary

You built three reusable modules:

### VPC Module

Creates:

* VPC
* Subnet
* Internet Gateway
* Route Table
* Route Association

Outputs:

* vpc_id
* subnet_id

---

### Security Group Module

Creates:

* Security Group
* Dynamic Ingress Rules
* Egress Rule

Outputs:

* sg_id

---

### EC2 Module

Creates:

* EC2 Instance

Outputs:

* instance_id
* public_ip

---

### Most Important Lesson

```text
Modules are the foundation
of scalable Terraform design.
```

Instead of one huge Terraform file:

```text
Networking Module
Security Module
Compute Module
```

work together to build infrastructure.

***





# Part C – Root Configuration, Providers, Variables, Locals, Data Sources, Module Wiring, tfvars Files & Workspace-Aware Deployment

---

# Why the Root Module Exists

In Part B, we created three independent modules:

```text
VPC Module
Security Group Module
EC2 Module
```

However, these modules still don't know about each other.

The Root Module acts as:

```text
Orchestrator
Controller
Glue Layer
```

It connects all modules together.

Think of it as:

```text
Root Module

      │
      ▼

 ┌─────────┐
 │   VPC   │
 └─────────┘
      │
      ▼
 ┌─────────┐
 │   SG    │
 └─────────┘
      │
      ▼
 ┌─────────┐
 │   EC2   │
 └─────────┘
```

---

# Root Configuration Files

```text
terraweek-capstone/

├── providers.tf
├── variables.tf
├── locals.tf
├── main.tf
└── outputs.tf
```

Each file has a specific responsibility.

---

# providers.tf

---

# Purpose

Defines:

```text
Terraform Version
Provider Version
Cloud Provider Configuration
```

---

# Configuration

```hcl
terraform {

  required_version = ">= 1.5"

  required_providers {

    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}

provider "aws" {
  region = "us-east-1"
}
```

---

# Why required_version?

```hcl
required_version = ">= 1.5"
```

Ensures:

```text
Compatible Terraform Version
```

Prevents:

```text
Terraform 0.x
Terraform 1.0
```

from running unsupported code.

---

# Why required_providers?

Locks provider versions.

Example:

```hcl
version = "~> 6.0"
```

Allows:

```text
6.0
6.1
6.5
6.50
```

but prevents:

```text
7.0
```

which may introduce breaking changes.

---

# Why Provider Configuration?

```hcl
provider "aws" {
  region = "us-east-1"
}
```

Tells Terraform:

```text
Deploy Resources Here
```

Without provider configuration:

```text
Terraform
    X
AWS
```

cannot communicate.

---

# variables.tf

---

# Purpose

Defines all root module inputs.

---

# Configuration

```hcl
variable "project_name" {

  type = string

  default = "terraweek"
}
```

---

```hcl
variable "vpc_cidr" {
  type = string
}
```

---

```hcl
variable "subnet_cidr" {
  type = string
}
```

---

```hcl
variable "instance_type" {
  type = string
}
```

---

```hcl
variable "ingress_ports" {
  type = list(number)
}
```

---

# Why Use Variables?

Bad practice:

```hcl
cidr_block = "10.0.0.0/16"
```

Hardcoded values.

---

Good practice:

```hcl
cidr_block = var.vpc_cidr
```

Now environments can supply:

```text
dev
staging
prod
```

different values.

---

# Variable Flow

```text
dev.tfvars
      │
      ▼

variables.tf

      │
      ▼

main.tf

      │
      ▼

module
```

---

# locals.tf

---

# Purpose

Stores calculated values.

Think:

```text
Variables = Inputs

Locals = Calculated Inputs
```

---

# Configuration

```hcl
locals {

  environment = terraform.workspace

  name_prefix =
    "${var.project_name}-${local.environment}"

  common_tags = {

    Project     = var.project_name
    Environment = local.environment
    ManagedBy   = "Terraform"
    Workspace   = terraform.workspace
  }
}
```

---

# Why Use Locals?

Without locals:

```hcl
"${var.project_name}-${terraform.workspace}"
```

repeated everywhere.

---

With locals:

```hcl
local.name_prefix
```

Cleaner.

More maintainable.

---

# Most Important Local

```hcl
environment = terraform.workspace
```

This powers the entire project.

---

If:

```bash
terraform workspace select dev
```

then:

```text
local.environment = dev
```

---

If:

```bash
terraform workspace select prod
```

then:

```text
local.environment = prod
```

---

No code changes required.

---

# Common Tags

Creates reusable tags.

```hcl
common_tags = {

  Project     = var.project_name
  Environment = local.environment
  ManagedBy   = "Terraform"
  Workspace   = terraform.workspace
}
```

Useful for:

```text
Cost Tracking
Inventory
Operations
Auditing
```

---

# Data Sources

---

# What is a Data Source?

Resources CREATE.

Data Sources READ.

---

Resource Example:

```hcl
resource "aws_instance" "server" {}
```

Creates EC2.

---

Data Source Example:

```hcl
data "aws_ami" "amazon_linux" {}
```

Reads existing AMI.

---

# Why Use Data Sources?

Bad:

```hcl
ami = "ami-123456"
```

Problem:

```text
AMI becomes outdated
```

---

Good:

```hcl
data "aws_ami" "amazon_linux"
```

Always fetch latest image.

---

# Configuration

```hcl
data "aws_ami" "amazon_linux" {

  most_recent = true

  owners = ["amazon"]

  filter {

    name = "name"

    values = ["al2023-ami-*"]
  }
}
```

---

# What Happens?

Terraform:

```text
Connect AWS
       │
       ▼
Find Latest Amazon Linux 2023 AMI
       │
       ▼
Return AMI ID
```

---

Example Result

```text
ami-0ab123456789
```

Changes automatically when AWS releases newer images.

---

# Module Wiring

This is where all modules become one system.

---

# VPC Module Call

```hcl
module "vpc" {

  source = "./modules/vpc"

  cidr               = var.vpc_cidr
  public_subnet_cidr = var.subnet_cidr

  environment  = local.environment
  project_name = var.project_name
}
```

---

# Input Flow

```text
dev.tfvars
      │
      ▼

vpc_cidr

      │
      ▼

VPC Module

      │
      ▼

Create VPC
```

---

# Security Group Module Call

```hcl
module "security_group" {

  source = "./modules/security-group"

  vpc_id = module.vpc.vpc_id

  ingress_ports = var.ingress_ports

  environment  = local.environment
  project_name = var.project_name
}
```

---

# Important Dependency

```hcl
vpc_id = module.vpc.vpc_id
```

This means:

```text
Security Group
Needs
VPC
```

Terraform automatically understands:

```text
Create VPC First
```

---

# Dependency Diagram

```text
VPC Module
     │
     ▼
Outputs vpc_id
     │
     ▼
Security Group Module
```

---

# EC2 Module Call

```hcl
module "ec2" {

  source = "./modules/ec2-instance"

  ami_id = data.aws_ami.amazon_linux.id

  instance_type = var.instance_type

  subnet_id = module.vpc.subnet_id

  security_group_ids = [
    module.security_group.sg_id
  ]

  environment  = local.environment
  project_name = var.project_name
}
```

---

# Dependency Chain

```text
VPC
 │
 ▼
Subnet
 │
 ▼
Security Group
 │
 ▼
EC2
```

Terraform automatically builds this graph.

---

# Complete Infrastructure Flow

```text
VPC Module
 │
 ├── VPC
 ├── Subnet
 ├── IGW
 └── Route Table

          │
          ▼

Security Group Module
 │
 ├── SSH
 ├── HTTP
 └── HTTPS

          │
          ▼

EC2 Module
 │
 └── Instance
```

---

# Root outputs.tf

---

# Purpose

Expose useful values after deployment.

---

Configuration:

```hcl
output "vpc_id" {
  value = module.vpc.vpc_id
}
```

---

```hcl
output "subnet_id" {
  value = module.vpc.subnet_id
}
```

---

```hcl
output "security_group_id" {
  value = module.security_group.sg_id
}
```

---

```hcl
output "instance_id" {
  value = module.ec2.instance_id
}
```

---

```hcl
output "public_ip" {
  value = module.ec2.public_ip
}
```

---

# Why Outputs Matter?

After apply:

```bash
terraform output
```

Example:

```text
instance_id = i-123456
public_ip = 54.12.34.56
vpc_id = vpc-123456
```

Useful for:

```text
SSH
Documentation
Automation
Verification
```

---

# Environment tfvars Files

This is where environments become different.

---

# dev.tfvars

```hcl
vpc_cidr      = "10.0.0.0/16"
subnet_cidr   = "10.0.1.0/24"

instance_type = "t2.micro"

ingress_ports = [
  22,
  80
]
```

---

# Characteristics

```text
Small Environment

SSH Enabled
HTTP Enabled

Cheapest Instance
```

---

# staging.tfvars

```hcl
vpc_cidr      = "10.1.0.0/16"
subnet_cidr   = "10.1.1.0/24"

instance_type = "t2.small"

ingress_ports = [
  22,
  80,
  443
]
```

---

# Characteristics

```text
Pre-Production

SSH Enabled
HTTP Enabled
HTTPS Enabled
```

---

# prod.tfvars

```hcl
vpc_cidr      = "10.2.0.0/16"
subnet_cidr   = "10.2.1.0/24"

instance_type = "t3.small"

ingress_ports = [
  80,
  443
]
```

---

# Characteristics

```text
Production

HTTP Enabled
HTTPS Enabled

No Public SSH
```

This is a realistic security pattern.

---

# tfvars Comparison Table

| Setting     | Dev         | Staging     | Prod        |
| ----------- | ----------- | ----------- | ----------- |
| VPC CIDR    | 10.0.0.0/16 | 10.1.0.0/16 | 10.2.0.0/16 |
| Subnet CIDR | 10.0.1.0/24 | 10.1.1.0/24 | 10.2.1.0/24 |
| Instance    | t2.micro    | t2.small    | t3.small    |
| SSH         | Yes         | Yes         | No          |
| HTTP        | Yes         | Yes         | Yes         |
| HTTPS       | No          | Yes         | Yes         |

---

# Workspace-Aware Deployment

---

# Deploy Dev

```bash
terraform workspace select dev
```

---

Plan:

```bash
terraform plan \
-var-file=dev.tfvars
```

---

Apply:

```bash
terraform apply \
-var-file=dev.tfvars
```

---

# Deploy Staging

```bash
terraform workspace select staging
```

---

Plan:

```bash
terraform plan \
-var-file=staging.tfvars
```

---

Apply:

```bash
terraform apply \
-var-file=staging.tfvars
```

---

# Deploy Prod

```bash
terraform workspace select prod
```

---

Plan:

```bash
terraform plan \
-var-file=prod.tfvars
```

---

Apply:

```bash
terraform apply \
-var-file=prod.tfvars
```

---

# What Happens Internally?

Same code:

```text
main.tf
modules/
variables.tf
```

is reused.

Only:

```text
Workspace
+
tfvars
```

change.

---

# Example Resource Names

Workspace:

```text
dev
```

Creates:

```text
terraweek-dev-vpc
terraweek-dev-sg
terraweek-dev-server
```

---

Workspace:

```text
staging
```

Creates:

```text
terraweek-staging-vpc
terraweek-staging-sg
terraweek-staging-server
```

---

Workspace:

```text
prod
```

Creates:

```text
terraweek-prod-vpc
terraweek-prod-sg
terraweek-prod-server
```

---

# Most Important Interview Concept

```text
One Codebase
      +
Multiple Workspaces
      +
Different tfvars
      =
Multiple Environments
```

without duplicating Terraform code.

---

# Part C Summary

You built the Root Module using:

### providers.tf

* Terraform version
* AWS provider
* Region configuration

### variables.tf

* Environment inputs
* CIDRs
* Instance type
* Ports

### locals.tf

* `terraform.workspace`
* Common tags
* Dynamic naming

### Data Source

* Latest Amazon Linux AMI
* No hardcoded AMI IDs

### Module Wiring

* VPC → Security Group → EC2
* Automatic dependency graph

### tfvars

* Dev
* Staging
* Prod

### Deployment

* Same code
* Different workspaces
* Different infrastructure

***



# Part D – Deployment Verification, State Isolation, Best Practices, Cleanup, Troubleshooting & Interview Notes

---

# Why Verification Matters

Many engineers stop after:

```bash
terraform apply
```

and assume everything is working.

Real DevOps work requires:

```text
Plan
Apply
Verify
Validate
Monitor
Maintain
```

Infrastructure is not considered complete until verification succeeds.

---

# Task 1 – Verify Terraform Deployment

---

# Verify Terraform State

First verify Terraform believes resources exist.

Run:

```bash
terraform state list
```

Example:

```text
module.vpc.aws_vpc.this

module.vpc.aws_subnet.public

module.vpc.aws_internet_gateway.igw

module.security_group.aws_security_group.this

module.ec2.aws_instance.this
```

---

# Why This Matters

Terraform state is the source of truth.

If resource exists in AWS but not in state:

```text
Terraform
     X
AWS
```

state drift may exist.

---

# Verify Outputs

Run:

```bash
terraform output
```

Example:

```text
instance_id = i-123456789

public_ip = 54.12.34.56

vpc_id = vpc-123456789
```

---

# Why Outputs Matter

Outputs provide:

```text
Infrastructure Details
```

without manually searching AWS Console.

Useful for:

* SSH
* Automation
* Validation
* Documentation

---

# Verify AWS Resources

Open AWS Console.

Check:

```text
VPC
Subnet
Internet Gateway
Route Table
Security Group
EC2
```

---

# Validation Checklist

---

## VPC Validation

Verify:

```text
Correct CIDR
Correct Name Tag
Correct Environment Tag
```

Example:

```text
terraweek-dev-vpc
```

or

```text
terraweek-prod-vpc
```

---

## Subnet Validation

Verify:

```text
Correct CIDR
Correct VPC
Auto Public IP Enabled
```

---

## Internet Gateway Validation

Verify:

```text
Attached To Correct VPC
```

Without this:

```text
EC2
   X
Internet
```

---

## Route Table Validation

Verify:

```text
0.0.0.0/0
      ↓
Internet Gateway
```

exists.

---

## Security Group Validation

Verify expected ports:

Development:

```text
22
80
```

---

Staging:

```text
22
80
443
```

---

Production:

```text
80
443
```

---

## EC2 Validation

Verify:

```text
Running State
Correct Instance Type
Correct Subnet
Correct Security Group
```

---

# Public IP Validation

Verify:

```text
Instance Has Public IP
```

Expected:

```text
54.x.x.x
```

or similar.

---

# SSH Validation

If SSH enabled:

```bash
ssh -i key.pem ec2-user@PUBLIC_IP
```

Successful connection confirms:

```text
Internet Gateway
Route Table
Security Group
Subnet
EC2
```

are working together.

---

# Task 2 – Verify Workspace Isolation

This is one of the most important Day 67 objectives.

---

# Check Active Workspace

```bash
terraform workspace show
```

Example:

```text
dev
```

---

# List Workspaces

```bash
terraform workspace list
```

Example:

```text
default
dev
staging
* prod
```

---

# Verify State Separation

Switch:

```bash
terraform workspace select dev
```

Run:

```bash
terraform state list
```

Observe resources.

---

Switch:

```bash
terraform workspace select prod
```

Run:

```bash
terraform state list
```

Observe different state.

---

# What Should Happen?

Dev state:

```text
Dev Resources
```

---

Prod state:

```text
Prod Resources
```

---

Terraform keeps them isolated.

---

# Local State Layout

```text
terraform.tfstate.d/

├── dev/
│   └── terraform.tfstate

├── staging/
│   └── terraform.tfstate

└── prod/
    └── terraform.tfstate
```

Each workspace owns its own state file.

---

# Isolation Verification Table

| Check             | Expected Result        |
| ----------------- | ---------------------- |
| Dev Workspace     | Dev Resources Only     |
| Staging Workspace | Staging Resources Only |
| Prod Workspace    | Prod Resources Only    |
| State Files       | Separate               |
| Resource Names    | Environment Specific   |

---

# Resource Naming Validation

Dev:

```text
terraweek-dev-vpc
terraweek-dev-server
```

---

Staging:

```text
terraweek-staging-vpc
terraweek-staging-server
```

---

Prod:

```text
terraweek-prod-vpc
terraweek-prod-server
```

---

# Why Naming Matters

Without naming convention:

```text
VPC
VPC
VPC
```

Impossible to identify environment.

---

Good naming:

```text
dev-vpc
staging-vpc
prod-vpc
```

Immediately identifiable.

---

# Task 3 – Best Practices

---

# 1. Never Hardcode Values

Bad:

```hcl
instance_type = "t2.micro"
```

---

Good:

```hcl
instance_type = var.instance_type
```

Benefits:

```text
Reusable
Flexible
Environment-Aware
```

---

# 2. Use Modules

Bad:

```text
Single 2000-line main.tf
```

---

Good:

```text
VPC Module
Security Module
EC2 Module
```

Benefits:

```text
Reusable
Readable
Maintainable
```

---

# 3. Use Outputs

Bad:

```text
Copy Resource IDs Manually
```

---

Good:

```hcl
output "vpc_id"
```

Allows:

```text
Automation
Integration
Verification
```

---

# 4. Use Workspaces

Avoid:

```text
Three Separate Repositories
```

---

Use:

```text
One Repository
Multiple Workspaces
```

Benefits:

```text
Consistency
Reduced Duplication
Easier Maintenance
```

---

# 5. Tag Everything

Example:

```hcl
tags = {

 Project     = "terraweek"
 Environment = "dev"
 ManagedBy   = "Terraform"
}
```

Benefits:

```text
Cost Tracking
Auditing
Operations
Inventory
```

---

# 6. Keep State Safe

Never commit:

```text
terraform.tfstate
```

---

State may contain:

```text
IDs
Metadata
Sensitive Information
```

---

Always use:

```gitignore
*.tfstate
*.tfstate.backup
```

---

# 7. Use Data Sources

Avoid:

```hcl
ami = "ami-12345"
```

---

Prefer:

```hcl
data "aws_ami"
```

Benefits:

```text
Latest Images
Less Maintenance
Fewer Failures
```

---

# Task 4 – Common Mistakes

---

# Mistake 1

Wrong Workspace

Example:

```bash
terraform workspace select prod
```

but intending:

```text
dev
```

Result:

```text
Production Changes
```

unexpectedly.

---

Always verify:

```bash
terraform workspace show
```

before apply.

---

# Mistake 2

Wrong tfvars File

Example:

```bash
terraform apply \
-var-file=prod.tfvars
```

while in:

```text
dev workspace
```

Results become confusing.

---

Best practice:

```text
Workspace
      ↔
Matching tfvars
```

---

# Mistake 3

Hardcoded AMI

Example:

```hcl
ami = "ami-old"
```

Eventually:

```text
Deprecated
Removed
Broken
```

---

Use data sources.

---

# Mistake 4

Missing Outputs

Without outputs:

```text
Module Communication Difficult
```

---

Example:

```text
Security Group
Needs VPC ID
```

Outputs solve this.

---

# Mistake 5

Everything in One File

Result:

```text
Huge Terraform Project
Hard To Debug
Hard To Maintain
```

---

Use modules.

---

# Task 5 – Cleanup / Destroy

Destroying infrastructure is as important as creating it.

---

# Why Destroy?

Avoid:

```text
Unexpected AWS Charges
```

---

Resources cost money:

```text
EC2
Elastic IPs
Load Balancers
NAT Gateways
RDS
```

---

# Destroy Dev

```bash
terraform workspace select dev
```

---

```bash
terraform destroy \
-var-file=dev.tfvars
```

---

# Destroy Staging

```bash
terraform workspace select staging
```

---

```bash
terraform destroy \
-var-file=staging.tfvars
```

---

# Destroy Prod

```bash
terraform workspace select prod
```

---

```bash
terraform destroy \
-var-file=prod.tfvars
```

---

# Important Destroy Rule

Always ensure:

```text
Workspace
      =
Correct tfvars
```

before destroying.

---

Bad:

```text
prod workspace
+
dev.tfvars
```

---

Good:

```text
prod workspace
+
prod.tfvars
```

---

# Post-Destroy Validation

Verify:

```bash
terraform state list
```

Expected:

```text
No resources found
```

---

Check AWS Console:

```text
VPC Deleted
Subnet Deleted
Security Group Deleted
EC2 Deleted
```

---

# Troubleshooting Guide

---

# Problem

```text
No Public IP
```

Possible Causes:

```text
map_public_ip_on_launch = false
```

or

```text
Wrong Subnet
```

---

# Problem

```text
Cannot SSH
```

Check:

```text
Port 22 Open?
Security Group Correct?
Public IP Present?
Key Pair Correct?
```

---

# Problem

```text
No Internet Access
```

Check:

```text
Internet Gateway
Route Table
Route Association
```

---

# Problem

```text
terraform apply fails
```

Check:

```text
AWS Credentials
Provider Configuration
Region
Permissions
```

---

# Problem

```text
Module Output Not Found
```

Verify:

```hcl
outputs.tf
```

contains expected output.

---

# Day 67 Interview Questions

### Q: Why use Terraform Workspaces?

**Answer:**

```text
To manage multiple environments
using the same Terraform codebase
while maintaining separate state files.
```

---

### Q: What does terraform.workspace return?

**Answer:**

```text
The currently selected workspace name.
```

---

### Q: How do modules communicate?

**Answer:**

```text
Outputs from one module
become inputs to another module.
```

Example:

```text
module.vpc.vpc_id
```

---

### Q: Why use Data Sources?

**Answer:**

```text
To read existing infrastructure
or dynamically fetch information
without creating resources.
```

---

### Q: Difference Between Resource and Data Source?

| Resource               | Data Source             |
| ---------------------- | ----------------------- |
| Creates Infrastructure | Reads Infrastructure    |
| Changes Environment    | Retrieves Information   |
| Managed by Terraform   | Referenced by Terraform |

---

### Q: Why Use tfvars?

**Answer:**

```text
To provide environment-specific values
without changing Terraform code.
```

---

### Q: Why Use Modules?

**Answer:**

```text
Reusability
Maintainability
Scalability
Separation of Concerns
```

---

# Part D Final Revision Sheet

```text
Day 67 Core Formula

Modules
    +
Workspaces
    +
tfvars
    +
Data Sources
    +
Outputs
    =
Multi-Environment Terraform Infrastructure
```

```text
VPC Module
      ↓
Security Group Module
      ↓
EC2 Module
```

```text
One Codebase
      +
Dev Workspace
Staging Workspace
Prod Workspace
      =
Environment Isolation
```

```text
Resources Create
Data Sources Read
```

```text
Variables = Inputs

Locals = Calculated Values

Outputs = Exported Values
```

***



# Part E – Interview Notes, Command Cheat Sheet, 1-Minute Revision Sheet, Architecture Diagram, Learning Journal & Master Notes

---

# Section A – Day 67 Architecture Diagram

This is the complete architecture built during Day 67.

```text
                    Terraform Root Module
                             │
                             │
        ┌────────────────────┼────────────────────┐
        │                    │                    │
        ▼                    ▼                    ▼

   VPC Module      Security Group Module     EC2 Module

        │                    │                    │

        ▼                    ▼                    ▼

   AWS VPC          Security Group          EC2 Instance

        │                    ▲                    ▲
        │                    │                    │
        └────── subnet_id ───┘                    │
                                                  │
                    sg_id ────────────────────────┘
```

---

# Complete Network Flow

```text
Internet
    │
    ▼

Internet Gateway
    │
    ▼

Route Table
    │
    ▼

Public Subnet
    │
    ▼

Security Group
    │
    ▼

EC2 Instance
```

---

# Multi-Environment Design

```text
Single Terraform Repository
              │
              │
              ▼

      Terraform Workspaces

     ┌──────┬─────────┬──────┐
     │      │         │      │
     ▼      ▼         ▼      ▼

   Dev   Staging    Prod  Default

     │      │         │
     ▼      ▼         ▼

Separate State Files

terraform.tfstate.d/

 ├── dev
 ├── staging
 └── prod
```

---

# Section B – Complete Interview Notes

---

## Q1. What is Terraform Workspace?

### Answer

A Terraform Workspace is a mechanism that allows multiple state files to be managed from a single Terraform configuration.

Each workspace maintains:

```text
Separate State
Same Code
```

Used for:

```text
Dev
Staging
Production
```

environments.

---

## Q2. What does terraform.workspace do?

### Answer

Returns the currently selected workspace.

Example:

```bash
terraform workspace select dev
```

returns:

```text
dev
```

when referenced as:

```hcl
terraform.workspace
```

---

## Q3. Why are Workspaces useful?

### Answer

Benefits:

```text
Single Codebase

Separate States

Environment Isolation

Reduced Duplication

Easier Maintenance
```

---

## Q4. What do Workspaces isolate?

### Answer

Workspaces isolate:

```text
Terraform State
```

They do NOT isolate:

```text
Network Design
CIDR Planning
AWS Accounts
Security Architecture
```

---

## Q5. Difference Between Variables and Locals?

### Answer

| Variables            | Locals               |
| -------------------- | -------------------- |
| Input Values         | Calculated Values    |
| Passed From User     | Generated Internally |
| Environment Specific | Derived Logic        |

Example:

```hcl
variable "project_name"
```

Input.

---

```hcl
local.environment
```

Calculated.

---

## Q6. Difference Between Resource and Data Source?

### Answer

### Resource

Creates infrastructure.

Example:

```hcl
resource "aws_instance"
```

---

### Data Source

Reads infrastructure.

Example:

```hcl
data "aws_ami"
```

---

## Q7. Why Use Modules?

### Answer

Modules improve:

```text
Reusability
Maintainability
Scalability
Readability
Testing
```

---

## Q8. How Do Modules Communicate?

### Answer

Using:

```text
Outputs
     ↓
Inputs
```

Example:

```text
module.vpc.vpc_id
```

passed to:

```text
module.security_group
```

---

## Q9. Why Use Outputs?

### Answer

Outputs expose useful information after deployment.

Examples:

```text
VPC ID
Subnet ID
Instance ID
Public IP
```

---

## Q10. Why Use tfvars?

### Answer

To provide different values for different environments without modifying Terraform code.

Example:

```text
dev.tfvars
staging.tfvars
prod.tfvars
```

---

## Q11. Why Use Data Sources Instead of Hardcoded AMIs?

### Answer

Benefits:

```text
Latest Images

Automatic Updates

Less Maintenance

Reduced Failure Risk
```

---

## Q12. Explain Module Dependency Flow

### Answer

```text
VPC
 │
 ▼
Security Group
 │
 ▼
EC2
```

Dependencies are automatically identified through module outputs.

---

# Section C – Day 67 Command Cheat Sheet

---

## Workspace Commands

Create:

```bash
terraform workspace new dev
```

```bash
terraform workspace new staging
```

```bash
terraform workspace new prod
```

---

List:

```bash
terraform workspace list
```

---

Show Current:

```bash
terraform workspace show
```

---

Switch:

```bash
terraform workspace select dev
```

```bash
terraform workspace select staging
```

```bash
terraform workspace select prod
```

---

Delete Workspace

```bash
terraform workspace delete dev
```

---

## Terraform Initialization

```bash
terraform init
```

---

## Formatting

```bash
terraform fmt
```

---

Recursive Formatting

```bash
terraform fmt -recursive
```

---

## Validation

```bash
terraform validate
```

---

## Plan

Dev:

```bash
terraform plan \
-var-file=dev.tfvars
```

---

Staging:

```bash
terraform plan \
-var-file=staging.tfvars
```

---

Prod:

```bash
terraform plan \
-var-file=prod.tfvars
```

---

## Apply

Dev:

```bash
terraform apply \
-var-file=dev.tfvars
```

---

Staging:

```bash
terraform apply \
-var-file=staging.tfvars
```

---

Prod:

```bash
terraform apply \
-var-file=prod.tfvars
```

---

Auto Approve:

```bash
terraform apply \
-auto-approve \
-var-file=dev.tfvars
```

---

## Destroy

Dev:

```bash
terraform destroy \
-var-file=dev.tfvars
```

---

Staging:

```bash
terraform destroy \
-var-file=staging.tfvars
```

---

Prod:

```bash
terraform destroy \
-var-file=prod.tfvars
```

---

## Outputs

Show All:

```bash
terraform output
```

---

Specific Output:

```bash
terraform output public_ip
```

---

## State Commands

List Resources:

```bash
terraform state list
```

---

Show Resource:

```bash
terraform state show RESOURCE_NAME
```

Example:

```bash
terraform state show module.ec2.aws_instance.this
```

---

# Section D – Common Troubleshooting Commands

---

## Validate Configuration

```bash
terraform validate
```

---

## Review Planned Changes

```bash
terraform plan
```

---

## Inspect Current State

```bash
terraform state list
```

---

## Check Current Workspace

```bash
terraform workspace show
```

---

## Verify Outputs

```bash
terraform output
```

---

## Refresh State

```bash
terraform refresh
```

*(Older command; modern workflows typically use plan/apply refresh behavior.)*

---

# Section E – Common Mistakes Checklist

Before every apply:

```text
✓ Correct Workspace?

✓ Correct tfvars File?

✓ Correct AWS Account?

✓ Correct Region?

✓ Correct Instance Type?

✓ Correct CIDR Range?

✓ Latest AMI Retrieved?

✓ terraform validate Passed?

✓ terraform plan Reviewed?
```

---

# Section F – 1 Minute Revision Sheet

```text
Terraform Workspaces

One Codebase
Multiple States

dev
staging
prod
```

---

```text
terraform.workspace

Returns

Current Workspace Name
```

---

```text
Variables

Input Values
```

---

```text
Locals

Calculated Values
```

---

```text
Outputs

Export Values
```

---

```text
Resources

Create Infrastructure
```

---

```text
Data Sources

Read Infrastructure
```

---

```text
Modules

Reusable Infrastructure Components
```

---

```text
VPC Module
      ↓
Security Group Module
      ↓
EC2 Module
```

---

```text
dev.tfvars

Small Environment
```

---

```text
staging.tfvars

Pre-Production Environment
```

---

```text
prod.tfvars

Production Environment
```

---

# Section G – What Day 67 Actually Taught Me

If six months later you remember only one page from Day 67, remember this:

```text
Infrastructure should not be copied.

Infrastructure should be reused.
```

---

Instead of:

```text
dev folder

staging folder

prod folder
```

with duplicated code,

build:

```text
Reusable Modules
```

and combine them using:

```text
Terraform Workspaces
```

and:

```text
Environment-Specific Variables
```

---

The core lesson is:

```text
Modules
      +
Workspaces
      +
tfvars
      =
Scalable Multi-Environment Infrastructure
```

---

# Section H – Final Day 67 Master Summary

```text
Day 67 = Terraform Capstone
```

Learned how to:

- Create reusable modules

- Build VPC infrastructure

- Create Security Groups dynamically

- Deploy EC2 instances

- Use Data Sources for AMIs

- Pass Outputs between modules

- Build a Root Module

- Use Variables and Locals

- Use Environment-Specific tfvars

- Create Dev, Staging and Prod Workspaces

- Maintain Separate State Files

- Deploy Multiple Environments from One Codebase

- Validate Infrastructure

- Destroy Infrastructure Safely

- Apply Terraform Best Practices

---

# Final Interview Formula

```text
Variables
      +
Locals
      +
Data Sources
      +
Modules
      +
Outputs
      +
Workspaces
      +
tfvars
      =
Production-Ready Terraform Architecture
```

***



# 1. Variable Validation

Terraform allows validation of user inputs before deployment.

This helps catch mistakes early.

---

## Example

```hcl
variable "instance_type" {

  type = string

  validation {

    condition = contains(
      ["t2.micro", "t2.small", "t3.small"],
      var.instance_type
    )

    error_message =
      "Invalid instance type selected."
  }
}
```

---

## What Happens?

Valid:

```hcl
instance_type = "t2.micro"
```

Terraform continues.

---

Invalid:

```hcl
instance_type = "m5.large"
```

Terraform fails immediately.

---

## Why Validation Matters

Benefits:

```text
Fail Fast

Prevent Wrong Deployments

Reduce Human Errors

Improve Code Quality
```

---

## Interview Answer

Q: Why use variable validation?

A:

```text
Variable validation ensures only
approved values are accepted
before Terraform creates resources.
```

---

# 2. Production State Management

Local state is acceptable for learning.

Production environments should use:

```text
Remote Backend
```

---

# Why Local State Is Risky

Example:

```text
Laptop Crash
     ↓
State File Lost
     ↓
Terraform Cannot Track Resources
```

---

# Recommended Production Architecture

```text
Terraform

      │

      ▼

S3 Backend

      │

      ▼

DynamoDB Locking
```

---

# S3 Backend Benefits

```text
Centralized State

Team Collaboration

Backup

Version History

High Availability
```

---

# DynamoDB Locking Benefits

Prevents:

```text
Engineer A
        +
Engineer B
        ↓
Both Run Apply
        ↓
State Corruption
```

---

DynamoDB provides:

```text
State Locking
```

Only one Terraform operation runs at a time.

---

# Backend Example

```hcl
terraform {

  backend "s3" {

    bucket = "terraform-state-prod"

    key = "terraweek/terraform.tfstate"

    region = "us-east-1"

    dynamodb_table = "terraform-locks"

    encrypt = true
  }
}
```

---

# Production Backend Checklist

```text
✓ S3 Backend

✓ DynamoDB Locking

✓ Versioning Enabled

✓ Encryption Enabled

✓ Restricted Access
```

---

## Interview Answer

Q: How should Terraform state be managed in production?

A:

```text
Store state remotely in S3,
enable versioning and encryption,
and use DynamoDB for state locking.
```

---

# 3. Golden Terraform Workflow

A common mistake is directly running:

```bash
terraform apply
```

without validation.

---

# Recommended Workflow

```bash
terraform fmt
```

↓

```bash
terraform validate
```

↓

```bash
terraform plan
```

↓

```bash
terraform apply
```

---

# Step 1 – Format

```bash
terraform fmt
```

Purpose:

```text
Consistent Formatting
Readable Code
```

---

# Step 2 – Validate

```bash
terraform validate
```

Purpose:

```text
Syntax Validation

Configuration Validation
```

---

# Step 3 – Plan

```bash
terraform plan
```

Purpose:

```text
Preview Changes

Detect Mistakes

Review Infrastructure Changes
```

---

# Step 4 – Apply

```bash
terraform apply
```

Purpose:

```text
Create Or Modify Infrastructure
```

---

# Interview Answer

Q: What is the recommended Terraform workflow?

A:

```text
terraform fmt
terraform validate
terraform plan
terraform apply
```

Always review the plan before applying changes.

---

# 4. Provider Version Conflict Troubleshooting

One common Terraform issue is provider version mismatch.

---

# Example Error Scenario

State uses:

```text
AWS Provider 6.50
```

But configuration uses:

```hcl
version = "~> 5.0"
```

Terraform cannot continue.

---

# Symptoms

```text
Provider Version Conflict

Provider Requirements Not Met

Initialization Failure
```

---

# Resolution Option 1

Update provider version.

```hcl
required_providers {

  aws = {

    source = "hashicorp/aws"

    version = "~> 6.0"
  }
}
```

---

# Resolution Option 2

Reinitialize Terraform.

```bash
terraform init -upgrade
```

---

# Resolution Option 3

Remove old lock file.

```bash
rm .terraform.lock.hcl
```

Then:

```bash
terraform init
```

---

# Interview Answer

Q: How do you resolve provider version conflicts?

A:

```text
Verify provider constraints,
update versions if necessary,
and reinitialize Terraform
using terraform init -upgrade.
```

---

# 5. LinkedIn / GitHub Project Proof Checklist

When publishing Day 67, capture evidence of deployment.

---

# Terraform Proof

```bash
terraform workspace list
```

---

```bash
terraform workspace show
```

---

```bash
terraform output
```

---

```bash
terraform state list
```

---

# AWS Proof

Capture screenshots of:

```text
VPC

Subnet

Route Table

Internet Gateway

Security Group

EC2 Instance
```

---

# Environment Proof

Show:

```text
Dev Workspace

Staging Workspace

Prod Workspace
```

---

# Portfolio Proof Checklist

```text
✓ GitHub Repository

✓ Terraform Code

✓ Module Structure

✓ Workspaces

✓ Terraform Outputs

✓ AWS Resources

✓ Architecture Diagram

✓ README Documentation
```

---

# 6. Most Important Day 67 Interview Concept

```text
Workspaces Isolate STATE

Workspaces Do NOT Isolate Infrastructure Design
```

---

Example:

```text
dev workspace
```

and

```text
prod workspace
```

have:

```text
Different State Files
```

but still use:

```text
Same Terraform Code
```

---

# Interview Question

Q: What do Terraform Workspaces isolate?

A:

```text
Terraform Workspaces isolate
state files only.

They do not isolate
network design,
security architecture,
or AWS accounts.
```

---

# Final Day 67 Advanced Formula

```text
Variables
      +
Validation
      +
Locals
      +
Data Sources
      +
Modules
      +
Outputs
      +
Workspaces
      +
tfvars
      +
Remote Backend
      +
State Locking
      =
Production Ready Terraform Platform
```







