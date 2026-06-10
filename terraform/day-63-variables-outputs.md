# Day 63 – Variables, Outputs, Data Sources, Locals & Expressions

## Learning Objective

The goal of Day 63 was to transform Terraform from hardcoded infrastructure into reusable, environment-aware infrastructure using:

* Variables
* Variable Files (tfvars)
* Outputs
* Data Sources
* Locals
* Built-in Functions
* Conditional Expressions
* Dynamic Blocks

Before Day 63:

```hcl
resource "aws_instance" "main" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
}
```

Problems:

```text
Hardcoded
Not reusable
Environment changes require code changes
Not production ready
```

After Day 63:

```hcl
resource "aws_instance" "main" {
  ami           = data.aws_ami.amazon_linux.id
  instance_type = var.instance_type
}
```

Result:

```text
Reusable
Environment-specific
Production friendly
Portable across regions
```

---

# Day 63 Mental Model

```text
USER INPUT
     │
     ▼
VARIABLES
     │
     ▼
LOCALS
     │
     ▼
DATA SOURCES
     │
     ▼
RESOURCES
     │
     ▼
TERRAFORM STATE
     │
     ▼
OUTPUTS
```

Remember this flow.

Everything learned in Day 63 fits into this architecture.

---

# Terraform Execution Flow

```text
terraform plan
      │
      ▼
Load Provider
      │
      ▼
Load Variables
      │
      ▼
Load tfvars
      │
      ▼
Validate Variables
      │
      ▼
Evaluate Locals
      │
      ▼
Read Data Sources
      │
      ▼
Build Dependency Graph
      │
      ▼
Generate Plan
      │
      ▼
terraform apply
      │
      ▼
Create Resources
      │
      ▼
Update State
      │
      ▼
Generate Outputs
```

Important:

```text
Variables load before locals.
Locals load before resources.
Outputs are generated after state is updated.
```

---

# Variables (variables.tf)

Terraform variable types used in Day 63:

```hcl
variable "region" {
  description = "AWS region"
  type        = string
  default     = "us-west-2"
}

variable "vpc_cidr" {
  description = "CIDR block for VPC"
  type        = string
  default     = "10.0.0.0/16"
}

variable "subnet_cidr" {
  description = "CIDR block for subnet"
  type        = string
  default     = "10.0.1.0/24"
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
}

variable "project_name" {
  description = "Project name"
  type        = string

  validation {
    condition     = length(trimspace(var.project_name)) > 0
    error_message = "Project name cannot be empty."
  }
}

variable "environment" {
  description = "Environment name"
  type        = string
  default     = "dev"
}

variable "allowed_ports" {
  description = "Ports allowed in Security Group"
  type        = list(number)
  default     = [22, 80, 443]
}

variable "extra_tags" {
  description = "Additional tags"
  type        = map(string)
  default     = {}
}
```

---

# Terraform Variable Types

| Type   | Example            |
| ------ | ------------------ |
| string | `"dev"`            |
| number | `8080`             |
| bool   | `true`             |
| list   | `[22,80,443]`      |
| map    | `{Owner="Deepak"}` |

---

# Why Variables Exist

Without variables:

```hcl
instance_type = "t2.micro"
cidr_block    = "10.0.0.0/16"
```

Every environment change requires code changes.

With variables:

```hcl
instance_type = var.instance_type
cidr_block    = var.vpc_cidr
```

Same code.

Different inputs.

---

# Variable Lifecycle

```text
terraform plan
     │
     ▼
Read variables.tf
     │
     ▼
Load values
     │
     ▼
Validate values
     │
     ▼
Build execution plan
```

Variables are inputs provided by:

```text
terraform.tfvars
prod.tfvars
CLI variables
Environment variables
Defaults
```

---

# Development Variables (terraform.tfvars)

```hcl
project_name  = "terraweek"
environment   = "dev"
instance_type = "t2.micro"
```

Used automatically when running:

```bash
terraform plan
terraform apply
```

---

# Production Variables (prod.tfvars)

```hcl
project_name  = "terraweek"
environment   = "prod"
instance_type = "t3.small"

vpc_cidr      = "10.1.0.0/16"
subnet_cidr   = "10.1.1.0/24"
```

Used with:

```bash
terraform plan -var-file="prod.tfvars"
terraform apply -var-file="prod.tfvars"
```

---

# Multi-Environment Strategy

```text
Terraform Code
      │
      ├── dev.tfvars
      ├── qa.tfvars
      ├── uat.tfvars
      └── prod.tfvars
```

Same Terraform code.

Different environment values.

This is the primary reason variables exist.

---

# Variable Precedence

Terraform can receive values from multiple places.

When the same variable exists in multiple locations, Terraform follows a precedence order.

## Lowest → Highest Priority

```text
Default Values
      ↓
TF_VAR_* Environment Variables
      ↓
terraform.tfvars
      ↓
*.auto.tfvars
      ↓
-var-file
      ↓
-var
```

Highest value wins.

### Example

Default:

```hcl
instance_type = "t2.micro"
```

Environment Variable:

```bash
export TF_VAR_instance_type=t3.micro
```

terraform.tfvars:

```hcl
instance_type = "t3.small"
```

CLI:

```bash
terraform plan -var="instance_type=t2.nano"
```

Final Result:

```text
t2.nano
```

---

# Locals

## Why Locals Exist

Without locals:

```hcl
"${var.project_name}-${var.environment}-server"
"${var.project_name}-${var.environment}-vpc"
"${var.project_name}-${var.environment}-sg"
```

Repeated everywhere.

With locals:

```hcl
locals {
  name_prefix = "${var.project_name}-${var.environment}"
}
```

Usage:

```hcl
"${local.name_prefix}-server"
```

Cleaner and easier to maintain.

---

# Common Tags

```hcl
locals {
  common_tags = {
    Project     = var.project_name
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}
```

Usage:

```hcl
tags = merge(local.common_tags, {
  Name = "${local.name_prefix}-server"
})
```

Benefits:

```text
Consistent tagging
Cost tracking
Ownership tracking
Automation support
Auditing
```

---

# Data Sources

## Purpose

Data sources read existing information.

They do not create infrastructure.

---

# Resource vs Data Source

## Resource

Creates infrastructure.

```hcl
resource "aws_instance" "main"
```

Flow:

```text
Terraform
   │
   ▼
AWS API
   │
   ▼
Create EC2
```

---

## Data Source

Reads existing information.

```hcl
data "aws_ami" "amazon_linux"
```

Flow:

```text
Terraform
   │
   ▼
AWS API
   │
   ▼
Fetch Existing Data
```

No resource is created.

---

# Dynamic AMI Lookup

```hcl
data "aws_ami" "amazon_linux" {
  most_recent = true

  owners = ["amazon"]

  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }

  filter {
    name   = "root-device-type"
    values = ["ebs"]
  }
}
```

Usage:

```hcl
ami = data.aws_ami.amazon_linux.id
```

Benefit:

```text
Always uses latest matching Amazon Linux AMI.
```

---

# Availability Zone Lookup

```hcl
data "aws_availability_zones" "available" {
  state = "available"
}
```

Usage:

```hcl
availability_zone =
data.aws_availability_zones.available.names[0]
```

Meaning:

```text
Select first available AZ automatically.
```

---

# Dynamic Blocks

Input:

```hcl
allowed_ports = [22,80,443]
```

Terraform:

```hcl
dynamic "ingress" {
  for_each = var.allowed_ports
}
```

Generates:

```text
Ingress Rule 22
Ingress Rule 80
Ingress Rule 443
```

Automatically.

Without dynamic blocks, each ingress rule must be written manually.

---

# Resources

Resources create infrastructure.

Examples:

```hcl
resource "aws_vpc"
resource "aws_subnet"
resource "aws_instance"
resource "aws_s3_bucket"
```

Flow:

```text
Terraform
      │
      ▼
AWS Provider
      │
      ▼
AWS API
      │
      ▼
Infrastructure Created
```

---

# Terraform Dependency Graph

Terraform automatically determines creation order.

Example:

```text
VPC
 ↓
Subnet
 ↓
Security Group
 ↓
EC2
 ↓
Outputs
```

Resources are created based on dependencies.

---

# Outputs

Outputs expose useful values after deployment.

Example:

```hcl
output "instance_public_ip" {
  value = aws_instance.main.public_ip
}
```

---

# Output Architecture

```text
AWS Resource
      │
      ▼
Terraform State
      │
      ▼
outputs.tf
      │
      ▼
terraform output
      │
      ▼
User / Jenkins / GitHub Actions
```

---

# State File Relationship

```text
Variables
    ↓
Resources
    ↓
Terraform State
    ↓
Outputs
```

Important:

```text
terraform output reads values from Terraform state.
```

---

# Output After Apply

```text
Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

Outputs:

instance_id = "i-0331c04ecaf33c341"

instance_public_dns = ""

instance_public_ip = "100.21.17.7"

security_group_id = "sg-0513d120f717a69c2"

subnet_id = "subnet-015f4f5323ce56036"

vpc_id = "vpc-0acc47bd462520288"
```

Verification:

```bash
terraform output instance_public_ip
```

Output:

```text
100.21.17.7
```

Verified successfully.

---

# Why Outputs Matter in Production

CI/CD tools use:

```bash
terraform output -json
```

To retrieve:

```text
VPC ID
Subnet ID
Public IP
Load Balancer DNS
Instance ID
```

Used by:

```text
Jenkins
GitHub Actions
GitLab CI
ArgoCD
```

---

# Five Useful Built-in Functions

## 1. upper()

```hcl
upper("terraweek")
```

Output:

```text
TERRAWEEK
```

Use Case:

```text
Standardized naming
Tags
Environment labels
```

---

## 2. join()

```hcl
join("-", ["terra","week","2026"])
```

Output:

```text
terra-week-2026
```

Use Case:

```text
Resource naming
Tag generation
```

---

## 3. lookup()

```hcl
lookup(
  {
    dev  = "t2.micro"
    prod = "t3.small"
  },
  "dev"
)
```

Output:

```text
t2.micro
```

Use Case:

Environment-based configurations.

---

## 4. cidrsubnet()

```hcl
cidrsubnet("10.0.0.0/16", 8, 1)
```

Output:

```text
10.0.1.0/24
```

Use Case:

VPC network design.

---

## 5. merge()

```hcl
merge(
  local.common_tags,
  {
    Name = "server"
  }
)
```

Output:

```hcl
{
  Project     = "terraweek"
  Environment = "dev"
  Name        = "server"
}
```

Use Case:

Centralized tagging strategy.

---

# Conditional Expressions

```hcl
var.environment == "prod"
?
"t3.small"
:
"t2.micro"
```

Meaning:

```text
Prod → t3.small
Dev  → t2.micro
```

Used for environment-specific decisions.

---

# Difference Between Variable, Local, Data Source, Resource and Output

| Component   | Purpose                    | Example                      |
| ----------- | -------------------------- | ---------------------------- |
| Variable    | Accept input values        | var.instance_type            |
| Local       | Store calculated values    | local.name_prefix            |
| Data Source | Read existing information  | data.aws_ami.amazon_linux.id |
| Resource    | Create infrastructure      | aws_instance.main            |
| Output      | Display values after apply | output "vpc_id"              |

---

# Easy Memory Trick

```text
Variable
   ↓
Input from User

Local
   ↓
Internal Calculation

Data Source
   ↓
Read Existing Information

Resource
   ↓
Create Infrastructure

Output
   ↓
Show Results
```

---

# Lab Issues and Lessons Learned

## Issue 1 – Unsupported Instance Type

Error:

```text
t2.micro is not supported in us-west-2d
```

Lesson:

```text
Not every instance type is available in every Availability Zone.
```

Fix:

```text
Change instance type or change Availability Zone.
```

---

## Issue 2 – BucketAlreadyExists

Error:

```text
BucketAlreadyExists
```

Lesson:

```text
S3 bucket names are globally unique.
```

Fix:

```hcl
aws_caller_identity
random_id
```

Used to generate unique bucket names.

---

## Issue 3 – InvalidSubnet.Conflict

Error:

```text
InvalidSubnet.Conflict
```

Lesson:

```text
Subnet CIDRs cannot overlap.

Changing Availability Zone can force subnet replacement.
```

---

## Issue 4 – Invalid Variable Validation Condition

Wrong:

```hcl
condition = length(local.name_prefix)
```

Lesson:

```text
Variable validation cannot reference locals.

Variables are evaluated before locals.
```

Correct:

```hcl
condition = length(trimspace(var.project_name)) > 0
```

---

## Issue 5 – trim() Error

Wrong:

```hcl
trim(var.project_name)
```

Error:

```text
Function trim expects 2 arguments
```

Correct:

```hcl
trimspace(var.project_name)
```

Lesson:

```text
trim() removes specific characters.
trimspace() removes whitespace.
```

---

# Interview Questions

### What is variable precedence?

```text
Default
↓
TF_VAR
↓
terraform.tfvars
↓
auto.tfvars
↓
var-file
↓
var
```

---

### Difference between Variable and Local?

```text
Variable = External Input

Local = Internal Calculation
```

---

### Difference between Resource and Data Source?

```text
Resource = Creates Infrastructure

Data Source = Reads Existing Information
```

---

### Why use Outputs?

```text
Expose resource information after deployment.
Useful for users and CI/CD pipelines.
```

---

### Why use Locals?

```text
Reduce duplication.
Centralize reusable calculations.
```

---

# 1-Minute Revision Sheet

```text
Variables = Inputs

Locals = Internal reusable values

Data Sources = Read existing AWS information

Resources = Create infrastructure

Outputs = Return deployment results

terraform.tfvars = Development values

prod.tfvars = Production values

Variables load before locals

Outputs come from Terraform state

aws_ami = Dynamic AMI lookup

aws_availability_zones = Dynamic AZ lookup

merge() = Combine maps

lookup() = Read map values

cidrsubnet() = Create subnets

Conditional Expressions = Environment-specific decisions

Dynamic Blocks = Generate repeated blocks automatically

S3 bucket names are globally unique

Not every instance type exists in every Availability Zone
```

---

# Final Day 63 Summary

```text
Day 63 transformed Terraform from hardcoded infrastructure into reusable, environment-aware infrastructure using variables, tfvars files, outputs, data sources, locals, dynamic blocks, functions, and conditional expressions while teaching important real-world lessons about AWS Availability Zones, subnet conflicts, S3 bucket uniqueness, Terraform variable evaluation order, and production-ready Infrastructure as Code practices.
```

***

#  Variable Resolution Architecture

Variables can receive values from multiple locations.

Terraform resolves them before creating the execution plan.

```text
User Input
     │
     ├── Default Value
     ├── TF_VAR Environment Variable
     ├── terraform.tfvars
     ├── *.auto.tfvars
     ├── -var-file
     └── -var

            │
            ▼

       variables.tf

            │
            ▼

          var.*

            │
            ▼

        Resources
```

Example:

```hcl
variable "instance_type" {
  default = "t2.micro"
}
```

Possible sources:

```text
Default Value
terraform.tfvars
prod.tfvars
Environment Variable
CLI Variable
```

Terraform selects the value based on precedence rules and then makes it available through:

```hcl
var.instance_type
```

This is how values flow from user input into infrastructure.

***

#  Why Day 63 Exists

Before Day 63, Terraform configurations were mostly hardcoded.

Example:

```hcl
instance_type = "t2.micro"
ami           = "ami-123456"
```

Problem:

```text
Environment changes require code changes.
Code becomes difficult to reuse.
Infrastructure is not production friendly.
```

Day 63 introduced:

```text
Variables
Locals
Data Sources
Outputs
Functions
Conditional Expressions
```

Transformation:

```text
Day 62
   │
   ▼
Hardcoded Infrastructure

   │
   ▼
Day 63

   │
   ▼
Reusable Infrastructure

   │
   ▼
Multiple Environments

   │
   ▼
Production-Ready Terraform
```

The biggest lesson from Day 63 is:

```text
Separate Infrastructure Logic from Infrastructure Values.
```

Keep the code same.

Change only the inputs.

***



## Production Environment Example

One Terraform codebase can manage multiple environments.

```text
Terraform Code
      │
      ├── dev.tfvars
      ├── qa.tfvars
      ├── uat.tfvars
      └── prod.tfvars
```

Result:

```text
Same Terraform Code
Different Environment Values
Different Infrastructure
```

This is the primary reason variables and tfvars files exist.

---

## Why Terraform Prompted for project_name

Example:

```hcl
variable "project_name" {
  type = string
}
```

Notice:

```text
No default value provided.
```

When Terraform runs:

```bash
terraform plan
```

Terraform asks:

```text
Enter a value:
```

Reason:

```text
Terraform cannot find a value from:
- Default
- tfvars
- Environment Variable
- CLI Variable
```

Therefore user input becomes mandatory.

---

## Why instance_public_dns Was Empty

Apply Output:

```text
instance_public_dns = ""
```

Reason:

```text
AWS sometimes takes additional time to generate
and publish the public DNS name after instance creation.
```

Possible later value:

```text
ec2-100-21-17-7.us-west-2.compute.amazonaws.com
```

This is normal AWS behavior and not a Terraform issue.

Terraform can display the DNS value after a refresh or a subsequent apply.
