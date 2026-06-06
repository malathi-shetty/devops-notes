# DAY 61 – TERRAFORM INTRODUCTION (FULL DEVOPS NOTES – LOSSLESS VERSION)

---

#  PART 1 – WHAT WE LEARNED

Terraform is an **Infrastructure as Code (IaC)** tool used to:

* Define cloud infrastructure using code instead of manual AWS console clicks
* Provision resources like EC2, S3, VPC, etc.
* Manage infrastructure across multiple cloud providers
* Track infrastructure state automatically

---

##  Core Idea

> Terraform = You define WHAT you want, Terraform decides HOW to achieve it

---

# PART 2 – WHY TERRAFORM EXISTS

Before Terraform:

* Infrastructure was created manually in AWS console
* Environments were inconsistent
* No version control for infrastructure
* Difficult to scale or reproduce infra
* High human error rate

Terraform solves this by:

* Infrastructure as Code (IaC)
* Version control using Git
* State tracking
* Automation across clouds

---

#  PART 3 – WHAT WE DID IN LAB

---

## Step 1 – Setup verification

```bash
terraform -version
aws --version
aws configure
aws sts get-caller-identity
```

✔ Output:

* Terraform installed
* AWS CLI configured
* IAM identity verified
* AWS connection working

---

## Step 2 – Project setup

```bash
mkdir terraform-basics
cd terraform-basics
```

---

## Step 3 – Terraform lifecycle execution

```bash
terraform init
terraform plan
terraform apply
```

---

### What each step did

### terraform init

* Downloads provider plugins (AWS)
* Creates `.terraform/` directory

### terraform plan

* Shows execution preview
* No changes applied

### terraform apply

* Creates real AWS resources
* Updates `terraform.tfstate`

---

## Step 4 – Resources created

* S3 bucket
* EC2 instance

---

## Step 5 – State inspection

```bash
terraform show
terraform state list
terraform state show <resource>
```

✔ State contains:

* EC2 instance ID
* S3 bucket name
* Metadata (AMI, tags, region)

---

## Step 6 – EC2 tag modification

Before:

```hcl
Name = "TerraWeek-Day1"
```

After:

```hcl
Name = "TerraWeek-Modified"
```

✔ Result:

```text
~ tags
```

Meaning:

* In-place update (no recreation)

---

## Step 7 – Destroy infrastructure

```bash
terraform destroy
```

✔ Removes:

* EC2
* S3
* State entries

---

#  PART 4 – TERRAFORM CORE WORKFLOW

```text
Code (main.tf)
      ↓
terraform init
      ↓
terraform plan
      ↓
terraform apply
      ↓
terraform.tfstate
      ↓
AWS Infrastructure
```

---

# PART 5 – CORE CONCEPTS (DETAILED)

---

##  Terraform State

Terraform state is:

👉 A JSON file that maps real infrastructure to Terraform configuration

It stores:

* Resource IDs
* Metadata
* Relationships
* Current infrastructure snapshot

---

### Why state is important

* Tracks real infrastructure
* Detects changes (drift detection base)
* Prevents duplication
* Enables updates instead of recreation

---

###  Never do:

* Edit state manually
* Store state in Git

---

##  Declarative vs Imperative

Terraform is **declarative**

👉 You define WHAT you want
👉 Terraform decides HOW to achieve it

Example:

* You say: “Create EC2”
* Terraform handles all AWS API steps

---

## Cloud Agnostic meaning

Terraform uses providers, so same syntax works for:

* AWS
* Azure
* GCP
* Kubernetes
* SaaS tools

---

##  Declarative refinement (important improvement)

Terraform is declarative because:

> It defines desired end state and automatically calculates steps to reach it

---

#  PART 6 – TERRAFORM CONFIG (REAL LAB CODE)

---

## Provider + S3

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "ap-south-1"
}

resource "aws_s3_bucket" "terra_bucket" {
  bucket = "terraweek-unique-bucket-12345"
}
```

---

## EC2 instance

```hcl
resource "aws_instance" "terra_ec2" {
  ami           = "ami-003c5247665391546"
  instance_type = "t3.medium"

  tags = {
    Name = "TerraWeek-Day1"
  }
}
```

---

#  PART 7 – TERRAFORM LIFECYCLE MODEL

```text
init → plan → apply → destroy
```

---

## terraform init

* Downloads providers
* Initializes working directory

---

## terraform plan

* Shows changes before execution
* Safe preview mode

---

## terraform apply

* Creates real infrastructure
* Updates state file

---

## terraform destroy

* Deletes all managed infrastructure

---

#  PART 8 – SYMBOLS IN PLAN OUTPUT

| Symbol | Meaning |
| ------ | ------- |
| +      | Create  |
| -      | Delete  |
| ~      | Modify  |
| -/+    | Replace |

---

#  PART 9 – ALL COMPARISON TABLES (FULL SET)

---

## Terraform vs AWS Console

| Feature         | AWS Console | Terraform |
| --------------- | ----------- | --------- |
| Speed           | Slow        | Fast      |
| Consistency     | Low         | High      |
| Version control | No          | Yes       |
| Automation      | No          | Yes       |
| Scaling         | Hard        | Easy      |

---

## plan vs apply vs destroy

| Feature          | plan | apply | destroy |
| ---------------- | ---- | ----- | ------- |
| Executes changes | No   | Yes   | Yes     |
| Preview          | Yes  | No    | No      |
| Updates AWS      | No   | Yes   | Yes     |
| Updates state    | No   | Yes   | Yes     |

---

## Terraform vs Other Tools

| Tool           | Purpose                     |
| -------------- | --------------------------- |
| Terraform      | Infrastructure provisioning |
| CloudFormation | AWS-only IaC                |
| Ansible        | Configuration management    |
| Pulumi         | Code-based IaC              |

---

## State file importance

| Aspect               | Meaning            |
| -------------------- | ------------------ |
| Source of truth      | Maps infra to code |
| Tracks resources     | IDs, metadata      |
| Enables updates      | Change detection   |
| Prevents duplication | Avoid re-creation  |

---

#  PART 10 – COMMAND CHEATSHEET

```bash
terraform init
terraform plan
terraform apply
terraform destroy
terraform show
terraform state list
terraform state show <resource>
terraform fmt
```

---

#  PART 11 – COMMON MISTAKES

---

* Wrong region mismatch
* Invalid AMI
* S3 bucket already exists
* Missing terraform init
* Editing state file manually (danger)
* Not understanding state dependency

---

# 🔧 PART 12 – TROUBLESHOOTING

---

## Resource not created

```bash
terraform plan
```

---

## Provider error

```bash
terraform init
```

---

## Region mismatch

Fix provider + re-run apply

---

## State issues

```bash
terraform refresh
```

---

#  PART 13 – VARIABLES, OUTPUTS, MODULES

---

## Variables

Used for dynamic values

```hcl
variable "instance_type" {
  default = "t2.micro"
}
```

---

## Outputs

Used to display results

```hcl
output "instance_id" {
  value = aws_instance.terra_ec2.id
}
```

---

## Modules

Reusable Terraform code blocks

👉 Like functions in programming

Used for:

* standard infra templates
* reusable EC2/VPC patterns
* production systems

---

# PART 14 – REAL-WORLD USAGE

Terraform is used for:

* AWS infrastructure provisioning (EC2, S3, VPC, RDS)
* Kubernetes clusters (EKS/GKE)
* CI/CD automation
* Multi-environment setup (dev/stage/prod)
* Infrastructure standardization

---

#  PART 15 – “I WISH I KNEW THIS EARLIER”

* Terraform is not execution tool → it is a **state comparison engine**
* AWS console is NOT source of truth → state file is
* Small change triggers infra update
* Region mistakes cause most errors
* State file is EVERYTHING

---

#  PART 16 – TERRAFORM MENTAL MODELS

---

## Core idea

```text
Code + State + AWS = Decision
```

---

## Terraform decision model

```text
Desired State (code)
     +
Current State (tfstate)
     +
Real AWS
     ↓
Terraform action plan
```

---

## Mental flow diagram

```text
main.tf
  ↓
terraform plan
  ↓
terraform apply
  ↓
terraform.tfstate
  ↓
AWS infrastructure
```

---

#  PART 17 – INTERVIEW QUESTIONS

Here are your **clean, copy-paste interview answers** in simple DevOps style:

---

## What is Terraform?

Terraform is an **Infrastructure as Code (IaC) tool** used to provision and manage cloud infrastructure using code instead of manual configuration.

It helps create, modify, and delete resources like EC2, S3, VPC, etc., in an automated and repeatable way.

---

## What is a state file?

The Terraform state file (`terraform.tfstate`) is a **JSON file that stores the mapping between Terraform code and real infrastructure**.

It contains details like:

* Resource IDs
* Metadata (AMI, instance type, tags)
* Current infrastructure status

---

## Why is state important?

State is important because it acts as the **source of truth for Terraform**.

It helps Terraform:

* Track existing resources
* Detect changes in infrastructure
* Avoid duplicate resource creation
* Update only what has changed

---

## What is `terraform init`?

`terraform init` is the **initialization command** that:

* Downloads required provider plugins (like AWS)
* Initializes backend configuration
* Sets up the working directory for Terraform

---

## What is `terraform plan`?

`terraform plan` is a **preview command** that shows what changes Terraform will make before applying them.

It compares:

* Terraform code
* State file
* Real infrastructure

No changes are made to AWS during this step.

---

## What is `terraform apply`?

`terraform apply` is the command that **executes the changes defined in the plan**.

It:

* Creates or updates real infrastructure
* Updates the state file
* Makes changes in AWS (or any provider)

---

## What is `terraform destroy`?

`terraform destroy` is used to **delete all infrastructure managed by Terraform**.

It:

* Removes EC2, S3, and other resources
* Cleans up the state file accordingly

---

## Why not edit the state file manually?

We should NOT edit the state file manually because:

* It can corrupt Terraform’s tracking system
* May cause resource duplication or deletion issues
* Breaks synchronization between code and real infrastructure
* Leads to unpredictable behavior

Terraform manages state automatically.

---

## What is the declarative approach?

Terraform follows a **declarative approach**, meaning:

You define **WHAT you want**, not HOW to do it.

Example:

* You define: “I want an EC2 instance”
* Terraform decides the steps required to create it using AWS APIs

---

