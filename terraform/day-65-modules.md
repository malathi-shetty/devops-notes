# Chapter 1 – Why This Day Matters

 

Before Day 65, I knew how to create AWS resources using Terraform.

I could create:

```text
VPC
Subnet
Security Group
EC2
Route Tables
Internet Gateway
```

one resource at a time.

That was useful for learning AWS and Terraform basics.

However, real companies do not build infrastructure by repeatedly writing the same resource blocks over and over again.

Real-world infrastructure must be:

```text
Reusable
Maintainable
Scalable
Consistent
```

Day 65 was the day I learned how Terraform solves this problem through Modules.

This was the point where Terraform started feeling less like resource creation and more like software engineering.

---

## The One Sentence Summary

If Future you forgets everything else, remember:

```text
Terraform Modules are reusable infrastructure components
that allow us to write infrastructure once and use it many times.
```

Everything learned on Day 65 revolves around this idea.

---

## Before Day 65

Imagine I needed:

```text
Web Server
API Server
Database Server
```

Without modules I might create:

```hcl
resource "aws_instance" "web" {}

resource "aws_instance" "api" {}

resource "aws_instance" "db" {}
```

Now imagine:

```text
Development
QA
Staging
Production
```

The same infrastructure would be copied repeatedly.

Problems:

```text
Code Duplication
Copy-Paste Errors
Inconsistent Configurations
Hard Maintenance
Difficult Scaling
```

This is exactly the problem Terraform Modules were designed to solve.

---

# Chapter 2 – The Big Idea Behind Modules

## What Is A Terraform Module?

The technical definition:

> A Terraform Module is a reusable collection of Terraform configuration files.

Simple definition:

```text
A Module = A Reusable Terraform Folder
```

Example:

```text
modules/ec2-instance

├── main.tf
├── variables.tf
└── outputs.tf
```

Terraform treats this entire folder as a reusable unit.

---

## The Function Analogy

Terraform Modules behave very similarly to programming functions.

Python:

```python
def create_server(name):
    return server
```

Terraform:

```text
Variables
     ↓
Resources
     ↓
Outputs
```

Both follow the same pattern:

```text
Input
 ↓
Processing
 ↓
Output
```

---

## Why Companies Love Modules

Without Modules:

```text
Dev Environment
QA Environment
Stage Environment
Production Environment
```

often contain duplicated infrastructure code.

With Modules:

```text
Write Once
Reuse Everywhere
```

Benefits:

```text
Consistency
Reusability
Maintainability
Scalability
Reduced Errors
```

---

# Chapter 3 – Mental Models That Make Modules Easy

These analogies are important because they help Future you remember the concept without memorizing Terraform syntax.

---

## Bakery Story

Imagine a bakery.

Customers keep ordering the same cake.

Bad Bakery:

Every time an order arrives:

```text
Mix Flour
Mix Sugar
Mix Cocoa
Bake
```

written again.

Again.

Again.

Again.

Very inefficient.

---

Smart Bakery:

Create one recipe card.

```text
Chocolate Cake Recipe
```

Whenever a customer orders:

```text
Use Recipe Card
```

No rewriting required.

---

Terraform equivalent:

Without Modules:

```hcl
resource "aws_instance" "web"

resource "aws_instance" "api"

resource "aws_instance" "backend"
```

Repeated logic.

---

With Modules:

```hcl
module "web_server"

module "api_server"

module "backend_server"
```

Reuse the recipe.

---

### Key Memory

```text
Terraform Module = Recipe Card
```

---

## Variables Analogy

Think of variables as an order form.

Customer fills:

```text
Name
Flavor
Size
```

Terraform fills:

```hcl
instance_name
instance_type
ami_id
```

Variables tell the module what should be created.

---

### Key Memory

```text
Variables = Order Form
```

---

## Outputs Analogy

After the cake is ready, the bakery gives a receipt.

Example:

```text
Order Number
```

Terraform does the same thing.

After creating infrastructure:

```text
Instance ID
Public IP
Private IP
Security Group ID
```

are returned.

---

### Key Memory

```text
Outputs = Receipt
```

---

## Dynamic Block Analogy

Imagine a security guard.

You tell the guard:

```text
Allow:

22
80
443
```

Instead of writing three separate rules manually, the guard creates them automatically.

Terraform Dynamic Blocks work the same way.

---

### Key Memory

```text
Dynamic Block = Assistant Repeating Work
```

---

## Registry Module Analogy

Imagine building a house.

Option 1:

```text
Build walls
Build doors
Build windows
Build roof
```

yourself.

---

Option 2:

```text
Hire Professional Builder
```

and simply say:

```text
Build House
```

Terraform Registry Modules work exactly like the professional builder.

---

### Key Memory

```text
Registry Module = Professional Builder
```

---

## Root vs Child Module Analogy

Bakery Office:

```text
Where work starts
```

Recipe Cards:

```text
Reusable instructions
```

Terraform equivalent:

```text
Root Module = Bakery Office

Child Module = Recipe Card
```

---

### Future you Memory Sheet

```text
Module = Recipe Card

Variables = Order Form

Outputs = Receipt

Dynamic Block = Assistant Repeating Work

Registry Module = Professional Builder

Root Module = Bakery Office

Child Module = Recipe Card
```

***



# Chapter 4 – Root Module vs Child Module

---

## Why This Concept Matters

At first, Root Module vs Child Module looked like simple terminology.

But this is actually one of Terraform's most important concepts because it explains:

```text
Where Terraform starts

Where reusable code lives

How infrastructure gets organized
```

Understanding this makes large Terraform projects much easier to navigate.

---

# Root Module

The Root Module is the directory where Terraform execution begins.

This is the location where I run:

```bash
terraform init

terraform plan

terraform apply

terraform destroy
```

Terraform always starts from here.

Think:

```text
Root Module
=
Project Entry Point
```

---

## Mental Model

Imagine a bakery.

The bakery office is where all work starts.

Orders arrive here.

Decisions happen here.

Recipe cards are stored elsewhere.

The bakery office is equivalent to:

```text
Root Module
```

---

## Day 65 Example

My project structure looked similar to:

```text
terraform-modules/

├── main.tf
├── variables.tf
├── outputs.tf

└── modules/
```

This folder was the Root Module.

Terraform commands were executed from here.

---

## Responsibilities Of Root Module

The Root Module typically:

```text
Calls Child Modules

Passes Variables

Connects Modules Together

Defines Environment Configuration

Produces Final Outputs
```

Think:

```text
Root Module = Conductor

Child Modules = Musicians
```

The conductor coordinates everything.

---

# Child Module

A Child Module is simply a module called by another module.

Examples from Day 65:

```text
modules/ec2-instance

modules/security-group
```

These modules were not executed directly.

Instead:

```text
Root Module
     ↓
Calls Child Module
```

---

## Example

```hcl
module "web_server" {

 source = "./modules/ec2-instance"

}
```

Terraform:

```text
Go into module folder

Read variables

Create resources

Return outputs
```

---

# Child Module Characteristics

A good child module:

```text
Has one responsibility

Accepts inputs

Creates resources

Returns outputs

Can be reused
```

---

# Single Responsibility Principle

One of the biggest hidden lessons of Day 65.

Initially many beginners think:

```text
Why not put everything inside one module?
```

Example:

```text
EC2
Security Group
VPC
IAM
RDS
```

all inside one giant module.

This works.

But becomes difficult to maintain.

---

## Better Design

```text
EC2 Module

Security Group Module

VPC Module

RDS Module
```

Each module has one job.

Benefits:

```text
Easy Reuse

Easy Debugging

Easy Maintenance

Cleaner Architecture
```

---

# Why We Created TWO Modules

This was intentional.

---

## Module 1

```text
EC2 Module
```

Job:

```text
Create EC2 Instances
```

Nothing else.

---

## Module 2

```text
Security Group Module
```

Job:

```text
Create Security Groups
```

Nothing else.

---

This follows:

```text
One Module
=
One Responsibility
```

---

# Module Interface Concept

This is a more advanced lesson that appeared later in the notes.

Think of a vending machine.

You don't care how the machine works internally.

You only care about:

Inputs:

```text
Money

Button Press
```

Outputs:

```text
Snack
```

---

Terraform Modules are similar.

Users should not need to understand internal implementation.

They only need:

Inputs:

```text
ami_id

instance_type

subnet_id
```

Outputs:

```text
public_ip

instance_id
```

This concept is called:

```text
Abstraction
```

---

# Module Anatomy Revisited

Every reusable module follows:

```text
Module

├── variables.tf
│      Inputs
│
├── main.tf
│      Logic
│
└── outputs.tf
       Results
```

---

# Local Module Example

Day 65 used local modules.

Example:

```hcl
source = "./modules/ec2-instance"
```

Meaning:

```text
Use my module

Use my code

Use local folder
```

---

# Child Module Lifecycle

Terraform processes a child module like this:

```text
Receive Inputs
        ↓
Read variables.tf
        ↓
Execute main.tf
        ↓
Create Resources
        ↓
Generate Outputs
        ↓
Return Results To Root Module
```

---

#  Question

### What is a Root Module?

Answer:

> A Root Module is the main Terraform configuration directory where Terraform commands such as init, plan, apply, and destroy are executed.

---

### What is a Child Module?

Answer:

> A Child Module is a reusable Terraform module that is called by another module using a module block.

---

### Difference Between Root Module and Child Module?

| Root Module                  | Child Module               |
| ---------------------------- | -------------------------- |
| Starting point               | Reusable component         |
| Runs Terraform commands      | Called by another module   |
| Coordinates infrastructure   | Creates specific resources |
| Usually environment-specific | Usually reusable           |

---

# Future you Memory

If everything is forgotten:

```text
Root Module
=
Bakery Office

Child Module
=
Recipe Card

Root Module
=
Conductor

Child Module
=
Musician

Root Module
=
Project Entry Point

Child Module
=
Reusable Infrastructure Component
```

---

# Chapter 5 – EC2 Module Deep Dive

---

## Why We Created An EC2 Module

Before modules:

```hcl
resource "aws_instance" "web" {}

resource "aws_instance" "api" {}

resource "aws_instance" "db" {}
```

Same logic.

Repeated.

Again.

Again.

Again.

---

The goal was:

```text
Write Once

Reuse Many Times
```

---

## Folder Structure

```text
modules/

└── ec2-instance/

    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

This became a reusable EC2 factory.

---

## Purpose Of The Module

The module's responsibility:

```text
Create EC2 Instances
```

Nothing more.

Nothing less.

---

## Inputs (Variables)

The EC2 module accepted:

```text
AMI ID

Instance Type

Subnet ID

Security Group IDs

Instance Name

Tags
```

These values were provided by the Root Module.

---

## Variables = Order Form

Mental Model:

```text
Customer fills order form
```

Terraform equivalent:

```hcl
instance_name = "terraweek-web"
```

or

```hcl
instance_name = "terraweek-api"
```

Same module.

Different input.

Different result.

---

## Main.tf Responsibility

The module's main.tf contained:

```text
aws_instance resource

Tag configuration

Instance settings
```

This is where actual infrastructure creation happened.

---

## Merge Function

Important Day 65 concept.

Used:

```hcl
merge(
 var.tags,
 {
   Name = var.instance_name
 }
)
```

---

### Why Use Merge?

Without merge:

```text
Name Tag Only
```

or

```text
Custom Tags Only
```

---

With merge:

```text
Name

Project

Owner

Environment

Day
```

all survive together.

---

### Memory Trick

Think:

```text
Combining Two Sticker Sheets
```

Nothing gets lost.

Everything gets combined.

---

## Outputs

The EC2 module returned:

```text
instance_id

public_ip

private_ip
```

---

## Why Outputs Matter

Without outputs:

```text
Instance Exists
```

But nobody knows:

```text
Instance ID

Public IP

Private IP
```

---

Outputs expose useful information.

Think:

```text
Receipt After Purchase
```

---

## Reusability Proof

The same module created:

```text
terraweek-web

terraweek-api
```

using:

```text
Same Code

Different Inputs
```

This was the biggest proof that modules work.

---

## Day 65 Lesson

The EC2 Module taught:

```text
Infrastructure Can Be Reused

Like Functions

Like Classes

Like Components
```

This is where Terraform started feeling like software engineering.

***




# Chapter 6 – Security Group Module Deep Dive

---

## Why Create A Separate Security Group Module?

When learning Terraform, a beginner often thinks:

```text id="a1b2c3"
Why not create the Security Group directly
inside the EC2 module?
```

At first this seems simpler.

But in real projects:

```text id="d4e5f6"
One Security Group

can be used by

Many EC2 Instances
```

Creating a dedicated module makes the Security Group reusable.

---

## Single Responsibility Principle

The Security Group Module had one job:

```text id="g7h8i9"
Create Security Groups
```

Not:

```text id="j1k2l3"
EC2

VPC

IAM

RDS
```

Just Security Groups.

---

## Folder Structure

```text id="m4n5o6"
modules/

└── security-group/

    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

---

## Inputs

The Security Group Module accepted:

```text id="p7q8r9"
VPC ID

Security Group Name

Ingress Ports

Tags
```

These values were supplied by the Root Module.

---

## Why VPC ID Was Required

A Security Group cannot exist by itself.

AWS requires:

```text id="s1t2u3"
Security Group
        ↓
Must belong to
        ↓
VPC
```

Therefore:

```hcl id="v4w5x6"
vpc_id
```

was a required input.

---

## Why Security Group Name Was Required

Different environments often need different names.

Examples:

```text id="y7z8a9"
web-sg

api-sg

prod-web-sg

qa-web-sg
```

Hardcoding would reduce flexibility.

Variables increased reusability.

---

## Why Ingress Ports Were Required

Different applications need different ports.

Examples:

```text id="b1c2d3"
SSH   → 22

HTTP  → 80

HTTPS → 443
```

Instead of hardcoding ports:

```hcl id="e4f5g6"
22

80

443
```

we passed them dynamically.

---

# Output

The module returned:

```text id="h7i8j9"
sg_id
```

Example:

```text id="k1l2m3"
sg-123456789
```

This output became extremely important later.

---

## Why sg_id Matters

Creating a Security Group is only half the job.

Resources that need protection must use it.

Example:

```text id="n4o5p6"
EC2 Instance
```

needs:

```text id="q7r8s9"
Security Group ID
```

Therefore:

```text id="t1u2v3"
Output sg_id
```

became the bridge between modules.

---

# Security Group Module Flow

```text id="w4x5y6"
Inputs
  ↓
Security Group Module
  ↓
Create Security Group
  ↓
Output sg_id
```

---

# Lesson Learned

The Security Group Module taught:

```text id="z7a8b9"
Modules should be reusable

Modules should do one thing well

Modules should expose useful outputs
```

---

# Chapter 7 – Dynamic Blocks

---

## Why Dynamic Blocks Exist

Without Dynamic Blocks:

```hcl id="c1d2e3"
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

Works.

But becomes repetitive.

---

Imagine:

```text id="f4g5h6"
10 Ports

20 Ports

50 Ports
```

Maintenance becomes painful.

---

# Dynamic Block Solution

Instead:

```hcl id="i7j8k9"
ingress_ports = [22,80,443]
```

Terraform generates all rules automatically.

---

## Mental Model

Think:

```text id="l1m2n3"
Teacher gives attendance list

Assistant writes attendance sheet
```

Teacher:

```text id="o4p5q6"
22

80

443
```

Assistant:

```text id="r7s8t9"
Create Rule

Create Rule

Create Rule
```

Dynamic Block = Assistant

---

## Another Mental Model

Think Python:

```python id="u1v2w3"
for port in ports:
    create_rule(port)
```

Terraform Dynamic Blocks work very similarly.

---

## What Dynamic Blocks Generate

Input:

```text id="x4y5z6"
22

80

443
```

Terraform creates:

```text id="a7b8c9"
SSH Rule

HTTP Rule

HTTPS Rule
```

inside the Security Group.

---

# Why This Was Important

Day 65 was the first time Terraform started behaving more like programming.

Instead of:

```text id="d1e2f3"
Write

Write

Write
```

we moved toward:

```text id="g4h5i6"
Provide Data

Terraform Generates Configuration
```

---

# Dynamic Block vs for_each

This is a common  question.

---

## Dynamic Block

Repeats:

```text id="j7k8l9"
Nested Configuration Blocks
```

Example:

```text id="m1n2o3"
Ingress Rules
```

inside ONE Security Group.

---

## for_each

Repeats:

```text id="p4q5r6"
Entire Resources
```

Example:

```text id="s7t8u9"
EC2

EC2

EC2
```

multiple instances.

---

## Easy Memory Trick

```text id="v1w2x3"
dynamic
=
Repeat inside resource

for_each
=
Repeat resource itself
```

---

#  Answer

### What Are Dynamic Blocks?

Answer:

> Dynamic Blocks generate repeated nested configuration blocks from a collection, reducing duplication and improving maintainability.

---

# Future you Memory

```text id="y4z5a6"
Dynamic Block

=
Terraform's Loop

inside a Resource
```

---

# Chapter 8 – Module Communication & Outputs

---

## One Of The Most Important Lessons Of Day 65

This chapter explains:

```text id="b7c8d9"
How Modules Talk To Each Other
```

Without this concept:

```text id="e1f2g3"
Modules become isolated
```

---

# The Problem

Security Group Module creates:

```text id="h4i5j6"
sg-12345
```

EC2 Module requires:

```text id="k7l8m9"
sg-12345
```

How does EC2 know about it?

---

# The Solution

Outputs.

Security Group Module:

```hcl id="n1o2p3"
output "sg_id"
```

Root Module:

```hcl id="q4r5s6"
module.web_sg.sg_id
```

EC2 Module receives:

```hcl id="t7u8v9"
security_group_ids
```

---

# Actual Flow

```text id="w1x2y3"
Security Group Module
        ↓
Creates SG
        ↓
Outputs sg_id
        ↓
Root Module Reads Output
        ↓
Passes sg_id
        ↓
EC2 Module
        ↓
EC2 Created
```

---

# Visual Diagram

```text id="z4a5b6"
module.web_sg
        |
        |
        | sg_id
        |
        v

module.web_server

module.api_server
```

One Security Group.

Multiple EC2 Instances.

Reusable architecture.

---

# Hidden Terraform Superpower

Many beginners miss this.

When Terraform sees:

```hcl id="c7d8e9"
module.web_sg.sg_id
```

Terraform automatically understands:

```text id="f1g2h3"
Security Group must exist first
```

before EC2 creation.

---

# Dependency Graph

Terraform builds:

```text id="i4j5k6"
VPC
 ↓
Security Group
 ↓
EC2
```

automatically.

No manual ordering required.

---

# Why This Matters

Terraform is not executing files line-by-line.

Terraform builds:

```text id="l7m8n9"
Dependency Graph
```

and executes resources in the correct order.

This is one of the most important Terraform concepts you'll encounter.

---

# Outputs Are Return Values

Think of a programming function.

Python:

```python id="o1p2q3"
def add(a,b):
    return result
```

Terraform:

```text id="r4s5t6"
Module
 ↓
Output
 ↓
Returned Value
```

Outputs are Terraform's return statements.

---

# Future you Memory

```text id="u7v8w9"
Variables
=
Inputs

Outputs
=
Return Values

module.web_sg.sg_id
=
Module Communication

Outputs
=
How Modules Talk

Outputs
=
How Terraform Creates Dependencies
```

***




# Chapter 9 – Terraform Registry & Registry Modules

---

# The Big Question

Up until this point, we built our own modules:

```text id="r9m1a1"
EC2 Module

Security Group Module
```

These are called:

```text id="m8n2b2"
Local Modules
```

because we wrote and maintained them ourselves.

Then a new question appeared:

```text id="x7y3c3"
Do engineers always write everything from scratch?
```

The answer is:

```text id="p6q4d4"
No.
```

And that is why Terraform Registry exists.

---

# What Is Terraform Registry?

Terraform Registry is a public repository containing reusable Terraform modules.

Think:

```text id="t5u5e5"
Python      → PyPI

NodeJS      → NPM

Docker      → Docker Hub

Terraform   → Terraform Registry
```

Instead of building everything yourself:

```text id="v4w6f6"
Search

Download

Reuse
```

existing modules.

---

# Simple Definition

Terraform Registry is:

```text id="g3h7i7"
A library of reusable Terraform modules
published by HashiCorp, AWS,
and the community.
```

---

# Why Registry Exists

Imagine building a VPC manually.

You must write:

```text id="j2k8l8"
VPC

Subnets

Route Tables

Internet Gateway

Associations

Routes

Security Components
```

Many resources.

Lots of code.

Lots of maintenance.

---

Terraform Registry allows:

```text id="m1n9o9"
Reuse Instead Of Rewrite
```

---

# Local Module vs Registry Module

---

## Local Module

Written by me.

Example:

```hcl id="a1b2c4"
source = "./modules/ec2-instance"
```

Meaning:

```text id="c3d4e5"
Use my code
```

---

Advantages:

```text id="f6g7h8"
Full Control

Custom Logic

Easy Learning
```

Disadvantages:

```text id="i9j1k2"
Must Maintain Yourself
```

---

## Registry Module

Written by experts.

Example:

```hcl id="l3m4n5"
source = "terraform-aws-modules/vpc/aws"
```

Meaning:

```text id="o6p7q8"
Use community-tested code
```

---

Advantages:

```text id="r9s1t2"
Battle Tested

Production Ready

Saves Time

Widely Used
```

Disadvantages:

```text id="u3v4w5"
Must Understand Documentation

Less Internal Control
```

---

# Mental Model

Imagine building a house.

Option 1:

```text id="x6y7z8"
Build Foundation

Build Walls

Build Roof

Build Windows
```

yourself.

---

Option 2:

```text id="a9b1c2"
Hire Professional Builder
```

and simply provide instructions.

Terraform Registry Modules are that professional builder.

---

# Future you Memory

```text id="d3e4f5"
Local Module
=
Build Yourself

Registry Module
=
Hire Expert Builder
```

---

# Why This Was A Major Learning Moment

Before Day 65:

```text id="g6h7i8"
Terraform
=
Writing Resources
```

After Registry Modules:

```text id="j9k1l2"
Terraform
=
Composing Reusable Components
```

This is a huge mindset shift.

---

# Chapter 10 – terraform init, Module Downloads & .terraform/modules

---

# The Confusion

When using Registry Modules, an important question appeared:

```text id="m3n4o5"
Where does Terraform get the module code?
```

Example:

```hcl id="p6q7r8"
module "vpc" {

 source = "terraform-aws-modules/vpc/aws"

}
```

No code exists locally.

Yet Terraform still creates infrastructure.

How?

---

# What terraform init Really Does

Most beginners think:

```text id="s9t1u2"
terraform init
=
start Terraform
```

Partly true.

But Day 65 revealed more.

---

When Terraform sees:

```hcl id="v3w4x5"
source = "terraform-aws-modules/vpc/aws"
```

it performs:

```text id="y6z7a8"
Find Module

Download Module

Store Module Locally

Prepare Terraform
```

---

# Module Download Process

Terraform Registry

```text id="b9c1d2"
Registry
    ↓
Download
    ↓
Local Machine
    ↓
.terraform/modules
```

---

# Hidden Folder Discovery

One important Day 65 lesson:

```text id="e3f4g5"
Downloaded modules
are stored locally.
```

Location:

```text id="h6i7j8"
.terraform/modules
```

---

# Why Many Beginners Miss This

The folder starts with:

```text id="k9l1m2"
.
```

which means:

```text id="n3o4p5"
Hidden Folder
```

Many file explorers hide it by default.

---

# Actual Discovery

After running:

```bash id="q6r7s8"
terraform init
```

Terraform created:

```text id="t9u1v2"
.terraform/
```

Inside:

```text id="w3x4y5"
modules/
```

Inside:

```text id="z6a7b8"
downloaded module code
```

---

# Verification

Command used:

```bash id="c9d1e2"
tree .terraform/modules
```

This showed:

```text id="f3g4h5"
main.tf

variables.tf

outputs.tf

README.md

examples
```

---

# Important Realization

Terraform is not calling modules remotely every time.

Instead:

```text id="i6j7k8"
Download Once

Store Locally

Use Locally
```

This improves performance.

---

# terraform init -upgrade

Another important discovery.

---

# The Problem

Suppose:

```text id="l9m1n2"
Module Version Updated
```

in Terraform Registry.

Your machine still has:

```text id="o3p4q5"
Old Downloaded Version
```

cached locally.

---

# Solution

Run:

```bash id="r6s7t8"
terraform init -upgrade
```

Terraform:

```text id="u9v1w2"
Checks Registry

Downloads New Version

Updates Local Cache
```

---

# Memory Trick

```text id="x3y4z5"
terraform init
=
Download If Missing

terraform init -upgrade
=
Download Latest Allowed Version
```

---

# Common Confusion

Problem:

```text id="a6b7c8"
Why didn't Terraform download again?
```

Answer:

```text id="d9e1f2"
Already Cached
```

Terraform reuses local copies when possible.

---

# Future you Memory

```text id="g3h4i5"
terraform init
=
Prepare Project

terraform init
=
Download Modules

terraform init
=
Initialize Providers

terraform init -upgrade
=
Refresh Module Versions
```

---

# Chapter 11 – Hand-Written VPC vs Registry VPC

---

# Why This Comparison Matters

This chapter demonstrates the true power of Registry Modules.

---

## Hand-Written VPC

A beginner typically writes:

```text id="j6k7l8"
VPC

Subnet

Internet Gateway

Route Table

Association
```

Around:

```text id="m9n1o2"
5–6 Resources
```

depending on implementation.

---

## Registry VPC Module

Using:

```hcl id="p3q4r5"
terraform-aws-modules/vpc/aws
```

Terraform creates far more.

---

Example resources include:

```text id="s6t7u8"
VPC

Default Route Table

Default ACL

Default Security Group

Internet Gateway

Public Subnets

Private Subnets

Route Tables

Associations

Routes
```

and more.

---

# Big Lesson

Registry modules often contain:

```text id="v9w1x2"
Years Of Engineering Knowledge
```

that would take significant effort to recreate.

---

# Why Companies Prefer Registry Modules

Benefits:

```text id="y3z4a5"
Less Code

More Features

Better Testing

Community Support

Faster Delivery
```

---

# Future you Memory

```text id="b6c7d8"
Hand-Written VPC
=
Learning

Registry VPC
=
Production Efficiency
```

---

# Key Day 65 Insight

The goal of Terraform is not:

```text id="e9f1g2"
Write More Code
```

The goal is:

```text id="h3i4j5"
Manage Infrastructure Efficiently
```

Registry Modules help achieve that.

***



# Chapter 12 – Terraform State Discovery & Module Ownership

---

# Why This Chapter Matters

Before Day 65, I thought Terraform State simply stored:

```text id="st101"
Created Resources
```

After Day 65, I learned Terraform State stores much more:

```text id="st102"
Resource Ownership

Relationships

Module Hierarchy

Infrastructure Mapping
```

This was a major understanding shift.

---

# The Discovery Command

Command used:

```bash id="st103"
terraform state list
```

At first glance it looks simple.

But it reveals how Terraform sees the infrastructure.

---

# What terraform state list Shows

Terraform outputs every object currently tracked in state.

Example:

```text id="st104"
module.vpc.aws_vpc.this

module.web_server.aws_instance.this

module.api_server.aws_instance.this

module.web_sg.aws_security_group.this
```

---

# First Realization

Terraform does not think:

```text id="st105"
EC2

Security Group

VPC
```

It thinks:

```text id="st106"
Who created EC2

Who created Security Group

Who created VPC
```

Ownership matters.

---

# Module Ownership

Example:

```text id="st107"
module.web_server.aws_instance.this
```

Break it down:

```text id="st108"
module.web_server
        ↓
Module Owner

aws_instance.this
        ↓
Resource
```

Terraform remembers:

```text id="st109"
This resource belongs
to this module.
```

---

# Why Ownership Matters

Imagine changing:

```text id="st110"
EC2 Module
```

Terraform immediately knows:

```text id="st111"
Which resources
were created by that module.
```

Without state ownership:

```text id="st112"
Terraform would be confused.
```

---

# Mental Model

Think of a school.

Student:

```text id="st113"
Rahul
```

belongs to:

```text id="st114"
Class 5A
```

Terraform tracks infrastructure similarly.

Resource:

```text id="st115"
EC2 Instance
```

belongs to:

```text id="st116"
EC2 Module
```

---

# Hidden Lesson

State is not just:

```text id="st117"
What Exists
```

State is also:

```text id="st118"
Who Owns What
```

---

# Day 65 Understanding Upgrade

Before:

```text id="st119"
State = Resource List
```

After:

```text id="st120"
State = Infrastructure Database
```

---

# Future you Memory

```text id="st121"
terraform state list

shows

Resource Ownership

Module Ownership

Terraform's View
of Infrastructure
```

---

# Chapter 13 – Dependency Graph Deep Dive

---

# The Most Important Hidden Terraform Concept

Many beginners think Terraform executes:

```text id="dg101"
Line 1

Line 2

Line 3
```

like a programming script.

Terraform does NOT work that way.

---

# What Terraform Actually Does

Terraform first builds:

```text id="dg102"
Dependency Graph
```

Then executes resources in the proper order.

---

# Example

Infrastructure:

```text id="dg103"
VPC

Security Group

EC2
```

---

Dependencies:

```text id="dg104"
EC2
needs
Security Group

Security Group
needs
VPC
```

Terraform builds:

```text id="dg105"
VPC
 ↓
Security Group
 ↓
EC2
```

---

# Why This Matters

You never explicitly wrote:

```text id="dg106"
Create VPC First

Then SG

Then EC2
```

Terraform figured it out automatically.

---

# How Terraform Knows

Because of references.

Example:

```hcl id="dg107"
module.web_sg.sg_id
```

This tells Terraform:

```text id="dg108"
EC2 depends on SG
```

---

# Another Example

```hcl id="dg109"
vpc_id = module.vpc.vpc_id
```

Terraform learns:

```text id="dg110"
Security Group depends on VPC
```

---

# The Graph Terraform Builds

```text id="dg111"
module.vpc
      ↓

module.web_sg
      ↓

module.web_server

module.api_server
```

---

# Why This Is Powerful

Terraform can:

```text id="dg112"
Create Resources In Parallel

Create Resources Safely

Avoid Race Conditions
```

---

# Mental Model

Think of building a house.

You cannot:

```text id="dg113"
Install Roof
```

before:

```text id="dg114"
Building Walls
```

Terraform understands such relationships automatically.

---

# Hidden Superpower

Because Terraform understands dependencies:

```text id="dg115"
Less Manual Work

Less Mistakes

Safer Infrastructure
```

---

# Future you Memory

```text id="dg116"
Terraform does not execute files.

Terraform executes graphs.
```

This sentence alone is  gold.

---

# Chapter 14 – Troubleshooting Journey

---

# Why Keep Troubleshooting Notes?

Many people delete troubleshooting notes.

That is a mistake.

Troubleshooting notes show:

```text id="tr101"
What confused me

Why it happened

How I fixed it
```

These are often remembered longer than theory.

---

# Issue 1 – sg_name Error

Error appeared:

```text id="tr102"
Unexpected attribute

sg_name is not expected here
```

---

# Immediate Reaction

Thought:

```text id="tr103"
Configuration Broken
```

---

# Verification

Ran:

```bash id="tr104"
terraform validate
```

Result:

```text id="tr105"
Success!
The configuration is valid.
```

---

# Root Cause

Problem was not Terraform.

Problem was:

```text id="tr106"
VS Code Warning
```

---

# Lesson

Trust:

```bash id="tr107"
terraform validate

terraform plan
```

more than editor warnings.

---

# Issue 2 – Hidden .terraform Folder

Problem:

```text id="tr108"
Could not find downloaded module
```

---

Cause:

```text id="tr109"
Folder hidden
```

because:

```text id="tr110"
.terraform
```

starts with a dot.

---

# Lesson

Many important Terraform files are hidden.

Always verify hidden files visibility.

---

# Issue 3 – Registry Module Download Confusion

Problem:

```text id="tr111"
Why isn't Terraform downloading again?
```

---

Cause:

```text id="tr112"
Already Cached
```

---

Solution:

```bash id="tr113"
terraform init -upgrade
```

---

# Lesson

Terraform avoids unnecessary downloads.

This is normal behavior.

---

# Issue 4 – AWS Region Confusion

Incorrect:

```text id="tr114"
us-west-2a
```

---

Correct:

```text id="tr115"
us-west-2
```

---

# Lesson

Availability Zone:

```text id="tr116"
us-west-2a
```

Region:

```text id="tr117"
us-west-2
```

Different concepts.

---

# Future you Memory

```text id="tr118"
Editor Warning
≠
Terraform Error
```

Always verify using Terraform commands.

---

# Chapter 15 – Learning Audit & Future Revisit Topics

---

# What Fully Clicked

By end of Day 65 I understood:

```text id="la101"
Modules

Variables

Outputs

Dynamic Blocks

Module Communication

Registry Modules
```

with confidence.

---

# What Improved Significantly

Terraform stopped feeling like:

```text id="la102"
Resource Creation
```

and started feeling like:

```text id="la103"
Software Engineering
```

---

# Biggest Mindset Shift

Before:

```text id="la104"
Write Resources
```

After:

```text id="la105"
Design Reusable Infrastructure
```

---

# Concepts Worth Revisiting Later

These were mentioned in the notes and should be preserved.

---

## Module Composition

Combining multiple modules into larger reusable systems.

Example:

```text id="la106"
VPC

Security Group

EC2

Load Balancer
```

working together.

---

## Module Testing

How engineers verify:

```text id="la107"
Module behaves correctly
```

before production use.

---

## Module Publishing

How custom modules are shared.

Possible destinations:

```text id="la108"
Private Registry

Terraform Registry

Git Repositories
```

---

## Documentation Standards

How professional modules provide:

```text id="la109"
Inputs

Outputs

Examples

Version Notes
```

---

## Version Upgrade Strategies

How teams safely move from:

```text id="la110"
Version A
```

to

```text id="la111"
Version B
```

without breaking infrastructure.

---

# Final Day 65 Transformation

Before Day 65:

```text id="la112"
Terraform User
```

After Day 65:

```text id="la113"
Terraform Module Builder
```

---

# One Sentence Future you Must Remember

```text id="la114"
Day 65 was the day Terraform stopped being a collection of resources and became a system of reusable infrastructure components.
```

***



# Appendix A – Complete  Handbook

---

# Terraform Modules

### What is a Terraform Module?

**Answer:**

A Terraform Module is a reusable collection of Terraform configuration files that groups resources together to reduce duplication and improve maintainability.

---

### Why do we use Modules?

**Answer:**

Modules help:

```text
Reduce code duplication

Improve reusability

Improve maintainability

Standardize infrastructure

Scale deployments easily
```

---

### What problem do Modules solve?

**Answer:**

Without modules, the same infrastructure code is repeatedly copied across environments.

Modules allow:

```text
Write Once

Reuse Many Times
```

---

### What is a Root Module?

**Answer:**

The Root Module is the main Terraform configuration directory where Terraform commands are executed.

Examples:

```bash
terraform init

terraform plan

terraform apply

terraform destroy
```

---

### What is a Child Module?

**Answer:**

A Child Module is a reusable module called by another module using a module block.

Example:

```hcl
module "web_server" {
 source = "./modules/ec2-instance"
}
```

---

### Difference Between Root Module and Child Module?

| Root Module             | Child Module             |
| ----------------------- | ------------------------ |
| Entry point             | Reusable component       |
| Runs Terraform commands | Called by another module |
| Environment specific    | Generic and reusable     |
| Coordinates modules     | Creates resources        |

---

# Variables & Outputs

---

### What are Variables?

**Answer:**

Variables are inputs passed into a module.

Example:

```hcl
instance_type
ami_id
instance_name
```

Variables make modules flexible and reusable.

---

### What are Outputs?

**Answer:**

Outputs expose values created by a module.

Example:

```hcl
instance_id

public_ip

sg_id
```

---

### Why are Outputs Important?

**Answer:**

Outputs allow:

```text
Sharing information

Module communication

Dependency creation
```

---

### How Do Modules Communicate?

**Answer:**

Modules communicate using Outputs.

Example:

```hcl
module.web_sg.sg_id
```

Security Group Module exposes:

```text
sg_id
```

which is consumed by the EC2 Module.

---

# Dynamic Blocks

---

### What is a Dynamic Block?

**Answer:**

Dynamic Blocks generate repeated nested configuration blocks from a collection.

Example:

```hcl
ingress_ports = [22,80,443]
```

Terraform automatically creates multiple ingress rules.

---

### Why Use Dynamic Blocks?

**Answer:**

Benefits:

```text
Less duplication

Cleaner code

Easier maintenance

More flexibility
```

---

### Dynamic Block vs for_each?

**Answer:**

Dynamic Block:

```text
Repeats nested blocks
inside a resource
```

Example:

```text
Ingress Rules
```

---

for_each:

```text
Repeats entire resources
```

Example:

```text
Multiple EC2 Instances
```

---

# Local Modules & Registry Modules

---

### What is a Local Module?

**Answer:**

A module created and maintained by the developer.

Example:

```hcl
source = "./modules/ec2-instance"
```

---

### What is a Registry Module?

**Answer:**

A module downloaded from Terraform Registry.

Example:

```hcl
source = "terraform-aws-modules/vpc/aws"
```

---

### Why Use Registry Modules?

**Answer:**

Benefits:

```text
Production Ready

Community Tested

Less Development Effort

Industry Standard
```

---

### Local Module vs Registry Module?

| Local Module      | Registry Module       |
| ----------------- | --------------------- |
| Written by you    | Written by community  |
| Full control      | Faster implementation |
| More maintenance  | Less maintenance      |
| Good for learning | Good for production   |

---

# Terraform Registry

---

### What is Terraform Registry?

**Answer:**

Terraform Registry is a repository of reusable Terraform modules maintained by HashiCorp, cloud providers, and the community.

---

### Terraform Registry Similar To?

```text
Python  → PyPI

NodeJS  → NPM

Docker  → Docker Hub

Terraform → Terraform Registry
```

---

# terraform init

---

### What does terraform init do?

**Answer:**

Terraform init:

```text
Initializes Terraform

Downloads Providers

Downloads Modules

Prepares Working Directory
```

---

### What does terraform init -upgrade do?

**Answer:**

Terraform checks for newer allowed versions and updates downloaded modules/providers.

---

### Where are downloaded modules stored?

**Answer:**

```text
.terraform/modules
```

---

# State & Dependency Graph

---

### What does terraform state list do?

**Answer:**

Displays all resources currently tracked by Terraform State.

---

### What important information does state contain?

**Answer:**

State stores:

```text
Resources

Ownership

Dependencies

Module Relationships
```

---

### What is Module Ownership?

**Answer:**

Terraform records which module created which resource.

Example:

```text
module.web_server.aws_instance.this
```

indicates ownership by:

```text
module.web_server
```

---

### What is a Dependency Graph?

**Answer:**

Terraform builds a graph of dependencies before creating resources.

Example:

```text
VPC
 ↓
Security Group
 ↓
EC2
```

---

### Does Terraform execute files line-by-line?

**Answer:**

No.

Terraform builds a dependency graph and executes resources according to dependencies.

---

### How Does Terraform Determine Dependencies?

**Answer:**

Using references.

Example:

```hcl
module.web_sg.sg_id
```

creates an implicit dependency.

---

# Versioning

---

### Why Pin Versions?

**Answer:**

To avoid unexpected behavior caused by newer releases.

---

### Exact Version?

```hcl
version = "5.1.0"
```

---

### Compatible Version?

```hcl
version = "~> 5.0"
```

---

### Version Range?

```hcl
version = ">=5.0,<6.0"
```

---

### Best Practice?

```text
Always pin versions.
```

---

# Real-World Questions

---

### When Should You Create A Module?

**Answer:**

Create a module when:

```text
Resource pattern
will be reused
3 or more times
```

---

### Why Not Put Everything In One Module?

**Answer:**

Large modules become:

```text
Hard To Reuse

Hard To Debug

Hard To Maintain
```

Use:

```text
Single Responsibility Principle
```

instead.

---

### What Was The Biggest Lesson Of Day 65?

**Answer:**

Terraform Modules transform infrastructure from:

```text
Resource Creation
```

into:

```text
Reusable Infrastructure Engineering
```

---

# Future you  Gold Answers

Memorize these one-liners:

```text
Module = Function For Infrastructure

Variables = Inputs

Outputs = Return Values

Modules Communicate Through Outputs

Dynamic Blocks = Loops Inside Resources

for_each = Loops Over Resources

Terraform Executes Dependency Graphs

Terraform State Stores Ownership

Registry Modules Save Engineering Effort

Infrastructure Should Be Reusable
```

***




# Appendix B – Commands Cheat Sheet

---

# Module Commands

---

## Initialize Terraform

```bash
terraform init
```

Purpose:

```text
Initialize Terraform

Download Providers

Download Modules

Prepare Working Directory
```

Memory:

```text
First command after cloning project
```

---

## Upgrade Downloaded Modules

```bash
terraform init -upgrade
```

Purpose:

```text
Check Registry

Download Newer Allowed Versions

Refresh Module Cache
```

Memory:

```text
Force Terraform to check for updates
```

---

# Validation Commands

---

## Validate Configuration

```bash
terraform validate
```

Purpose:

```text
Syntax Check

Configuration Check

Catch Errors Before Plan
```

Memory:

```text
Trust validate
more than editor warnings
```

---

## Format Terraform Files

```bash
terraform fmt
```

Purpose:

```text
Auto-format Terraform Code
```

Memory:

```text
Beautify Code
```

---

# Planning Commands

---

## Generate Execution Plan

```bash
terraform plan
```

Purpose:

```text
Preview Changes

No Resources Created
```

Memory:

```text
What Terraform WILL do
```

---

## Apply Changes

```bash
terraform apply
```

Purpose:

```text
Create Resources

Modify Resources

Delete Resources
```

Memory:

```text
Actually execute the plan
```

---

## Destroy Infrastructure

```bash
terraform destroy
```

Purpose:

```text
Remove Managed Resources
```

Memory:

```text
Delete Everything Terraform Created
```

---

# State Commands

---

## View State Resources

```bash
terraform state list
```

Purpose:

```text
Show Resources In State

Show Module Ownership
```

Example:

```text
module.vpc.*

module.web_server.*

module.web_sg.*
```

---

## Show Resource Details

```bash
terraform state show <resource>
```

Example:

```bash
terraform state show module.web_server.aws_instance.this
```

Purpose:

```text
Inspect Resource Stored In State
```

---

# Module Investigation Commands

---

## View Downloaded Modules

```bash
tree .terraform/modules
```

Purpose:

```text
See Downloaded Registry Module Files
```

---

## Explore Hidden Terraform Folder

Linux/Mac:

```bash
ls -la
```

Windows Git Bash:

```bash
ls -la
```

Purpose:

```text
Show Hidden Files

Show .terraform Folder
```

---

# Frequently Used Day 65 Commands

```bash
terraform fmt

terraform validate

terraform init

terraform init -upgrade

terraform plan

terraform apply

terraform destroy

terraform state list

tree .terraform/modules
```

---

# Appendix C – Common Mistakes & Troubleshooting Handbook

---

# Mistake 1

### Trusting VS Code More Than Terraform

Problem:

```text
VS Code shows warning
```

Immediate reaction:

```text
Code must be broken
```

Reality:

```text
Editor warning
```

Verification:

```bash
terraform validate
```

Lesson:

```text
Terraform Validate > Editor Warning
```

---

# Mistake 2

### Cannot Find Downloaded Module

Problem:

```text
Where did Terraform download module?
```

Cause:

```text
.terraform hidden folder
```

Solution:

```bash
ls -la
```

or

```bash
tree .terraform/modules
```

---

# Mistake 3

### terraform init Not Downloading Again

Problem:

```text
Module not downloading
```

Cause:

```text
Already cached locally
```

Solution:

```bash
terraform init -upgrade
```

---

# Mistake 4

### Confusing Region And Availability Zone

Wrong:

```text
us-west-2a
```

Correct Region:

```text
us-west-2
```

Availability Zone:

```text
us-west-2a
```

Memory:

```text
Region = City

AZ = Neighborhood
```

---

# Mistake 5

### Hardcoding Values

Bad:

```hcl
instance_type = "t2.micro"
```

inside module logic.

Better:

```hcl
instance_type = var.instance_type
```

Lesson:

```text
Reusable Modules Need Variables
```

---

# Mistake 6

### Creating Giant Modules

Bad:

```text
VPC

EC2

RDS

IAM

Security Group
```

inside one module.

Result:

```text
Hard To Reuse

Hard To Debug
```

Better:

```text
Separate Modules
```

---

# Mistake 7

### Forgetting Outputs

Problem:

```text
Module creates resource

Nobody can use it
```

Solution:

```hcl
output "sg_id"
```

Lesson:

```text
Outputs make modules useful
```

---

# Mistake 8

### Repeating Ingress Rules

Bad:

```hcl
ingress {}

ingress {}

ingress {}
```

Better:

```hcl
dynamic "ingress"
```

Lesson:

```text
Automate Repetition
```

---

# Future you Troubleshooting Checklist

Before Panicking:

```text
1. terraform fmt

2. terraform validate

3. terraform plan

4. Check Variables

5. Check Outputs

6. Check Dependencies

7. Check State

8. Check Hidden Files
```

---

# Appendix D – 1-Minute Revision Sheet

---

## Terraform Module Formula

```text
Variables
   ↓

Resources

   ↓

Outputs
```

---

## Root vs Child

```text
Root Module
=
Starting Point

Child Module
=
Reusable Component
```

---

## Module Communication

```text
Outputs

↓

Root Module

↓

Inputs

↓

Another Module
```

---

## Dynamic Block

```text
Loop Inside Resource
```

---

## for_each

```text
Loop Over Resources
```

---

## Registry Module

```text
Community Built Module
```

---

## Local Module

```text
Self Built Module
```

---

## terraform init

```text
Initialize

Download Providers

Download Modules
```

---

## terraform init -upgrade

```text
Refresh Downloads
```

---

## State

```text
Terraform Database
```

Stores:

```text
Resources

Ownership

Dependencies
```

---

## Dependency Graph

```text
Terraform Executes Graph

Not Files
```

---

## One-Liners To Remember

```text
Module = Function For Infrastructure

Variables = Inputs

Outputs = Return Values

Dynamic Blocks = Loops

Registry = Reusable Components

Terraform State = Infrastructure Database

Terraform Uses Dependency Graphs

Infrastructure Should Be Reusable
```

---

# Appendix E – Architecture Diagrams

---

## Module Structure

```text
Root Module
│
├── main.tf
├── variables.tf
├── outputs.tf
│
└── modules
     │
     ├── ec2-instance
     │    ├── main.tf
     │    ├── variables.tf
     │    └── outputs.tf
     │
     └── security-group
          ├── main.tf
          ├── variables.tf
          └── outputs.tf
```

---

## Module Communication

```text
Security Group Module
        │
        │ output sg_id
        ▼

Root Module
        │
        │ pass sg_id
        ▼

EC2 Module
```

---

## Infrastructure Architecture

```text
VPC
 │
 ▼

Security Group
 │
 ▼

EC2 Web Server

EC2 API Server
```

---

## Dependency Graph

```text
module.vpc
      │
      ▼

module.web_sg
      │
      ▼

module.web_server

module.api_server
```

---

## Day 65 Mental Model Diagram

```text
Recipe Card
      =
Terraform Module

Order Form
      =
Variables

Receipt
      =
Outputs

Assistant
      =
Dynamic Block

Professional Builder
      =
Registry Module

Bakery Office
      =
Root Module

Recipe Card
      =
Child Module
```

***



## 1. terraform get

Original Hint:

```bash
terraform get
```

Downloads modules without full initialization.

I don't think we captured this.

Add:

```text
terraform get

Purpose:
Download modules only

Difference:

terraform init
=
Providers + Modules + Backend

terraform get
=
Modules Only
```

---

## 2. Local Module Source Format

Original Hint:

```hcl
source = "./modules/ec2-instance"
```

We mentioned it several times.

✅ Covered

---

## 3. Registry Module Source Format

Original Hint:

```hcl
source = "terraform-aws-modules/vpc/aws"
```

We covered it.

✅ Covered

---

## 4. Dynamic Block Internal Syntax

Task specifically mentioned:

```hcl
dynamic "ingress" {

  for_each = var.ingress_ports

  content {

  }

}
```

We explained the concept but not the internal structure.

Missing detail:

```text
dynamic block structure

dynamic "ingress"
       ↓

for_each
       ↓

content {}
```

---

## 5. Registry Module Inputs & Outputs Documentation

Original Hint:

```text
Registry modules document
all inputs and outputs
on registry.terraform.io
```

Not explicitly captured.

Add:

```text
Before using a Registry Module:

Read Inputs

Read Outputs

Read Examples

Read Version Notes
```

---

## 6. README.md Best Practice

Original task:

```text
Add README.md to every custom module
```

Mentioned once.

Could be expanded.

Example:

```text
README should contain:

Purpose

Inputs

Outputs

Examples

Version Notes
```

---

## 7. Hand-Written VPC vs Registry VPC Resource Count

Original challenge asked:

```text
Compare:
How many resources were created?
```

We explained conceptually but not explicitly.

Missing lesson:

```text
Hand Written VPC

Usually:
VPC
Subnet
Route Table
IGW
Association

Registry VPC

Often creates:
VPC
Default ACL
Default Route Table
Default SG
Subnets
Routes
Associations
IGW

and more

Result:
Much larger infrastructure footprint
```

---

# One Very Important Missing  Question

### What is the difference between:

```bash
terraform init
```

and

```bash
terraform get
```

Answer:

| terraform init             | terraform get |
| -------------------------- | ------------- |
| Downloads providers        | ❌             |
| Downloads modules          | ✅             |
| Initializes backend        | ✅             |
| Prepares working directory | ✅             |
| Modules only               | ❌             |
| Full initialization        | ✅             |

***




# Terraform Module Best Practices (Day 65)

## 1. Single Responsibility Principle

A module should do one job only.

❌ Bad

```text
networking-module

Creates:
VPC
EC2
RDS
Security Group
IAM
```

✅ Good

```text
modules/

ec2-instance/
security-group/
vpc/
```

Each module should have one responsibility.

---

## 2. Make Modules Reusable

Avoid hardcoded values.

❌ Bad

```hcl
instance_type = "t2.micro"
```

✅ Good

```hcl
instance_type = var.instance_type
```

Same module can be used for:

```text
Dev
QA
UAT
Prod
```

without changing code.

---

## 3. Use Variables For Everything

Modules should accept inputs through variables.

Example:

```hcl
variable "ami_id" {}
variable "instance_type" {}
variable "subnet_id" {}
```

Memory:

```text
Variables
=
Module Inputs
```

---

## 4. Always Define Outputs

Modules should expose useful values.

Example:

```hcl
output "instance_id"
output "public_ip"
output "sg_id"
```

Why?

Because other modules need them.

Example:

```hcl
module.web_sg.sg_id
```

---

## 5. Modules Communicate Through Outputs

```text
Security Group Module
         ↓
      sg_id
         ↓
      Output
         ↓
      Root Module
         ↓
      EC2 Module
```

Never hardcode IDs.

---

## 6. Keep Provider Configuration In Root Module

✅ Correct

Root Module:

```hcl
provider "aws" {
  region = "ap-south-1"
}
```

Child Module:

```text
No Provider Block
```

Why?

Makes modules portable and reusable.

---

## 7. Follow Standard Module Structure

Small Module:

```text
module-name/

├── main.tf
├── variables.tf
├── outputs.tf
```

Production Module:

```text
module-name/

├── main.tf
├── variables.tf
├── outputs.tf
├── versions.tf
├── README.md
```

Industry standard.

---

## 8. Root Module vs Child Module Structure

```text
terraform-modules/

├── main.tf
├── variables.tf
├── outputs.tf
├── providers.tf
│
└── modules/
    │
    ├── ec2-instance/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    │
    └── security-group/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

Memory:

```text
Root Module
controls infrastructure

Child Modules
contain reusable logic
```

---

## 9. Use Meaningful Variable Names

❌ Bad

```hcl
variable "x"
variable "y"
```

✅ Good

```hcl
variable "instance_type"
variable "security_group_ids"
variable "subnet_id"
```

Improves readability and maintenance.

---

## 10. Use Dynamic Blocks For Repeated Nested Blocks

Instead of:

```hcl
ingress {}
ingress {}
ingress {}
```

Use:

```hcl
dynamic "ingress" {
  for_each = var.ingress_ports

  content {
    ...
  }
}
```

Memory:

```text
Dynamic Block
=
Loop Inside Resource
```

---

## 11. Dynamic Block vs for_each

```text
dynamic
=
Repeat nested blocks
inside a resource
```

Example:

```text
Multiple ingress rules
inside one Security Group
```

---

```text
for_each
=
Repeat entire resources
```

Example:

```text
Multiple EC2 instances
```

---

## 12. Keep Modules Small

❌ Bad

```text
3000-line module
```

✅ Good

```text
Small EC2 Module

Small SG Module

Small VPC Module
```

Benefits:

```text
Easy to understand

Easy to debug

Easy to reuse
```

---

## 13. Use Local Modules For Learning

Example:

```hcl
source = "./modules/ec2-instance"
```

Best for:

```text
Learning

Customization

Understanding Terraform
```

---

## 14. Use Registry Modules For Production

Example:

```hcl
source = "terraform-aws-modules/vpc/aws"
```

Benefits:

```text
Community Tested

Production Ready

Less Code

Faster Delivery
```

---

## 15. Why Registry Modules Are Powerful

Hand-written VPC:

```text
Few resources
```

Registry VPC Module:

```text
Many production-ready resources
```

Memory:

```text
Registry Module
=
Years of engineering knowledge
packaged into reusable code
```

---

## 16. Always Pin Registry Module Versions

❌ Bad

```hcl
source = "terraform-aws-modules/vpc/aws"
```

without version.

✅ Good

```hcl
version = "~> 5.0"
```

or

```hcl
version = "5.1.0"
```

Why?

```text
Avoid unexpected changes
```

---

## 17. Read Module Documentation Before Use

Before using a registry module, review:

```text
Inputs

Outputs

Examples

Version Notes
```

---

## 18. Add README.md To Every Module

Document:

```text
Purpose

Inputs

Outputs

Usage Example
```

Example:

```text
EC2 Module

Inputs:
ami_id
instance_type
subnet_id

Outputs:
instance_id
public_ip
private_ip
```

---

## 19. Use Tags Consistently

Example:

```hcl
tags = {
  Environment = "Dev"
  Project     = "TerraWeek"
  ManagedBy   = "Terraform"
}
```

Benefits:

```text
Cost Tracking

Resource Identification

Governance
```

---

## 20. Never Hardcode Environment-Specific Values

❌ Bad

```hcl
name = "prod-web-server"
```

✅ Good

```hcl
name = "${var.environment}-web-server"
```

Reusable across environments.

---

## 21. merge() Function

Example:

```hcl
tags = merge(
  {
    Name = var.instance_name
  },
  var.tags
)
```

Purpose:

```text
Combine Name tag
with additional tags
```

Memory:

```text
merge()
=
Join multiple maps
into one map
```

---

## 22. terraform init

```bash
terraform init
```

Purpose:

```text
Initialize project

Download providers

Download modules
```

---

## 23. terraform init -upgrade

```bash
terraform init -upgrade
```

Purpose:

```text
Check newer allowed versions

Update downloaded modules/providers

Refresh local cache
```

Memory:

```text
init
=
Download if missing

init -upgrade
=
Check for newer versions
```

---

## 24. terraform get

```bash
terraform get
```

Purpose:

```text
Download modules only
```

Difference:

```text
terraform init
=
Providers + Modules

terraform get
=
Modules only
```

---

## 25. Verify Module Ownership In State

Command:

```bash
terraform state list
```

Example:

```text
module.vpc.*

module.web_server.*

module.web_sg.*
```

Shows:

```text
Terraform ownership

Which module created which resource
```

Memory:

```text
State
=
Infrastructure Database

+
Ownership Tracking
```

---

## 26. Terraform Executes Dependency Graphs

Terraform does NOT execute files top-to-bottom.

Terraform builds:

```text
VPC
 ↓
Security Group
 ↓
EC2
```

using dependencies.

Memory:

```text
Terraform executes
Dependency Graphs

NOT files
```

---

# Five Core Module Best Practices

```text
Always pin registry module versions

Keep modules focused

Use variables instead of hardcoding

Always define outputs

Add README.md to every module
```

---

# Day 65 Answer

**What are Terraform Module Best Practices?**

> Keep modules focused on a single responsibility, make them reusable through variables, expose useful outputs, avoid hardcoded values, use dynamic blocks for repeated configuration, follow standard module structure, keep providers in the root module, pin registry module versions, document modules with README files, and use outputs for module communication. These practices make Terraform code reusable, maintainable, and scalable.

---

# Final Revision Summary

```text
Terraform Modules are reusable infrastructure functions.

Variables = Inputs
Resources = Work
Outputs = Return Values

Root Module calls Child Modules.

Modules communicate through Outputs.

Dynamic Blocks are loops inside resources.

Terraform executes Dependency Graphs, not files.

Local Modules come from my code.
Registry Modules come from Terraform Registry.

State stores resources, ownership, and dependencies.

Modules help me avoid copy-pasting infrastructure.
```




***


### Comparison: hand-written VPC vs registry VPC module (resources created)

The reason you're struggling is because everyone writes:

```text id="l2sj4s"
Hand-written VPC = 6 resources
Registry Module = 17 resources
```

but nobody explains **WHY those resources exist**.

So let's rebuild Day 62 from scratch mentally.

---

# Imagine You Want To Build A Small Housing Society

You buy land.

That's your:

```text id="m4u62s"
VPC
```

---

Inside the society, you create roads.

That's your:

```text id="4x3hqx"
Subnet
```

---

You create a main gate.

That's your:

```text id="r6k4r8"
Internet Gateway
```

---

You create sign boards telling cars where to go.

That's your:

```text id="d5y5m2"
Route Table
```

---

You attach sign boards to roads.

That's:

```text id="1zv8bg"
Route Table Association
```

---

You hire security guards.

That's:

```text id="95df1g"
Security Group
```

---

# Day 62 Hand-Written VPC

You manually built:

```text id="o1o3u6"
Land (VPC)
Road (Subnet)
Main Gate (IGW)
Sign Board (Route Table)
Attach Sign Board (Association)
Security Guard (SG)
```

ASCII:

```text id="pjfjc5"
Internet
   |
   |
[IGW]
   |
[VPC]
   |
[Subnet]
   |
[EC2]
```

Simple.

Small.

Learning purpose only.

---

# Why Registry Module Created More Resources

Now imagine a real housing society.

Would it have:

```text id="vnd94r"
One road?
```

No.

---

Usually:

```text id="gmpg7e"
Public Road A
Public Road B

Private Road A
Private Road B
```

---

That's why module created:

```text id="rq1vly"
2 Public Subnets
2 Private Subnets
```

---

ASCII:

```text id="fjsdy8"
              VPC
               |
      -------------------
      |                 |
 Public A          Public B

 Private A         Private B
```

---

# Why Two Availability Zones?

AWS has data centers.

Imagine:

```text id="6lfm0k"
Building A
Building B
```

If Building A burns down:

```text id="vmu53u"
Everything dies
```

Bad.

---

So AWS says:

```text id="h25u35"
Put resources in multiple zones
```

---

Your module did:

```hcl id="vg0p6g"
azs = [
 "us-west-2a",
 "us-west-2b"
]
```

Meaning:

```text id="tjjikj"
Building A
Building B
```

---

That's why resources doubled.

---

# Why Default Resources Exist

When AWS creates a VPC:

AWS secretly creates:

```text id="u8pgci"
Default Network ACL
Default Route Table
Default Security Group
```

even if you don't ask.

---

Registry module manages them.

Your Day 62 code ignored them.

---

Think:

Buying apartment.

Builder automatically gives:

```text id="tnjlwm"
Main entrance rules
Basic security
Basic parking rules
```

---

Registry module says:

```text id="jlwm6n"
I will manage these too
```

---

# Why More Route Tables?

Day 62:

```text id="0w6r88"
One subnet
One route table
```

Easy.

---

Registry:

```text id="0ivfcn"
Public Subnets
Private Subnets
```

need different routing.

---

Public subnet:

```text id="gngf3v"
Can access internet
```

---

Private subnet:

```text id="mgcuj2"
Cannot directly access internet
```

---

Therefore:

```text id="0l7z7z"
More route tables
```

---

# Why More Associations?

Each subnet needs attachment.

Example:

```text id="d7w3n5"
Subnet A -> Route Table A

Subnet B -> Route Table B

Subnet C -> Route Table C
```

Each attachment is:

```text id="1w6n7n"
Association
```

---

# Real Comparison

## Day 62

You basically built:

```text id="aj3a0i"
Small House
```

ASCII:

```text id="uwzdaz"
Internet
   |
  IGW
   |
  VPC
   |
Subnet
   |
 EC2
```

Resources:

```text id="ktjlwm"
VPC
Subnet
IGW
Route Table
Association
SG

≈ 6
```

---

## Registry Module

You built:

```text id="wz1a1i"
Apartment Complex
```

ASCII:

```text id="pp8k67"
                 Internet
                     |
                    IGW
                     |
                    VPC
                     |
     --------------------------------
     |              |              |
 Public-A      Public-B      Private-A
                               |
                           Private-B
```

Plus:

```text id="5hrqeo"
Default ACL
Default Route Table
Default SG
Routes
Associations
```

Resources:

```text id="qndjlwm"
≈ 17
```

---

# What Should be Remembered

Don't memorize:

```text id="nho7gm"
6 resources
17 resources
```

You'll forget.

Remember this:

```text id="3a4l4s"
Day 62:
I built a small house manually.

Day 65:
I hired a professional builder
(Terraform Registry Module).

The professional builder created
many extra things automatically
that real production environments need.
```

That is the actual lesson.

The number 17 is not important.

The reason **why it became 17** is the important part. 













***

# Day 65 – Terraform Modules  - Revision NOTES

## Why Day 65 Matters

Before Day 65, I knew how to create AWS resources.

After Day 65, I learned how engineers build reusable infrastructure at scale.

This was the day Terraform started feeling like software engineering rather than resource creation.

---

# The Problem Modules Solve

Suppose I need:

```text
Web Server
API Server
Database Server
```

Without modules:

```hcl
resource "aws_instance" "web" {}
resource "aws_instance" "api" {}
resource "aws_instance" "db" {}
```

Now multiply this across:

```text
Dev
QA
Stage
Prod
```

Problems:

```text
Code Duplication
Copy-Paste Errors
Hard Maintenance
Difficult Scaling
```

Modules solve this problem.

---

# Terraform Module = Function for Infrastructure

Think:

```text
Input
 ↓
Module
 ↓
Output
```

Just like a programming function.

Python:

```python
def create_server(name):
    return server
```

Terraform:

```text
Variables
 ↓
Resources
 ↓
Outputs
```

---

# Bakery Story (Mental Model)

Terraform Module = Recipe Card

```text
Recipe Card
↓
Reusable
↓
Used Many Times
```

Variables = Order Form

```text
Customer Name
Cake Flavor
```

Outputs = Receipt

```text
Order Number
```

Dynamic Block = Assistant Repeating Work

Registry Module = Professional Builder

Root Module = Bakery Office

Child Module = Recipe Card

---

# What Is A Terraform Module?

A module is simply:

```text
A folder containing Terraform files
```

Example:

```text
ec2-instance/
├── main.tf
├── variables.tf
└── outputs.tf
```

Terraform treats this folder as a reusable component.

---

# Module Anatomy

```text
Module
│
├── variables.tf
│     Inputs
│
├── main.tf
│     Resources
│
└── outputs.tf
      Returned Values
```

---

# Root Module vs Child Module

## Root Module

Where Terraform starts execution.

Commands run here:

```bash
terraform init
terraform plan
terraform apply
terraform destroy
```

Example:

```text
terraform-modules/
```

---

## Child Module

Reusable module called by another module.

Examples:

```text
modules/ec2-instance
modules/security-group
```

Child modules are not executed directly.

---

# Custom Module 1 – EC2 Module

Folder:

```text
modules/ec2-instance
```

Purpose:

```text
Create reusable EC2 instances
```

Inputs:

```text
AMI ID
Instance Type
Subnet ID
Security Group IDs
Instance Name
Tags
```

Outputs:

```text
Instance ID
Public IP
Private IP
```

---

# Merge Function

Used:

```hcl
merge(
  var.tags,
  {
    Name = var.instance_name
  }
)
```

Purpose:

Combine custom tags with Name tag.

Without merge:

```text
Some tags may be overwritten
```

With merge:

```text
All tags survive
```

Example:

```text
Name
Project
Owner
Environment
Day
```

---

# Custom Module 2 – Security Group Module

Folder:

```text
modules/security-group
```

Purpose:

```text
Reusable Security Groups
```

Inputs:

```text
VPC ID
Security Group Name
Ingress Ports
Tags
```

Output:

```text
sg_id
```

---

# Module Communication

Security Group Module creates:

```text
sg_id
```

EC2 Module needs:

```text
security_group_ids
```

Connection:

```hcl
module.web_sg.sg_id
```

This is how modules communicate.

Important:

```text
Outputs are how modules share information.
```

Without outputs:

```text
Modules become isolated.
```

---

# Dynamic Blocks

Without Dynamic Block:

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

Repetitive.

---

With Dynamic Block:

```hcl
ingress_ports = [22,80,443]
```

Terraform automatically generates:

```text
SSH
HTTP
HTTPS
```

rules.

Mental model:

```python
for port in ports:
    create_rule(port)
```

---

# Dynamic Block vs for_each

Important  Concept

### dynamic

Repeats nested blocks.

Example:

```text
Ingress Rules
```

inside one Security Group.

---

### for_each

Repeats entire resources.

Example:

```text
EC2
EC2
EC2
```

multiple resources.

---

# Infrastructure Built On Day 65

Created:

```text
1 Security Group

2 EC2 Instances

terraweek-web
terraweek-api
```

Both instances used:

```text
Same EC2 Module
Same Security Group Module
```

Different names only.

Proof that modules are reusable.

---

# Local Modules

Modules written by me.

Example:

```hcl
source = "./modules/ec2-instance"
```

Meaning:

```text
Use my code
```

---

# Registry Modules

Modules written and maintained by others.

Example:

```hcl
source = "terraform-aws-modules/vpc/aws"
```

Meaning:

```text
Use community-tested code
```

---

# What Is Terraform Registry?

Think:

```text
Python  → PyPI
NodeJS  → NPM
Docker  → Docker Hub
Terraform → Terraform Registry
```

Repository of reusable Terraform modules.

---

# Why Replace Hand-Written VPC?

Hand-written:

```text
VPC
Subnet
Internet Gateway
Route Table
Association
Security Group
```

Approximately:

```text
6 Resources
```

---

Registry VPC Module:

```text
VPC
Default ACL
Default Route Table
Default Security Group
Internet Gateway
2 Public Subnets
2 Private Subnets
Route Tables
Associations
Routes
```

Approximately:

```text
17 Resources
```

More functionality.

Less code.

---

# terraform init and Module Downloads

When adding a registry module:

```bash
terraform init
```

Terraform downloads module source code.

To refresh versions:

```bash
terraform init -upgrade
```

---

# Where Are Downloaded Modules Stored?

Location:

```text
.terraform/modules/
```

Verified:

```bash
tree .terraform/modules/vpc
```

Contents:

```text
main.tf
variables.tf
outputs.tf
README.md
examples
```

Terraform downloads the actual source code.

---

# Terraform State Discovery

Command:

```bash
terraform state list
```

Output:

```text
module.vpc.*
module.web_server.*
module.api_server.*
module.web_sg.*
```

Important lesson:

Terraform tracks:

```text
Who created what
```

and records module ownership.

---

# Dependency Graph (Very Important)

Terraform does NOT think:

```text
Create VPC
Create SG
Create EC2
```

Terraform builds a graph:

```text
VPC
 ↓
Security Group
 ↓
EC2
```

Because:

```hcl
module.web_sg.sg_id
```

creates an implicit dependency.

Terraform automatically creates resources in the correct order.

---

# Version Pinning

Exact Version:

```hcl
version = "5.1.0"
```

Compatible:

```hcl
version = "~> 5.0"
```

Range:

```hcl
version = ">=5.0,<6.0"
```

Best Practice:

```text
Always pin versions.
Never rely on latest.
```

---

# Real-World Module Structure

```text
modules/
├── vpc
├── ec2
├── security-group
├── rds
├── eks
├── iam
└── s3
```

Used across:

```text
Dev
QA
Stage
Prod
```

without rewriting infrastructure.

---

# When Should I Create A Module?

Rule:

```text
Will I copy-paste this resource
3 or more times?
```

YES →

```text
Create Module
```

NO →

```text
Keep Resource Simple
```

---

# Biggest Confusion Of The Day

### Error

```text
Unexpected attribute:
sg_name is not expected here
```

Terraform validation:

```bash
terraform validate
```

Output:

```text
Success! The configuration is valid.
```

Problem:

```text
VS Code warning
```

not Terraform.

Lesson:

Always trust:

```bash
terraform validate
terraform plan
```

over editor warnings.

---

# Other Troubleshooting Lessons

### Hidden .terraform Folder

Problem:

```text
Could not see downloaded module
```

Reason:

```text
.terraform is hidden
```

---

### Registry Download Confusion

Problem:

```text
Didn't see module download again
```

Reason:

```text
Already cached locally
```

Solution:

```bash
terraform init -upgrade
```

---

### AWS Region Mistake

Wrong:

```text
us-west-2a
```

Correct:

```text
us-west-2
```

---

#  Questions

### What is a Terraform Module?

A reusable collection of Terraform configuration files used to reduce duplication and standardize infrastructure deployments.

---

### Difference Between Root and Child Module?

Root Module:

```text
Where Terraform commands are executed.
```

Child Module:

```text
Reusable module called by another module.
```

---

### Why Use Outputs?

Outputs expose values so other modules can consume them.

---

### How Do Modules Communicate?

Using outputs.

Example:

```hcl
module.web_sg.sg_id
```

---

### What Are Dynamic Blocks?

Dynamic blocks generate repeated nested configuration blocks from a collection.

---

### Why Use Registry Modules?

Reusable, production-tested, community-maintained infrastructure components.

---

### Difference Between Local and Registry Modules?

Local:

```hcl
source = "./modules/ec2-instance"
```

Registry:

```hcl
source = "terraform-aws-modules/vpc/aws"
```

---

### Where Are Downloaded Modules Stored?

```text
.terraform/modules/
```

---

# Five Things Future Must Never Forget

1. Modules are functions for infrastructure.

2. Variables are inputs.

3. Outputs are return values.

4. Registry modules save huge amounts of effort.

5. Terraform Modules make Infrastructure as Code scalable.

---

# One-Line Summary

```text
Day 65 was the day I stopped creating AWS resources individually and started building reusable infrastructure components using Terraform Modules.
```
