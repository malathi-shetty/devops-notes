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

***


## 1. Variables (`variables.tf`)

### Variables Used

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

## What are Variables?

Variables make Terraform configurations reusable and flexible.

Instead of hardcoding values, Terraform accepts values through variables.

Example:

```hcl
instance_type = var.instance_type
```

---

## Five Variable Types in Terraform

| Type   | Example            |
| ------ | ------------------ |
| string | `"dev"`            |
| number | `8080`             |
| bool   | `true`             |
| list   | `[22,80,443]`      |
| map    | `{Owner="Deepak"}` |

### String

Stores text values.

```hcl
variable "environment" {
  type    = string
  default = "dev"
}
```

---

### Number

Stores numeric values.

```hcl
variable "instance_count" {
  type    = number
  default = 2
}
```

---

### Bool

Stores true/false values.

```hcl
variable "enable_monitoring" {
  type    = bool
  default = true
}
```

---

### List

Stores ordered collections.

```hcl
variable "allowed_ports" {
  type    = list(number)
  default = [22,80,443]
}
```

---

### Map

Stores key-value pairs.

```hcl
variable "extra_tags" {
  type    = map(string)
  default = {}
}
```

---

## Variable Validation

Terraform can validate user input before creating infrastructure.

Example:

```hcl
variable "project_name" {
  type = string

  validation {
    condition     = length(trimspace(var.project_name)) > 0
    error_message = "Project name cannot be empty."
  }
}
```

Benefits:

* Prevents invalid inputs
* Improves reliability
* Reduces deployment failures

---

## Variable Files

### terraform.tfvars

```hcl
project_name  = "terraweek"
environment   = "dev"
instance_type = "t2.micro"
```

Automatically loaded by Terraform.

Commands:

```bash
terraform plan
terraform apply
```

---

### prod.tfvars

```hcl
project_name  = "terraweek"
environment   = "prod"
instance_type = "t3.small"

vpc_cidr      = "10.1.0.0/16"
subnet_cidr   = "10.1.1.0/24"
```

Commands:

```bash
terraform plan -var-file="prod.tfvars"
terraform apply -var-file="prod.tfvars"
```

---

# Variable Precedence

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

---

## Example 1 – Default Value

```hcl
variable "instance_type" {
  default = "t2.micro"
}
```

Result:

```text
t2.micro
```

---

## Example 2 – terraform.tfvars Overrides Default

```hcl
instance_type = "t3.micro"
```

Result:

```text
t3.micro
```

---

## Example 3 – prod.tfvars Overrides terraform.tfvars

```bash
terraform plan -var-file="prod.tfvars"
```

Result:

```text
t3.small
```

---

## Example 4 – CLI Variable Overrides Everything

```bash
terraform plan -var="instance_type=t2.nano"
```

Result:

```text
t2.nano
```

---

## Example 5 – Environment Variable

```bash
export TF_VAR_environment="staging"
```

Terraform maps:

```text
TF_VAR_environment
```

to

```text
var.environment
```

Result:

```text
staging
```

---

## Easy Precedence Table

| Priority (High → Low) | Source               | Example                                  | Result |
| --------------------- | -------------------- | ---------------------------------------- | ------ |
| 1                     | `-var`               | `terraform plan -var="environment=qa"`   | qa     |
| 2                     | `-var-file`          | `terraform plan -var-file="prod.tfvars"` | prod   |
| 3                     | `terraform.tfvars`   | `environment="stage"`                    | stage  |
| 4                     | `TF_VAR_environment` | `uat`                                    | uat    |
| 5                     | Default              | `default="dev"`                          | dev    |

---

# Outputs

Outputs display useful information after Terraform creates resources.

Example:

```hcl
output "instance_public_ip" {
  value = aws_instance.main.public_ip
}
```

Commands:

```bash
terraform output
```

Displays all outputs.

```bash
terraform output instance_public_ip
```

Displays specific output.

```bash
terraform output -json
```

Displays JSON output.

---

## Outputs Created

```text
vpc_id
subnet_id
instance_id
instance_public_ip
instance_public_dns
security_group_id
```

---

## Apply Output

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

# Data Sources

Data sources fetch existing information.

They do not create resources.

Example:

```hcl
data "aws_ami" "amazon_linux" {
  most_recent = true
  owners      = ["amazon"]
}
```

Used for:

* Latest AMI lookup
* Availability Zones
* AWS Account ID

Examples:

```hcl
data "aws_ami" "amazon_linux"
data "aws_availability_zones" "available"
data "aws_caller_identity" "current"
```

---

# Resource vs Data Source

| Resource                 | Data Source                 |
| ------------------------ | --------------------------- |
| Creates infrastructure   | Reads existing information  |
| Managed by Terraform     | Read-only                   |
| Can be created/destroyed | Cannot be created/destroyed |
| Example: aws_instance    | Example: aws_ami            |

### Resource Example

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```

Creates a VPC.

---

### Data Source Example

```hcl
data "aws_ami" "amazon_linux" {
  most_recent = true
}
```

Reads AMI information.

---

# Locals

Locals store reusable calculated values.

Example:

```hcl
locals {
  name_prefix = "${var.project_name}-${var.environment}"

  common_tags = {
    Project     = var.project_name
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}
```

Benefits:

* Reduces duplication
* Improves readability
* Centralized naming
* Consistent tagging

---

# Variable vs Local vs Data vs Output

| Component   | Purpose                   | Example                        |
| ----------- | ------------------------- | ------------------------------ |
| Variable    | User Input                | `var.instance_type`            |
| Local       | Internal Calculation      | `local.name_prefix`            |
| Data Source | Read Existing Information | `data.aws_ami.amazon_linux.id` |
| Output      | Show Results              | `output "vpc_id"`              |

---

## Easy Memory Trick

```text
Variable
   ↓
Input from User

Local
   ↓
Internal Calculation

Data
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

# Five Useful Terraform Functions

## 1. upper()

Converts text to uppercase.

```hcl
upper("terraweek")
```

Output:

```text
TERRAWEEK
```

Use Case:

* Tags
* Resource names
* Standardized naming

---

## 2. join()

Combines strings.

```hcl
join("-", ["terra","week","2026"])
```

Output:

```text
terra-week-2026
```

Use Case:

* Naming resources
* Generating tags

---

## 3. lookup()

Fetches values from a map.

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

* Environment-specific settings

---

## 4. length()

Returns collection size.

```hcl
length(["a","b","c"])
```

Output:

```text
3
```

Use Case:

* Validation
* Conditional logic

---

## 5. cidrsubnet()

Creates subnet CIDRs.

```hcl
cidrsubnet("10.0.0.0/16",8,1)
```

Output:

```text
10.0.1.0/24
```

Use Case:

* VPC network planning

---

## Additional Functions Practiced

### format()

```hcl
format("arn:aws:s3:::%s","my-bucket")
```

Output:

```text
arn:aws:s3:::my-bucket
```

---

### toset()

```hcl
toset(["a","b","a"])
```

Output:

```text
["a","b"]
```

Removes duplicates.

---

### merge()

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

Used for centralized tagging.

---

# Conditional Expressions

Terraform supports if-else style logic.

Syntax:

```hcl
condition ? true_value : false_value
```

Example:

```hcl
instance_type = var.environment == "prod" ? "t3.small" : "t3.micro"
```

Result:

```text
prod  → t3.small
dev   → t3.micro
```

Use Cases:

* Environment-based sizing
* Dynamic configurations
* Cost optimization

---

# Day 63 Key Learning

1. Variables make Terraform reusable.
2. Validation prevents bad input.
3. tfvars files separate environment configurations.
4. Variable precedence determines final values.
5. Outputs expose important resource information.
6. Data sources fetch existing AWS information.
7. Locals reduce duplication and standardize tagging.
8. Functions simplify string, collection and network operations.
9. Conditional expressions enable dynamic infrastructure decisions.
10. Terraform configurations become reusable, scalable and environment-aware.

***



# Terraform Console

Terraform Console is an interactive command-line tool used to test Terraform expressions, variables, functions, and calculations without creating or modifying infrastructure.

It helps validate logic before running `terraform plan` or `terraform apply`.

## Command

```bash
terraform console
```

## Examples

### String Functions

```hcl
upper("terraweek")
```

Output:

```text
TERRAWEEK
```

```hcl
join("-", ["terra","week","2026"])
```

Output:

```text
terra-week-2026
```

---

### Collection Functions

```hcl
length(["a","b","c"])
```

Output:

```text
3
```

```hcl
lookup({dev="t2.micro", prod="t3.small"}, "dev")
```

Output:

```text
t2.micro
```

---

### Networking Function

```hcl
cidrsubnet("10.0.0.0/16", 8, 1)
```

Output:

```text
10.0.1.0/24
```

---

## Benefits of Terraform Console

* Test Terraform functions quickly
* Validate expressions before deployment
* Learn Terraform syntax interactively
* Debug variable values and calculations
* Reduce errors before running apply

---

# Dynamic Blocks

Dynamic blocks allow Terraform to generate repeated nested blocks automatically using loops.

They help avoid writing the same configuration multiple times.

In this project, a dynamic block was used to create multiple Security Group ingress rules from the `allowed_ports` variable.

## Example

```hcl
dynamic "ingress" {
  for_each = var.allowed_ports

  content {
    from_port   = ingress.value
    to_port     = ingress.value
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

If:

```hcl
allowed_ports = [22, 80, 443]
```

Terraform automatically creates:

```text
Port 22 Rule
Port 80 Rule
Port 443 Rule
```

without manually writing three separate ingress blocks.

## Benefits of Dynamic Blocks

* Eliminates duplicate code
* Improves maintainability
* Makes configurations reusable
* Supports variable-driven infrastructure
* Useful for Security Groups, IAM policies, and Route Tables

---

# Availability Zone Selection Using Data Source

Instead of hardcoding an Availability Zone, Terraform can dynamically fetch all available Availability Zones in the selected AWS region.

This makes the configuration portable and reusable across different AWS regions.

## Availability Zone Data Source

```hcl
data "aws_availability_zones" "available" {
  state = "available"
}
```

This retrieves all available Availability Zones in the current region.

Example Output:

```text
us-west-2a
us-west-2b
us-west-2c
us-west-2d
```

---

## Using the First Availability Zone

```hcl
availability_zone = data.aws_availability_zones.available.names[0]
```

Terraform selects the first available Availability Zone from the list.

Example:

```text
us-west-2a
```

---

## Benefits

* No hardcoded Availability Zones
* Works across different AWS regions
* Increases portability
* Improves automation
* Reduces manual configuration changes

---

## Summary

### Terraform Console

Used to test Terraform functions, expressions, and calculations interactively.

### Dynamic Blocks

Used to generate repeated nested configuration blocks automatically.

### Availability Zone Data Source

Used to dynamically retrieve available AWS Availability Zones and make infrastructure region-independent.

***


# Day 63 Comparison Tables

## 1. Variable vs Local vs Output vs Data Source

| Feature                   | Variable                | Local                   | Output                     | Data Source                |
| ------------------------- | ----------------------- | ----------------------- | -------------------------- | -------------------------- |
| Purpose                   | Accept input from user  | Store calculated values | Display values after apply | Fetch existing information |
| Created By                | User                    | Terraform config        | Terraform config           | Terraform provider         |
| Mutable?                  | Yes (user can override) | No                      | No                         | No                         |
| Used For                  | Customization           | Reusable expressions    | Sharing information        | Dynamic lookups            |
| Example                   | region, instance_type   | name_prefix             | public_ip                  | latest AMI                 |
| Syntax                    | var.name                | local.name              | output "name"              | data.aws_ami               |
| Creates Resources?        | ❌ No                    | ❌ No                    | ❌ No                       | ❌ No                       |
| Reads Existing Resources? | ❌ No                    | ❌ No                    | ❌ No                       | ✅ Yes                      |
| Day 63 Example            | var.instance_type       | local.common_tags       | instance_public_ip         | aws_ami.amazon_linux       |

### Mental Model

```text
User Input
    ↓
 Variables
    ↓
 Locals
    ↓
 Resources
    ↓
 Outputs

Data Sources
    ↓
Provide existing AWS information
    ↓
Resources use that information
```

---

## 2. Resource vs Data Source

| Resource                   | Data Source              |
| -------------------------- | ------------------------ |
| Creates something          | Reads something          |
| Managed by Terraform       | Not managed by Terraform |
| Changes infrastructure     | Only fetches information |
| Appears in terraform apply | Read during plan/apply   |
| Can be destroyed           | Cannot be destroyed      |

### Examples

#### Resource

```hcl
resource "aws_instance" "main" {
  ami = "ami-123"
}
```

Creates EC2.

---

#### Data Source

```hcl
data "aws_ami" "amazon_linux" {
  most_recent = true
}
```

Fetches AMI information.

No EC2 is created.

---

## 3. Hardcoded Values vs Variables

### Before Day 63

```hcl
cidr_block = "10.0.0.0/16"
instance_type = "t3.micro"
```

Problem:

```text
Need to edit code for every environment.
```

---

### After Day 63

```hcl
cidr_block = var.vpc_cidr
instance_type = var.instance_type
```

Benefit:

```text
Same code
Different environments
```

```text
Dev
 └─ t2.micro

Prod
 └─ t3.small
```

---

## 4. terraform.tfvars vs prod.tfvars

| Feature              | terraform.tfvars    | prod.tfvars |
| -------------------- | ------------------- | ----------- |
| Loaded Automatically | ✅ Yes               | ❌ No        |
| Use Case             | Default environment | Production  |
| Command Needed       | None                | -var-file   |
| Typical Values       | Dev                 | Prod        |

### Dev

```hcl
project_name = "terraweek"
environment  = "dev"
instance_type = "t2.micro"
```

Run:

```bash
terraform plan
```

---

### Prod

```hcl
project_name = "terraweek"
environment  = "prod"
instance_type = "t3.small"
```

Run:

```bash
terraform plan -var-file="prod.tfvars"
```

---

## 5. Variable Precedence

| Priority | Source           |
| -------- | ---------------- |
| Lowest   | Variable Default |
| ↑        | terraform.tfvars |
| ↑        | *.auto.tfvars    |
| ↑        | -var-file        |
| ↑        | TF_VAR_*         |
| Highest  | -var             |

### Example

Default

```hcl
instance_type = "t2.micro"
```

terraform.tfvars

```hcl
instance_type = "t3.micro"
```

Environment Variable

```bash
export TF_VAR_instance_type=t3.small
```

CLI

```bash
terraform plan -var="instance_type=t2.nano"
```

Final value:

```text
t2.nano
```

Because CLI wins.

---

## 6. Static AMI vs Data Source AMI

### Before

```hcl
ami = "ami-123456"
```

Problem:

```text
Works only in one region
Eventually becomes outdated
```

---

### After

```hcl
ami = data.aws_ami.amazon_linux.id
```

Benefits:

```text
Latest AMI
Works across regions
Less maintenance
Production friendly
```

---

## 7. Individual Tags vs Locals + merge()

### Before

```hcl
tags = {
  Name = "TerraWeek-VPC"
}
```

Repeated everywhere.

---

### After

```hcl
locals {
  common_tags = {
    Project = var.project_name
    Environment = var.environment
  }
}
```

```hcl
tags = merge(
  local.common_tags,
  {
    Name = "${local.name_prefix}-vpc"
  }
)
```

Benefits:

| Before           | After            |
| ---------------- | ---------------- |
| Duplicate tags   | Centralized      |
| Hard to maintain | Easy to maintain |
| Inconsistent     | Consistent       |

---

## 8. Static Ports vs Dynamic Block

### Before

```hcl
ingress {
  from_port = 22
}

ingress {
  from_port = 80
}
```

---

### After

```hcl
dynamic "ingress" {
  for_each = var.allowed_ports
}
```

Values:

```hcl
allowed_ports = [22,80,443]
```

Generated automatically.

Benefits:

```text
Less code
Reusable
Environment friendly
```

---

## 9. String Functions Comparison

| Function  | Purpose         | Example             |
| --------- | --------------- | ------------------- |
| upper()   | Uppercase       | TERRAWEEK           |
| lower()   | Lowercase       | terraweek           |
| join()    | Combine list    | terra-week-2026     |
| format()  | Template string | arn:aws:s3:::bucket |
| replace() | Replace text    | prod → dev          |

---

## 10. Collection Functions Comparison

| Function | Purpose            |
| -------- | ------------------ |
| length() | Count items        |
| lookup() | Get value from map |
| merge()  | Combine maps       |
| keys()   | Return keys        |
| values() | Return values      |
| toset()  | Remove duplicates  |

---

## 11. Conditionals

### Without Condition

```hcl
instance_type = "t2.micro"
```

Always same.

---

### With Condition

```hcl
instance_type = var.environment == "prod" ? "t3.small" : "t2.micro"
```

Result:

| Environment | Instance Type |
| ----------- | ------------- |
| dev         | t2.micro      |
| test        | t2.micro      |
| prod        | t3.small      |

---

## 12. Outputs vs terraform state

| Outputs            | State                       |
| ------------------ | --------------------------- |
| User friendly      | Internal                    |
| Intended for users | Intended for Terraform      |
| Safe to expose     | Contains sensitive metadata |
| terraform output   | terraform state show        |

Example:

```bash
terraform output instance_public_ip
```

Output:

```text
100.21.17.7
```

---

## 13. Issues You Actually Faced During Day 63

| Issue                       | Root Cause                      | Fix                                  |
| --------------------------- | ------------------------------- | ------------------------------------ |
| t2.micro unsupported in AZ  | AZ didn't support instance type | Switched AZ / instance type          |
| S3 bucket already exists    | Global namespace                | Added random_id                      |
| Invalid variable validation | Used local inside validation    | Changed to var.project_name          |
| trim() error                | Wrong function                  | Used trimspace()                     |
| Subnet CIDR conflict        | Existing subnet overlap         | Recreate subnet / use different CIDR |
| Public DNS empty initially  | AWS metadata not refreshed      | Re-run plan/apply                    |

---

## One-Line Summary of Day 63

| Before Day 63         | After Day 63       |
| --------------------- | ------------------ |
| Hardcoded Terraform   | Reusable Terraform |
| Fixed values          | Variables          |
| Static tags           | Locals + merge     |
| Static AMIs           | Data Sources       |
| Manual outputs lookup | Outputs            |
| One environment       | Multi-environment  |
| Repeated code         | Dynamic code       |
| Region dependent      | Region agnostic    |


