# Part 1: Ansible Fundamentals

---

# Before Learning Ansible

Let's first understand the problem Ansible is trying to solve.

Most beginners learn:

```bash
ansible all -m ping
```

without understanding:

> Why was Ansible invented in the first place?

If you understand the problem, Ansible becomes very easy.

---

# Chapter 1: The Village Story (Configuration Management)

Imagine you are the principal of a school.

You have:

```text
Class A
Class B
Class C
Class D
Class E
```

One day you decide:

```text
Every classroom should have:

✓ Whiteboard
✓ Projector
✓ 20 Chairs
✓ Teacher Desk
```

You have two options.

---

## Option 1: Manual Work

You visit every classroom.

```text
Go to Class A
Install projector

Go to Class B
Install projector

Go to Class C
Install projector

Go to Class D
Install projector

Go to Class E
Install projector
```

Problems:

```text
Time consuming
Tiring
Easy to forget something
Difficult to repeat
```

---

## Option 2: Give Instructions Once

You write:

```text
Every classroom must have:

20 Chairs
1 Whiteboard
1 Projector
1 Teacher Desk
```

Then workers follow the instructions.

Now every classroom looks identical.

---

# This Is Configuration Management

Configuration Management means:

```text
Define how systems should look

and

Automatically make them look that way
```

---

# Real Server Example

Instead of classrooms we have servers.

Example:

```text
Web Server 1
Web Server 2
Web Server 3
Web Server 4
```

All servers should have:

```text
Nginx installed
Git installed
Port 80 open
Same users
Same configuration
```

---

Without Configuration Management:

```bash
ssh server1
install nginx

ssh server2
install nginx

ssh server3
install nginx

ssh server4
install nginx
```

---

Imagine:

```text
100 Servers
500 Servers
1000 Servers
```

Impossible to manage manually.

---

# Configuration Management Definition

Configuration Management is:

```text
The process of defining,
maintaining,
and enforcing

the desired state

of systems automatically.
```

---

# Most Important Term

## Desired State

This term appears everywhere.

Think:

```text
Current State
vs
Desired State
```

Example:

Current State

```text
Nginx = Not Installed
```

Desired State

```text
Nginx = Installed
```

Ansible's job:

```text
Make Current State
match
Desired State
```

---

# Restaurant Analogy

Imagine ordering pizza.

You tell the restaurant:

```text
I want:

Large Pizza
Extra Cheese
No Onion
```

You don't care HOW they make it.

You only care about:

```text
Final Result
```

That is:

```text
Desired State
```

Ansible works the same way.

You say:

```yaml
Install nginx
```

Ansible figures out:

```text
How to install it
```

---

# Why Configuration Management Is Needed

---

## 1. Consistency

Without automation:

```text
Server A -> nginx installed
Server B -> nginx missing
Server C -> wrong version
```

Chaos.

---

With Configuration Management:

```text
Server A -> same
Server B -> same
Server C -> same
```

Everything identical.

---

## 2. Automation

Humans get tired.

Automation doesn't.

---

Manual:

```text
Repeat 500 times
```

Automation:

```text
Run once
```

---

## 3. Scalability

Managing:

```text
1 server -> easy
10 servers -> okay
100 servers -> difficult
1000 servers -> impossible manually
```

Configuration Management solves this.

---

## 4. Reduced Human Error

Humans forget.

Example:

Server 1

```text
Installed nginx
```

Server 2

```text
Forgot nginx
```

Production issue begins.

---

Automation never forgets.

---

## 5. Version Control

All configurations stored in Git.

Example:

```text
Yesterday:
nginx 1.25

Today:
nginx 1.26
```

Git shows exactly:

```text
Who changed it
When
Why
```

---

## 6. Faster Recovery

Suppose a server dies.

Without automation:

```text
Rebuild manually
```

Hours of work.

---

With automation:

```text
Launch new server

Run Ansible

Done
```

Minutes.

---

# Memory Trick

Remember:

```text
Consistency
Automation
Scalability
Reduced Errors
Version Control
Recovery
```

Mnemonic:

```text
CASRVR

(Can A Smart Robot Verify Recovery?)
```

---

# Chapter 2: What Is Ansible?

Definition:

```text
Ansible is a Configuration Management
and Automation Tool.
```

It helps automate:

```text
Server Configuration
Software Installation
User Management
Application Deployment
Cloud Provisioning
```

---

# Super Simple Definition

Think:

```text
Ansible = Remote Control
for Servers
```

Instead of controlling:

```text
TV
```

you control:

```text
100 Servers
```

from one place.

---

# Real Example

Without Ansible:

```bash
ssh server1
sudo dnf install git

ssh server2
sudo dnf install git

ssh server3
sudo dnf install git
```

---

With Ansible:

```bash
ansible all -m dnf -a "name=git state=present" --become
```

One command.

All servers.

---

ASCII View

```text
You

 |
 v

Ansible

 |
 |------------ Server 1
 |
 |------------ Server 2
 |
 |------------ Server 3
 |
 |------------ Server 4
```

---

# Chapter 3: Ansible vs Chef vs Puppet vs Salt

Before Ansible existed, other tools existed.

---

# School Analogy

Imagine managing students.

---

## Puppet

Every student carries a notebook.

Teacher writes instructions.

Students periodically check notebook.

```text
Teacher -> Notebook

Student reads later
```

This is:

```text
Pull Model
```

---

## Chef

Same concept.

Students periodically ask:

```text
Any new instructions?
```

---

## Salt

Students may:

```text
Ask Teacher
OR
Teacher tells them
```

Hybrid.

---

## Ansible

Teacher directly speaks.

```text
Do this now
```

Students immediately act.

This is:

```text
Push Model
```

---

# Comparison Table

| Feature       | Ansible | Puppet | Chef | Salt        |
| ------------- | ------- | ------ | ---- | ----------- |
| Agent Needed  | No      | Yes    | Yes  | Usually     |
| Easy Learning | High    | Medium | Hard |             |
| Uses SSH      | Yes     | No     | No   | Sometimes   |
| Setup         | Easy    | Medium | Hard |             |
| Language      | YAML    | DSL    | Ruby | YAML/Python |
| Model         | Push    | Pull   | Pull | Hybrid      |

---

# Interview Answer

Why is Ansible popular?

Because:

```text
No agents
Easy setup
YAML
SSH based
Fast adoption
Cloud friendly
```

---

# Chapter 4: Agentless Architecture

This is THE MOST IMPORTANT concept.

Interviewers love asking it.

---

# What Is An Agent?

Think:

```text
Security Guard
```

living permanently inside a building.

Many tools install:

```text
Agent
```

on every server.

Example:

```text
Server 1 -> Agent
Server 2 -> Agent
Server 3 -> Agent
Server 4 -> Agent
```

---

Those agents:

```text
Run continuously
Consume RAM
Consume CPU
Need updates
Need maintenance
```

---

# Ansible Says

```text
I don't want guards everywhere.
```

Instead:

```text
I'll visit when needed.
```

---

This is:

```text
Agentless Architecture
```

---

# How Agentless Works

When you run:

```bash
ansible all -m ping
```

Ansible:

```text
1. Connects using SSH

2. Copies module temporarily

3. Executes task

4. Collects result

5. Removes temporary files
```

---

Visual

```text
Control Node

     |
     | SSH
     |
     v

Managed Node

     |
     | Temporary Module
     |
     v

Execute

     |
     v

Return Result
```

---

# Why Agentless Is Amazing

---

## Simpler Setup

No installation everywhere.

---

## Lower Resource Usage

No background process.

---

## Easier Maintenance

Nothing to patch.

---

## Better Security

Less software running.

Less attack surface.

---

# Memory Trick

Agentless means:

```text
Visit
Do Work
Leave
```

Like a plumber.

Not a permanent resident.

---

# Part 1 Revision Sheet

## Configuration Management

```text
Define desired state

Automatically enforce it
```

---

## Desired State

```text
How systems SHOULD look
```

---

## Why Needed

```text
Consistency
Automation
Scalability
Reduced Errors
Version Control
Recovery
```

---

## What Is Ansible

```text
Automation Tool

Remote Control For Servers
```

---

## Why Popular

```text
Easy
Agentless
SSH
YAML
Fast Setup
```

---

## Agentless

```text
No software installed permanently
on managed nodes
```

---

## Ansible Workflow

```text
Connect
Execute
Return Result
Disconnect
```

***


# Part 2: Ansible Core Components & Architecture (Master Notes Version)

## Teach Me Like I'm 10 + Deep Understanding + Interview Ready

---

# Before Learning Components

Most people memorize:

```text
Control Node
Managed Node
Inventory
Modules
Playbooks
```

but don't understand:

```text
How they work together.
```

Let's fix that.

---

# The School Principal Analogy

Imagine:

You are the principal of a school.

You want to tell teachers:

```text
Install Projector
Arrange Chairs
Paint Classroom
```

You don't run to every classroom yourself.

Instead you use:

```text
Office
Teacher List
Instructions
Workers
Classrooms
```

---

Ansible works exactly like this.

| School       | Ansible         |
| ------------ | --------------- |
| Principal    | DevOps Engineer |
| Office       | Control Node    |
| Teacher List | Inventory       |
| Instructions | Playbook        |
| Workers      | Modules         |
| Classrooms   | Managed Nodes   |

---

# Entire Ansible Architecture

```text
                     YOU
                      |
                      v

            +----------------+
            | Control Node   |
            | (Ansible)      |
            +----------------+
                    |
                    |
          Reads Inventory
                    |
                    v

            +----------------+
            | Inventory File |
            +----------------+
                    |
                    |
          Finds Target Hosts
                    |
                    v

            +----------------+
            | Playbook       |
            +----------------+
                    |
                    |
          Selects Modules
                    |
                    v

            +----------------+
            | Modules        |
            +----------------+
                    |
                    |
                 SSH
                    |
      --------------------------------
      |              |              |
      v              v              v

+-----------+  +-----------+  +-----------+
| Web Node  |  | App Node  |  | DB Node   |
+-----------+  +-----------+  +-----------+

```

---

# Memory Formula

Always remember:

```text
Control Node

     ↓

Inventory

     ↓

Playbook

     ↓

Module

     ↓

Managed Node
```

---

# This Is The Entire Ansible Flow

Everything in Ansible follows this flow.

---

# Chapter 1: Control Node

---

# What Is A Control Node?

Control Node is:

```text
The machine where
Ansible is installed
and executed.
```

Simple definition:

```text
Control Node = Brain
```

---

# Human Body Analogy

```text
Brain = Control Node

Hands = Managed Nodes
```

Brain sends commands.

Hands perform work.

---

Without Brain:

```text
Nothing happens
```

Without Control Node:

```text
Ansible cannot run
```

---

# In Your Lab

Control Node was:

```text
malathi@Deepak

Ubuntu WSL
```

Because:

```bash
ansible --version
```

was executed there.

---

# Responsibilities Of Control Node

---

## 1. Stores Inventory

Example:

```ini
[web]
web-server

[app]
app-server
```

---

## 2. Stores Playbooks

Example:

```yaml
install-nginx.yml
```

---

## 3. Runs Ansible Commands

Example:

```bash
ansible all -m ping
```

---

## 4. Connects Using SSH

```text
Control Node
     |
     |
    SSH
     |
     v
Managed Node
```

---

## 5. Executes Modules

Example:

```text
ping
copy
dnf
service
user
```

---

# Interview Question

### Can there be multiple Control Nodes?

Answer:

```text
Yes.

Large organizations often use multiple
control nodes for redundancy and scale.
```

---

# Memory Trick

Control Node = School Principal Office

Everything starts here.

---

# Chapter 2: Managed Nodes

---

# What Are Managed Nodes?

Managed Nodes are:

```text
Servers managed by Ansible.
```

---

Examples:

```text
EC2 Instances
Virtual Machines
Physical Servers
Cloud Servers
```

---

# In Your Lab

Managed Nodes:

```text
web-server

app-server

db-server
```

---

# Restaurant Analogy

Imagine:

```text
Manager = Control Node

Waiters = Managed Nodes
```

Manager gives instructions.

Waiters perform work.

---

# Requirements For Managed Nodes

---

## Requirement 1

SSH Access

Ansible needs:

```text
Port 22
```

open.

---

Without SSH:

```text
No connection
```

---

## Requirement 2

Valid User

Example:

```text
ec2-user
ubuntu
centos
```

---

Wrong user:

```text
Permission Denied
```

---

## Requirement 3

Python

Ansible modules need Python.

Usually:

```text
/usr/bin/python3
```

exists already.

---

# Important Interview Question

Do managed nodes need Ansible installed?

Answer:

```text
NO
```

Because:

```text
Ansible is agentless.
```

---

# Biggest Beginner Mistake

Thinking:

```text
Install Ansible on every server
```

Wrong.

Only Control Node needs Ansible.

---

# Memory Trick

Managed Node = Worker

Worker does work.

Worker doesn't manage itself.

---

# Chapter 3: Inventory

---

# What Is Inventory?

Inventory is:

```text
A file containing
all managed hosts.
```

---

Without Inventory

Ansible doesn't know:

```text
Where to connect
```

---

Think of Inventory as:

```text
Phone Contact List
```

---

Example:

Your phone:

```text
Mom
Dad
Friend
Boss
```

Inventory:

```text
Web Server
App Server
DB Server
```

---

# Example Inventory

```ini
[web]
web-server ansible_host=16.146.52.72

[app]
app-server ansible_host=35.92.55.203

[db]
db-server ansible_host=16.144.35.155
```

---

# Breaking It Down

---

## Group Name

```ini
[web]
```

Means:

```text
Group called web
```

---

## Host Alias

```ini
web-server
```

Human-friendly name.

---

## Actual IP

```ini
ansible_host=16.146.52.72
```

Real destination.

---

# Why Groups Matter

Suppose:

```text
100 Web Servers
50 App Servers
20 DB Servers
```

Instead of:

```text
Listing all servers every time
```

Use groups.

---

Example:

```bash
ansible web -m ping
```

Meaning:

```text
Run only on web servers
```

---

# Inventory Hierarchy

```text
Inventory

 |
 |---- Web Group
 |
 |---- App Group
 |
 |---- DB Group
```

---

# Chapter 4: Modules

---

# What Is A Module?

Module = Small Worker

---

Imagine LEGO.

Each LEGO block performs one task.

---

Module Examples

```text
ping
copy
dnf
service
user
file
```

---

# Real Examples

---

## Ping Module

```bash
ansible all -m ping
```

Checks:

```text
Can I reach the server?
```

---

Result:

```text
pong
```

---

## Copy Module

```bash
ansible all -m copy
```

Copies files.

---

## User Module

```bash
ansible all -m user
```

Creates users.

---

## DNF Module

```bash
ansible all -m dnf
```

Installs packages.

---

# Memory Trick

Module = Employee

One employee.

One job.

---

# Why Modules Exist

Without modules:

```text
Ansible would need
thousands of commands.
```

Modules make everything reusable.

---

# Chapter 5: Playbooks

---

# What Is A Playbook?

Playbook is:

```text
A YAML file
containing automation tasks.
```

---

Think:

```text
Recipe Book
```

---

Example

Recipe:

```text
1 Add Oil

2 Add Onion

3 Add Tomato
```

---

Playbook:

```yaml
1 Install Git

2 Install Nginx

3 Start Service
```

---

# Example Playbook

```yaml
---
- name: Install Git

  hosts: web

  tasks:

    - name: Install Git Package

      dnf:
        name: git
        state: present
```

---

# Reading Like English

```yaml
Install Git

On web servers

Using dnf

Ensure Git exists
```

---

# Why YAML?

YAML is:

```text
Human Friendly
```

Compare:

```json
{
 "name":"git"
}
```

vs

```yaml
name: git
```

Much easier.

---

# Memory Trick

Playbook = Recipe Book

Module = Individual Cooking Step

---

# Full Architecture Flow

Let's combine everything.

---

You run:

```bash
ansible web -m ping
```

---

Step 1

```text
Control Node
receives command
```

---

Step 2

```text
Reads Inventory
```

Find:

```text
web-server
```

---

Step 3

```text
Loads ping module
```

---

Step 4

```text
SSH Connection
```

---

Step 5

```text
Module copied temporarily
```

---

Step 6

```text
Module executes
```

---

Step 7

```text
Returns result
```

---

Step 8

```text
Temporary files removed
```

---

ASCII Flow

```text
You

 |
 v

Control Node

 |
 v

Inventory

 |
 v

Find Host

 |
 v

SSH

 |
 v

Managed Node

 |
 v

Run Module

 |
 v

Result

 |
 v

Control Node
```

---

# Ultimate Memory Map

```text
CONTROL NODE
      |
      |
      v

 INVENTORY
      |
      |
      v

 PLAYBOOK
      |
      |
      v

  MODULES
      |
      |
      v

MANAGED NODES
```

---

# Interview Gold Questions

### What is a Control Node?

Machine where Ansible is installed and executed.

---

### What is a Managed Node?

Target server managed by Ansible.

---

### Does a Managed Node need Ansible installed?

No.

Only SSH and Python are required.

---

### What is Inventory?

File containing hosts and groups.

---

### What is a Module?

Reusable unit of work.

---

### What is a Playbook?

YAML file defining automation tasks.

---

### What is the relationship between Inventory, Playbooks and Modules?

```text
Inventory = Targets

Playbook = Instructions

Modules = Actual Workers

Managed Nodes = Execution Location
```

---

# 30-Second Revision

```text
Control Node
=
Brain

Managed Node
=
Worker

Inventory
=
Contact List

Module
=
Employee

Playbook
=
Recipe Book

SSH
=
Road

Ansible
=
Manager
```

***

# Part 3: How Ansible Actually Works Internally

## Push Model • SSH Workflow • Module Execution • Facts • Idempotency • Desired State

---

# Why Most Beginners Get Confused

Most people learn:

```bash
ansible all -m ping
```

and see:

```text
pong
```

But they never ask:

```text
What actually happened?
```

---

# The Magic Behind The Curtain

When you run:

```bash
ansible all -m ping
```

A LOT of things happen behind the scenes.

---

# Big Picture

```text
You
 |
 v

Control Node
(Ansible)

 |
 | SSH
 |
 v

Managed Node

 |
 v

Execute Module

 |
 v

Return Result

 |
 v

Delete Temporary Files
```

---

# Ansible's Core Philosophy

Ansible follows:

```text
Push Model
```

Understanding this is very important.

---

# Chapter 1: Push vs Pull Model

---

# School Analogy

Imagine a Principal wants classrooms cleaned.

---

## Push Model

Principal says:

```text
Class A -> Clean Now

Class B -> Clean Now

Class C -> Clean Now
```

Action happens immediately.

---

This is:

```text
Push
```

---

## Pull Model

Principal writes instructions.

Students periodically check.

```text
Any new instructions?
```

If yes:

```text
Execute
```

---

This is:

```text
Pull
```

---

# Visual

Push:

```text
Teacher
   |
   v

Students
```

---

Pull:

```text
Teacher

Students ask:

"Anything new?"
```

---

# Ansible Uses Push

Meaning:

```text
Control Node
pushes instructions
to Managed Nodes
```

---

Example

You run:

```bash
ansible web -m dnf -a "name=git state=present"
```

Immediately:

```text
Control Node
     |
     v
Web Server

Install Git NOW
```

---

# Why Push Is Popular

---

## Faster

No waiting.

---

## Immediate

Execute instantly.

---

## Simple

No agents required.

---

# Memory Trick

```text
Push

Teacher Talks

Students Listen
```

---

# Chapter 2: SSH Workflow

---

# How Does Ansible Reach Servers?

Answer:

```text
SSH
```

---

# SSH Analogy

Think of SSH as:

```text
Private Tunnel
```

between:

```text
Control Node

and

Managed Node
```

---

Visual

```text
Control Node

     |
     |
   SSH Tunnel
     |
     |

Managed Node
```

---

# Real Example

You execute:

```bash
ansible web -m ping
```

---

Step 1

Ansible checks:

```text
Inventory
```

Finds:

```text
web-server
```

---

Step 2

Opens SSH connection.

```text
Control Node
      |
      |
      | SSH
      |
      v

Web Server
```

---

Step 3

Authenticates.

Using:

```text
SSH Key
```

Example:

```text
ansible-lab.pem
```

---

Step 4

Executes task.

---

Step 5

Returns result.

---

# Why SSH Keys Matter

Imagine:

```text
House Key
```

---

Only people with correct key enter.

---

Wrong key:

```text
Access Denied
```

---

Same in AWS.

Wrong PEM:

```text
Permission denied (publickey)
```

Remember your lab issue?

Exactly this.

---

# Real Lab Example

AWS Expected:

```text
tws key
```

You Used:

```text
different pem
```

Result:

```text
Permission denied
```

---

# Why?

Because:

```text
Public Key

≠

Private Key
```

match.

---

# Memory Trick

SSH Key Pair:

```text
Lock = Public Key

Key = Private Key
```

Wrong key.

Door won't open.

---

# Chapter 3: What Happens During Module Execution?

This is where most interviewers go deeper.

---

# Beginner Belief

Most beginners think:

```text
Ansible command

↓

Runs directly
```

Wrong.

---

Actual Process:

```text
Connect

Copy Module

Execute Module

Return Result

Delete Module
```

---

# Example

Command:

```bash
ansible all -m ping
```

---

Behind The Scenes

Step 1

```text
SSH Connection
```

---

Step 2

```text
Copy ping module
```

to remote server.

---

Step 3

Module runs.

---

Step 4

Produces:

```text
pong
```

---

Step 5

Result returned.

---

Step 6

Temporary files removed.

---

Visual

```text
Control Node

     |
     |
 Copy Module
     |
     v

Managed Node

     |
 Execute
     |
     v

Return Result

     |
 Delete Temp Files
```

---

# Why This Matters

Because:

```text
No permanent agent
```

exists.

---

This temporary execution is the secret behind:

```text
Agentless Architecture
```

---

# Memory Trick

Ansible is like:

```text
Delivery Person
```

---

It:

```text
Arrives

Delivers Package

Gets Signature

Leaves
```

---

Not:

```text
Lives There Forever
```

---

# Chapter 4: Facts Gathering

One of the most important concepts.

---

# What Are Facts?

Facts are:

```text
Information about a server
```

---

Examples:

```text
OS Name

IP Address

CPU

Memory

Hostname

Architecture

Disk Information
```

---

Think:

```text
Hospital Checkup
```

Before treatment.

Doctor first checks:

```text
Temperature

Blood Pressure

Pulse
```

---

Similarly:

Ansible checks:

```text
Server Details
```

---

# Example Command

```bash
ansible all -m setup
```

---

Output:

```text
Hostname

Kernel

CPU

RAM

Network
```

Thousands of details.

---

# Visual

```text
Managed Node

     |
     v

Collect Information

     |
     v

Facts

     |
     v

Control Node
```

---

# Why Facts Are Useful

Suppose:

```text
Ubuntu Server
```

Needs:

```bash
apt install nginx
```

---

But:

```text
Amazon Linux
```

Needs:

```bash
dnf install nginx
```

---

Ansible can automatically detect:

```text
Operating System
```

using facts.

---

# Real Example

```yaml
when:
  ansible_distribution == "Ubuntu"
```

---

Meaning:

```text
Run only on Ubuntu
```

---

# Memory Trick

Facts = Server Biography

Everything about the server.

---

# Chapter 5: Desired State

This is Ansible's biggest idea.

---

Most tools focus on:

```text
Commands
```

Ansible focuses on:

```text
State
```

---

# What Is State?

State means:

```text
Current Condition
```

---

Example

Current:

```text
Git Not Installed
```

Desired:

```text
Git Installed
```

---

Ansible's Job:

```text
Move

Current State

to

Desired State
```

---

Visual

```text
Current

Git Missing

      |
      |
      v

Desired

Git Installed
```

---

# Real Example

```yaml
dnf:
  name: git
  state: present
```

Notice:

```text
state: present
```

---

You didn't say:

```text
Run dnf install git
```

---

You said:

```text
Ensure git exists
```

Huge difference.

---

# Restaurant Analogy

Customer says:

```text
I want Pizza
```

---

Customer does NOT say:

```text
Mix Flour

Add Water

Bake 12 Minutes
```

---

Same idea.

You declare:

```text
Desired Outcome
```

Ansible handles:

```text
Execution
```

---

# Chapter 6: Idempotency (Interview Favorite)

Most important Ansible interview topic.

---

# Difficult Word

```text
Idempotency
```

Sounds scary.

Actually simple.

---

Definition:

```text
Running a task multiple times

produces the same result.
```

---

# Fan Switch Analogy

Suppose fan is ON.

You press:

```text
ON
```

again.

---

Result:

```text
Still ON
```

Nothing changes.

---

That's:

```text
Idempotent
```

---

# Example

Install Git.

```yaml
dnf:
  name: git
  state: present
```

---

First Run

```text
Git installed
```

---

Second Run

```text
Already installed
```

---

Third Run

```text
Already installed
```

---

No duplicate installation.

---

# Why Important?

Without idempotency:

```text
Install Git

Install Git

Install Git

Install Git
```

Could cause issues.

---

Ansible first checks:

```text
Current State
```

Then:

```text
Needed?
```

---

Only changes if necessary.

---

Visual

```text
Desired State

      |
      v

Already Matching?

   YES
      |
      v

Do Nothing
```

---

# Memory Trick

Idempotent = Smart Worker

Before doing work:

```text
Checks if work
is already done.
```

---

# Interview Answer

### What is Idempotency?

```text
Idempotency means executing
the same task multiple times
produces the same final state
without unintended changes.
```

---

# Complete Internal Workflow

Let's put everything together.

---

You execute:

```bash
ansible web -m dnf -a "name=git state=present"
```

---

Step 1

```text
Control Node
```

receives command.

---

Step 2

Reads:

```text
Inventory
```

---

Step 3

Finds:

```text
web-server
```

---

Step 4

Creates:

```text
SSH Connection
```

---

Step 5

Authenticates

Using:

```text
Private Key
```

---

Step 6

Transfers:

```text
dnf Module
```

---

Step 7

Module checks:

```text
Is Git Installed?
```

---

Step 8

If No:

```text
Install Git
```

---

Step 9

If Yes:

```text
Do Nothing
```

(Idempotency)

---

Step 10

Return Result.

---

Step 11

Delete Temporary Files.

---

Final Architecture

```text
YOU

 |
 v

CONTROL NODE

 |
 v

INVENTORY

 |
 v

SSH

 |
 v

MANAGED NODE

 |
 v

MODULE

 |
 v

CHECK STATE

 |
 +------------+
 |            |
 |            |
NO           YES
 |            |
 v            v

MAKE      DO NOTHING
CHANGE

 |
 v

RETURN RESULT

 |
 v

DELETE TEMP FILES
```

---

# Ultimate Revision Sheet

## Push Model

```text
Control Node pushes tasks
to servers.
```

---

## SSH

```text
Secure tunnel
between nodes.
```

---

## Module Execution

```text
Connect

Copy Module

Execute

Return Result

Delete
```

---

## Facts

```text
Information
about a server.
```

---

## Desired State

```text
How the system
should look.
```

---

## Idempotency

```text
Run many times

Same final result.
```

---

# One-Line Memory Map

```text
Push
↓
SSH
↓
Copy Module
↓
Execute
↓
Gather Facts
↓
Check Desired State
↓
Apply Changes If Needed
↓
Return Result
↓
Delete Temp Files
```

If you can explain this flow without looking at notes, you've crossed from **"I know Ansible commands"** to **"I understand how Ansible works internally."** That's the level interviewers usually look for.

***

# Part 4: Inventory, Inventory Groups, Host Variables & ansible.cfg

## Teach Me Like I'm 10 + Deep Understanding + Real Lab Understanding

---

# Why Part 4 Is Important

Most beginners think:

```text
Inventory = List of Servers
```

That's true.

But not complete.

Inventory is actually:

```text
The Map of Your Entire Infrastructure
```

Without Inventory:

```text
Ansible doesn't know:

Who to connect to
Where to connect
Which user to use
Which group to target
```

---

# Big Picture

Before this:

```text
Control Node
Managed Nodes
Modules
Playbooks
```

Now the question becomes:

```text
How does Ansible find servers?
```

Answer:

```text
Inventory
```

---

# Real Life Analogy

Imagine you're a delivery company.

You have:

```text
100 Houses
```

To deliver packages.

Without addresses:

```text
Impossible
```

---

Inventory is:

```text
Address Book
```

for servers.

---

Visual

```text
You

 |
 v

Address Book
(Inventory)

 |
 v

Server Locations
```

---

# Chapter 1: What Is Inventory?

Definition:

```text
Inventory is a file that contains
the list of managed nodes
that Ansible can manage.
```

---

Simple Definition

```text
Inventory = Contact List
```

---

Your phone:

```text
Mom
Dad
Friend
Boss
```

Inventory:

```text
Web Server
App Server
DB Server
```

---

# Smallest Inventory

```ini
web-server
```

---

Meaning:

```text
One server exists
```

---

# Inventory With IP

```ini
16.146.52.72
```

---

Meaning:

```text
Connect directly using IP
```

---

# Better Version

```ini
web-server ansible_host=16.146.52.72
```

---

Breakdown

```ini
web-server
```

Human-friendly name.

---

```ini
ansible_host=16.146.52.72
```

Actual destination.

---

Visual

```text
Alias

web-server

      |
      v

Actual Host

16.146.52.72
```

---

# Why Aliases Are Useful

Which is easier?

```text
18.220.43.182
```

or

```text
web-server
```

Obviously:

```text
web-server
```

---

Humans remember names.

Computers use IPs.

Inventory allows both.

---

# Chapter 2: Inventory Groups

As infrastructure grows:

```text
5 Servers
10 Servers
50 Servers
500 Servers
```

Managing individually becomes difficult.

---

Imagine School

Instead of:

```text
Student1
Student2
Student3
Student4
Student5
```

You create:

```text
Class A
Class B
Class C
```

Groups.

---

Same concept.

---

Example

```ini
[web]
web-server1
web-server2

[app]
app-server1
app-server2

[db]
db-server1
```

---

Visual

```text
Inventory

 |
 +---- WEB
 |       |
 |       +--- web-server1
 |       +--- web-server2
 |
 +---- APP
 |       |
 |       +--- app-server1
 |       +--- app-server2
 |
 +---- DB
         |
         +--- db-server1
```

---

# Why Groups Matter

Suppose:

```text
100 Web Servers
```

Install Nginx.

Without Groups:

```bash
ansible web1,web2,web3,web4...
```

Nightmare.

---

With Groups:

```bash
ansible web -m ping
```

Easy.

---

Meaning:

```text
Target all web servers
```

---

# Memory Trick

```text
Group = WhatsApp Group
```

Instead of messaging:

```text
100 people individually
```

Message:

```text
Entire Group
```

---

# Chapter 3: Parent Groups (Children)

Very important concept.

---

Imagine Company Structure

```text
Engineering

 |
 +---- Developers

 |
 +---- Testers

 |
 +---- DevOps
```

---

Engineering contains:

```text
Multiple Teams
```

---

Ansible supports this.

---

Example

```ini
[web]
web1
web2

[app]
app1
app2

[production:children]
web
app
```

---

Meaning:

```text
production

contains

web
and
app
```

---

Visual

```text
production

 |
 +---- web
 |       |
 |       +--- web1
 |       +--- web2
 |
 +---- app
         |
         +--- app1
         +--- app2
```

---

# Why Children Groups Exist

Without children:

```text
Target web

Target app
```

separately.

---

With children:

```bash
ansible production -m ping
```

Targets everything.

---

# Memory Trick

```text
Children Groups

=

Folder Inside Folder
```

---

# Chapter 4: Host Variables

Now things get interesting.

Not every server is identical.

---

Example

```text
Web Server
IP = 10.0.1.10

App Server
IP = 10.0.1.20

DB Server
IP = 10.0.1.30
```

Each server needs unique information.

---

Inventory supports variables.

---

Example

```ini
web-server ansible_host=16.146.52.72
```

---

Here:

```text
ansible_host
```

is a variable.

---

Meaning:

```text
Actual IP Address
```

---

# Common Host Variables

---

## ansible_host

Actual destination IP.

Example:

```ini
web-server ansible_host=16.146.52.72
```

---

## ansible_user

SSH username.

Example:

```ini
web-server ansible_user=ec2-user
```

---

Meaning:

```bash
ssh ec2-user@16.146.52.72
```

---

## ansible_port

SSH port.

Default:

```text
22
```

Custom:

```ini
ansible_port=2222
```

---

## ansible_ssh_private_key_file

Private key location.

Example:

```ini
ansible_ssh_private_key_file=~/.ssh/ansible.pem
```

---

Meaning:

```text
Use this key for login
```

---

# Real Example

```ini
web-server

ansible_host=16.146.52.72

ansible_user=ec2-user

ansible_ssh_private_key_file=~/.ssh/ansible.pem
```

---

Ansible now knows:

```text
Where to connect

Which user

Which key
```

---

# Interview Question

Why use host variables?

Answer:

```text
To store host-specific
connection information.
```

---

# Chapter 5: Group Variables

Sometimes every server in a group shares settings.

---

Example

All web servers use:

```text
ec2-user
```

---

Instead of:

```ini
web1 ansible_user=ec2-user

web2 ansible_user=ec2-user

web3 ansible_user=ec2-user
```

Repeated.

---

Use group variables.

---

Example

```ini
[web:vars]

ansible_user=ec2-user
```

---

Meaning:

```text
Apply to all web servers
```

---

Visual

```text
web group

 |
 +--- web1
 |
 +--- web2
 |
 +--- web3

All inherit:

ansible_user=ec2-user
```

---

# Memory Trick

```text
Host Variable

=
One Student

Group Variable

=
Entire Classroom
```

---

# Chapter 6: ansible.cfg

One of the most overlooked files.

Interviewers love it.

---

# What Is ansible.cfg?

Definition:

```text
Ansible configuration file.
```

---

Think:

```text
Ansible Settings Menu
```

---

Phone Example

Settings:

```text
Brightness
Volume
WiFi
Bluetooth
```

---

Ansible Settings:

```text
Inventory Path
SSH Settings
Forks
Timeout
Users
```

---

# Example

```ini
[defaults]

inventory=inventory.ini
```

---

Meaning:

```text
Use inventory.ini
as default inventory
```

---

Without This

Every command needs:

```bash
ansible all -i inventory.ini -m ping
```

---

With ansible.cfg

```bash
ansible all -m ping
```

Cleaner.

---

# Common Settings

---

## inventory

Default inventory file.

```ini
inventory=inventory.ini
```

---

## remote_user

Default SSH user.

```ini
remote_user=ec2-user
```

---

## host_key_checking

Controls fingerprint verification.

```ini
host_key_checking=False
```

---

Lab environments often disable this.

---

## timeout

SSH timeout.

```ini
timeout=30
```

---

## forks

Parallel execution count.

```ini
forks=10
```

---

Meaning:

```text
Manage 10 servers simultaneously
```

---

# Configuration Search Order

Very important interview question.

When Ansible starts, it searches:

```text
1. ANSIBLE_CONFIG

2. ./ansible.cfg

3. ~/.ansible.cfg

4. /etc/ansible/ansible.cfg
```

---

Visual

```text
Highest Priority

ANSIBLE_CONFIG

      ↓

Current Directory

      ↓

Home Directory

      ↓

System Directory

Lowest Priority
```

---

First match wins.

---

# Chapter 7: Inventory Graph (Mental Model)

This is how professionals think.

---

```text
Inventory

 |
 +---- Production
 |        |
 |        +---- Web
 |        |      |
 |        |      +--- web1
 |        |      +--- web2
 |        |
 |        +---- App
 |               |
 |               +--- app1
 |               +--- app2
 |
 +---- Staging
          |
          +--- test1
          +--- test2
```

---

When you run:

```bash
ansible production -m ping
```

Ansible traverses:

```text
Production

↓

Web + App

↓

All Hosts
```

---

# Full Flow

```text
You

 |
 v

ansible web -m ping

 |
 v

Read ansible.cfg

 |
 v

Read Inventory

 |
 v

Find Group

 |
 v

Find Hosts

 |
 v

Read Variables

 |
 v

SSH Connect

 |
 v

Execute Module

 |
 v

Return Result
```

---

# Interview Gold Questions

### What is Inventory?

```text
A file containing
managed hosts and groups.
```

---

### Why use Inventory Groups?

```text
To manage multiple servers
logically and efficiently.
```

---

### Difference between Host Variables and Group Variables?

```text
Host Variables
=
One Host

Group Variables
=
All Hosts In Group
```

---

### What is ansible.cfg?

```text
Main configuration file
for Ansible behavior.
```

---

### What is the search order of ansible.cfg?

```text
ANSIBLE_CONFIG

↓

Current Directory

↓

Home Directory

↓

System Directory
```

---

# Ultimate Revision Sheet

```text
Inventory
=
Contact List

Group
=
WhatsApp Group

Children Group
=
Group of Groups

Host Variables
=
One Server Settings

Group Variables
=
Shared Settings

ansible.cfg
=
Ansible Settings Menu
```

---

# One-Line Memory Map

```text
Inventory
↓
Groups
↓
Children
↓
Variables
↓
ansible.cfg
↓
SSH
↓
Managed Node
```

If Part 1 taught **what Ansible is**, Part 2 taught **its components**, and Part 3 taught **its internal engine**, then Part 4 teaches **how Ansible finds, organizes, and connects to infrastructure**—which is exactly what you were doing in your AWS lab with web, app, and db servers.

***

# Part 5: Ad-Hoc Commands (The Fastest Way to Use Ansible)



---

# Before Learning Ad-Hoc Commands

Most beginners learn Playbooks first.

But historically many admins first used:

```bash
ansible all -m ping
```

before writing playbooks.

Why?

Because sometimes you don't need a full recipe.

You just need:

```text
Do this one thing right now.
```

That's exactly what Ad-Hoc Commands are for.

---

# What Does Ad-Hoc Mean?

Ad-Hoc means:

```text
Temporary
Quick
One-Time
Immediate
```

---

# Real Life Analogy

Imagine your mom says:

```text
Bring me a glass of water.
```

You don't create:

```text
Water Delivery Procedure Document
Version 1.0
```

😂

You just:

```text
Bring water.
```

Done.

---

That's an Ad-Hoc command.

---

# Playbook vs Ad-Hoc

---

## Playbook

Like:

```text
Full Recipe Book
```

Example:

```yaml
Install Git

Install Nginx

Create User

Start Service
```

Many steps.

---

## Ad-Hoc

Like:

```text
One Quick Instruction
```

Example:

```bash
ansible all -m ping
```

One task.

Done.

---

# Memory Trick

```text
Playbook
=
Movie

Ad-Hoc
=
YouTube Short
```

---

# General Syntax

Every Ad-Hoc command follows:

```bash
ansible <target> -m <module> -a "<arguments>"
```

---

Breakdown

```bash
ansible
```

Ansible command.

---

```bash
<target>
```

Which servers?

---

```bash
-m
```

Which module?

---

```bash
-a
```

Arguments for module.

---

# Visual

```text
ansible

 |
 +---- Target Hosts

 |
 +---- Module

 |
 +---- Arguments
```

---

# Example

```bash
ansible web -m ping
```

Meaning:

```text
Target = web

Module = ping

Arguments = none
```

---

# Example

```bash
ansible web -m dnf -a "name=git state=present"
```

Meaning:

```text
Target web servers

Use dnf module

Install Git
```

---

# Chapter 1: Ping Module

This is usually the first command everyone runs.

---

# Biggest Beginner Misunderstanding

People think:

```text
ping module

=

ICMP ping
```

Wrong.

---

It does NOT do:

```bash
ping 8.8.8.8
```

---

Instead it checks:

```text
Can Ansible connect?
Can SSH work?
Can Python run?
```

---

# Command

```bash
ansible all -m ping
```

---

Flow

```text
SSH Connect

↓

Execute Ping Module

↓

Return Pong
```

---

Result

```json
{
  "ping": "pong"
}
```

---

# What Pong Means

Pong means:

```text
SSH Success

Python Success

Module Execution Success
```

---

Visual

```text
Control Node

      |

      | SSH

      v

Managed Node

      |

      v

pong
```

---

# If Ping Fails

Usually one of these:

```text
Wrong IP

Wrong User

Wrong Key

Port 22 Closed

Python Missing
```

---

# Lab Connection

When your EC2 access failed:

```text
Permission denied (publickey)
```

Ping would also fail.

Because:

```text
SSH failed
```

---

# Chapter 2: Command Module

Most used module.

---

# Purpose

Runs commands remotely.

---

Example

```bash
ansible all -m command -a "hostname"
```

---

Meaning

```text
Ask every server:

What is your hostname?
```

---

Result

```text
web-server

app-server

db-server
```

---

Visual

```text
Control Node

     |

     v

hostname

     |

     v

Managed Node
```

---

# More Examples

---

## Check Uptime

```bash
ansible all -m command -a "uptime"
```

---

## Check Date

```bash
ansible all -m command -a "date"
```

---

## Check Current User

```bash
ansible all -m command -a "whoami"
```

---

# Important Limitation

Command module:

```text
NO shell features
```

---

This fails:

```bash
ansible all -m command -a "ls | grep txt"
```

---

Why?

Because:

```text
Pipe is shell functionality
```

---

# Memory Trick

```text
Command Module

=
Simple Command Only
```

---

# Chapter 3: Shell Module

Shell module is more powerful.

---

# Purpose

Runs shell commands.

---

Example

```bash
ansible all -m shell -a "ls | grep txt"
```

Works.

---

Because:

```text
Executed through shell
```

---

Visual

```text
Command Module

Direct Command
```

---

```text
Shell Module

Command

↓

Shell

↓

Execution
```

---

# Examples

---

## Find Log Files

```bash
ansible all -m shell -a "ls | grep log"
```

---

## Redirect Output

```bash
ansible all -m shell -a "date > test.txt"
```

---

## Multiple Commands

```bash
ansible all -m shell -a "pwd && date"
```

---

# Interview Question

Command vs Shell?

---

Command

```text
Safer

Faster

No Pipes
```

---

Shell

```text
Supports

Pipes
Redirects
Variables
Wildcards
```

---

Rule:

```text
Use Command when possible

Use Shell when necessary
```

---

# Chapter 4: Copy Module

Very common in production.

---

# Purpose

Copy files from:

```text
Control Node

↓

Managed Node
```

---

Analogy

```text
Courier Service
```

---

Visual

```text
Control Node

file.txt

    |

    |

    v

Managed Node

file.txt
```

---

# Example

```bash
ansible all -m copy -a "src=test.txt dest=/tmp/test.txt"
```

---

Meaning

```text
Take local file

Copy to remote server
```

---

# Breakdown

```text
src
=
Source
```

---

```text
dest
=
Destination
```

---

# Real Production Use

Copy:

```text
Config Files

Nginx Config

Apache Config

Scripts

Certificates
```

---

# Memory Trick

```text
Copy Module

=
Courier Boy
```

---

# Chapter 5: File Module

Used for filesystem operations.

---

# Create Directory

```bash
ansible all -m file -a "path=/tmp/demo state=directory"
```

---

Meaning

```text
Create folder
```

---

# Delete Directory

```bash
ansible all -m file -a "path=/tmp/demo state=absent"
```

---

Meaning

```text
Remove folder
```

---

# Change Permissions

```bash
ansible all -m file -a "path=/tmp/demo mode=755"
```

---

Meaning

```text
Modify permissions
```

---

# Memory Trick

```text
File Module

=
File Manager
```

---

# Chapter 6: DNF/YUM/APT Module

Very important.

Used constantly.

---

# Purpose

Install packages.

---

Example

Amazon Linux:

```bash
ansible all -m dnf -a "name=git state=present"
```

---

Meaning

```text
Ensure Git is installed
```

---

# Notice

Not:

```text
Install Git
```

---

Instead:

```text
Ensure Git Exists
```

---

This is:

```text
Desired State
```

---

# Install Multiple Packages

```bash
ansible all -m dnf -a "name=git,nginx state=present"
```

---

# Remove Package

```bash
ansible all -m dnf -a "name=git state=absent"
```

---

# States

---

Present

```text
Package Must Exist
```

---

Absent

```text
Package Must Not Exist
```

---

Latest

```text
Install Latest Version
```

---

# Visual

```text
Current State

Git Missing

      |

      v

Desired State

Git Installed
```

---

# Memory Trick

```text
DNF Module

=
Package Manager Worker
```

---

# Chapter 7: Service Module

After installing software:

```text
Need to start it
```

---

Example

Install:

```text
Nginx
```

---

Then:

```text
Start Nginx
```

---

Command

```bash
ansible all -m service -a "name=nginx state=started"
```

---

Meaning

```text
Start nginx service
```

---

# Common States

Started

```text
Running
```

---

Stopped

```text
Not Running
```

---

Restarted

```text
Restart Service
```

---

Reloaded

```text
Reload Config
```

---

# Enable At Boot

```bash
ansible all -m service -a "name=nginx enabled=yes"
```

---

Meaning

```text
Start automatically after reboot
```

---

# Memory Trick

```text
Service Module

=
Power Button
```

---

# Chapter 8: User Module

Creates Linux users.

---

Example

```bash
ansible all -m user -a "name=devops"
```

---

Meaning

```text
Create user

devops
```

---

# Remove User

```bash
ansible all -m user -a "name=devops state=absent"
```

---

# Production Use

```text
Developer Accounts

Admin Accounts

Service Accounts
```

---

# Memory Trick

```text
User Module

=
HR Department
```

Creates employees.

Removes employees.

---

# Chapter 9: Become (Sudo)

Critical concept.

---

# Problem

Normal user may not install packages.

Example:

```bash
ec2-user
```

tries:

```text
Install nginx
```

---

Linux says:

```text
Permission Denied
```

---

Need:

```text
sudo
```

---

In Ansible:

```bash
--become
```

---

Example

```bash
ansible all -m dnf -a "name=git state=present" --become
```

---

Meaning

```text
Temporarily become root
```

---

Visual

```text
ec2-user

     |

     v

Become

     |

     v

root

     |

     v

Install Package
```

---

# Interview Question

What does `--become` do?

Answer:

```text
Executes tasks using elevated
privileges, usually root.
```

---

# Complete Ad-Hoc Flow

Suppose:

```bash
ansible web -m dnf -a "name=nginx state=present" --become
```

---

Step 1

```text
Read Inventory
```

---

Step 2

```text
Find Web Servers
```

---

Step 3

```text
SSH Connect
```

---

Step 4

```text
Become Root
```

---

Step 5

```text
Copy DNF Module
```

---

Step 6

```text
Check Current State
```

---

Step 7

```text
Install Nginx If Missing
```

---

Step 8

```text
Return Result
```

---

Step 9

```text
Delete Temporary Files
```

---

ASCII Flow

```text
YOU

 |
 v

AD-HOC COMMAND

 |
 v

INVENTORY

 |
 v

TARGET HOSTS

 |
 v

SSH

 |
 v

BECOME ROOT

 |
 v

MODULE

 |
 v

CHECK STATE

 |
 v

MAKE CHANGES

 |
 v

RETURN RESULT
```

---

# Ad-Hoc Commands vs Playbooks

| Ad-Hoc          | Playbook       |
| --------------- | -------------- |
| One Task        | Multiple Tasks |
| Quick           | Structured     |
| Temporary       | Reusable       |
| CLI Based       | YAML Based     |
| Troubleshooting | Automation     |

---

# Ultimate Revision Sheet

```text
Ping
=
Connectivity Check

Command
=
Simple Commands

Shell
=
Advanced Commands

Copy
=
Transfer Files

File
=
Manage Files/Folders

DNF
=
Install Packages

Service
=
Manage Services

User
=
Manage Users

Become
=
Sudo
```

---

# One-Line Memory Map

```text
Ping
↓
Command
↓
Shell
↓
Copy
↓
File
↓
DNF
↓
Service
↓
User
↓
Become
```

If you master Part 5, you can already perform **80% of day-to-day Linux administration through Ansible without writing a single Playbook.**

### Part 6 Next (Very Important)

We'll cover:

```text
Host Patterns
all
*
web
web:app
web:&prod
web:!db

Inventory Graph

Group Hierarchy

Pattern Matching Logic

How Ansible Decides Targets
```

This is where people finally understand commands like:

```bash
ansible web -m ping
ansible all -m ping
ansible production -m ping
```

instead of memorizing them blindly.

***

# Part 6: Host Patterns, Target Selection & How Ansible Decides Where To Run

## Teach Me Like I'm 10 + Deep Understanding + Interview Ready

---

# Why Part 6 Is Important

Most beginners run:

```bash
ansible all -m ping
```

and think:

```text
Ansible magically knows where to go.
```

No.

Ansible follows a targeting system called:

```text
Host Patterns
```

This determines:

```text
Which servers run the task

Which servers are ignored
```

---

# Real Life Analogy

Imagine you're a school principal.

You announce:

```text
All Students Report To Assembly
```

Who comes?

```text
Everyone
```

---

Now you announce:

```text
Only Class 10
```

Who comes?

```text
Class 10 Only
```

---

Now:

```text
Only Cricket Team
```

Who comes?

```text
Cricket Team
```

---

Ansible works exactly like this.

---

# The Inventory We'll Use

Imagine:

```ini
[web]
web1
web2

[app]
app1
app2

[db]
db1

[production:children]
web
app

[staging]
test1
test2
```

---

Visual

```text
Inventory

 |
 +---- web
 |       |
 |       +--- web1
 |       +--- web2
 |
 +---- app
 |       |
 |       +--- app1
 |       +--- app2
 |
 +---- db
 |       |
 |       +--- db1
 |
 +---- production
 |       |
 |       +--- web
 |       |
 |       +--- app
 |
 +---- staging
         |
         +--- test1
         +--- test2
```

---

# Chapter 1: The `all` Pattern

Most common.

---

Command

```bash
ansible all -m ping
```

---

Meaning

```text
Target Every Host
In Inventory
```

---

Visual

```text
all

 |
 +---- web1
 +---- web2
 +---- app1
 +---- app2
 +---- db1
 +---- test1
 +---- test2
```

---

# Real Life Analogy

Teacher says:

```text
Everyone Assemble
```

Nobody excluded.

---

# Memory Trick

```text
all
=
Entire School
```

---

# Chapter 2: Specific Host

Sometimes you want:

```text
One Server Only
```

---

Command

```bash
ansible web1 -m ping
```

---

Meaning

```text
Target Only web1
```

---

Visual

```text
Inventory

web1  ← selected

web2

app1

app2

db1
```

---

# Why Useful?

Example:

```text
Testing

Troubleshooting

Verification
```

---

Before touching:

```text
500 servers
```

test on:

```text
1 server
```

---

# Chapter 3: Group Pattern

Most common in production.

---

Command

```bash
ansible web -m ping
```

---

Meaning

```text
Target all hosts
inside web group
```

---

Visual

```text
web

 |
 +---- web1
 |
 +---- web2
```

---

Targets:

```text
web1

web2
```

---

Ignores:

```text
app1

app2

db1
```

---

# Real Production Example

Install nginx.

```bash
ansible web -m dnf -a "name=nginx state=present"
```

---

Meaning:

```text
Install nginx

Only on web servers
```

---

# Memory Trick

```text
Group
=
WhatsApp Group
```

Message sent to group.

Everyone receives it.

---

# Chapter 4: Multiple Groups Using Colon (:)

Very important.

---

Command

```bash
ansible web:app -m ping
```

---

Meaning

```text
web OR app
```

---

Visual

```text
Selected

web1
web2

app1
app2
```

---

Ignored

```text
db1
```

---

Think:

```text
Union
```

---

Math Example

```text
Web = {web1,web2}

App = {app1,app2}

Web:App

=

{web1,web2,app1,app2}
```

---

# Real Example

Deploy application.

```text
Web Servers

+

App Servers
```

but not database.

---

Command:

```bash
ansible web:app -m ping
```

---

# Memory Trick

```text
:
=
OR
```

---

# Chapter 5: Intersection (&)

One of the most important interview topics.

---

Imagine:

```ini
[web]
web1
web2

[production]
web1
app1
```

---

Visual

```text
web

web1
web2

production

web1
app1
```

---

Common Host

```text
web1
```

---

Command

```bash
ansible web:&production -m ping
```

---

Meaning

```text
Hosts that exist in BOTH groups
```

---

Result

```text
web1
```

Only.

---

Visual

```text
      web

   web1
   web2


        ∩

production

web1
app1


Result

web1
```

---

# Real Life Analogy

Students who are:

```text
Class 10

AND

Cricket Team
```

Not everyone.

Only overlap.

---

# Memory Trick

```text
&
=
AND
```

---

# Chapter 6: Exclusion (!)

Extremely useful.

---

Command

```bash
ansible web:!web2 -m ping
```

---

Meaning

```text
All web hosts

EXCEPT web2
```

---

Result

```text
web1
```

---

Visual

```text
web

web1

web2  ← removed
```

---

# Another Example

```bash
ansible all:!db -m ping
```

---

Meaning

```text
Everything

Except

Database Servers
```

---

Selected

```text
web1
web2

app1
app2

test1
test2
```

---

Ignored

```text
db1
```

---

# Real Production Use

Suppose:

```text
Patch Every Server
```

But NOT:

```text
Database
```

because database maintenance is scheduled later.

---

# Memory Trick

```text
!
=
NOT
```

---

# Chapter 7: Wildcards (*)

Used for pattern matching.

---

Inventory

```text
web-prod-1

web-prod-2

web-stage-1

app-prod-1
```

---

Command

```bash
ansible web* -m ping
```

---

Matches

```text
web-prod-1

web-prod-2

web-stage-1
```

---

Ignores

```text
app-prod-1
```

---

# Memory Trick

```text
*
=
Anything After This
```

---

# Chapter 8: Combining Patterns

Professionals do this often.

---

Example

```bash
ansible web:app:!staging -m ping
```

---

Meaning

```text
web

OR

app

BUT NOT

staging
```

---

Visual

```text
Take:

web

+

app

Remove:

staging
```

---

# Think Like Mathematics

Pattern logic:

```text
Union

Intersection

Exclusion
```

---

Ansible patterns are basically:

```text
Set Theory
```

without calling it that.

---

# Chapter 9: Inventory Graph

One of the coolest commands.

---

Command

```bash
ansible-inventory --graph
```

---

Purpose

```text
Show inventory hierarchy visually.
```

---

Example Output

```text
@all

 |--@web
 |   |--web1
 |   |--web2
 |
 |--@app
 |   |--app1
 |   |--app2
 |
 |--@db
 |   |--db1
```

---

# Why Useful?

Helps answer:

```text
What groups exist?

What hosts exist?

Which hosts belong where?
```

---

# Chapter 10: How Ansible Resolves Targets

Let's follow the full journey.

---

Command

```bash
ansible web -m ping
```

---

Step 1

Read:

```text
ansible.cfg
```

---

Step 2

Find:

```text
Inventory File
```

---

Step 3

Locate:

```text
web group
```

---

Step 4

Expand group.

```text
web1

web2
```

---

Step 5

Read variables.

```text
ansible_host

ansible_user

SSH key
```

---

Step 6

Connect.

---

Step 7

Execute module.

---

Step 8

Return results.

---

Visual

```text
ansible web -m ping

        |
        v

Read Inventory

        |
        v

Find Group

        |
        v

Expand Hosts

        |
        v

Load Variables

        |
        v

SSH Connect

        |
        v

Run Module

        |
        v

Return Result
```

---

# Pattern Cheat Sheet

---

## All Hosts

```bash
ansible all -m ping
```

---

## Single Host

```bash
ansible web1 -m ping
```

---

## Group

```bash
ansible web -m ping
```

---

## Union (OR)

```bash
ansible web:app -m ping
```

---

## Intersection (AND)

```bash
ansible web:&production -m ping
```

---

## Exclusion (NOT)

```bash
ansible all:!db -m ping
```

---

## Wildcard

```bash
ansible web* -m ping
```

---

# Interview Questions

---

### What does `all` mean?

```text
Every host in inventory.
```

---

### What does `web:app` mean?

```text
Hosts in web OR app group.
```

---

### What does `web:&production` mean?

```text
Hosts present in both groups.
```

---

### What does `all:!db` mean?

```text
All hosts except db group.
```

---

### What command shows inventory hierarchy?

```bash
ansible-inventory --graph
```

---

# Ultimate Revision Sheet

```text
all
=
Everything

web
=
One Group

web:app
=
OR

web:&prod
=
AND

all:!db
=
NOT

web*
=
Wildcard
```

---

# One-Line Memory Map

```text
Inventory
      ↓
Groups
      ↓
Patterns
      ↓
OR (:)
      ↓
AND (&)
      ↓
NOT (!)
      ↓
Wildcard (*)
      ↓
Target Hosts
      ↓
Execute Module
```

---

## What You've Learned So Far (Parts 1–6)

```text
Part 1
What Ansible Is

        ↓

Part 2
Core Components

        ↓

Part 3
Internal Working

        ↓

Part 4
Inventory & Variables

        ↓

Part 5
Ad-Hoc Commands

        ↓

Part 6
Host Patterns & Target Selection
```

At this point, you understand the foundation that many engineers use daily. The next major topic after this is usually **Playbooks**, where all these pieces (inventory, modules, variables, become, groups, patterns) start working together in real automation workflows.

***

# Part 7: Playbooks — The Heart of Ansible

## Teach Me Like I'm 10 + Deep Understanding + Real DevOps Thinking

---

# Why Playbooks Exist

Imagine you need to configure 100 servers.

Tasks:

```text
Install Git

Install Nginx

Create User

Start Service

Enable Service

Copy Config File
```

You could run:

```bash
ansible all -m dnf ...
ansible all -m user ...
ansible all -m service ...
ansible all -m copy ...
```

again and again.

But that's painful.

---

Ansible says:

```text
Write everything once.

Run everything together.
```

That file is called:

```text
Playbook
```

---

# Real Life Analogy

Imagine cooking.

You don't say:

```text
Add oil

Wait

Add onion

Wait

Add tomato
```

every day.

Instead you save the recipe.

---

Recipe Book

```text
Step 1

Step 2

Step 3

Step 4
```

---

Ansible Playbook

```text
Task 1

Task 2

Task 3

Task 4
```

---

# Simple Definition

A Playbook is:

```text
A YAML file that contains
automation instructions.
```

---

# Biggest Difference

Ad-Hoc Command

```text
One Task
```

---

Playbook

```text
Many Tasks
```

---

Visual

```text
Ad-Hoc

Install Git
```

---

```text
Playbook

Install Git

Install Nginx

Create User

Copy Config

Start Service
```

---

# Memory Trick

```text
Ad-Hoc
=
Single Tool

Playbook
=
Toolbox
```

---

# Chapter 1: Playbook Structure

Smallest Playbook

```yaml
---
- hosts: all

  tasks:

    - name: Test Connectivity

      ping:
```

---

Let's decode it.

---

# YAML Start

```yaml
---
```

Means:

```text
YAML document starts here.
```

Think:

```text
Book Cover Page
```

---

# hosts

```yaml
hosts: all
```

Means:

```text
Who should execute this?
```

---

Examples

```yaml
hosts: all
```

Every server.

---

```yaml
hosts: web
```

Only web servers.

---

```yaml
hosts: production
```

Only production servers.

---

# Memory Trick

```text
hosts
=
Audience
```

---

Teacher asks:

```text
Who should hear this?
```

Same idea.

---

# tasks

```yaml
tasks:
```

Means:

```text
List of work to perform.
```

---

Visual

```text
Playbook

 |
 +---- Task 1

 |
 +---- Task 2

 |
 +---- Task 3
```

---

# name

```yaml
name:
```

Human readable description.

---

Example

```yaml
name: Install Nginx
```

---

Used for:

```text
Readability

Troubleshooting

Logging
```

---

# Why Names Matter

Bad:

```yaml
- dnf:
```

No clue what happens.

---

Good:

```yaml
- name: Install Nginx Package
```

Immediately understandable.

---

# Memory Trick

```text
Task Name

=

Movie Title
```

---

# Chapter 2: First Real Playbook

---

Goal:

```text
Install Git
```

---

Playbook

```yaml
---
- name: Install Git

  hosts: all

  become: yes

  tasks:

    - name: Install Git Package

      dnf:
        name: git
        state: present
```

---

Read Like English

```text
For all servers

Become root

Install Git

Ensure it exists
```

---

# What Happens Internally

```text
Read Inventory

↓

Find Hosts

↓

SSH

↓

Become Root

↓

Execute DNF Module

↓

Check State

↓

Install If Needed

↓

Return Result
```

---

# Chapter 3: Running Playbooks

Command:

```bash
ansible-playbook install-git.yml
```

---

Visual

```text
install-git.yml

        |

        v

Ansible Playbook Engine

        |

        v

Managed Nodes
```

---

# Difference From Ad-Hoc

Ad-Hoc

```bash
ansible all -m ping
```

---

Playbook

```bash
ansible-playbook site.yml
```

---

# Memory Trick

```text
ansible

=
One Task

ansible-playbook

=
Many Tasks
```

---

# Chapter 4: Multiple Tasks

Real power starts here.

---

Example

```yaml
---
- hosts: all

  become: yes

  tasks:

    - name: Install Git

      dnf:
        name: git
        state: present

    - name: Install Nginx

      dnf:
        name: nginx
        state: present

    - name: Start Nginx

      service:
        name: nginx
        state: started
```

---

Visual

```text
Task 1

Install Git

     ↓

Task 2

Install Nginx

     ↓

Task 3

Start Nginx
```

---

# Important Rule

Tasks execute:

```text
Top

↓

Bottom
```

---

Exactly like:

```text
Recipe Steps
```

---

# Chapter 5: Become

Most package installations need root.

---

Without become

```yaml
dnf:
```

may fail.

---

With become

```yaml
become: yes
```

Ansible does:

```text
sudo
```

automatically.

---

Visual

```text
ec2-user

    |

    v

become

    |

    v

root
```

---

# Memory Trick

```text
become

=

Temporary Superhero Mode
```

---

# Chapter 6: Idempotency Inside Playbooks

Most important concept.

---

Suppose:

```yaml
dnf:
  name: git
  state: present
```

---

Run 1

```text
Git Installed
```

---

Run 2

```text
Already Installed
```

---

Run 3

```text
Still Installed
```

---

No duplication.

---

# Why This Is Amazing

Traditional Script

```bash
dnf install git
dnf install git
dnf install git
```

---

Ansible

```text
Checks Current State First
```

---

Visual

```text
Need Git?

      |

      v

Already Exists?

      |

YES --------> Do Nothing

NO ---------> Install
```

---

# Chapter 7: Play Recap

What is a Play?

---

Many beginners confuse:

```text
Playbook

Play

Task
```

---

Let's separate them.

---

# Task

Smallest unit.

Example:

```yaml
Install Git
```

---

# Play

Group of tasks applied to hosts.

Example:

```yaml
- hosts: web

  tasks:
```

---

# Playbook

Collection of plays.

---

Visual

```text
Playbook

 |
 +---- Play 1

 |       |
 |       +--- Task 1
 |       +--- Task 2

 |
 +---- Play 2

         |
         +--- Task 3
         +--- Task 4
```

---

# School Analogy

```text
Playbook
=
Entire School Event

Play
=
One Classroom Activity

Task
=
Single Instruction
```

---

# Chapter 8: Multiple Plays

Example

```yaml
---
- hosts: web

  tasks:

    - name: Install Nginx

      dnf:
        name: nginx
        state: present

- hosts: db

  tasks:

    - name: Install MySQL

      dnf:
        name: mysql
        state: present
```

---

Meaning

```text
Web Servers

↓

Install Nginx

----------------

DB Servers

↓

Install MySQL
```

---

Visual

```text
Playbook

 |
 +---- WEB PLAY

 |        |
 |        +--- Install Nginx

 |
 +---- DB PLAY

          |
          +--- Install MySQL
```

---

# Chapter 9: YAML Basics

YAML is extremely important.

Most Ansible errors are YAML errors.

---

# Rule 1: Indentation Matters

Correct

```yaml
tasks:

  - name: Install Git

    dnf:
      name: git
```

---

Wrong

```yaml
tasks:

- name: Install Git
dnf:
name: git
```

---

Think:

```text
Python Style

Spacing Matters
```

---

# Rule 2: No Tabs

Always use:

```text
Spaces
```

Never:

```text
Tabs
```

---

# Rule 3: Colon Format

```yaml
name: git
```

---

Meaning:

```text
Key : Value
```

---

Visual

```text
name ---> git

state ---> present
```

---

# Chapter 10: Real Interview Flow

Suppose interviewer asks:

### What happens when you run a playbook?

Answer:

```text
1. Read Playbook

2. Read Inventory

3. Find Hosts

4. Connect Using SSH

5. Become User (if needed)

6. Execute Tasks Sequentially

7. Check Desired State

8. Make Changes If Needed

9. Return Results

10. Show Summary
```

---

# End-of-Play Summary

You'll see:

```text
ok

changed

unreachable

failed
```

---

Meaning

### ok

```text
Task succeeded

No change required
```

---

### changed

```text
Task succeeded

Something modified
```

---

### unreachable

```text
SSH issue
```

---

### failed

```text
Task execution failed
```

---

# Ultimate Revision Sheet

```text
Playbook
=
Recipe Book

Play
=
Group Of Tasks For Hosts

Task
=
Single Action

hosts
=
Target Servers

become
=
sudo

tasks
=
Work List

name
=
Description

ansible-playbook
=
Run Playbook
```

---

# One-Line Memory Map

```text
Playbook
    ↓
Play
    ↓
Hosts
    ↓
Tasks
    ↓
Modules
    ↓
SSH
    ↓
Managed Nodes
    ↓
Desired State
    ↓
Results
```

At this point you've completed the foundation that most "Ansible Beginner to Intermediate" courses cover.

The next major section is where Ansible becomes truly powerful:

```text
Variables
↓
Facts Variables
↓
Registered Variables
↓
Conditionals (when)
↓
Loops
↓
Tags
↓
Handlers
```

This is the stage where playbooks stop being static and start making decisions like a real automation system.

***

# Part 8: Variables, Facts, Registered Variables, Conditionals & Loops

## The Point Where Ansible Starts Thinking Instead of Just Executing

---

# Why We Need Variables

Imagine writing a playbook:

```yaml
Install nginx

Install nginx

Install nginx
```

Now company decides:

```text
Install Apache instead
```

You must edit everywhere.

Painful.

---

Ansible says:

```text
Store values in variables.

Change once.

Use everywhere.
```

---

# Real Life Analogy

Think of a school form.

Instead of writing:

```text
Malathi
Malathi
Malathi
Malathi
```

everywhere,

you write:

```text
Student_Name = Malathi
```

Then use:

```text
Student_Name
```

everywhere.

---

# Memory Trick

```text
Variable

=

Label On A Box
```

The label is not the thing.

It points to the thing.

---

# Chapter 1: Basic Variables

Example:

```yaml
---
- hosts: all

  vars:

    package_name: nginx

  tasks:

    - name: Install Package

      dnf:
        name: "{{ package_name }}"
        state: present
```

---

# Read Like English

```text
package_name

=

nginx

Install package_name
```

Which becomes:

```text
Install nginx
```

---

# Visual

```text
Variable

package_name
      |
      |
      v

    nginx
```

---

# Why Use Variables?

Without Variables

```yaml
nginx
nginx
nginx
nginx
```

---

With Variables

```yaml
package_name
package_name
package_name
```

Change one value:

```yaml
package_name: httpd
```

Entire playbook changes.

---

# Chapter 2: Variable Precedence (Beginner Version)

Suppose:

```yaml
package_name: nginx
```

and later:

```yaml
package_name: apache
```

Which wins?

---

Answer:

```text
Closest definition wins.
```

Think:

```text
School Rule

Principal

↓

Teacher

↓

Student
```

Most specific instruction wins.

---

For now remember:

```text
Host Variable

beats

Group Variable
```

because it's more specific.

---

# Chapter 3: Facts Variables

Remember Part 3?

We learned:

```bash
ansible all -m setup
```

collects facts.

---

Facts are automatically stored as variables.

---

Example Facts

```text
Hostname

OS

CPU

RAM

IP Address

Architecture
```

---

Visual

```text
Server

      |
      v

Gather Facts

      |
      v

Variables Created
```

---

# Example

```yaml
- name: Show Hostname

  debug:

    msg: "{{ ansible_hostname }}"
```

---

Output

```text
web-server
```

---

# Example

```yaml
- name: Show OS

  debug:

    msg: "{{ ansible_distribution }}"
```

Output:

```text
Amazon Linux

Ubuntu

CentOS
```

depending on server.

---

# Most Common Facts

---

## Hostname

```yaml
{{ ansible_hostname }}
```

---

## IP Address

```yaml
{{ ansible_default_ipv4.address }}
```

---

## OS

```yaml
{{ ansible_distribution }}
```

---

## Memory

```yaml
{{ ansible_memtotal_mb }}
```

---

## CPU Count

```yaml
{{ ansible_processor_vcpus }}
```

---

# Memory Trick

```text
Facts

=

Server Biography
```

Everything about the server.

---

# Chapter 4: Debug Module

Very important for learning.

---

Purpose:

```text
Print information.
```

---

Example

```yaml
- debug:

    msg: "Hello World"
```

---

Output

```text
Hello World
```

---

Example

```yaml
- debug:

    msg: "{{ ansible_hostname }}"
```

Output

```text
web-server
```

---

Think:

```text
Debug

=

Print Statement
```

like Python.

---

# Chapter 5: Registered Variables

One of the biggest interview topics.

---

# The Problem

Suppose:

```yaml
hostname
```

runs.

Result appears.

But how do we use that result later?

---

Answer:

```text
Register it.
```

---

# Real Life Analogy

Imagine exam results.

Teacher announces:

```text
95 Marks
```

Then forgets.

Useless.

---

Instead:

```text
Write it down.
```

Now you can use it later.

---

# Example

```yaml
- name: Get Hostname

  command: hostname

  register: host_output
```

---

Meaning:

```text
Run command

Save result

inside

host_output
```

---

Visual

```text
hostname command

      |
      v

web-server

      |
      v

host_output
```

---

# Using Registered Variable

```yaml
- debug:

    msg: "{{ host_output.stdout }}"
```

---

Output

```text
web-server
```

---

# Important

Register:

```text
Stores output

for later use
```

---

# Memory Trick

```text
Register

=

Save Result For Later
```

---

# Chapter 6: Conditionals (when)

Now Ansible starts making decisions.

---

Without Conditionals

```text
Do Everything
```

---

With Conditionals

```text
Do It Only If Needed
```

---

# Real Life Analogy

Mom says:

```text
If it's raining

Take umbrella
```

---

Not:

```text
Take umbrella always
```

---

That's a condition.

---

# Example

```yaml
- name: Install Apache

  dnf:
    name: httpd
    state: present

  when:

    ansible_distribution == "Amazon"
```

---

Meaning

```text
Only run

if OS = Amazon Linux
```

---

Visual

```text
Check OS

     |

     v

Amazon?

   /   \

 YES   NO

  |      |

Run   Skip
```

---

# Ubuntu Example

```yaml
when:

  ansible_distribution == "Ubuntu"
```

---

# Why Important?

Because:

```text
Different OS

Different Commands

Different Packages
```

---

# Interview Answer

What is `when`?

```text
Used to execute tasks
only when a condition evaluates true.
```

---

# Chapter 7: Multiple Conditions

Example

```yaml
when:

  ansible_distribution == "Ubuntu"
  and
  ansible_memtotal_mb > 1024
```

---

Meaning

```text
Ubuntu

AND

Memory > 1 GB
```

Both must be true.

---

# Chapter 8: Loops

Huge productivity feature.

---

# Problem

Without Loop

```yaml
Install Git

Install Nginx

Install Vim

Install Curl
```

Repeated code.

---

Ansible says:

```text
Repeat automatically.
```

---

# Real Life Analogy

Teacher says:

```text
Distribute books

to all students
```

instead of:

```text
Give book to A

Give book to B

Give book to C
```

---

# Example

```yaml
- name: Install Packages

  dnf:

    name: "{{ item }}"

    state: present

  loop:

    - git
    - nginx
    - vim
    - curl
```

---

# Visual

```text
Loop

 |
 +--- git

 |
 +--- nginx

 |
 +--- vim

 |
 +--- curl
```

---

Ansible does:

```text
Install git

Install nginx

Install vim

Install curl
```

automatically.

---

# What Is item?

Inside loop:

```yaml
{{ item }}
```

means:

```text
Current Object
```

---

Iteration 1

```text
item = git
```

---

Iteration 2

```text
item = nginx
```

---

Iteration 3

```text
item = vim
```

---

# Memory Trick

```text
item

=

Current Student
```

inside attendance list.

---

# Chapter 9: Loop With Users

Create multiple users.

---

Without Loop

```yaml
Create dev1

Create dev2

Create dev3
```

---

With Loop

```yaml
- name: Create Users

  user:

    name: "{{ item }}"

  loop:

    - dev1
    - dev2
    - dev3
```

---

Result

```text
dev1 created

dev2 created

dev3 created
```

---

# Chapter 10: Combining Register + When

This is where Ansible becomes powerful.

---

Example

Check disk space.

Save result.

Make decision.

---

```yaml
- command: df -h

  register: disk_output
```

---

Now:

```yaml
- debug:

    msg: "Disk Checked"

  when:

    disk_output.rc == 0
```

---

Meaning

```text
Run only if

previous command succeeded
```

---

Visual

```text
Command

     |

     v

Register Output

     |

     v

Condition Check

     |

     v

Run Or Skip
```

---

# Real DevOps Thinking

This is exactly how automation works.

```text
Check Something

↓

Store Result

↓

Make Decision

↓

Take Action
```

---

# Complete Example

```yaml
---
- hosts: all

  become: yes

  vars:

    packages:

      - git
      - nginx
      - vim

  tasks:

    - name: Install Packages

      dnf:

        name: "{{ item }}"

        state: present

      loop: "{{ packages }}"

    - name: Show Hostname

      debug:

        msg: "{{ ansible_hostname }}"
```

---

# Read Like English

```text
For all servers

Become root

Packages list:
Git
Nginx
Vim

Loop through list

Install each package

Display hostname
```

---

# Ultimate Revision Sheet

```text
Variables
=
Reusable Values

Facts
=
Server Information

Debug
=
Print Information

Register
=
Store Output

When
=
Condition

Loop
=
Repeat Work

Item
=
Current Loop Value
```

---

# One-Line Memory Map

```text
Variables
      ↓
Facts
      ↓
Debug
      ↓
Register
      ↓
When
      ↓
Loop
      ↓
Automation Logic
```

---

# The Big Picture So Far

```text
Part 1
What Is Ansible

      ↓

Part 2
Architecture

      ↓

Part 3
Internal Working

      ↓

Part 4
Inventory

      ↓

Part 5
Ad-Hoc Commands

      ↓

Part 6
Host Patterns

      ↓

Part 7
Playbooks

      ↓

Part 8
Variables + Decisions
```

Now you've reached the stage where Ansible is no longer just **executing commands**. It's **collecting information, making decisions, storing results, and automating intelligently**.

The next major topic is:

```text
Handlers
↓
Tags
↓
Templates (Jinja2)
↓
Files
↓
Roles
↓
Ansible Galaxy
```

This is where playbooks become production-grade and reusable.

***

# Part 9: Handlers, Tags, Templates, Files, Roles & Ansible Galaxy

## The Point Where Ansible Becomes Production-Grade

Up to Part 8, Ansible could:

```text
Connect to Servers
↓
Run Tasks
↓
Use Variables
↓
Make Decisions
↓
Use Loops
```

But real companies have a new problem:

```text
100 Servers
500 Tasks
50 Playbooks
20 Engineers
```

Without structure:

```text
Chaos
```

Part 9 solves that.

---

# Chapter 1: Handlers

## The Problem

Imagine:

```yaml
Install Nginx

Copy nginx.conf

Restart Nginx
```

---

Now imagine:

```text
nginx.conf didn't change
```

Do we really need:

```text
Restart Nginx?
```

No.

Restarting unnecessarily can cause:

```text
Brief Downtime

Connection Drops

Unnecessary Work
```

---

# Real Life Analogy

Imagine your room.

Mom says:

```text
Clean room only if dirty.
```

Not:

```text
Clean room every minute.
```

---

Handlers work exactly like that.

---

# What Is a Handler?

A Handler is:

```text
A special task

that runs only when notified.
```

---

Visual

```text
Task

  |
  v

Something Changed?

  |
 YES
  |
  v

Notify Handler

  |
  v

Run Handler
```

---

# Example

```yaml
tasks:

  - name: Copy Nginx Config

    copy:

      src: nginx.conf

      dest: /etc/nginx/nginx.conf

    notify: Restart Nginx

handlers:

  - name: Restart Nginx

    service:

      name: nginx

      state: restarted
```

---

# Read Like English

```text
Copy config

If config changed

Tell handler

Restart nginx
```

---

# Important

If file unchanged:

```text
Handler does NOT run.
```

---

# Memory Trick

```text
Handler

=

Fire Alarm
```

Alarm only triggers:

```text
When Smoke Exists
```

Not all day.

---

# Interview Question

### Why use Handlers?

Answer:

```text
To execute tasks only when
a change occurs.
```

---

# Chapter 2: Tags

## The Problem

Imagine Playbook:

```yaml
Install Git

Install Nginx

Create User

Deploy App

Restart Service
```

---

Today you only want:

```text
Deploy App
```

Not everything.

---

Without Tags:

```text
Entire Playbook Runs
```

---

With Tags:

```text
Run Specific Sections
```

---

# Real Life Analogy

Netflix Categories:

```text
Action

Comedy

Drama
```

You select category.

Not every movie.

---

# Example

```yaml
tasks:

  - name: Install Nginx

    dnf:

      name: nginx

      state: present

    tags:
      - install

  - name: Start Nginx

    service:

      name: nginx

      state: started

    tags:
      - service
```

---

# Run Only Install Tasks

```bash
ansible-playbook site.yml --tags install
```

---

Result

```text
Install Nginx
```

runs.

---

```text
Start Nginx
```

skipped.

---

# Run Multiple Tags

```bash
ansible-playbook site.yml --tags install,service
```

---

# Skip Tags

```bash
ansible-playbook site.yml --skip-tags service
```

---

# Memory Trick

```text
Tags

=

Bookmarks
```

Jump directly to section.

---

# Chapter 3: Templates (Jinja2)

This is where many people finally understand Ansible's power.

---

# Problem

Suppose you have:

```text
Web Server 1
IP = 10.0.1.10

Web Server 2
IP = 10.0.1.20

Web Server 3
IP = 10.0.1.30
```

Each server needs:

```text
Different Config Values
```

---

Without Templates

Create:

```text
config1.conf

config2.conf

config3.conf
```

Terrible.

---

Ansible says:

```text
Create One Template

Generate Many Files
```

---

# Real Life Analogy

Think of:

```text
School Certificate
```

Template:

```text
Certificate of Achievement

Awarded To: _______
```

Fill blank space differently.

---

# What Is Jinja2?

Jinja2 is:

```text
Template Engine
```

Used inside:

```text
.j2 files
```

---

# Example

Template:

```jinja2
Server Name: {{ ansible_hostname }}

IP Address: {{ ansible_default_ipv4.address }}
```

---

Generated Output

Server1:

```text
Server Name: web1

IP Address: 10.0.1.10
```

---

Server2:

```text
Server Name: web2

IP Address: 10.0.1.20
```

---

Same template.

Different output.

---

# Template Module

```yaml
- name: Create Config

  template:

    src: nginx.conf.j2

    dest: /etc/nginx/nginx.conf
```

---

Visual

```text
Template File

nginx.conf.j2

       |

       v

Variables

       |

       v

Final Config
```

---

# Memory Trick

```text
Template

=

Photocopy Form

+

Fill In Blanks
```

---

# Chapter 4: File Module vs Copy Module vs Template Module

This is a favorite interview question.

---

# File Module

Purpose:

```text
Manage Files/Folders
```

Example:

```yaml
file:
  path: /tmp/demo
  state: directory
```

Creates folder.

---

# Copy Module

Purpose:

```text
Copy Static File
```

Example:

```yaml
copy:
  src: app.conf
  dest: /tmp/app.conf
```

Exact copy.

---

# Template Module

Purpose:

```text
Copy Dynamic File
```

Example:

```yaml
template:
  src: app.conf.j2
  dest: /tmp/app.conf
```

Variables replaced.

---

# Comparison

```text
File
=
Manage

Copy
=
Static Copy

Template
=
Dynamic Copy
```

---

# Chapter 5: Roles

This is one of the biggest Ansible concepts.

---

# The Problem

Imagine:

```yaml
500 Lines

1000 Lines

2000 Lines
```

in one playbook.

---

Nightmare.

---

# Real Life Analogy

Imagine building a house.

Do you hire:

```text
One Person
```

for everything?

No.

---

You have:

```text
Electrician

Plumber

Painter

Carpenter
```

Each has a role.

---

Ansible Roles follow the same idea.

---

# What Is a Role?

A Role is:

```text
Reusable Project Structure

for a specific purpose.
```

---

Example Roles

```text
nginx-role

mysql-role

docker-role

java-role
```

---

Each role handles one responsibility.

---

# Role Structure

```text
nginx-role

|
+--- tasks

|
+--- handlers

|
+--- templates

|
+--- files

|
+--- vars

|
+--- defaults
```

---

# Why This Structure Exists

Without Roles

```text
Everything Mixed Together
```

---

With Roles

```text
Organized

Reusable

Maintainable
```

---

# Visual

```text
Project

 |
 +---- nginx-role

 |
 +---- mysql-role

 |
 +---- docker-role
```

---

# Memory Trick

```text
Role

=

Department
```

inside company.

---

# Using Roles

Playbook:

```yaml
---
- hosts: web

  roles:

    - nginx-role
```

---

Meaning

```text
Execute everything

inside nginx-role
```

---

# Chapter 6: Ansible Galaxy

Now imagine:

```text
Need Docker Role
```

Do you build from scratch?

Maybe.

But someone already built it.

---

This leads to:

```text
Ansible Galaxy
```

---

# What Is Ansible Galaxy?

Think:

```text
GitHub

for Ansible Roles
```

---

Official entity: Ansible Galaxy

---

Purpose:

```text
Download Reusable Roles

Created By Community
```

---

# Real Life Analogy

Instead of writing:

```text
Calculator App
```

from scratch,

install existing app.

---

Same idea.

---

# Example

Install Role:

```bash
ansible-galaxy install geerlingguy.nginx
```

---

Meaning:

```text
Download nginx role
```

---

Use Role

```yaml
roles:

  - geerlingguy.nginx
```

---

# Visual

```text
Ansible Galaxy

      |

      v

Download Role

      |

      v

Use In Playbook
```

---

# Why Companies Love Roles

Because:

```text
Reusable

Standardized

Tested

Easy To Maintain
```

---

# Real Production Flow

Imagine deploying Nginx.

Without Roles:

```text
Install Package

Copy Config

Create Directories

Start Service

Enable Service

Restart On Change
```

inside one giant file.

---

With Roles:

```yaml
roles:

  - nginx-role
```

Done.

---

# The Big Picture

```text
Task
=
Single Action

Handler
=
Runs When Changed

Tag
=
Run Specific Tasks

Template
=
Dynamic File

Role
=
Reusable Component

Galaxy
=
Role Marketplace
```

---

# How Everything Connects

```text
Playbook

    |
    v

Roles

    |
    +---- Tasks

    |
    +---- Variables

    |
    +---- Templates

    |
    +---- Files

    |
    +---- Handlers

    |
    +---- Defaults
```

---

# Ultimate Revision Sheet

```text
Handler
=
Run Only When Notified

Tag
=
Run Selected Tasks

Template
=
Dynamic File Generator

File Module
=
Manage Files/Folders

Copy Module
=
Copy Static Files

Template Module
=
Copy Dynamic Files

Role
=
Reusable Project Structure

Ansible Galaxy
=
Role Repository
```

---

# One-Line Memory Map

```text
Handlers
     ↓
Tags
     ↓
Templates
     ↓
Files
     ↓
Roles
     ↓
Galaxy
     ↓
Production Automation
```

---

## What You Know Now (Parts 1–9)

```text
Part 1 → What is Ansible
Part 2 → Components
Part 3 → Internal Working
Part 4 → Inventory & Variables
Part 5 → Ad-Hoc Commands
Part 6 → Host Patterns
Part 7 → Playbooks
Part 8 → Variables, Facts, Loops, Conditions
Part 9 → Handlers, Tags, Templates, Roles, Galaxy
```

The next logical section is **Part 10: Ansible Vault, Error Handling, Blocks, Imports vs Includes, and Production Best Practices** — the topics that separate a learner from someone running Ansible safely in real enterprise environments.

***

# Part 10: Ansible Vault, Error Handling, Blocks, Imports vs Includes & Production Best Practices

## The Difference Between "I Know Ansible" and "I Can Run Ansible In Production"

Up to Part 9, you learned how to:

```text
Connect Servers
↓
Run Tasks
↓
Use Variables
↓
Use Loops
↓
Use Conditions
↓
Use Roles
↓
Use Templates
```

Now comes the question:

```text
What happens when things go wrong?
```

Because in real production:

```text
Servers Fail

Passwords Exist

Tasks Break

Networks Disconnect

Configurations Crash
```

Part 10 is about surviving those situations.

---

# Chapter 1: Why Ansible Vault Exists

Imagine your playbook contains:

```yaml
db_password: MySecret123
```

Looks innocent.

---

But your playbook is stored in:

```text
GitHub

GitLab

Bitbucket
```

Now everyone can see:

```text
Database Password
```

Very dangerous.

---

# Real Life Analogy

Imagine writing:

```text
ATM PIN = 1234
```

on the front of your wallet.

Bad idea.

---

Instead:

```text
Lock It
```

That's exactly what Vault does.

---

# What Is Ansible Vault?

Vault is:

```text
Ansible's Encryption System
```

Used to protect:

```text
Passwords

API Keys

SSH Secrets

Tokens

Certificates
```

---

# Visual

```text
Secret Data

     |
     v

Ansible Vault

     |
     v

Encrypted Data

$ANSIBLE_VAULT....
```

---

# Example

Before Vault

```yaml
db_password: MySecret123
```

---

After Vault

```text
$ANSIBLE_VAULT;1.1;AES256

6135646437383439...
```

Unreadable.

---

# Memory Trick

```text
Vault

=

Bank Locker
```

---

# Creating Vault File

```bash
ansible-vault create secrets.yml
```

---

Flow

```text
Enter Password

↓

Open Editor

↓

Write Secrets

↓

Save

↓

Encrypted
```

---

# Viewing Vault

```bash
ansible-vault view secrets.yml
```

---

# Editing Vault

```bash
ansible-vault edit secrets.yml
```

---

# Encrypt Existing File

```bash
ansible-vault encrypt vars.yml
```

---

# Decrypt

```bash
ansible-vault decrypt vars.yml
```

---

# Running Playbook With Vault

```bash
ansible-playbook site.yml --ask-vault-pass
```

---

Meaning

```text
Ask Vault Password

↓

Decrypt

↓

Execute
```

---

# Interview Question

Why use Vault?

Answer:

```text
To securely store sensitive data
such as passwords, API keys,
and secrets in encrypted form.
```

---

# Chapter 2: The Problem With Failures

Suppose:

```yaml
Task 1 → Success

Task 2 → Success

Task 3 → Failed

Task 4 → ?
```

---

What should happen?

By default:

```text
Playbook Stops
```

---

Visual

```text
Task 1 ✔

Task 2 ✔

Task 3 ✖

STOP
```

---

This protects systems.

Because:

```text
Continuing may cause damage.
```

---

# Chapter 3: ignore_errors

Sometimes failure is acceptable.

---

Example

Remove file.

```yaml
- name: Delete File

  file:

    path: /tmp/demo.txt

    state: absent
```

---

What if file doesn't exist?

Maybe you don't care.

---

Use:

```yaml
ignore_errors: yes
```

---

Example

```yaml
- name: Delete File

  file:

    path: /tmp/demo.txt

    state: absent

  ignore_errors: yes
```

---

Meaning

```text
If this task fails

Continue anyway
```

---

Visual

```text
Task Failed

     |

     v

ignore_errors

     |

     v

Continue
```

---

# Memory Trick

```text
ignore_errors

=

Keep Walking
```

---

# Chapter 4: failed_when

One of the coolest Ansible features.

---

Normally:

```text
Return Code 0

=

Success
```

---

But sometimes:

```text
Command returns 0

Yet output indicates problem
```

---

You can define your own failure.

---

Example

```yaml
- command: cat app.log

  register: result

  failed_when:

    "'ERROR' in result.stdout"
```

---

Meaning

```text
If output contains ERROR

Treat task as failed
```

---

Visual

```text
Command Output

      |

      v

Contains ERROR?

    /     \

 YES      NO

Fail    Success
```

---

# Memory Trick

```text
failed_when

=

My Failure Rules
```

---

# Chapter 5: changed_when

Sometimes command changes nothing.

But Ansible reports:

```text
changed
```

---

You can override.

---

Example

```yaml
changed_when: false
```

---

Meaning

```text
Never report changed
```

---

Useful for:

```text
Checks

Validation Commands

Read-Only Tasks
```

---

# Chapter 6: Blocks

Now things get powerful.

---

# Real Life Analogy

Imagine:

```text
Pack Laptop

Pack Charger

Pack Mouse
```

These belong together.

---

Instead of handling separately:

```text
Group Them
```

---

That's a Block.

---

Example

```yaml
tasks:

  - block:

      - name: Install Nginx

        dnf:

          name: nginx

          state: present

      - name: Start Nginx

        service:

          name: nginx

          state: started
```

---

Visual

```text
Block

 |
 +--- Task 1

 |
 +--- Task 2
```

---

# Why Blocks Matter

Because blocks allow:

```text
Rescue

Always
```

---

Which gives:

```text
Try

Catch

Finally
```

similar to programming.

---

# Chapter 7: Rescue

Imagine:

```text
Install Application
```

fails.

You want recovery action.

---

Example

```yaml
tasks:

  - block:

      - name: Install App

        command: /bad-command

    rescue:

      - name: Recovery Message

        debug:

          msg: "Installation Failed"
```

---

Flow

```text
Try Task

    |

Success? ---- YES ---- Done

    |

    NO

    |

Rescue Runs
```

---

# Real Life Analogy

Car breaks down.

---

Rescue:

```text
Call Tow Truck
```

---

# Memory Trick

```text
rescue

=

Backup Plan
```

---

# Chapter 8: Always

Runs no matter what.

---

Example

```yaml
always:

  - debug:

      msg: "Cleanup Running"
```

---

Visual

```text
Task

 |

Success OR Failure

 |

Always Runs
```

---

# Real Life Analogy

Exam finishes.

---

Regardless of score:

```text
Return Question Paper
```

Always happens.

---

# Complete Flow

```text
Block

 |

Success?
 /    \

YES   NO
 |      |

Done  Rescue

   \   /

    \ /

   Always
```

---

# Chapter 9: Import vs Include

Huge Interview Topic.

---

# Problem

Large Playbooks become:

```text
1000+

2000+

5000 Lines
```

Impossible to manage.

---

Need:

```text
Split Files
```

---

# Import

Think:

```text
Compile Time
```

---

Example

```yaml
- import_tasks: install.yml
```

---

Meaning

```text
Load Before Execution Starts
```

---

Visual

```text
Main Playbook

      |

      v

Import

      |

      v

Merge Files

      |

      v

Execute
```

---

# Include

Think:

```text
Runtime
```

---

Example

```yaml
- include_tasks: install.yml
```

---

Meaning

```text
Load While Running
```

---

Visual

```text
Execution

    |

    v

Need File?

    |

    v

Load Now
```

---

# Memory Trick

```text
Import

=

Read Entire Book First

Include

=

Read Chapter When Needed
```

---

# Interview Answer

Import:

```text
Static
Loaded before execution
```

Include:

```text
Dynamic
Loaded during execution
```

---

# Chapter 10: Production Best Practices

This section separates juniors from professionals.

---

# Best Practice 1

Never hardcode secrets.

Bad:

```yaml
password: admin123
```

---

Good:

```text
Vault
```

---

# Best Practice 2

Use Roles

Bad:

```text
3000-line playbook
```

---

Good:

```text
Roles

Nginx Role

Docker Role

Java Role
```

---

# Best Practice 3

Use Meaningful Names

Bad:

```yaml
- name: task1
```

---

Good:

```yaml
- name: Install Nginx Package
```

---

# Best Practice 4

Prefer Modules Over Shell

Bad:

```yaml
shell: dnf install nginx
```

---

Good:

```yaml
dnf:
  name: nginx
  state: present
```

---

Why?

Because modules are:

```text
Idempotent

Safer

Cleaner
```

---

# Best Practice 5

Use Handlers

Bad:

```text
Restart Service Every Time
```

---

Good:

```text
Restart Only When Changed
```

---

# Best Practice 6

Use Variables

Avoid:

```yaml
nginx
nginx
nginx
nginx
```

---

Use:

```yaml
package_name: nginx
```

---

# Best Practice 7

Test Before Production

Flow:

```text
Dev

↓

Test

↓

Stage

↓

Production
```

Never:

```text
Direct Production
```

---

# The Enterprise Mental Model

A professional Ansible workflow looks like:

```text
Inventory
      ↓
Variables
      ↓
Vault
      ↓
Playbooks
      ↓
Roles
      ↓
Templates
      ↓
Handlers
      ↓
Testing
      ↓
Production
```

---

# Ultimate Revision Sheet

```text
Vault
=
Encrypted Secrets

ignore_errors
=
Continue On Failure

failed_when
=
Custom Failure Logic

changed_when
=
Custom Change Logic

Block
=
Task Group

Rescue
=
Recovery Plan

Always
=
Runs No Matter What

Import
=
Static Load

Include
=
Dynamic Load
```

---

# One-Line Memory Map

```text
Vault
   ↓
Errors
   ↓
Blocks
   ↓
Rescue
   ↓
Always
   ↓
Import
   ↓
Include
   ↓
Production Best Practices
```

---

## What You Know After Parts 1–10

```text
Part 1 → What Is Ansible
Part 2 → Architecture
Part 3 → Internal Working
Part 4 → Inventory
Part 5 → Ad-Hoc Commands
Part 6 → Host Patterns
Part 7 → Playbooks
Part 8 → Variables & Logic
Part 9 → Roles & Templates
Part 10 → Security & Production Features
```

At this point you know **most of the Ansible concepts asked in DevOps interviews**.

The final advanced layer would be:

```text
Part 11
↓
Ansible Execution Strategy
Forks
Serial
Rolling Updates
Delegation
Local Actions
Async & Poll
Facts Caching
Dynamic Inventory
AWS Inventory
Performance Optimization
Real Production Deployment Patterns
```

This is where Ansible scales from managing **5 servers** to **5,000 servers**.

***


# Part 11: Advanced Ansible Execution, Scaling & Real Production Deployments

## How Ansible Manages 5,000 Servers Without Creating Chaos

Up to Part 10 you've learned:

```text
Inventory
↓
Modules
↓
Playbooks
↓
Variables
↓
Roles
↓
Templates
↓
Vault
↓
Error Handling
```

Now we answer:

```text
What happens when
5000 servers exist?
```

Because running:

```bash
ansible all -m ping
```

against 5000 servers is very different from:

```text
3 EC2 Instances
```

---

# Big Picture

Most beginners think:

```text
Ansible

↓

Server 1

↓

Server 2

↓

Server 3
```

One at a time.

Wrong.

---

Ansible uses:

```text
Parallelism
```

---

Visual

```text
Control Node

      |

      +---- Server 1

      +---- Server 2

      +---- Server 3

      +---- Server 4

      +---- Server 5
```

All together.

---

# Chapter 1: Forks

## What Is a Fork?

A fork means:

```text
Parallel Worker
```

---

Real Life Analogy

Imagine delivering parcels.

One delivery person:

```text
100 Houses

↓

Very Slow
```

---

10 delivery people:

```text
100 Houses

↓

Much Faster
```

---

Same concept.

---

Visual

```text
Ansible

 |

 +--- Worker 1

 +--- Worker 2

 +--- Worker 3

 +--- Worker 4

 +--- Worker 5
```

---

# Default Fork Value

Usually:

```text
5
```

Meaning:

```text
5 Servers At Same Time
```

---

Example

```ini
forks=20
```

inside:

```ini
ansible.cfg
```

---

Meaning

```text
20 Parallel Connections
```

---

# Why Not Use 1000 Forks?

Because:

```text
CPU Usage

Memory Usage

Network Usage
```

increase.

---

Visual

```text
Too Few Forks

Slow

------------

Too Many Forks

Control Node Overloaded

------------

Balanced Forks

Best Performance
```

---

# Memory Trick

```text
Forks

=

Workers In Factory
```

More workers.

More parallel work.

---

# Chapter 2: Serial

One of the most important production concepts.

---

# The Problem

Imagine:

```text
100 Web Servers
```

running production traffic.

---

You run:

```yaml
Restart Nginx
```

on all servers.

---

Result:

```text
100 Servers Restart

↓

Website Down
```

Disaster.

---

Need:

```text
Gradual Updates
```

---

This is:

```text
Serial
```

---

# Example

```yaml
---
- hosts: web

  serial: 10

  tasks:
```

---

Meaning:

```text
Update 10 Servers

↓

Then Next 10

↓

Then Next 10
```

---

Visual

```text
100 Servers

Batch 1
1-10

↓

Batch 2
11-20

↓

Batch 3
21-30
```

---

# Real Life Analogy

Teacher checking papers.

Not:

```text
100 Students At Once
```

Instead:

```text
10 At A Time
```

---

# Memory Trick

```text
Serial

=

Batch Processing
```

---

# Interview Question

Why use serial?

Answer:

```text
To perform rolling updates
and reduce downtime.
```

---

# Chapter 3: Rolling Updates

Built using:

```text
Serial
```

---

# What Is Rolling Update?

Instead of:

```text
Update Everything
```

You do:

```text
Few Servers

↓

Verify

↓

Few More

↓

Verify
```

---

Visual

```text
Server Group

100 Servers

 |

 +--- Update 10

 |

 +--- Verify

 |

 +--- Update Next 10

 |

 +--- Verify
```

---

# Real Production Example

Updating:

```text
Nginx

Docker

Java

Application Version
```

---

Without Rolling Update:

```text
Huge Risk
```

---

With Rolling Update:

```text
Controlled Risk
```

---

# Memory Trick

```text
Rolling Update

=

Changing Tires

One At A Time
```

Not all four at once.

---

# Chapter 4: Delegation

Extremely important interview topic.

---

# Problem

Suppose playbook targets:

```text
Web Servers
```

But one task must run on:

```text
Control Node
```

---

How?

---

Using:

```yaml
delegate_to:
```

---

Example

```yaml
- name: Update Load Balancer

  command: update-lb

  delegate_to: localhost
```

---

Meaning

```text
Play Targets Web Servers

But This Task

Runs On Control Node
```

---

Visual

```text
Target Host

Web Server

      |

Task Delegated

      |

      v

Control Node
```

---

# Real Production Example

Before patching:

```text
Remove Server

From Load Balancer
```

After patching:

```text
Add Back
```

---

Load Balancer task runs:

```text
Control Node
```

not web server.

---

# Memory Trick

```text
Delegate

=

Assign Work To Someone Else
```

---

# Chapter 5: Local Actions

Closely related.

---

Instead of:

```yaml
delegate_to: localhost
```

you may see:

```yaml
local_action
```

---

Example

```yaml
- local_action:

    command echo "Deployment Started"
```

---

Meaning:

```text
Run Locally

Not Remotely
```

---

Visual

```text
Normal Task

Control Node

↓

Remote Server

------------

Local Action

Control Node

↓

Control Node
```

---

# Chapter 6: Async Tasks

Huge production feature.

---

# The Problem

Imagine installing:

```text
Large Software

Database

Kernel Update
```

takes:

```text
30 Minutes
```

---

Normal execution:

```text
Wait
Wait
Wait
Wait
```

---

Ansible supports:

```text
Async Execution
```

---

Example

```yaml
- name: Long Task

  command: big-install.sh

  async: 3600

  poll: 0
```

---

Meaning

```text
Start Task

Don't Wait
```

---

Visual

```text
Start Task

     |

     v

Background

     |

     v

Continue
```

---

# Real Life Analogy

Start washing machine.

Do other work.

Return later.

---

# Memory Trick

```text
Async

=

Background Job
```

---

# Chapter 7: Poll

Poll controls:

```text
How Often To Check
```

---

Example

```yaml
async: 600

poll: 10
```

---

Meaning

```text
Check Every 10 Seconds
```

---

Visual

```text
Task Running

↓

10 sec

↓

Check

↓

10 sec

↓

Check
```

---

# Chapter 8: Dynamic Inventory

One of the biggest cloud concepts.

---

# Static Inventory Problem

Inventory:

```ini
web1

web2

web3
```

---

What if AWS creates:

```text
web4

web5

web6
```

today?

---

Inventory outdated.

---

Need:

```text
Automatic Discovery
```

---

This is:

```text
Dynamic Inventory
```

---

# Real Life Analogy

Static Contact List:

```text
Must Update Manually
```

---

Dynamic Contact List:

```text
Automatically Updated
```

---

# Visual

```text
AWS

     |

     v

EC2 Instances

     |

     v

Dynamic Inventory

     |

     v

Ansible
```

---

# Chapter 9: AWS Dynamic Inventory

Massively important for DevOps.

---

Instead of:

```ini
web1

web2

web3
```

Ansible asks AWS:

```text
What EC2 Instances Exist?
```

AWS replies:

```text
web1

web2

web3

web4

web5
```

---

Automatically.

---

Visual

```text
Ansible

    |

    v

AWS API

    |

    v

EC2 Metadata

    |

    v

Inventory Generated
```

---

# Why Companies Love This

Servers are:

```text
Created

Destroyed

Auto-Scaled
```

daily.

---

Static inventory becomes:

```text
Maintenance Nightmare
```

---

# Chapter 10: Facts Caching

Huge optimization.

---

Remember:

```text
Gather Facts
```

runs every playbook.

---

On:

```text
5000 Servers
```

this becomes expensive.

---

Solution:

```text
Cache Facts
```

---

Visual

```text
First Run

Server

↓

Gather Facts

↓

Cache

------------

Second Run

Read Cache

↓

Skip Collection
```

---

# Memory Trick

```text
Facts Cache

=

Saved Notes
```

instead of re-reading book.

---

# Chapter 11: Strategy

Ansible decides:

```text
How Tasks Execute
```

using strategy.

---

Default:

```text
linear
```

---

Meaning:

```text
Task 1 On All Servers

↓

Task 2 On All Servers

↓

Task 3 On All Servers
```

---

Visual

```text
Task 1

Server1
Server2
Server3

↓

Task 2

Server1
Server2
Server3
```

---

# Free Strategy

Another strategy:

```yaml
strategy: free
```

---

Meaning:

```text
Fast Servers Continue

Slow Servers Don't Block
```

---

Visual

```text
Server1 Finished

↓

Moves To Next Task

Server2 Still Busy
```

---

Useful for:

```text
Large Environments
```

---

# Chapter 12: Real Enterprise Deployment Flow

This is what companies actually do.

---

Step 1

```text
Dynamic Inventory
```

Discover servers.

---

Step 2

```text
Gather Facts
```

Collect information.

---

Step 3

```text
Load Variables
```

---

Step 4

```text
Decrypt Vault
```

---

Step 5

```text
Remove Node

From Load Balancer
```

(delegate)

---

Step 6

```text
Deploy Application
```

---

Step 7

```text
Restart Service
```

(handler)

---

Step 8

```text
Health Check
```

---

Step 9

```text
Add Node Back
```

---

Step 10

```text
Move To Next Batch
```

(serial)

---

Visual

```text
Inventory
     ↓
Facts
     ↓
Vault
     ↓
Delegate
     ↓
Deploy
     ↓
Handler
     ↓
Health Check
     ↓
Add Back To LB
     ↓
Next Batch
```

---

# Ultimate Revision Sheet

```text
Forks
=
Parallel Workers

Serial
=
Batch Updates

Rolling Update
=
Gradual Deployment

Delegate
=
Run Elsewhere

Local Action
=
Run On Controller

Async
=
Background Task

Poll
=
Check Progress

Dynamic Inventory
=
Auto Discovery

AWS Inventory
=
Read EC2 Automatically

Facts Cache
=
Reuse Collected Facts

Strategy
=
Execution Method
```

---

# One-Line Memory Map

```text
Forks
   ↓
Parallelism
   ↓
Serial
   ↓
Rolling Updates
   ↓
Delegation
   ↓
Async
   ↓
Dynamic Inventory
   ↓
Fact Caching
   ↓
Enterprise Deployments
```

---

# The Entire Ansible Journey (Parts 1–11)

```text
Part 1  → What Is Ansible
Part 2  → Components
Part 3  → Internal Working
Part 4  → Inventory & Variables
Part 5  → Ad-Hoc Commands
Part 6  → Host Patterns
Part 7  → Playbooks
Part 8  → Variables, Facts, Loops, Conditions
Part 9  → Handlers, Tags, Templates, Roles
Part 10 → Vault, Error Handling, Blocks
Part 11 → Scaling, Dynamic Inventory, Enterprise Deployments
```

## Final Mental Model

If someone wakes you up at 3 AM and asks:

```text
What is Ansible?
```

Think:

```text
Inventory
     ↓
Hosts
     ↓
Playbooks
     ↓
Tasks
     ↓
Modules
     ↓
Variables
     ↓
Conditions
     ↓
Roles
     ↓
Templates
     ↓
Handlers
     ↓
Vault
     ↓
Deployments
     ↓
Automation At Scale
```

That single flow explains almost the entire Ansible ecosystem from beginner to advanced DevOps engineer level.

***

# Part 12: Ansible Interview Mastery & Real Production Scenarios

## The "Why" Behind Every Ansible Concept

Up to Part 11, you learned:

```text
What Ansible Is

How It Works

How To Write Playbooks

How To Use Roles

How To Use Vault

How To Scale
```

Now comes the most important stage.

Most people know:

```text
Commands
```

But interviewers ask:

```text
Why?
```

and

```text
What happens internally?
```

Part 12 focuses on thinking like a DevOps Engineer.

---

# Scenario 1: Why Not Use Shell Commands Everywhere?

Interviewer:

```text
Why not use shell instead of modules?
```

---

Many beginners answer:

```text
Because modules are better.
```

Weak answer.

---

Real Answer

Imagine:

```bash
shell: yum install nginx
```

Ansible executes blindly.

---

Visual

```text
Shell

↓

Run Command

↓

Hope It Works
```

---

But module:

```yaml
dnf:
  name: nginx
  state: present
```

does:

```text
Check Current State

↓

Compare Desired State

↓

Change Only If Needed
```

---

Visual

```text
Desired State

↓

Current State

↓

Difference?

YES → Fix

NO  → Skip
```

---

Therefore modules provide:

```text
Idempotency

Structured Output

Error Handling

Cross Platform Support
```

---

Interview Answer

```text
Modules are preferred because they are idempotent,
easier to maintain, provide structured output,
and understand the desired state.
```

---

# Scenario 2: Why Is Ansible Agentless?

Interviewer:

```text
Why doesn't Ansible require agents?
```

---

Real Problem

Tools like:

```text
Puppet

Chef

Salt (agent mode)
```

often require:

```text
Agent Installation

Agent Maintenance

Agent Upgrades
```

---

Visual

```text
100 Servers

↓

Install 100 Agents

↓

Maintain 100 Agents
```

---

Ansible avoids that.

---

Flow

```text
Control Node

↓

SSH

↓

Execute

↓

Disconnect
```

---

Benefits

```text
Simpler

Less Maintenance

Lower Resource Usage
```

---

Memory Trick

```text
Ansible

=

Temporary Visitor

Not Permanent Resident
```

---

# Scenario 3: What Happens During Playbook Execution?

Interview Favorite.

---

Suppose:

```bash
ansible-playbook site.yml
```

runs.

---

Internal Flow

```text
1 Read Playbook

↓

2 Read Inventory

↓

3 Identify Hosts

↓

4 Gather Facts

↓

5 Load Variables

↓

6 Load Roles

↓

7 Connect Using SSH

↓

8 Execute Tasks

↓

9 Trigger Handlers

↓

10 Generate Summary
```

---

Visual

```text
Playbook

↓

Inventory

↓

Hosts

↓

Facts

↓

Variables

↓

Tasks

↓

Handlers

↓

Results
```

---

# Scenario 4: Why Roles?

Interviewer:

```text
Why use Roles?
```

---

Imagine:

```text
Nginx Setup

500 Lines
```

---

Used in:

```text
Dev

Test

UAT

Production
```

---

Without Roles

```text
Copy Paste

Copy Paste

Copy Paste
```

Nightmare.

---

With Role

```text
nginx-role
```

Reusable everywhere.

---

Visual

```text
Role

     |

     +---- Dev

     +---- Test

     +---- UAT

     +---- Prod
```

---

Interview Answer

```text
Roles improve reusability,
maintainability,
organization,
and standardization.
```

---

# Scenario 5: Why Handlers?

Imagine:

```yaml
Copy Config

Restart Service
```

---

Run playbook 20 times.

---

Without Handlers

```text
Restart

Restart

Restart

Restart
```

---

Potential downtime.

---

Handler Logic

```text
Config Changed?

YES → Restart

NO → Skip
```

---

Interview Answer

```text
Handlers ensure services restart
only when a change occurs.
```

---

# Scenario 6: Why Dynamic Inventory?

Static Inventory:

```ini
web1
web2
web3
```

---

AWS Auto Scaling creates:

```text
web4
web5
web6
```

---

Inventory becomes wrong.

---

Dynamic Inventory:

```text
Ask Cloud Provider

↓

Generate Inventory
```

Automatically.

---

Visual

```text
AWS

↓

API

↓

Inventory

↓

Ansible
```

---

Interview Answer

```text
Dynamic inventory automatically discovers
resources from cloud providers,
avoiding manual inventory maintenance.
```

---

# Scenario 7: Why Serial?

Suppose:

```text
100 Production Servers
```

Need restart.

---

Without Serial

```text
100 Restart Simultaneously

↓

Outage
```

---

With

```yaml
serial: 10
```

---

Flow

```text
10 Servers

↓

Verify

↓

Next 10

↓

Verify
```

---

Interview Answer

```text
Serial enables rolling updates
and minimizes downtime.
```

---

# Scenario 8: Why Vault?

Imagine:

```yaml
db_password: admin123
```

inside Git repository.

---

Huge security risk.

---

Vault

```text
Encrypt Secrets
```

---

Visual

```text
Password

↓

Vault

↓

Encrypted Data
```

---

Interview Answer

```text
Vault secures sensitive data such as
passwords, tokens, API keys, and secrets.
```

---

# Scenario 9: Register vs Facts

Interviewers love this.

---

Facts

```text
Automatically Collected
```

Examples:

```text
Hostname

OS

RAM

CPU
```

---

Register

```text
User Captures Output
```

Example:

```yaml
register: result
```

---

Visual

```text
Facts

↓

System Information

------------

Register

↓

Task Output
```

---

Memory Trick

```text
Facts

=

Server Talks About Itself

Register

=

You Save Result
```

---

# Scenario 10: Import vs Include

Another favorite.

---

Import

```text
Static

Loaded Before Execution
```

---

Include

```text
Dynamic

Loaded During Execution
```

---

Visual

```text
Import

↓

Build Entire House Plan

Before Construction

-------------------

Include

↓

Open Next Page

When Needed
```

---

# Scenario 11: Forks

Interviewer:

```text
What are forks?
```

---

Answer:

```text
Forks determine the number of parallel
connections Ansible can execute simultaneously.
```

---

Visual

```text
Forks = 5

Server1
Server2
Server3
Server4
Server5
```

processed together.

---

# Scenario 12: Async

Problem:

```text
Database Upgrade

45 Minutes
```

---

Without Async

```text
Wait
```

---

With Async

```text
Start

↓

Background

↓

Continue
```

---

Interview Answer

```text
Async allows long-running tasks
to execute without blocking playbook execution.
```

---

# The Most Important DevOps Deployment Interview Question

## Explain How You Would Deploy an Application Using Ansible

Expected Flow:

```text
1 Dynamic Inventory

↓

2 Gather Facts

↓

3 Load Variables

↓

4 Decrypt Vault

↓

5 Remove Node From LB

↓

6 Deploy Artifact

↓

7 Update Config

↓

8 Restart Service

↓

9 Health Check

↓

10 Add Back To LB

↓

11 Continue Next Batch
```

---

Visual

```text
Load Balancer

      |

Remove Server

      |

Deploy

      |

Restart

      |

Health Check

      |

Add Back

      |

Next Server
```

---

# Real Production Thinking

Most beginners think:

```text
Ansible

=

Run Commands
```

Wrong.

---

Real answer:

```text
Ansible

=

Desired State Automation Platform
```

---

Everything revolves around:

```text
Current State

↓

Desired State

↓

Difference

↓

Fix Difference
```

---

# Ultimate Interview Revision Sheet

```text
Inventory
=
List Of Servers

Playbook
=
Automation Recipe

Task
=
Single Action

Module
=
Work Unit

Role
=
Reusable Component

Handler
=
Triggered Action

Variable
=
Reusable Value

Fact
=
System Information

Register
=
Stored Output

Loop
=
Repeat Tasks

When
=
Condition

Vault
=
Encrypted Secrets

Serial
=
Rolling Deployment

Forks
=
Parallel Workers

Dynamic Inventory
=
Auto Discovery

Async
=
Background Execution

Delegate
=
Run Elsewhere
```

---

# The Final Mental Model

```text
Inventory
      ↓
Hosts
      ↓
SSH
      ↓
Facts
      ↓
Variables
      ↓
Playbooks
      ↓
Tasks
      ↓
Modules
      ↓
Roles
      ↓
Templates
      ↓
Handlers
      ↓
Vault
      ↓
Serial
      ↓
Deployments
      ↓
Automation At Scale
```

***


# Part 13: Terraform + AWS Lab Setup Master Notes

## Before Ansible Can Manage Servers, Servers Must Exist

---

# The Big Question

People learn:

```text
Ansible manages servers.
```

But then:

```text
Where did servers come from?
```

Good question.

---

Before Ansible:

```text
Need Infrastructure
```

Infrastructure means:

```text
EC2 Instances

Security Groups

Key Pairs

Networking
```

---

# Real Life Analogy

Imagine:

```text
Teacher Wants To Teach
```

But:

```text
No Classroom Exists
```

First:

```text
Build Classroom
```

Then:

```text
Teach Students
```

---

Same thing.

```text
Terraform
=
Build Infrastructure

Ansible
=
Configure Infrastructure
```

---

# Visual

```text
Terraform
      |
      v

Create EC2

      |
      v

Create Security Group

      |
      v

Create Key Pair

      |
      v

Servers Ready

      |
      v

Ansible Starts
```

---

# Why Terraform First?

Without Terraform:

```text
Login AWS

Click EC2

Click Launch

Choose AMI

Choose Security Group

Choose Key Pair
```

Every time.

Very slow.

---

Terraform says:

```text
Write Once

Create Automatically
```

---

# Memory Trick

```text
Terraform

=

Construction Company

Ansible

=

Interior Designer
```

---

# Typical Lab Architecture

Your notes used:

```text
Control Node

Managed Node 1

Managed Node 2
```

---

Visual

```text
AWS

 |

 +--- Control Node

 |

 +--- App Server

 |

 +--- Web Server
```

---

# Why Multiple Servers?

Because Ansible exists to manage:

```text
Many Servers
```

not:

```text
One Server
```

---

# EC2 Instance

Think:

```text
Virtual Computer
```

inside AWS.

---

Visual

```text
Laptop

↓

Physical Machine

------------

EC2

↓

Virtual Machine
```

---

# Amazon Linux 2023

Why chosen?

Because:

```text
Official AWS Linux

Stable

Free

Common In Labs
```

---

# Security Group

One of the most misunderstood topics.

---

Think:

```text
Firewall
```

---

Real Life

```text
Apartment Security Guard
```

---

Guard decides:

```text
Who Can Enter
```

---

Security Group decides:

```text
Which Ports Can Enter
```

---

Example

```text
22  -> SSH

80  -> HTTP

443 -> HTTPS
```

---

Visual

```text
Internet

    |

    v

Security Group

    |

Allowed?

YES -> Enter

NO  -> Block
```

---

# Key Pair

Another critical concept.

---

Problem:

```text
How Do We Login?
```

---

AWS uses:

```text
SSH Key Authentication
```

instead of password.

---

Visual

```text
Public Key

↓

Stored On Server

------------

Private Key

↓

Stored On Laptop
```

---

# Real Life Analogy

House Door.

---

Server owns:

```text
Lock
```

You own:

```text
Key
```

---

Both must match.

---

# Memory Trick

```text
Public Key
=
Lock

Private Key
=
Actual Key
```

---

# End Goal

Terraform creates:

```text
EC2

Security Group

Key Pair
```

So Ansible can start working.

---

# The Complete Flow

```text
Terraform

↓

AWS Infrastructure

↓

EC2 Servers

↓

SSH Access

↓

Ansible Inventory

↓

Automation Begins
```

---

# Part 14: SSH Troubleshooting Master Notes

## Why "Permission Denied" Happens

This is one of the most important real-world sections.

---

# What Is SSH?

SSH means:

```text
Secure Shell
```

Think:

```text
Remote Keyboard
```

---

Visual

```text
Laptop

    |

SSH

    |

EC2 Server
```

---

# Login Flow

```text
Private Key

↓

Server Verification

↓

Authentication

↓

Shell Access
```

---

# Common Error 1

```text
Permission denied (publickey)
```

---

Meaning:

```text
Server Rejected Your Key
```

---

# Real Life Analogy

Wrong house key.

---

Visual

```text
Key

↓

Lock

↓

No Match

↓

Door Stays Closed
```

---

# Common Causes

## Wrong Key

```bash
ssh -i wrong.pem
```

---

## Wrong User

Amazon Linux:

```bash
ec2-user
```

Ubuntu:

```bash
ubuntu
```

---

Many beginners do:

```bash
ssh ubuntu@amazon-linux
```

Fails.

---

# Common Error 2

```text
UNPROTECTED PRIVATE KEY FILE
```

---

Why?

Linux protects SSH keys.

---

Bad:

```text
Everyone Can Read Key
```

---

Fix:

```bash
chmod 400 mykey.pem
```

---

Meaning:

```text
Only Owner Can Read
```

---

Visual

```text
Before

-rwxrwxrwx

Everyone

------------

After

-r--------

Owner Only
```

---

# Common Error 3

```text
Connection Timed Out
```

---

Usually means:

```text
Security Group

Network

Port 22 Closed
```

---

Visual

```text
SSH Request

↓

Port 22

↓

Blocked

↓

Timeout
```

---

# SSH Troubleshooting Flow

```text
Can Ping?

     |

     v

Port 22 Open?

     |

     v

Correct User?

     |

     v

Correct Key?

     |

     v

chmod 400?

     |

     v

SSH Works
```

---

# Part 15: ansible.cfg Deep Dive

## The Brain Configuration File

---

# What Is ansible.cfg?

Think:

```text
Global Settings
```

for Ansible.

---

Real Life Analogy

Phone Settings.

---

You configure once.

Everything follows it.

---

# Visual

```text
ansible.cfg

      |

      +--- Inventory

      +--- User

      +--- SSH Key

      +--- Forks

      +--- Host Checking
```

---

# inventory

Example

```ini
inventory=inventory.ini
```

Meaning:

```text
Default Inventory Location
```

---

Without this:

```bash
ansible all -i inventory.ini
```

every time.

---

With config:

```bash
ansible all
```

enough.

---

# remote_user

```ini
remote_user=ec2-user
```

Meaning:

```text
Default Login User
```

---

Without:

```bash
-u ec2-user
```

every command.

---

# private_key_file

```ini
private_key_file=mykey.pem
```

Meaning:

```text
Default SSH Key
```

---

Without:

```bash
--private-key=mykey.pem
```

every time.

---

# host_key_checking

```ini
host_key_checking=False
```

---

Why used in labs?

Because:

```text
Avoid Fingerprint Prompt
```

---

Production?

Usually:

```text
Keep Enabled
```

for security.

---

# forks

```ini
forks=20
```

---

Meaning:

```text
20 Parallel Workers
```

---

Visual

```text
Forks=5

5 Servers At Once

------------

Forks=20

20 Servers At Once
```

---

# Configuration Priority

Interview Favorite.

---

Which wins?

```text
Environment Variable

↓

Current Directory ansible.cfg

↓

Home Directory

↓

System Config
```

Most specific wins.

---

# Part 16: Complete Lab Walkthrough

## Understanding WHY Every Command Was Executed

---

# Step 1

Create Infrastructure

```text
Terraform
```

---

Purpose:

```text
Create Servers Automatically
```

---

# Step 2

SSH Into Control Node

Purpose:

```text
Install Ansible
```

---

Visual

```text
Laptop

↓

Control Node
```

---

# Step 3

Install Ansible

Purpose:

```text
Turn Control Node

Into Automation Machine
```

---

# Step 4

Create Inventory

Example

```ini
[web]
10.0.1.10

[app]
10.0.1.20
```

---

Purpose:

```text
Tell Ansible

Which Servers Exist
```

---

# Step 5

Test Connectivity

```bash
ansible all -m ping
```

---

Not ICMP ping.

Actually:

```text
SSH Test
```

---

Visual

```text
SSH Connect

↓

Run Ping Module

↓

pong
```

---

# Step 6

Gather Facts

```bash
ansible all -m setup
```

---

Purpose:

```text
Learn Everything

About Server
```

---

# Step 7

Run Adhoc Commands

Example

```bash
ansible all -m command -a "uptime"
```

---

Purpose:

```text
Quick Operations
```

without writing playbooks.

---

# Step 8

Configure ansible.cfg

Purpose:

```text
Avoid Repeating

Inventory

SSH User

Private Key
```

---

# Step 9

Create Playbook

Purpose:

```text
Store Automation As Code
```

---

Visual

```text
Adhoc

↓

One Time

------------

Playbook

↓

Reusable
```

---

# Step 10

Execute Playbook

```bash
ansible-playbook site.yml
```

---

Flow

```text
Read Inventory

↓

Find Hosts

↓

SSH

↓

Run Tasks

↓

Show Results
```

---

# FINAL MASTER MENTAL MODEL

Everything from your notes can now be remembered using one giant picture:

```text
Terraform
    ↓
EC2
    ↓
Security Group
    ↓
SSH Key
    ↓
SSH Access
    ↓
Control Node
    ↓
Inventory
    ↓
ansible.cfg
    ↓
Ping
    ↓
Facts
    ↓
Modules
    ↓
Adhoc Commands
    ↓
Playbooks
    ↓
Variables
    ↓
Loops
    ↓
Conditions
    ↓
Handlers
    ↓
Templates
    ↓
Roles
    ↓
Vault
    ↓
Rolling Updates
    ↓
Dynamic Inventory
    ↓
Production Deployments
```
***

Excellent choices. These 6 parts are what usually transform someone from:

```text
"I know Ansible commands"
```

to

```text
"I can explain, design, troubleshoot,
and operate Ansible in production."
```

I would recommend doing them in this order:

```text
Part 17 → Real Deployment Project
Part 19 → Ansible vs Terraform
Part 20 → Jenkins + Ansible
Part 21 → Kubernetes + Ansible
Part 22 → Production Troubleshooting
Part 18 → 100+ Interview Questions
```

Why?

Because interview questions become much easier after you've seen real deployment patterns.

---

# Part 17: End-to-End Real Deployment Project

## Deploying an Application to Production Using Ansible

This is the closest thing to what happens in a real company.

---

# Big Picture

Imagine your company has:

```text
2 Load Balancers

4 Web Servers

2 App Servers

1 Database
```

Architecture:

```text
Users
  |
  v
Load Balancer
  |
  +----- Web1
  |
  +----- Web2
  |
  +----- Web3
  |
  +----- Web4
         |
         v
     App Servers
         |
         v
      Database
```

---

# Business Requirement

Developer says:

```text
New Version Ready
```

Need deployment.

But:

```text
No Downtime
```

---

# Beginner Thinking

```text
SSH

Copy Files

Restart

Hope It Works
```

Very risky.

---

# Professional Thinking

```text
Automated
Repeatable
Auditable
Rollback Ready
```

---

# Project Structure

```text
ansible-project/

|
+--- inventory/

|
+--- group_vars/

|
+--- roles/

|      |
|      +--- app/

|      +--- nginx/

|
+--- playbooks/

|
+--- templates/

|
+--- vault/
```

---

# Deployment Flow

## Step 1

Discover Servers

```text
Inventory
```

---

Visual

```text
Inventory

↓

Web Servers

↓

App Servers
```

---

## Step 2

Load Variables

```yaml
app_version: v2.5.1
```

---

Purpose:

```text
Deploy Version Dynamically
```

---

## Step 3

Decrypt Secrets

Vault:

```yaml
db_password
api_token
```

---

Visual

```text
Vault

↓

Decrypt

↓

Use Secrets
```

---

## Step 4

Remove Node From Load Balancer

Very important.

---

Before deployment:

```text
LB

 |
 +--- Web1

 +--- Web2

 +--- Web3

 +--- Web4
```

---

Remove:

```text
Web1
```

---

Result:

```text
LB

 |
 +--- Web2

 +--- Web3

 +--- Web4
```

Traffic continues.

---

# Why?

Because:

```text
Users Should Not Hit
Server Being Updated
```

---

# Step 5

Backup Existing Version

Example:

```text
/app/current

↓

/app/backup
```

---

Memory Trick

```text
Always Create Escape Route
```

---

# Step 6

Deploy New Artifact

Example:

```text
myapp-v2.5.1.jar
```

or

```text
Docker Image
```

---

Visual

```text
Old Version

↓

Replace

↓

New Version
```

---

# Step 7

Update Configuration

Template:

```jinja2
DB_HOST={{ db_host }}
APP_VERSION={{ version }}
```

---

Generates:

```text
DB_HOST=10.0.1.10
APP_VERSION=v2.5.1
```

---

# Step 8

Restart Service

Handler executes.

```text
Config Changed?

↓

Restart
```

---

# Step 9

Health Check

Example:

```bash
curl localhost:8080/health
```

Expected:

```text
200 OK
```

---

Visual

```text
Deploy

↓

Restart

↓

Health Check

↓

Healthy?
```

---

# Step 10

Add Server Back

```text
Web1 Returns To LB
```

---

Visual

```text
LB

 |
 +--- Web1

 +--- Web2

 +--- Web3

 +--- Web4
```

---

# Step 11

Move To Next Server

Using:

```yaml
serial: 1
```

---

Meaning:

```text
Update One Server At A Time
```

---

# Full Production Flow

```text
Inventory
     ↓
Vault
     ↓
Remove From LB
     ↓
Backup
     ↓
Deploy
     ↓
Template
     ↓
Restart
     ↓
Health Check
     ↓
Add Back To LB
     ↓
Next Server
```

---

# Why Companies Use Ansible

Because deployment becomes:

```text
Predictable

Repeatable

Safe
```

instead of:

```text
Manual

Error Prone

Stressful
```

---

# Ultimate Memory Trick

Think of Ansible Deployment like a hospital surgery:

```text
Take Patient To Operating Room

↓

Remove From Public Area

↓

Backup Records

↓

Perform Surgery

↓

Check Health

↓

Return Patient

↓

Next Patient
```

That's exactly what a rolling deployment does.

---

## What You'll Learn Next

**Part 19: Ansible vs Terraform Deep Dive**

This is one of the most important DevOps interview topics because many engineers confuse:

```text
Terraform
=
Provision Infrastructure

Ansible
=
Configure Infrastructure
```

But the real relationship is much deeper. We'll break it down with architecture diagrams, state management, idempotency comparisons, and real production workflows.

***

# Part 19: Ansible vs Terraform Deep Dive

## The Most Confused DevOps Interview Topic

Almost every DevOps beginner eventually asks:

```text
If Terraform can create servers

Why do we need Ansible?
```

Or:

```text
If Ansible can install software

Why do we need Terraform?
```

The answer is:

```text
They solve different problems.
```

---

# The 10-Year-Old Explanation

Imagine building a new school.

Question:

```text
Who builds the school building?

Who arranges desks,
chairs,
whiteboards,
projectors?
```

Different jobs.

---

# Real Life Analogy

Construction Company

```text
Build Land

Build Building

Build Rooms
```

---

Interior Designer

```text
Arrange Furniture

Paint Walls

Install Equipment
```

---

Visual

```text
Terraform
=
Construction Company

Ansible
=
Interior Designer
```

---

# Core Purpose

Terraform:

```text
Creates Infrastructure
```

Examples:

```text
EC2

VPC

Subnets

Security Groups

Load Balancers

RDS

S3
```

---

Ansible:

```text
Configures Infrastructure
```

Examples:

```text
Install Nginx

Install Docker

Create Users

Deploy Applications

Update Config Files

Restart Services
```

---

# Visual

```text
Terraform

↓

Creates Server

↓

Server Exists

-----------------

Ansible

↓

Uses Server

↓

Configures Server
```

---

# The Biggest Mental Model

Terraform asks:

```text
What infrastructure should exist?
```

---

Ansible asks:

```text
What should run inside it?
```

---

# Example

Need:

```text
3 Web Servers

1 Database

1 Load Balancer
```

---

Terraform does:

```text
Create VPC

Create Subnets

Create EC2

Create Security Groups

Create RDS

Create Load Balancer
```

---

Result

```text
Infrastructure Exists
```

---

Then Ansible does:

```text
Install Nginx

Install Java

Deploy Application

Update Config

Start Services
```

---

Result

```text
Application Works
```

---

# Visual

```text
Terraform

↓

Infrastructure

↓

EC2
VPC
RDS
LB

↓

Ansible

↓

Software

↓

Nginx
Java
Docker
App
```

---

# Why Terraform Is Better At Infrastructure

Imagine:

```text
Need 200 EC2 Instances
```

Terraform:

```text
AWS API

↓

Create 200 Instances
```

directly.

---

Ansible:

```text
Can Do It

But Not Its Specialty
```

---

Terraform was designed for:

```text
Infrastructure Provisioning
```

---

# Why Ansible Is Better At Configuration

Imagine:

```text
Install Docker

Create User

Copy Config

Restart Service
```

Terraform can do some of this.

But:

```text
Ansible Was Built For It
```

---

Visual

```text
Terraform

Infrastructure Expert

----------------

Ansible

Configuration Expert
```

---

# State File (Huge Interview Topic)

This is where Terraform becomes unique.

---

# What Is Terraform State?

Terraform remembers:

```text
What It Created
```

inside:

```text
terraform.tfstate
```

---

Visual

```text
Terraform Code

      |

      v

State File

      |

      v

AWS Reality
```

---

# Why?

Suppose:

```text
Desired

3 EC2
```

---

Reality:

```text
2 EC2
```

---

Terraform knows:

```text
One Missing

Create It
```

because state exists.

---

# Memory Trick

```text
Terraform State

=

Memory Book
```

---

# Does Ansible Have State?

No.

This is one of the biggest differences.

---

Ansible:

```text
Connect

Execute

Disconnect
```

---

Visual

```text
Ansible

↓

SSH

↓

Task

↓

Done
```

---

No permanent state file.

---

# Interview Question

Why doesn't Ansible need a state file?

Answer:

```text
Ansible works by connecting
to systems and evaluating
current state directly,
instead of maintaining
a separate infrastructure state file.
```

---

# Desired State Comparison

Terraform:

```text
I Want 3 EC2
```

---

Ansible:

```text
I Want Nginx Installed
```

---

Both are:

```text
Desired State Tools
```

but at different layers.

---

Visual

```text
Terraform

Infrastructure Desired State

----------------

Ansible

Configuration Desired State
```

---

# Idempotency Comparison

Huge interview topic.

---

What is idempotency?

```text
Run Again

Same Result
```

---

Terraform Example

Current:

```text
3 EC2
```

Desired:

```text
3 EC2
```

Apply:

```text
No Change
```

---

Ansible Example

Current:

```text
Nginx Installed
```

Desired:

```text
Nginx Installed
```

Run:

```text
No Change
```

---

Both support:

```text
Idempotency
```

---

# Execution Method

Terraform:

```text
Uses Cloud APIs
```

---

Visual

```text
Terraform

↓

AWS API

↓

Create Resources
```

---

Ansible:

```text
Uses SSH/WinRM
```

---

Visual

```text
Ansible

↓

SSH

↓

Execute Modules
```

---

# Real Production Workflow

This is how companies actually use them.

---

Step 1

Terraform

```text
Create VPC
```

---

Step 2

Terraform

```text
Create EC2
```

---

Step 3

Terraform

```text
Create Security Groups
```

---

Step 4

Terraform

```text
Create Load Balancer
```

---

Infrastructure complete.

---

Step 5

Ansible

```text
Install Nginx
```

---

Step 6

Ansible

```text
Deploy Application
```

---

Step 7

Ansible

```text
Configure Services
```

---

Visual

```text
Terraform
     ↓
Infrastructure
     ↓
Ansible
     ↓
Configuration
     ↓
Application Ready
```

---

# Why Not Use Only Terraform?

Because Terraform becomes ugly when doing:

```text
Large Config Files

Service Management

OS Configuration

Complex Deployments
```

---

Terraform excels at:

```text
Cloud Resources
```

---

Ansible excels at:

```text
Operating Systems
```

---

# Why Not Use Only Ansible?

Because creating:

```text
VPC

Subnets

NAT

Route Tables

Load Balancers
```

using Ansible is possible,

but Terraform is purpose-built for that.

---

# Interview Scenario

Interviewer asks:

```text
How would you build a new environment?
```

Strong Answer:

```text
Use Terraform to provision
infrastructure such as VPCs,
subnets, EC2 instances,
security groups, databases,
and load balancers.

Then use Ansible to configure
the servers, install software,
deploy applications,
manage users,
and maintain configurations.
```

---

# Real DevOps Pipeline

```text
Git Push
    |
    v

Terraform

    |
    v

Infrastructure

    |
    v

Ansible

    |
    v

Configuration

    |
    v

Application Deployment

    |
    v

Health Checks
```

---

# Ultimate Revision Sheet

```text
Terraform
=
Infrastructure Provisioning

Ansible
=
Configuration Management

Terraform State
=
Infrastructure Memory

Ansible
=
Agentless Execution

Terraform
=
Cloud Resources

Ansible
=
Operating Systems

Terraform
=
API Driven

Ansible
=
SSH Driven

Terraform
=
Creates Servers

Ansible
=
Configures Servers
```

---

# One-Line Memory Map

```text
Terraform
     ↓
Infrastructure
     ↓
Servers Exist
     ↓
Ansible
     ↓
Configuration
     ↓
Application Ready
```

---

# The Golden Interview Answer

If someone asks:

```text
When do you use Terraform and Ansible together?
```

Answer:

```text
Terraform provisions the infrastructure.
Ansible configures the infrastructure.
Terraform creates the house.
Ansible furnishes and operates the house.
```

That single sentence explains the relationship better than most 30-minute explanations.

---

## Next Part

```text
Part 20
↓
Ansible + Jenkins CI/CD Pipeline
```

Where we'll build the complete flow:

```text
Developer
   ↓
Git Push
   ↓
Jenkins
   ↓
Terraform
   ↓
Ansible
   ↓
Deployment
   ↓
Production
```

This is one of the most common real-world DevOps architectures.

***


# Part 20: Ansible + Jenkins CI/CD Pipeline

## How Real Companies Deploy Applications Automatically

Up to now:

```text
Developer Writes Code
```

and

```text
Ansible Deploys Code
```

But a question remains:

```text
Who starts Ansible?
```

Do engineers manually run:

```bash
ansible-playbook deploy.yml
```

every day?

Large companies don't.

They use:

```text
CI/CD Pipeline
```

---

# The Big Picture

Imagine:

```text
Developer Fixes Bug
```

Should deployment require:

```text
Open Laptop

SSH

Run Commands

Hope Nothing Breaks
```

No.

Too slow.

Too risky.

---

Instead:

```text
Git Push

↓

Pipeline Runs

↓

Testing

↓

Build

↓

Deploy

↓

Production
```

Automatically.

---

# What Is CI/CD?

CI means:

```text
Continuous Integration
```

CD means:

```text
Continuous Delivery
or
Continuous Deployment
```

---

# 10-Year-Old Analogy

Imagine school homework.

Without CI/CD:

```text
Teacher Checks

Every Notebook

Manually
```

Very slow.

---

With CI/CD:

```text
Homework Submitted

↓

Automatic Checking

↓

Results Generated
```

---

# Where Jenkins Fits

Jenkins is:

```text
Automation Orchestrator
```

Think:

```text
Project Manager
```

---

Visual

```text
Developer

    |

    v

Jenkins

    |

    +---- Build

    +---- Test

    +---- Deploy

    +---- Notify
```

---

# Jenkins + Ansible Relationship

Think:

```text
Jenkins
=
Boss

Ansible
=
Worker
```

---

Jenkins says:

```text
Deploy Version 2.5
```

---

Ansible says:

```text
Okay

I'll Configure Servers
```

---

Visual

```text
Jenkins

↓

Triggers

↓

Ansible

↓

Deployment
```

---

# End-to-End Flow

Let's deploy a Java application.

---

Step 1

Developer writes code.

```text
Developer

↓

Code Changes
```

---

Step 2

Push to Git.

```bash
git push origin main
```

---

Visual

```text
Laptop

↓

Git Repository
```

---

# Step 3

Webhook Triggers Jenkins

Git says:

```text
New Commit Arrived
```

---

Visual

```text
Git

↓

Webhook

↓

Jenkins
```

---

# What Is Webhook?

Think:

```text
Automatic Phone Call
```

---

Without webhook:

```text
Jenkins

"Anything New?"

"Anything New?"

"Anything New?"
```

---

With webhook:

```text
Git

"Hey Jenkins

New Code!"
```

---

Much faster.

---

# Step 4

Jenkins Starts Pipeline

Pipeline means:

```text
Series Of Automated Steps
```

---

Visual

```text
Build

↓

Test

↓

Package

↓

Deploy
```

---

# Step 5

Build Application

Java Example:

```bash
mvn clean package
```

---

Purpose:

```text
Convert Source Code

↓

Runnable Artifact
```

---

Visual

```text
Java Files

↓

Build

↓

app.jar
```

---

# Step 6

Run Tests

Example:

```text
Unit Tests

Integration Tests
```

---

Why?

Because:

```text
Broken Code

Should Never Reach Production
```

---

Visual

```text
Build

↓

Tests

↓

Pass?

YES → Continue

NO → Stop
```

---

# Step 7

Store Artifact

Artifact examples:

```text
app.jar

war file

docker image
```

---

Visual

```text
Application

↓

Artifact Repository

↓

Stored
```

---

Common repositories:

* Nexus Repository Manager
* JFrog Artifactory

---

# Step 8

Jenkins Calls Ansible

Example:

```bash
ansible-playbook deploy.yml
```

---

Visual

```text
Jenkins

↓

Ansible Playbook

↓

Servers
```

---

# What Happens Next?

Ansible performs:

```text
Remove From LB

↓

Backup

↓

Deploy

↓

Restart

↓

Health Check

↓

Add Back
```

Exactly what we learned in Part 17.

---

# Full Deployment Diagram

```text
Developer

    |

    v

Git

    |

    v

Webhook

    |

    v

Jenkins

    |

    v

Build

    |

    v

Tests

    |

    v

Artifact

    |

    v

Ansible

    |

    v

Servers

    |

    v

Production
```

---

# Jenkins Pipeline Stages

A professional pipeline often contains:

```text
Stage 1
Checkout

Stage 2
Build

Stage 3
Unit Tests

Stage 4
Security Scan

Stage 5
Package

Stage 6
Upload Artifact

Stage 7
Deploy Using Ansible

Stage 8
Health Check

Stage 9
Notify Team
```

---

Visual

```text
Checkout
     ↓
Build
     ↓
Test
     ↓
Security
     ↓
Package
     ↓
Deploy
     ↓
Verify
     ↓
Notify
```

---

# Why Security Scan?

Imagine developer accidentally commits:

```text
Critical Vulnerability
```

Pipeline catches it early.

---

Visual

```text
Code

↓

Security Scan

↓

Safe?

YES → Continue

NO → Stop
```

---

# Deployment Environments

Most companies don't deploy directly to production.

---

Flow

```text
Dev

↓

Test

↓

UAT

↓

Production
```

---

Visual

```text
Developer

↓

Dev

↓

QA

↓

UAT

↓

Production
```

---

# Why Multiple Environments?

Because:

```text
Production

=

Real Customers
```

---

Need confidence first.

---

# How Ansible Handles Environments

Inventory example:

```text
inventory/

├── dev
├── qa
├── prod
```

---

Deploy command:

```bash
ansible-playbook deploy.yml -i inventory/prod
```

---

Meaning:

```text
Same Playbook

Different Environment
```

---

# Blue-Green Deployment

Very common interview topic.

---

Normal Deployment

```text
Production

↓

Update Existing Servers
```

Risky.

---

Blue-Green Deployment

Two environments:

```text
Blue

Green
```

---

Visual

```text
Users

 |

 v

Blue Environment
(Current)
```

---

Deploy new version to:

```text
Green Environment
```

first.

---

Visual

```text
Blue → v1

Green → v2
```

---

After verification:

```text
Switch Traffic
```

---

Visual

```text
Before

Users → Blue

------------

After

Users → Green
```

---

# Canary Deployment

Another important strategy.

---

Instead of:

```text
100% Traffic
```

---

Send:

```text
5% Traffic
```

first.

---

Visual

```text
Users

↓

5% → New Version

95% → Old Version
```

---

If healthy:

```text
20%

50%

100%
```

---

# Rollback

Most important production concept.

---

Suppose deployment fails.

Need:

```text
Return To Old Version
```

immediately.

---

Visual

```text
v1

↓

Deploy v2

↓

Problem

↓

Rollback

↓

v1
```

---

# Why Backups Matter

Remember Part 17:

```text
Backup

↓

Deploy
```

Now you see why.

---

Without backup:

```text
Rollback Impossible
```

---

# Notifications

After deployment:

Jenkins often sends messages to:

* Slack
* Microsoft Teams
* Email

---

Visual

```text
Deployment

↓

Success

↓

Notify Team
```

---

# Real Enterprise Architecture

```text
Developer
     ↓
Git
     ↓
Webhook
     ↓
Jenkins
     ↓
Build
     ↓
Test
     ↓
Security Scan
     ↓
Artifact Repository
     ↓
Ansible
     ↓
Load Balancer
     ↓
Web Servers
     ↓
Application
     ↓
Users
```

---

# Ultimate Revision Sheet

```text
Git
=
Source Code

Webhook
=
Automatic Trigger

Jenkins
=
Pipeline Orchestrator

Build
=
Create Artifact

Test
=
Validate Code

Artifact
=
Deployable Package

Ansible
=
Deployment Engine

Blue-Green
=
Two Environments

Canary
=
Small Traffic First

Rollback
=
Return To Old Version
```

---

# One-Line Memory Map

```text
Developer
     ↓
Git Push
     ↓
Webhook
     ↓
Jenkins
     ↓
Build
     ↓
Test
     ↓
Artifact
     ↓
Ansible
     ↓
Deploy
     ↓
Health Check
     ↓
Production
```


***

# Part 21: Ansible + Kubernetes

## How Ansible Fits Into The Kubernetes World

This is where many DevOps engineers get confused.

They learn Kubernetes and think:

```text
Kubernetes replaces Ansible
```

Wrong.

Or they learn Ansible and think:

```text
Ansible replaces Kubernetes
```

Also wrong.

---

# The Big Question

Imagine we have:

```text
10 EC2 Instances
```

running:

```text
Nginx
Java
Docker
```

Ansible is perfect.

---

But what if we have:

```text
500 Containers

Auto Scaling

Self Healing

Rolling Updates
```

Now we need:

```text
Kubernetes
```

---

# 10-Year-Old Analogy

Imagine a city.

Terraform:

```text
Builds Land
Roads
Buildings
```

---

Ansible:

```text
Installs Furniture
Computers
Electricity
```

---

Kubernetes:

```text
Runs The Entire City Daily

Manages Workers

Replaces Sick Workers

Handles Traffic
```

---

Visual

```text
Terraform
     ↓
Infrastructure

Ansible
     ↓
Configuration

Kubernetes
     ↓
Application Operations
```

---

# What Problem Does Kubernetes Solve?

Before Kubernetes:

```text
Application

↓

Server
```

---

If server dies:

```text
Application Dies
```

---

Visual

```text
Server Crash

↓

Website Down
```

---

Kubernetes says:

```text
I Will Keep The App Running
```

---

Visual

```text
Container Dies

↓

Kubernetes Detects

↓

Creates New Container
```

---

# Kubernetes Core Components

Think of Kubernetes as a school.

---

# Control Plane

Principal's Office

Responsible for:

```text
Decision Making
```

---

# Worker Nodes

Classrooms

Responsible for:

```text
Running Applications
```

---

Visual

```text
Control Plane

      |

      +---- Worker 1

      +---- Worker 2

      +---- Worker 3
```

---

# Pod

Most important Kubernetes concept.

---

What is a Pod?

Think:

```text
Protective Box
```

around a container.

---

Visual

```text
Pod

 |

 +---- Container
```

---

Real Example

```text
Pod

 |

 +---- Nginx Container
```

---

# Deployment

Suppose:

```text
Need 3 Copies
```

of application.

---

Without Deployment:

```text
Create Manually
```

---

With Deployment:

```text
Kubernetes Maintains
3 Copies
```

---

Visual

```text
Deployment

      |

      +---- Pod 1

      +---- Pod 2

      +---- Pod 3
```

---

# Service

Pods have changing IPs.

Problem:

```text
Users Need Stable Access
```

---

Solution:

```text
Service
```

---

Visual

```text
Users

  |

  v

Service

  |

  +---- Pod1

  +---- Pod2

  +---- Pod3
```

---

# How Ansible Helps Kubernetes

Many beginners think:

```text
Kubernetes Runs Apps

Therefore Ansible Is Useless
```

Wrong.

---

Ansible is often used for:

```text
Provisioning Kubernetes Nodes

Installing Kubernetes

Configuring Nodes

Managing Cluster Settings

Deploying Kubernetes Resources
```

---

Visual

```text
Terraform

↓

Create EC2

↓

Ansible

↓

Install Kubernetes

↓

Create Cluster

↓

Kubernetes

↓

Run Applications
```

---

# Real Production Flow

Step 1

Terraform:

```text
Create 6 EC2
```

---

Step 2

Ansible:

```text
Install Docker

Install kubeadm

Install kubelet

Install kubectl
```

---

Step 3

Ansible:

```text
Initialize Cluster
```

---

Step 4

Kubernetes:

```text
Run Applications
```

---

Visual

```text
Terraform
     ↓
EC2
     ↓
Ansible
     ↓
K8s Cluster
     ↓
Applications
```

---

# Kubernetes Installation Using Ansible

Without Ansible:

Imagine:

```text
50 Nodes
```

Need:

```text
Install Docker

Install kubelet

Configure Network

Configure Runtime
```

50 times.

Nightmare.

---

With Ansible:

```yaml
hosts: kubernetes_nodes
```

---

One execution.

All nodes configured.

---

Visual

```text
Ansible

↓

Node1

Node2

Node3

Node4

Node5
```

---

# Deploying Applications

Can Ansible deploy applications into Kubernetes?

Yes.

---

Example Flow

```text
Ansible

↓

Apply Manifest

↓

Kubernetes

↓

Creates Pods
```

---

Visual

```text
Ansible

↓

deployment.yaml

↓

Kubernetes API

↓

Pods Created
```

---

# Rolling Updates Comparison

## Ansible Rolling Update

```yaml
serial: 1
```

Updates:

```text
Server By Server
```

---

Visual

```text
Web1

↓

Web2

↓

Web3
```

---

## Kubernetes Rolling Update

Updates:

```text
Pod By Pod
```

Automatically.

---

Visual

```text
Pod1 v1

↓

Pod1 v2

↓

Pod2 v2

↓

Pod3 v2
```

---

# Self Healing

Ansible:

```text
Runs

Finishes

Leaves
```

---

Kubernetes:

```text
Always Watching
```

---

Visual

```text
Container Dies

↓

Kubernetes Notices

↓

Creates New Container
```

---

This is one of the biggest differences.

---

# Ansible vs Kubernetes

| Ansible                  | Kubernetes              |
| ------------------------ | ----------------------- |
| Configuration Management | Container Orchestration |
| Uses SSH                 | Uses Kubernetes API     |
| Agentless                | Cluster Based           |
| Runs Tasks               | Continuously Watches    |
| Configures Servers       | Runs Containers         |
| Finishes Execution       | Always Active           |

---

# Real Enterprise Architecture

Modern companies often use:

```text
Terraform
     ↓
Cloud Infrastructure
     ↓
Ansible
     ↓
Kubernetes Cluster Setup
     ↓
Kubernetes
     ↓
Application Operations
```

---

# GitOps Evolution

Traditional:

```text
Git
 ↓
Jenkins
 ↓
Ansible
 ↓
Servers
```

---

Modern Kubernetes:

```text
Git
 ↓
GitOps Tool
 ↓
Kubernetes
 ↓
Pods
```

---

Common GitOps tools include:

* Argo CD
* Flux

---

# Where Ansible Still Wins

Even in Kubernetes environments, Ansible is commonly used for:

```text
OS Hardening

User Management

Patch Management

Cluster Installation

Node Configuration

Certificate Management

Network Configuration
```

---

# Complete DevOps Mental Model

This is one of the most important diagrams in your DevOps journey:

```text
Git
  ↓

Jenkins
  ↓

Terraform
  ↓

VPC
EC2
Load Balancer
  ↓

Ansible
  ↓

Docker
Kubernetes
Configuration
  ↓

Kubernetes
  ↓

Pods
Services
Deployments
  ↓

Users
```

---

# Ultimate Revision Sheet

```text
Terraform
=
Creates Infrastructure

Ansible
=
Configures Infrastructure

Docker
=
Packages Application

Kubernetes
=
Runs Containers At Scale

Pod
=
Smallest Deployable Unit

Deployment
=
Maintains Desired Pods

Service
=
Stable Access To Pods

Control Plane
=
Brain

Worker Node
=
Runs Pods

Self Healing
=
Automatic Recovery

Rolling Update
=
Gradual Pod Replacement
```

---

# One-Line Memory Map

```text
Terraform
     ↓
Infrastructure
     ↓
Ansible
     ↓
Kubernetes Setup
     ↓
Pods
     ↓
Services
     ↓
Users
```

---

## Next Part

```text
Part 22
↓
Real Production Troubleshooting
```

This is where you'll learn what actually breaks at 2 AM:

```text
Permission Denied
SSH Failures
Inventory Problems
Playbook Errors
Vault Issues
Template Bugs
Handler Problems
Deployment Failures
Kubernetes Node Issues
CI/CD Failures
```

and, more importantly:

```text
How experienced DevOps engineers think
when troubleshooting them.
```

***

# Part 22: Real Production Troubleshooting

## How Senior DevOps Engineers Think at 2 AM

This is the part that separates:

```text
Junior Engineer
=
Knows Commands
```

from

```text
Senior Engineer
=
Finds Root Cause
```

---

# The Biggest Mistake Beginners Make

Server breaks.

They immediately try:

```bash
restart
restart
restart
restart
```

---

Senior engineers do:

```text
Observe

↓

Gather Evidence

↓

Identify Root Cause

↓

Fix

↓

Verify
```

---

# The Golden Troubleshooting Rule

Never start with:

```text
What Should I Restart?
```

Start with:

```text
What Changed?
```

---

# Production Thinking Model

Whenever something breaks:

```text
What Changed?

↓

Where Is Failure?

↓

Can I Reproduce?

↓

What Logs Exist?

↓

Root Cause?

↓

Fix

↓

Verify
```

---

# Troubleshooting Layer Model

Most outages belong to one layer.

```text
Layer 1
Network

Layer 2
SSH

Layer 3
OS

Layer 4
Ansible

Layer 5
Application

Layer 6
Database

Layer 7
Load Balancer
```

---

# Memory Trick

Think:

```text
Water Pipeline
```

---

Visual

```text
User

↓

Load Balancer

↓

Server

↓

Application

↓

Database
```

Problem exists somewhere.

Find where.

---

# Problem 1: ansible all -m ping Fails

---

Error

```text
UNREACHABLE
```

---

Most beginners think:

```text
Ansible Broken
```

Wrong.

---

Check:

```text
Can SSH Work?
```

---

Manual Test

```bash
ssh ec2-user@server-ip
```

---

If SSH fails:

```text
Not Ansible Problem

SSH Problem
```

---

Visual

```text
Ansible

↓

SSH

↓

Server

If SSH Broken

Everything Broken
```

---

# Troubleshooting Flow

```text
Can Ping Server?

↓

Port 22 Open?

↓

Correct User?

↓

Correct Key?

↓

SSH Works?
```

---

# Problem 2: Permission Denied (publickey)

Most common AWS issue.

---

Meaning:

```text
Server Rejected Key
```

---

Possible Causes

```text
Wrong Key

Wrong User

Wrong Permissions
```

---

Check

```bash
chmod 400 key.pem
```

---

Check User

Amazon Linux:

```bash
ec2-user
```

Ubuntu:

```bash
ubuntu
```

---

Memory Trick

```text
Wrong Key

=

Wrong House Key
```

---

# Problem 3: Host Not Found

Example

```text
webserver1

UNREACHABLE
```

---

Usually:

```text
Inventory Issue
```

---

Check

```bash
ansible-inventory --graph
```

---

Visual

```text
Inventory

↓

Host Exists?

NO

↓

Fix Inventory
```

---

# Problem 4: Playbook Syntax Error

Error:

```text
YAML Parsing Failed
```

---

Common Cause

Bad indentation.

---

Wrong

```yaml
tasks:
- name: Install nginx
   yum:
```

---

Right

```yaml
tasks:
  - name: Install nginx
    yum:
```

---

Memory Trick

```text
YAML

=

Python

Indentation Matters
```

---

# Problem 5: Variable Not Defined

Error

```text
VARIABLE IS UNDEFINED
```

---

Example

```yaml
{{ app_version }}
```

doesn't exist.

---

Check

```bash
ansible-inventory --list
```

or

```yaml
debug:
  var: app_version
```

---

Visual

```text
Variable Requested

↓

Variable Exists?

NO

↓

Failure
```

---

# Problem 6: Template Not Rendering Correctly

Template:

```jinja2
DB={{ db_host }}
```

---

Output:

```text
DB=
```

---

Problem:

```text
Variable Empty
```

---

Debug:

```yaml
- debug:
    var: db_host
```

---

# Problem 7: Handler Not Running

Many beginners panic.

---

Playbook succeeds.

Service not restarted.

---

Why?

Handlers run only when:

```text
Changed = True
```

---

Visual

```text
Task

↓

Changed?

YES

↓

Handler Runs

------------

NO

↓

Skip Handler
```

---

# Problem 8: Vault Password Error

Error

```text
Decryption Failed
```

---

Possible Causes

```text
Wrong Vault Password

Wrong Vault File

Corrupted Vault
```

---

Check

```bash
ansible-vault view secrets.yml
```

---

If view fails:

```text
Vault Issue Confirmed
```

---

# Problem 9: Package Installation Fails

Example

```yaml
dnf:
  name: nginx
```

---

Error:

```text
Package Not Found
```

---

Check:

```text
Repository Available?

Internet Available?

Correct Package Name?
```

---

Visual

```text
Install Request

↓

Repository

↓

Package Exists?

YES -> Install

NO -> Fail
```

---

# Problem 10: Service Won't Start

Example

```yaml
service:
  name: nginx
  state: started
```

---

But:

```text
FAILED
```

---

Check manually

```bash
systemctl status nginx
```

---

Then:

```bash
journalctl -xe
```

---

Memory Trick

```text
Ansible Reports

System Logs Explain
```

---

# Problem 11: Deployment Succeeded But Website Down

Most dangerous situation.

---

Ansible says:

```text
SUCCESS
```

---

Users say:

```text
Website Dead
```

---

Possible Causes

```text
Service Not Running

Wrong Config

Load Balancer Issue

Firewall Issue

Application Crash
```

---

Visual

```text
Deployment

↓

Success

↓

Health Check

↓

Failure
```

---

This is why production deployments include:

```text
Health Checks
```

---

# Problem 12: Rolling Deployment Stops Midway

Example

```yaml
serial: 2
```

---

Batch 1 succeeds.

Batch 2 fails.

---

Good News

```text
Ansible Stops
```

---

Meaning:

```text
Not All Servers Broken
```

---

Visual

```text
Server1 OK

Server2 OK

------------

Server3 FAIL

↓

Stop
```

---

# Problem 13: High CPU During Ansible Run

Cause:

```text
Too Many Forks
```

---

Example

```ini
forks=500
```

---

Control Node becomes overloaded.

---

Fix:

```text
Reduce Forks
```

---

Memory Trick

```text
Forks

=

Workers

Too Many Workers

↓

Chaos
```

---

# Problem 14: Dynamic Inventory Returns No Hosts

AWS Inventory Example.

---

Check:

```text
AWS Credentials

IAM Permissions

Region
```

---

Visual

```text
Ansible

↓

AWS API

↓

Permission Denied?

↓

No Inventory
```

---

# Problem 15: Jenkins Deployment Fails

Pipeline:

```text
Build

↓

Deploy
```

fails.

---

Always ask:

```text
Build Failed?

or

Deployment Failed?
```

---

Visual

```text
Pipeline

↓

Build

↓

Deploy

↓

Health Check
```

Identify exact stage.

---

# Problem 16: Kubernetes Pods CrashLoopBackOff

Common K8s issue.

---

Meaning:

```text
Container Starts

↓

Container Crashes

↓

Restart

↓

Crash

↓

Restart
```

---

Visual

```text
Start

↓

Crash

↓

Restart

↓

Crash
```

---

Check

```bash
kubectl logs pod-name
```

---

Then

```bash
kubectl describe pod pod-name
```

---

# Problem 17: Database Connection Failure

Application log:

```text
Cannot Connect To DB
```

---

Check:

```text
DB Running?

Credentials Correct?

Firewall Open?

DNS Correct?
```

---

Visual

```text
Application

↓

Database

↓

Connection?
```

---

# The Senior Engineer Method

When an alert comes:

Never think:

```text
How Do I Fix It?
```

First think:

```text
What Exactly Is Broken?
```

---

# Root Cause Analysis (RCA)

After incident.

Ask:

```text
What Happened?

Why?

How Prevent Again?
```

---

Example

Bad RCA:

```text
Restarted Server
Issue Fixed
```

---

Good RCA:

```text
Disk Filled

↓

Application Failed

↓

Monitoring Missing

↓

Added Alert
```

---

# The 5 Why Technique

Example:

```text
Website Down
```

Why?

```text
App Crashed
```

Why?

```text
Disk Full
```

Why?

```text
Logs Growing
```

Why?

```text
Rotation Missing
```

Why?

```text
Never Configured
```

Root Cause Found.

---

# Ultimate Troubleshooting Framework

```text
Observe

↓

Verify

↓

Isolate

↓

Identify

↓

Fix

↓

Validate

↓

Document

↓

Prevent
```

---

# The DevOps Master Flow

Everything you've learned now fits into one picture:

```text
Git
  ↓

Jenkins
  ↓

Terraform
  ↓

AWS Infrastructure
  ↓

Ansible
  ↓

Docker
  ↓

Kubernetes
  ↓

Application
  ↓

Monitoring
  ↓

Users
```

And troubleshooting means finding **which layer is failing**.

---

# Final Memory Map (Parts 17–22)

```text
Part 17
=
Real Deployment Project

Part 18
=
Interview Mastery (next)

Part 19
=
Terraform vs Ansible

Part 20
=
Jenkins + Ansible

Part 21
=
Ansible + Kubernetes

Part 22
=
Production Troubleshooting
```

The only major section left in this advanced series is:

```text
Part 18
=
100+ Ansible Interview Questions
```

which becomes much easier now because you've already built the full mental model from infrastructure → deployment → Kubernetes → troubleshooting.

***

# Part 18: 100+ Ansible Interview Questions Master Notes

## The Questions Interviewers Actually Ask

Most people prepare like this:

```text
Read Notes

↓

Memorize Definitions

↓

Forget During Interview
```

Senior engineers prepare differently.

```text
Understand Concept

↓

Understand WHY

↓

Explain Using Real Example
```

---

# Section 1: Ansible Fundamentals

---

## Q1: What is Ansible?

### Short Answer

```text
Ansible is an agentless configuration management,
automation, and orchestration tool.
```

### 10-Year-Old Answer

Imagine:

```text
1 Teacher

↓

100 Students
```

Instead of teaching individually:

```text
Teacher Gives One Instruction

↓

Everyone Follows
```

That's Ansible.

---

## Q2: Why is Ansible called Agentless?

### Answer

Because no agent is installed on managed nodes.

Communication happens using:

```text
SSH (Linux)

WinRM (Windows)
```

---

### Diagram

```text
Control Node

↓

SSH

↓

Managed Node
```

---

## Q3: What are the main components of Ansible?

Answer:

```text
Control Node

Managed Nodes

Inventory

Modules

Playbooks

Variables

Roles
```

---

## Q4: What is a Control Node?

Answer:

```text
Machine where Ansible is installed
and from which automation is executed.
```

---

## Q5: What are Managed Nodes?

Answer:

```text
Target servers managed by Ansible.
```

---

Visual

```text
Control Node

    |

    +---- Web Server

    +---- App Server

    +---- DB Server
```

---

# Section 2: Inventory

---

## Q6: What is Inventory?

Answer:

```text
A file containing information
about managed hosts and groups.
```

---

Example

```ini
[web]
server1
server2

[db]
server3
```

---

## Q7: Why use Groups?

Answer:

Because actions can target:

```text
One Host

or

Many Hosts
```

---

Example

```bash
ansible web -m ping
```

Runs on all web servers.

---

## Q8: Static vs Dynamic Inventory?

### Static

```text
Manually Defined
```

### Dynamic

```text
Generated Automatically
```

from:

* Amazon Web Services
* Microsoft Azure
* Google Cloud

---

## Q9: Why use Dynamic Inventory?

Because cloud servers:

```text
Appear

Disappear

Scale
```

constantly.

---

## Q10: How do you verify inventory?

```bash
ansible-inventory --graph
```

or

```bash
ansible-inventory --list
```

---

# Section 3: Modules

---

## Q11: What is a Module?

Answer:

```text
A reusable unit of work executed by Ansible.
```

---

Examples

```text
yum

dnf

copy

service

user

file
```

---

## Q12: Difference Between Command and Shell Module?

### command

```text
Does Not Use Shell
```

---

### shell

```text
Uses Shell Features
```

---

Example

Works only with shell:

```bash
cat file.txt | grep error
```

because pipe exists.

---

## Q13: Why prefer modules over shell commands?

Because modules are:

```text
Idempotent

Safer

More Predictable
```

---

## Q14: Most Common Modules?

```text
copy

template

service

file

package

user
```

---

# Section 4: Playbooks

---

## Q15: What is a Playbook?

Answer:

```text
A YAML file containing automation tasks.
```

---

Visual

```text
Playbook

↓

Tasks

↓

Modules

↓

Execution
```

---

## Q16: Why Playbooks Instead of Adhoc Commands?

Adhoc:

```text
One Time
```

Playbook:

```text
Reusable

Version Controlled

Repeatable
```

---

## Q17: What Language Does Ansible Use?

```text
YAML
```

---

## Q18: Why YAML?

Because it's:

```text
Human Readable
```

---

## Q19: What is a Task?

Answer:

```text
Single action inside a playbook.
```

Example:

```yaml
- name: Install nginx
```

---

## Q20: What is a Play?

Answer:

```text
Collection of tasks
executed against hosts.
```

---

# Section 5: Variables

---

## Q21: Why use Variables?

Avoid hardcoding.

---

Bad

```yaml
version: 2.1
```

---

Better

```yaml
version: "{{ app_version }}"
```

---

## Q22: What is Variable Precedence?

Interview favorite.

Question:

```text
If same variable exists
in multiple places,
which wins?
```

Answer:

```text
Most Specific Wins
```

---

Memory Trick

```text
Host

beats

Group

beats

Defaults
```

---

## Q23: What is group_vars?

Variables shared by a group.

---

Example

```text
group_vars/web.yml
```

---

## Q24: What is host_vars?

Variables specific to one host.

---

Example

```text
host_vars/server1.yml
```

---

# Section 6: Facts

---

## Q25: What are Facts?

Information gathered from managed nodes.

---

Examples

```text
IP Address

OS

Memory

CPU
```

---

## Q26: Which Module Collects Facts?

```text
setup
```

---

Command

```bash
ansible all -m setup
```

---

## Q27: Why Use Facts?

Dynamic automation.

Example:

```text
Ubuntu

↓

apt

RHEL

↓

dnf
```

---

# Section 7: Handlers

---

## Q28: What is a Handler?

Special task triggered only when changes occur.

---

Example

```text
Config Changed

↓

Restart Service
```

---

## Q29: Why Handlers?

Avoid unnecessary restarts.

---

Bad

```text
Restart Every Run
```

---

Good

```text
Restart Only If Needed
```

---

## Q30: When Does Handler Run?

Only when:

```text
changed = true
```

---

# Section 8: Templates

---

## Q31: Difference Between Copy and Template?

Copy:

```text
Static File
```

Template:

```text
Dynamic File
```

---

## Q32: What is Jinja2?

Templating engine used by Ansible.

---

Example

```jinja2
Server={{ hostname }}
```

---

## Q33: Why Templates?

Because environments differ.

```text
Dev

QA

Prod
```

Need different values.

---

# Section 9: Roles

---

## Q34: What is a Role?

Reusable automation package.

---

Visual

```text
Role

↓

Tasks

Variables

Templates

Handlers

Files
```

---

## Q35: Why Roles?

Better organization.

Better reuse.

---

## Q36: Typical Role Structure?

```text
tasks/

handlers/

templates/

vars/

defaults/

files/
```

---

## Q37: What is Ansible Galaxy?

Repository of reusable roles.

---

Think:

```text
GitHub For Roles
```

---

# Section 10: Vault

---

## Q38: What is Ansible Vault?

Tool for encrypting sensitive information.

---

Examples

```text
Passwords

Tokens

Keys
```

---

## Q39: Why Vault?

Never store secrets in plain text.

---

## Q40: Vault Commands?

```bash
ansible-vault create

ansible-vault edit

ansible-vault view

ansible-vault encrypt

ansible-vault decrypt
```

---

# Section 11: Idempotency

---

## Q41: What is Idempotency?

Running multiple times produces same result.

---

Example

Install nginx.

Already installed.

Result:

```text
No Change
```

---

## Q42: Why Important?

Safe reruns.

---

Visual

```text
Run 1

↓

Configured

Run 2

↓

No Extra Change
```

---

# Section 12: Error Handling

---

## Q43: ignore_errors vs failed_when?

### ignore_errors

Ignore failure.

### failed_when

Define custom failure.

---

## Q44: What is Block?

Group of tasks.

---

## Q45: What is Rescue?

Runs when block fails.

---

Visual

```text
Block

↓

Failure

↓

Rescue
```

---

## Q46: What is Always?

Runs regardless of success/failure.

---

# Section 13: Advanced

---

## Q47: What is Serial?

Rolling deployment control.

---

Example

```yaml
serial: 1
```

Meaning:

```text
One Server At A Time
```

---

## Q48: Why Use Serial?

Avoid downtime.

---

## Q49: What Are Forks?

Parallel workers.

---

Example

```ini
forks=20
```

---

Meaning:

```text
20 Hosts Simultaneously
```

---

## Q50: Strategy Linear vs Free?

### Linear

Everyone waits.

### Free

Hosts run independently.

---

# MOST IMPORTANT SCENARIO QUESTIONS

These are the questions that decide interviews.

---

## Q51

How would you deploy application to 100 servers with zero downtime?

Answer:

```text
Use serial deployment.

Remove node from LB.

Deploy.

Health check.

Add back.

Move to next node.
```

---

## Q52

How do you store database passwords?

Answer:

```text
Ansible Vault
```

---

## Q53

How would you manage different environments?

Answer:

```text
Separate inventories

Separate variables

Dev

QA

Prod
```

---

## Q54

How would you install software on 500 servers?

Answer:

```text
Inventory Group

Playbook

Parallel Execution
```

---

## Q55

How would you troubleshoot unreachable hosts?

Answer:

```text
SSH

Inventory

Security Group

Credentials

Port 22
```

---

## Q56

Difference Between Terraform and Ansible?

Answer:

```text
Terraform
=
Infrastructure Provisioning

Ansible
=
Configuration Management
```

---

## Q57

How would Jenkins interact with Ansible?

Answer:

```text
Jenkins triggers ansible-playbook
during deployment stages.
```

---

## Q58

How does Ansible work with Kubernetes?

Answer:

```text
Ansible provisions and configures
Kubernetes clusters and can deploy
resources through Kubernetes APIs.
```

---

#  Questions

---

## Q59

Why is Ansible agentless?

## Q60

How does Ansible achieve idempotency?

## Q61

What is fact caching?

## Q62

How does dynamic inventory work?

## Q63

How does variable precedence work?

## Q64

How do handlers differ from tasks?

## Q65

How do you perform rolling updates?

## Q66

How do you secure secrets?

## Q67

How do you structure enterprise playbooks?

## Q68

How do you troubleshoot large-scale deployments?

---

# The 20 Questions Asked Most Often

```text
What is Ansible?

Why Agentless?

Inventory?

Modules?

Playbooks?

Variables?

Facts?

Handlers?

Templates?

Roles?

Galaxy?

Vault?

Idempotency?

Serial?

Forks?

Dynamic Inventory?

Terraform vs Ansible?

Jenkins + Ansible?

Kubernetes + Ansible?

Production Troubleshooting?
```

