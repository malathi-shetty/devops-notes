

# Part 1 - Foundation + Why Services Exist + Deployment Revision

---

# Before Learning Services

To understand Services, first understand:

```text
User
 ↓
Service
 ↓
Pods
 ↑
Deployment
```

Everything in today's lab revolves around this flow.

---

# The Biggest Kubernetes Problem

Imagine you build a website.

Users need to access it.

Your website runs inside Pods.

Example:

```text
Pod-1 → 10.244.0.6

Pod-2 → 10.244.0.7

Pod-3 → 10.244.0.8
```

Looks fine.

Then Pod-1 crashes.

Kubernetes creates a new Pod.

```text
Old Pod-1 → 10.244.0.6

New Pod-1 → 10.244.0.15
```

IP changed.

Now anything pointing to:

```text
10.244.0.6
```

breaks.

This is the core problem.

---

# Why Pod IPs Cannot Be Trusted

Pods are:

```text
Temporary
Replaceable
Ephemeral
```

Think:

```text
Pod dies
↓
Deployment creates new Pod
↓
New Pod gets new IP
```

Therefore:

```text
Never rely on Pod IPs.
```

This is one of the most important Kubernetes principles.

---

# Pizza Shop Analogy

---

## Pod = Chef

Imagine a pizza shop.

```text
Chef A
Chef B
Chef C
```

Each chef has a phone number.

```text
Chef A → 111

Chef B → 222

Chef C → 333
```

Customers call chefs directly.

Everything works.

---

Suddenly:

```text
Chef B quits.
```

New chef joins.

```text
Chef D → 444
```

Now everyone who knew:

```text
222
```

fails.

---

This is exactly what happens with Pods.

```text
Pod dies
↓
New Pod created
↓
New IP assigned
```

---

# Deployment = Manager

Now imagine the pizza shop manager.

You tell him:

```text
Always keep 3 chefs.
```

Manager ensures:

```text
3 chefs available
```

If one leaves:

```text
Chef quits
↓
Manager hires another chef
```

---

Deployment does the same thing.

You tell Kubernetes:

```yaml
replicas: 3
```

Meaning:

```text
Always keep 3 Pods running.
```

If one Pod dies:

```text
Deployment creates another Pod.
```

---

# Service = Reception Desk

Customers should not call chefs directly.

Instead:

```text
Customer
   ↓
Reception Desk
   ↓
Chef A
Chef B
Chef C
```

Reception desk knows:

```text
Which chefs exist

Which chefs are healthy

Who gets the next order
```

---

In Kubernetes:

```text
Customer
   ↓
Service
   ↓
Pods
```

Service becomes the stable entry point.

---

# The Golden Kubernetes Chain

Remember this forever:

```text
Deployment
     ↓
Creates
     ↓
Pods
     ↓
Service Finds Pods
     ↓
Users Access Service
```

Short version:

```text
Deployment
    ↓
Pods
    ↓
Service
    ↓
Users
```

---

# What Deployment Actually Does

Many beginners think:

```text
Deployment serves traffic.
```

Wrong.

Deployment does NOT handle traffic.

Deployment's only job:

```text
Create Pods

Replace Pods

Scale Pods
```

That's it.

---

# What Service Actually Does

Many beginners think:

```text
Service creates Pods.
```

Wrong.

Service does NOT create Pods.

Service's job:

```text
Find Pods

Load Balance Traffic

Provide Stable Access
```

---

# One-Line Memory Trick

```text
Deployment = Pod Factory

Service = Traffic Manager
```

Memorize this.

---

# Deployment Revision

Before learning Services, let's revise Deployment.

---

# Think Of Every Deployment As

```text
Identity Card
      +
Deployment Rules
      +
Pod Blueprint
      +
Container Instructions
```

---

# Deployment Manifest Skeleton

```yaml
apiVersion: apps/v1

kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:

    metadata:
      labels:
        app: nginx

    spec:

      containers:
      - name: nginx
        image: nginx:latest
```

---

# Deployment YAML (Every Line Explained)

```yaml
apiVersion: apps/v1
```

Question Kubernetes asks:

```text
Which API should understand this object?
```

Answer:

```text
apps/v1
```

---

```yaml
kind: Deployment
```

Question:

```text
What resource should I create?
```

Answer:

```text
Deployment
```

---

```yaml
metadata:
```

Question:

```text
Who are you?
```

Metadata stores identity information.

---

```yaml
name: nginx-deployment
```

Question:

```text
What is your name?
```

Answer:

```text
nginx-deployment
```

---

```yaml
spec:
```

Question:

```text
What do you want?
```

Desired state starts here.

---

```yaml
replicas: 3
```

Question:

```text
How many Pods should exist?
```

Answer:

```text
3
```

---

```yaml
selector:
```

Question:

```text
Which Pods belong to me?
```

---

```yaml
matchLabels:
```

Question:

```text
Which labels should match?
```

---

```yaml
app: nginx
```

Meaning:

```text
Manage Pods having:

app=nginx
```

---

```yaml
template:
```

Question:

```text
What should future Pods look like?
```

This is the Pod blueprint.

---

```yaml
metadata:
```

Metadata for future Pods.

---

```yaml
labels:
```

Labels attached to future Pods.

---

```yaml
app: nginx
```

Every Pod created gets:

```yaml
app: nginx
```

---

```yaml
spec:
```

Pod specification begins.

---

```yaml
containers:
```

Question:

```text
What containers should run?
```

---

```yaml
name: nginx
```

Container name.

---

```yaml
image: nginx:latest
```

Docker image to pull and run.

---

# Read Deployment YAML Like English

Take the entire YAML and translate it:

```text
Use apps/v1 API.

Create a Deployment.

Call it nginx-deployment.

Keep 3 Pods running.

Manage Pods having app=nginx.

Use this template when creating Pods.

Give every Pod label app=nginx.

Inside each Pod run nginx container.

Pull nginx:latest image.
```

This is exactly how seniors mentally read Deployment YAML.

---

# Deployment Ownership Tree

This is extremely important.

Most beginners struggle because they don't understand indentation.

Think:

```text
Deployment
│
├── apiVersion
├── kind
│
├── metadata
│    └── name
│
└── spec
     │
     ├── replicas
     │
     ├── selector
     │    └── matchLabels
     │          └── app
     │
     └── template
          │
          ├── metadata
          │    └── labels
          │          └── app
          │
          └── spec
               │
               └── containers
                    │
                    ├── name
                    └── image
```

---

# Parent → Child Example

Look at:

```yaml
spec:
  replicas: 3
```

Means:

```text
replicas belongs to spec
```

---

Look at:

```yaml
metadata:
  name: nginx-deployment
```

Means:

```text
name belongs to metadata
```

---

Look at:

```yaml
containers:
- name: nginx
  image: nginx:latest
```

Means:

```text
name belongs to container

image belongs to container
```

---

# Deployment Mental Model

When Kubernetes reads Deployment YAML it asks:

```text
1. What are you creating?

2. What is its name?

3. How many Pods do you want?

4. Which Pods belong to you?

5. What should new Pods look like?

6. Which container should run?

7. Which image should be pulled?
```

If Kubernetes gets those answers:

```text
Deployment created successfully.
```

---

# Most Important Takeaways From Part 1

Remember these:

```text
Pods are temporary.
```

```text
Pod IPs change.
```

```text
Deployment creates Pods.
```

```text
Deployment maintains replica count.
```

```text
Service does NOT create Pods.
```

```text
Service finds Pods.
```

```text
Service routes traffic to Pods.
```

```text
Deployment = Pod Factory
```

```text
Service = Traffic Manager
```

```text
Deployment
    ↓
Pods
    ↓
Service
    ↓
Users
```

---



# Part 2 - Service Fundamentals + Service YAML Deep Dive

---

# What Is A Kubernetes Service?

Before Services:

```text
User
 ↓
Pod IP
```

Example:

```text
User
 ↓
10.244.0.6
```

Works fine.

Then Pod restarts.

```text
10.244.0.6
        ↓
10.244.0.15
```

Application breaks.

---

# Service Solves This Problem

Instead of talking to Pods:

```text
User
 ↓
Service
 ↓
Pods
```

Users never know Pod IPs.

Users only know:

```text
Service Name

or

Service IP
```

---

# One-Line Definition

A Service is:

```text
A stable network endpoint
that provides access to Pods.
```

Or simpler:

```text
Service = Permanent Address

Pods = Temporary Workers
```

---

# Real World Analogy

Imagine a company.

---

## Employees = Pods

```text
Employee A

Employee B

Employee C
```

Employees come and go.

---

## Company Phone Number = Service

Customers call:

```text
1800-COMPANY
```

not:

```text
Employee's personal number
```

Even if employees change:

```text
Company phone number stays same.
```

That company phone number is a Service.

---

# What Does A Service Actually Do?

A Service has 3 major jobs.

---

## Job 1: Stable IP

Pods:

```text
10.244.0.6

10.244.0.7

10.244.0.8
```

can change.

Service:

```text
10.96.156.88
```

stays stable.

---

## Job 2: Stable DNS Name

Instead of:

```text
10.96.156.88
```

you can use:

```text
web-app-clusterip
```

or

```text
web-app-clusterip.default.svc.cluster.local
```

Much easier.

---

## Job 3: Load Balancing

Suppose:

```text
Pod-1
Pod-2
Pod-3
```

exist.

Service distributes traffic.

```text
Request 1 → Pod-1

Request 2 → Pod-2

Request 3 → Pod-3
```

Service distributes traffic across available Pods.
The exact balancing behavior depends on Kubernetes networking implementation.

---

# Think Of Every Service As

```text
Identity Card
      +
Service Rules
      +
Pod Finder
      +
Traffic Rules
```

---

# Service Mental Model

Read every Service as:

```text
I am a Service.

My name is ______.

I should expose Pods whose label is ______.

I will listen on port ______.

I will forward traffic to Pod port ______.

I am of type ______.
```

This is the easiest way to understand Services.

---

# Service Manifest Skeleton

```yaml
apiVersion: v1                   # Which Kubernetes API should understand this object?

kind: Service                    # What resource should Kubernetes create?

metadata:                        # Identity of Service

  name: my-service          # Service name

spec:                            # Desired state / Service rules

  type: ClusterIP               # How should users reach me?

  selector:                     # Which Pods belong to me?

    app: my-app                # Find Pods having app=web-app

  ports:                        # Traffic rules

  - port: 80                    # Service listens on port 80

    targetPort: 80              # Forward traffic to Pod port 80
```

This skeleton works for almost every Service.

---

# Service YAML (Every Line Explained)

---

## apiVersion

```yaml
apiVersion: v1
```

Question Kubernetes asks:

```text
Which API should understand this object?
```

Answer:

```text
v1
```

---

## kind

```yaml
kind: Service
```

Question:

```text
What resource should I create?
```

Answer:

```text
Service
```

---

## metadata

```yaml
metadata:
```

Question:

```text
Who are you?
```

Stores identity information.

---

## name

```yaml
name: web-app-service
```

Question:

```text
What is your name?
```

Answer:

```text
web-app-service
```

---

## spec

```yaml
spec:
```

Question:

```text
What do you want me to do?
```

Everything inside spec is desired state.

---

## type

```yaml
type: ClusterIP
```

Question:

```text
How should users reach you?
```

Answer:

```text
Inside cluster only.
```

We will learn all Service types later.

---

## selector

```yaml
selector:
```

Question:

```text
Which Pods belong to you?
```

Service does not know Pod names.

Service finds Pods using labels.

---

## app label

```yaml
app: web-app
```

Meaning:

```text
Find Pods having:

app=web-app
```

---

## ports

```yaml
ports:
```

Question:

```text
How should traffic flow?
```

Traffic configuration starts here.

---

## port

```yaml
port: 80
```

Question:

```text
Which port should Service listen on?
```

Answer:

```text
80
```

---

## targetPort

```yaml
targetPort: 80
```

Question:

```text
Which Pod port receives traffic?
```

Answer:

```text
80
```

---

# Read Service YAML Like English

Given:

```yaml
apiVersion: v1

kind: Service

metadata:
  name: web-app-service

spec:
  type: ClusterIP

  selector:
    app: web-app

  ports:
  - port: 80
    targetPort: 80
```

Translate to English:

```text
Use v1 API.

Create a Service.

Call it web-app-service.

Make it a ClusterIP Service.

Find Pods having app=web-app.

Listen on port 80.

Forward traffic to Pod port 80.
```

This is exactly how experienced engineers read YAML.

---

### See how similar this is to Deployment?

Deployment says:
```bash
Create Pods.
```
Service says:
```bash
Find Pods and route traffic to them.
```
---

# Kubernetes  Mode

When Kubernetes reads Service YAML it asks:

```text
1. What are you creating?

2. What is its name?

3. What type of Service is it?

4. Which Pods belong to it?

5. Which port should I listen on?

6. Which Pod port should I forward traffic to?
```

If those answers exist:

```text
Service created.
```

---

# Service Ownership Tree

This is one of the most important diagrams.

```text
Service
│
├── apiVersion
├── kind
│
├── metadata
│    └── name
│
└── spec
     │
     ├── type
     │
     ├── selector
     │    └── app
     │
     └── ports
          │
          ├── port
          │
          └── targetPort
```

---

# Parent → Child Understanding

---

## Example 1

```yaml
metadata:
  name: web-app-service
```

Meaning:

```text
name belongs to metadata
```

---

## Example 2

```yaml
selector:
  app: web-app
```

Meaning:

```text
app belongs to selector
```

---

## Example 3

```yaml
ports:
- port: 80
  targetPort: 80
```

Meaning:

```text
port belongs to ports

targetPort belongs to ports
```

---

# Labels And Selectors

This is the heart of Services.

---

## Pod Labels

Suppose Pod has:

```yaml
labels:
  app: web-app
```

---

## Service Selector

Service:

```yaml
selector:
  app: web-app
```

Service asks:

```text
Show me Pods having:

app=web-app
```

Pod replies:

```text
I have app=web-app
```

Match found.

Traffic can flow.

---

# School Bus Analogy

Pods = Students

Labels = School Badges

---

Students:

```text
Student A → Blue Badge

Student B → Blue Badge

Student C → Red Badge
```

---

Service:

```yaml
selector:
  app: blue
```

Meaning:

```text
Only students wearing blue badges
can board this bus.
```

---

This is exactly how selectors work.

---

# Why Selectors Are Important

Without selector:

```text
Service cannot find Pods.
```

Wrong selector:

```yaml
selector:
  app: database
```

Pods:

```yaml
labels:
  app: web-app
```

Result:

```text
No match.

No endpoints.

No traffic.
```

---

# Service → Pod Connection

Think:

```text
Labels
     ↓
Selectors
     ↓
Service Finds Pods
```

---

# Port vs TargetPort

Very important  question.

---

## port

```yaml
port: 80
```

Service listens here.

---

## targetPort

```yaml
targetPort: 80
```

Pod receives traffic here.

---

# Example 1

```yaml
port: 80
targetPort: 80
```

Traffic:

```text
User
 ↓
80
 ↓
80
```

---

# Example 2

```yaml
port: 80
targetPort: 8080
```

Traffic:

```text
User
 ↓
Service Port 80
 ↓
Pod Port 8080
```

Perfectly valid.

---

# Real World Analogy

Imagine a hotel.

---

Reception Number:

```text
100
```

Room Number:

```text
505
```

Customer calls:

```text
100
```

Reception forwards to:

```text
505
```

---

In Kubernetes:

```text
port = Reception Number

targetPort = Room Number
```

---

# Common Beginner Mistake

Many people think:

```text
port and targetPort
must be same.
```

Wrong.

They can be different.

Example:

```yaml
ports:
- port: 80
  targetPort: 3000
```

This is very common.

---

# Service Formula

Every Service can be remembered as:

```text
Service
     +
Selector
     +
Ports
```

Or:

```text
Service
     ↓
Find Pods
     ↓
Forward Traffic
```

---

# Most Important Things To Remember

```text
Service does not create Pods.
```

```text
Deployment creates Pods.
```

```text
Service finds Pods using labels.
```

```text
Selector must match Pod labels.
```

```text
port = Service Port
```

```text
targetPort = Pod Port
```

```text
Service provides:
- Stable IP
- Stable DNS
- Load Balancing
```

---

# Ultimate Service Memory Trick

Read every Service like this:

```text
I am a Service.

My name is ______.

I should expose Pods whose label is ______.

I will listen on port ______.

I will forward traffic to Pod port ______.

I am of type ______.
```

If you can fill those six blanks, you understand Services.

---



# Part 3 - Task 1 Lab: Deploy the Application

This section covers the **actual lab you performed**.

---

# Goal of Task 1

Before creating Services, we need Pods.

Services cannot route traffic unless Pods exist.

Think:

```text
No Pods
↓
Nothing to Route Traffic To
↓
Service Useless
```

Therefore:

```text
Step 1
Create Deployment

Step 2
Deployment Creates Pods

Step 3
Service Finds Pods
```

---

# What We Created

File:

```text
app-deployment.yaml
```

Contents:

```yaml
apiVersion: apps/v1

kind: Deployment

metadata:
  name: web-app

  labels:
    app: web-app

spec:

  replicas: 3

  selector:
    matchLabels:
      app: web-app

  template:

    metadata:
      labels:
        app: web-app

    spec:

      containers:
      - name: nginx

        image: nginx:1.25

        ports:
        - containerPort: 80
```

---

# Read This YAML Like English

```text
Use apps/v1 API.

Create a Deployment.

Call it web-app.

Attach label app=web-app.

Keep 3 Pods running.

Manage Pods having app=web-app.

Use this template when creating Pods.

Give every Pod label app=web-app.

Run nginx container.

Use nginx:1.25 image.

Container listens on port 80.
```

---

# YAML Ownership Tree

```text
Deployment
│
├── apiVersion
├── kind
│
├── metadata
│    │
│    ├── name
│    │
│    └── labels
│         │
│         └── app
│
└── spec
     │
     ├── replicas
     │
     ├── selector
     │    │
     │    └── matchLabels
     │          │
     │          └── app
     │
     └── template
          │
          ├── metadata
          │     │
          │     └── labels
          │            │
          │            └── app
          │
          └── spec
                │
                └── containers
                      │
                      ├── name
                      │
                      ├── image
                      │
                      └── ports
                            │
                            └── containerPort
```

---

# Why Labels Matter

Notice:

Deployment selector:

```yaml
selector:
  matchLabels:
    app: web-app
```

Pod labels:

```yaml
labels:
  app: web-app
```

Both must match.

---

Think:

```text
Deployment asks:

"Which Pods belong to me?"
```

Answer:

```text
Any Pod having:

app=web-app
```

---

# Applying The Deployment

Command:

```bash
kubectl apply -f app-deployment.yaml
```

Output:

```text
deployment.apps/web-app created
```

Meaning:

```text
Deployment created successfully.
```

---

# Verify Deployment

Command:

```bash
kubectl get deployments
```

Expected:

```text
NAME      READY   UP-TO-DATE   AVAILABLE

web-app   3/3     3            3
```

Meaning:

```text
Desired Pods = 3

Running Pods = 3
```

---

# Verify Pods

Command:

```bash
kubectl get pods -o wide
```

Your output:

```text
web-app-5c44989c65-6jsqd   Running   10.244.0.6

web-app-5c44989c65-h8q6k   Running   10.244.0.7

web-app-5c44989c65-qvk9b   Running   10.244.0.8
```

---

# Verification Answer

Question:

```text
Are all 3 Pods running?
```

Answer:

```text
YES
```

---

Question:

```text
What are the Pod IPs?
```

Answer:

```text
10.244.0.6

10.244.0.7

10.244.0.8
```

---

# Why Were We Asked To Note Pod IPs?

This is extremely important.

Most beginners think:

```text
Great.

I'll use these IPs forever.
```

Wrong.

---

# The Hidden Problem

Suppose Pod:

```text
10.244.0.6
```

dies.

Deployment creates replacement Pod.

New Pod:

```text
10.244.0.15
```

Now:

```text
10.244.0.6
```

does not exist.

---

# Visualizing The Problem

Before restart:

```text
Pod A → 10.244.0.6

Pod B → 10.244.0.7

Pod C → 10.244.0.8
```

---

After restart:

```text
Pod A → 10.244.0.15

Pod B → 10.244.0.7

Pod C → 10.244.0.8
```

---

Clients still trying:

```text
10.244.0.6
```

fail.

---

# This Is Why Services Exist

Without Service:

```text
Client
 ↓
Pod IP
```

Bad.

---

With Service:

```text
Client
 ↓
Service
 ↓
Pods
```

Good.

---

Users connect to:

```text
Stable Service
```

instead of:

```text
Changing Pod IPs
```

---

# Understanding Replicas

You configured:

```yaml
replicas: 3
```

Meaning:

```text
Always keep 3 Pods.
```

---

If 1 Pod dies:

```text
3 Pods
 ↓
2 Pods
```

Deployment notices:

```text
Expected = 3

Actual = 2
```

Deployment fixes it:

```text
Create New Pod
```

Back to:

```text
3 Pods
```

---

# What Deployment Is Actually Doing

Deployment continuously checks:

```text
Desired State
vs
Actual State
```

Example:

Desired:

```text
3 Pods
```

Actual:

```text
2 Pods
```

Mismatch.

Deployment creates:

```text
1 new Pod
```

Now:

```text
3 Pods
```

State restored.

---

# How Kubernetes Named The Pods

You saw:

```text
web-app-5c44989c65-6jsqd

web-app-5c44989c65-h8q6k

web-app-5c44989c65-qvk9b
```

Structure:

```text
web-app
   ↓
Deployment Name

5c44989c65
   ↓
ReplicaSet Hash

6jsqd
   ↓
Random Pod ID
```

---

# Resource Hierarchy

Very important.

```text
Deployment
     ↓
ReplicaSet
     ↓
Pods
```

---

Actual hierarchy:

```text
web-app (Deployment)
        ↓
ReplicaSet
        ↓
web-app-xxxxx
web-app-xxxxx
web-app-xxxxx
```

---

# How To Inspect Everything

Deployment:

```bash
kubectl get deployment
```

---

ReplicaSet:

```bash
kubectl get rs
```

---

Pods:

```bash
kubectl get pods
```

---

Detailed Deployment:

```bash
kubectl describe deployment web-app
```

---

Detailed Pod:

```bash
kubectl describe pod <pod-name>
```



---

## What does a Deployment do?

Answer:

```text
Creates Pods.

Maintains desired replica count.

Replaces failed Pods.

Manages rolling updates.
```

---

## Why not use Pod IP directly?

Answer:

```text
Pod IPs are temporary.

Pods get new IPs after restart.
```

---

## What does replicas: 3 mean?

Answer:

```text
Always keep 3 Pods running.
```

---

## How does Deployment know which Pods belong to it?

Answer:

```text
Using labels and selectors.
```

Example:

```yaml
selector:
  matchLabels:
    app: web-app
```

---

# Most Important Things Learned In Task 1

```text
Deployment creates Pods.
```

```text
Pods get IP addresses.
```

```text
Pod IPs are NOT permanent.
```

```text
Deployment maintains desired replica count.
```

```text
replicas: 3
=
Always keep 3 Pods running.
```

```text
Deployment uses labels to identify Pods.
```

```text
Clients should NOT use Pod IPs directly.
```

```text
This is the exact reason Services exist.
```

---

# Mental Model After Task 1

At this point in the lab:

```text
Deployment
     ↓
Created
     ↓
3 Pods
```

Current state:

```text
User ❌

Service ❌

Deployment ✅

Pods ✅
```

We have Pods.

But users still cannot safely access them.

Next step:

```text
Create Service
↓
Give Pods Stable Access
```

---



# Part 4 - Task 2: ClusterIP Service (Internal Access)

---

# Why We Need A Service Now

After Task 1:

```text
Deployment
     ↓
3 Pods
```

Pods existed:

```text
10.244.0.6

10.244.0.7

10.244.0.8
```

Problem:

```text
Pods can restart.

Pod IPs can change.
```

So we need:

```text
One Stable Address
```

That stable address is:

```text
Service
```

---

# What Is ClusterIP?

ClusterIP is the default Service type.

Think:

```text
Inside Cluster Only
```

---

# Real Life Example

Imagine a school.

Students:

```text
Student A

Student B

Student C
```

sit in different classrooms.

Classrooms may change.

---

Teacher doesn't remember:

```text
Classroom Number
```

Teacher remembers:

```text
Class Leader
```

The Class Leader always knows where students are.

---

In Kubernetes:

```text
Students = Pods

Class Leader = Service
```

---

# ClusterIP Definition

A ClusterIP Service:

```text
Creates a stable internal IP

Creates a DNS name

Load balances traffic

Works only inside cluster
```

---

# Service YAML Used In Lab

File:

```text
clusterip-service.yaml
```

Content:

```yaml
apiVersion: v1

kind: Service

metadata:
  name: web-app-clusterip

spec:
  type: ClusterIP

  selector:
    app: web-app

  ports:
  - port: 80
    targetPort: 80
```

---

# Read YAML Like English

```text
Use v1 API.

Create a Service.

Call it web-app-clusterip.

Make it a ClusterIP Service.

Find Pods having app=web-app.

Listen on port 80.

Forward traffic to Pod port 80.
```

---

# Ownership Tree

```text
Service
│
├── apiVersion
├── kind
│
├── metadata
│    └── name
│
└── spec
     │
     ├── type
     │
     ├── selector
     │    └── app
     │
     └── ports
          │
          ├── port
          │
          └── targetPort
```

---

# Kubernetes Questions While Reading This YAML

Kubernetes asks:

```text
What should I create?
```

Answer:

```text
Service
```

---

Question:

```text
What is its name?
```

Answer:

```text
web-app-clusterip
```

---

Question:

```text
How should users reach it?
```

Answer:

```text
ClusterIP
```

---

Question:

```text
Which Pods belong to it?
```

Answer:

```text
app=web-app
```

---

Question:

```text
Which port should it listen on?
```

Answer:

```text
80
```

---

Question:

```text
Where should traffic go?
```

Answer:

```text
Pod Port 80
```

---

# Apply Service

Command:

```bash
kubectl apply -f clusterip-service.yaml
```

Output:

```text
service/web-app-clusterip created
```

Meaning:

```text
Service successfully created.
```

---

# Verify Service

Command:

```bash
kubectl get svc
```

Your output contained:

```text
web-app-clusterip
ClusterIP
10.96.156.88
80/TCP
```

---

# Understanding The Output

```text
NAME
```

Service name:

```text
web-app-clusterip
```

---

```text
TYPE
```

Service type:

```text
ClusterIP
```

---

```text
CLUSTER-IP
```

Service IP:

```text
10.96.156.88
```

---

Important:

```text
This IP is stable.
```

Even if Pods restart:

```text
10.96.156.88
```

stays same.

---

# Why Is ClusterIP Important?

Before:

```text
User
 ↓
10.244.0.6
```

Dangerous.

---

After:

```text
User
 ↓
10.96.156.88
 ↓
Pods
```

Safe.

---

# Service Discovers Pods Using Labels

Pods:

```yaml
labels:
  app: web-app
```

Service:

```yaml
selector:
  app: web-app
```

Match found:

```text
app=web-app
```

Service automatically attaches to Pods.

---

# Service Traffic Flow

```text
Client
 ↓
ClusterIP Service
 ↓
Pod
```

Example:

```text
Client
 ↓
10.96.156.88
 ↓
10.244.0.6
```

---

Next request:

```text
Client
 ↓
10.96.156.88
 ↓
10.244.0.7
```

---

Next request:

```text
Client
 ↓
10.96.156.88
 ↓
10.244.0.8
```

---

This is load balancing.

---

# Why We Needed BusyBox

ClusterIP is:

```text
Internal Only
```

Meaning:

```text
Cannot be reached from browser.

Cannot be reached from laptop.

Cannot be reached from internet.
```

Must test:

```text
Inside Cluster
```

ClusterIP is designed for internal cluster communication and is not directly accessible from outside the cluster.

---

# Create Temporary Test Pod

Command:

```bash
kubectl run test-client \
--image=busybox:latest \
--rm -it \
--restart=Never -- sh
```

---

# Understanding This Command

```bash
kubectl run
```

Create Pod.

---

```bash
test-client
```

Pod name.

---

```bash
--image=busybox:latest
```

Use BusyBox image.

---

```bash
--rm
```

Delete pod after exit.

---

```bash
-it
```

Interactive terminal.

---

```bash
sh
```

Open shell.

---

# Inside BusyBox

Command:

```bash
wget -qO- http://web-app-clusterip
```

Output:

```html
Welcome to nginx!
```

Success.

---

# What Happened Behind The Scenes?

You typed:

```bash
wget http://web-app-clusterip
```

Kubernetes:

```text
DNS lookup
 ↓
Find Service
 ↓
Get ClusterIP
 ↓
Forward to Pod
 ↓
Return HTML
```

---

# Visual Flow

```text
BusyBox Pod
       ↓
web-app-clusterip
       ↓
ClusterIP
       ↓
One of the nginx Pods
       ↓
Nginx Welcome Page
```

---

# Verification Question

Question:

```text
Does the Service respond?
```

Answer:

```text
YES
```

Evidence:

```html
Welcome to nginx!
```

page returned successfully.

---

# Another Verification Question

Question:

```text
Try wget multiple times.
```

Why?

Because Kubernetes:

```text
Load balances traffic.
```

---

# Load Balancing Example

Request 1:

```text
Pod A
```

Request 2:

```text
Pod B
```

Request 3:

```text
Pod C
```

---

User sees:

```text
Same nginx page.
```

But behind the scenes:

```text
Different Pods may serve requests.
```

---

# What Is An Endpoint?

This becomes important later.

Service does NOT directly store Pods.

Instead:

```text
Service
 ↓
EndpointSlices
 ↓
Pods
```

Modern Kubernetes stores backend endpoint information in EndpointSlice resources.

---

Think:

```text
Service = Reception Desk

Endpoints = Employee Directory

Pods = Employees
```

---

# Check Endpoints

Command:

```bash
kubectl get endpoints web-app-clusterip
```

Would show something like:

```text
10.244.0.6:80

10.244.0.7:80

10.244.0.8:80
```

These are real backend Pods.

---

# Common Beginner Mistake

People think:

```text
Service contains Pods.
```

Not exactly.

Actually:

```text
Service
 ↓
Endpoints
 ↓
Pods
```

---

# ClusterIP Memory Trick

Think:

```text
ClusterIP
=
Cluster Only
```

If someone asks:

```text
Can browser access ClusterIP?
```

Answer:

```text
No.
```

---

If someone asks:

```text
Can another Pod access ClusterIP?
```

Answer:

```text
Yes.
```

---



### What is ClusterIP?

```text
Default Kubernetes Service type.

Provides stable internal IP.

Accessible only within cluster.
```

---

### Why use ClusterIP?

```text
Stable networking between services.
```

---

### Can ClusterIP be accessed externally?

```text
No.
```

---

### How does Service find Pods?

```text
Using selectors and labels.
```

Example:

```yaml
selector:
  app: web-app
```

---

### Why did we use BusyBox?

```text
ClusterIP can only be tested from inside cluster.
```

---

# Actual Lab Verification Answer

Question:

```text
Does the Service respond?
```

Answer:

```text
Yes.
```

Evidence:

```html
Welcome to nginx!
```

was returned successfully from:

```bash
wget -qO- http://web-app-clusterip
```

---

Question:

```text
What is the ClusterIP?
```

Answer:

```text
10.96.156.88
```

---

Question:

```text
Why is ClusterIP useful?
```

Answer:

```text
Provides stable IP and DNS name even if Pods restart.
```

---

# What We Learned In Task 2

```text
ClusterIP is the default Service type.
```

```text
ClusterIP works only inside cluster.
```

```text
Service provides stable IP.
```

```text
Service provides stable DNS.
```

```text
Service load balances traffic.
```

```text
Service finds Pods using labels.
```

```text
BusyBox is useful for testing internal services.
```

```text
Endpoints contain real Pod IPs.
```

---

# Mental Model After Task 2

Current architecture:

```text
Deployment
      ↓
3 Pods
      ↓
ClusterIP Service
      ↓
Stable IP
      ↓
Stable DNS
```

Now internal communication works.

Next problem:

```text
How can users outside the cluster
access the application?
```

That is solved by:

```text
NodePort Service
```

---




# Part 5 - Task 3: Kubernetes DNS & Service Discovery

---

# Why DNS Exists

Let's first forget Kubernetes.

Imagine your friend says:

```text
My website IP is:

142.251.42.78
```

Would you remember it?

Probably not.

---

Instead we use:

```text
google.com
```

Much easier.

DNS converts:

```text
google.com
      ↓
142.251.42.78
```

---

# Same Problem Exists In Kubernetes

Pods have IPs:

```text
10.244.0.6

10.244.0.7

10.244.0.8
```

Hard to remember.

Also:

```text
Pod IPs change.
```

Bad idea.

---

So Kubernetes creates DNS names automatically.

Instead of:

```text
10.96.156.88
```

you can use:

```text
web-app-clusterip
```

Much easier.

---

# What Is Kubernetes DNS?

Kubernetes runs a DNS server inside the cluster.

Usually:

```text
CoreDNS
```

CoreDNS automatically creates DNS records for:

```text
Services
```

and sometimes:

```text
Pods
```

---

# Real Life Analogy

Imagine a school.

Instead of remembering:

```text
Student:
Roll No 4321
```

you remember:

```text
Rahul
```

DNS is basically:

```text
Name
 ↓
Address
```

lookup.

---

# What Happens When A Service Is Created?

You created:

```yaml
metadata:
  name: web-app-clusterip
```

Kubernetes automatically created DNS records.

You never wrote:

```yaml
dns:
```

anywhere.

Kubernetes did it automatically.

---

# DNS Names Kubernetes Creates

For a Service:

```text
web-app-clusterip
```

Kubernetes creates:

```text
web-app-clusterip.default.svc.cluster.local
```

---

# Anatomy Of Full DNS Name

```text
web-app-clusterip.default.svc.cluster.local
```

Break it:

```text
web-app-clusterip
      ↓
Service Name
```

```text
default
      ↓
Namespace
```

```text
svc
      ↓
Service
```

```text
cluster.local
      ↓
Cluster Domain
```

---

# Memory Trick

Think:

```text
service.namespace.svc.cluster.local
```

Always.

---

Example:

```text
web-app-clusterip.default.svc.cluster.local
```

means:

```text
Service:
web-app-clusterip

Namespace:
default
```

---

# Short Name vs Full Name

Kubernetes allows two styles.

---

## Short Name

```text
web-app-clusterip
```

Used when:

```text
Both Pods are in same namespace.
```

---

## Full Name

```text
web-app-clusterip.default.svc.cluster.local
```

Used when:

```text
Cross-namespace communication

or

Explicit DNS reference
```

---

# Your Actual Test

You created BusyBox:

```bash
kubectl run dns-test \
--image=busybox:latest \
--rm -it \
--restart=Never -- sh
```

---

# Test 1: Short Name

Command:

```bash
wget -qO- http://web-app-clusterip
```

Result:

```html
Welcome to nginx!
```

Success.

---

# What Happened Internally?

```text
BusyBox Pod
      ↓
DNS Query
      ↓
web-app-clusterip
      ↓
CoreDNS
      ↓
10.96.156.88
      ↓
Service
      ↓
Nginx Pod
      ↓
Response
```

---

# Test 2: Full DNS Name

Command:

```bash
wget -qO- http://web-app-clusterip.default.svc.cluster.local
```

Result:

```html
Welcome to nginx!
```

Success.

---

# Why Did Both Work?

Because both names point to:

```text
10.96.156.88
```

the same Service.

---

Think:

```text
Rahul

and

Rahul Sharma
```

Both refer to same person.

---

# DNS Resolution Flow

When you typed:

```bash
wget http://web-app-clusterip
```

BusyBox asks:

```text
What is web-app-clusterip?
```

CoreDNS replies:

```text
10.96.156.88
```

Then request continues.

---

# Your nslookup Command

You ran:

```bash
nslookup web-app-clusterip
```

Output:

```text
Server: 10.96.0.10

Address: 10.96.0.10:53
```

---

# What Is 10.96.0.10?

That is:

```text
CoreDNS Service IP
```

Kubernetes DNS Server.

Think:

```text
Phone Directory
```

for the cluster.

---

# Important Part Of Your Output

You saw:

```text
Name:
web-app-clusterip.default.svc.cluster.local

Address:
10.96.156.88
```

This is the important answer.

---

# Verification Answer

Question:

```text
What IP did nslookup return?
```

Answer:

```text
10.96.156.88
```

---

Question:

```text
Does it match kubectl get svc?
```

Your service:

```bash
kubectl get svc web-app-clusterip
```

showed:

```text
CLUSTER-IP

10.96.156.88
```

DNS returned:

```text
10.96.156.88
```

Perfect match.

---

# Why Did NXDOMAIN Appear?

You saw:

```text
server can't find
web-app-clusterip.svc.cluster.local

NXDOMAIN
```

and got worried.

---

Most beginners think:

```text
DNS broken.
```

Actually:

```text
DNS working perfectly.
```

---

# What Is NXDOMAIN?

NXDOMAIN means:

```text
Non-Existent Domain
```

or:

```text
No record found.
```

---

# Why Did BusyBox Show NXDOMAIN?

BusyBox tries multiple search paths automatically.

It attempts:

```text
web-app-clusterip.svc.cluster.local
```

then

```text
web-app-clusterip.cluster.local
```

then

```text
web-app-clusterip.default.svc.cluster.local
```

---

Some fail.

One succeeds.

---

The successful result:

```text
web-app-clusterip.default.svc.cluster.local
```

returned:

```text
10.96.156.88
```

So everything worked.

---

# Think Of It Like This

You ask:

```text
Rahul?
```

No response.

Then:

```text
Rahul Sharma?
```

Found.

That first failure doesn't matter.

---

# Service Discovery

This entire DNS system is called:

```text
Service Discovery
```

Meaning:

```text
Applications find each other
using names
instead of IPs.
```

---

# Example

Frontend Pod:

```text
frontend
```

needs Database.

Without DNS:

```text
10.244.0.15
```

Bad.

---

With DNS:

```text
mysql-service
```

Good.

---

Application code:

```text
Connect to mysql-service
```

instead of:

```text
Connect to 10.244.0.15
```

---

# Why DNS Is Powerful

Imagine database Pod restarts.

Old IP:

```text
10.244.0.15
```

New IP:

```text
10.244.0.21
```

Application still works because:

```text
mysql-service
```

didn't change.

---

# DNS Mental Model

```text
Application
      ↓
Service Name
      ↓
CoreDNS
      ↓
ClusterIP
      ↓
Service
      ↓
Pod
```

---

# Kubernetes Networking Chain

This is one of the most important diagrams.

```text
Application
      ↓
DNS Name
      ↓
ClusterIP
      ↓
Endpoints
      ↓
Pod IP
      ↓
Container
```

Everything in Kubernetes networking follows this flow.

---

# Commands You Learned

Check Service:

```bash
kubectl get svc
```

---

Test DNS:

```bash
nslookup web-app-clusterip
```

---

Test Short Name:

```bash
wget http://web-app-clusterip
```

---

Test Full Name:

```bash
wget http://web-app-clusterip.default.svc.cluster.local
```

---

#  Questions

## What DNS server does Kubernetes use?

Answer:

```text
CoreDNS
```

---

## Does every Service get a DNS name?

Answer:

```text
Yes
```

---

## What is the full DNS format?

Answer:

```text
service.namespace.svc.cluster.local
```

---

## Can Pods communicate using Service names?

Answer:

```text
Yes
```

---

## Why use DNS instead of Pod IPs?

Answer:

```text
Pod IPs change.

Service names remain stable.
```

---

# What We Learned In Task 3

```text
Kubernetes automatically creates DNS entries.
```

```text
CoreDNS provides name resolution.
```

```text
Services have short names.
```

```text
Services have full DNS names.
```

```text
DNS resolves Service Name → ClusterIP.
```

```text
Applications should use Service names.
```

```text
Pod IPs should not be hardcoded.
```

```text
Service discovery is based on DNS.
```

```text
NXDOMAIN in your output was normal.
```

```text
DNS successfully resolved:
10.96.156.88
```

---

# Lab Verification Answer

```text
nslookup returned:

10.96.156.88
```

```text
It exactly matched:

kubectl get svc web-app-clusterip
```

```text
DNS was working correctly.
```

---

# Mental Model After Task 3

Current Architecture:

```text
Client Pod
     ↓
DNS Name
(web-app-clusterip)
     ↓
CoreDNS
     ↓
10.96.156.88
     ↓
Service
     ↓
Endpoints
     ↓
Nginx Pods
```

Everything inside the cluster now works perfectly.

Next problem:

```text
How can a user outside Kubernetes
access the application?
```

That is solved by:

```text
NodePort Service
```

---




# Part 6 - Task 4: NodePort Service (External Access)

---

# Problem After Task 3

Current situation:

```text
Inside Cluster
      ↓
Everything Works
```

You successfully accessed:

```text
web-app-clusterip
```

and

```text
web-app-clusterip.default.svc.cluster.local
```

from BusyBox.

---

But now imagine a real user.

User sits outside Kubernetes.

```text
Browser
   ↓
Internet
```

Can browser access:

```text
web-app-clusterip
```

No.

---

Can browser access:

```text
10.96.156.88
```

No.

---

Why?

Because:

```text
ClusterIP
=
Internal Only
```

---

# New Goal

We want:

```text
Internet User
       ↓
Kubernetes App
```

This is where:

```text
NodePort
```

comes in.

---

# What Is NodePort?

NodePort exposes your Service on a port of every Kubernetes node.

Think:

```text
ClusterIP
=
Inside Building

NodePort
=
Building Main Gate
```

---

# Real Life Example

Imagine a company.

---

Employees:

```text
Pods
```

---

Reception Desk:

```text
ClusterIP Service
```

---

Main Building Gate:

```text
NodePort
```

---

Visitors outside can only use:

```text
Main Gate
```

not internal desks.

---

# NodePort Definition

NodePort:

```text
Creates ClusterIP

Creates NodePort

Allows external access
```

---

Traffic Flow:

```text
User
 ↓
Node IP
 ↓
NodePort
 ↓
Service
 ↓
Pod
```

---

# Service YAML Used

File:

```text
nodeport-service.yaml
```

---

Content:

```yaml
apiVersion: v1

kind: Service

metadata:
  name: web-app-nodeport

spec:
  type: NodePort

  selector:
    app: web-app

  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```

---

# Read This YAML Like English

```text
Use v1 API.

Create a Service.

Call it web-app-nodeport.

Make it a NodePort Service.

Find Pods having app=web-app.

Listen on port 80.

Forward traffic to Pod port 80.

Expose node port 30080.
```

---

# Full Line-By-Line Explanation

---

## apiVersion

```yaml
apiVersion: v1
```

Question:

```text
Which API understands this object?
```

Answer:

```text
v1
```

---

## kind

```yaml
kind: Service
```

Question:

```text
What resource should I create?
```

Answer:

```text
Service
```

---

## metadata

```yaml
metadata:
```

Question:

```text
Who are you?
```

---

## name

```yaml
name: web-app-nodeport
```

Question:

```text
What is your name?
```

Answer:

```text
web-app-nodeport
```

---

## spec

```yaml
spec:
```

Question:

```text
What do you want?
```

---

## type

```yaml
type: NodePort
```

Question:

```text
How should users reach you?
```

Answer:

```text
Through a node port.
```

---

## selector

```yaml
selector:
  app: web-app
```

Question:

```text
Which Pods belong to me?
```

Answer:

```text
Pods having:

app=web-app
```

---

## port

```yaml
port: 80
```

Question:

```text
Which Service port should I listen on?
```

Answer:

```text
80
```

---

## targetPort

```yaml
targetPort: 80
```

Question:

```text
Which Pod port receives traffic?
```

Answer:

```text
80
```

---

## nodePort

```yaml
nodePort: 30080
```

Question:

```text
Which node port should be opened?
```

Answer:

```text
30080
```

---

# Special Rule About NodePort

Allowed range:

```text
30000 - 32767
```

Valid:

```text
30080
31000
32000
32767
```

Invalid:

```text
80
443
8080
```

---

# Apply Service

Command:

```bash
kubectl apply -f nodeport-service.yaml
```

Output:

```text
service/web-app-nodeport created
```

---

# Verify Service

Command:

```bash
kubectl get svc
```

Your output:

```text
web-app-nodeport

TYPE:
NodePort

CLUSTER-IP:
10.96.182.87

PORT(S):
80:30080/TCP
```

---

# Understanding PORT(S)

This confuses many beginners.

You saw:

```text
80:30080/TCP
```

Meaning:

```text
Service Port = 80

NodePort = 30080
```

---

Think:

```text
80
 ↓
30080
 ↓
Pod:80
```

---

# What You Expected

You expected:

```text
http://52.13.41.107:30080
```

to work.

---

But it failed.

---

# First Attempt

You tried:

```bash
curl http://localhost:30080
```

Result:

```text
Connection failed
```

---

# Second Attempt

You tried:

```bash
curl http://52.13.41.107:30080
```

Result:

```text
Connection failed
```

---

# Why Did It Fail?

This is the most important lesson of today's lab.

---

# Your Kubernetes Cluster Is Kind

You are NOT running:

```text
EKS

AKS

GKE
```

---

You are running:

```text
Kind
```

(Kubernetes IN Docker)

---

# Actual Architecture

Your EC2:

```text
AWS EC2
     ↓
Docker Container
     ↓
Kind Cluster
     ↓
Pods
```

---

Visual:

```text
EC2
│
└── Docker Container
      │
      └── Kubernetes Node
            │
            └── Pods
```

---

# Check Docker Container

You ran:

```bash
docker ps
```

Output:

```text
devops-cluster-control-plane
```

This is your Kubernetes node.

---

Important:

```text
NodePort opened
inside Docker container
```

not directly on EC2.

---

# How You Proved It

You entered container:

```bash
docker exec -it devops-cluster-control-plane bash
```

Inside container:

```bash
curl http://172.18.0.2:30080
```

Result:

```html
Welcome to nginx!
```

Success.

---

You also tested:

```bash
curl http://localhost:30080
```

inside container.

Result:

```html
Welcome to nginx!
```

Success.

---

# What Did This Prove?

It proved:

```text
NodePort Works
```

---

Problem was:

```text
Kind Networking
```

not Kubernetes.

---

# Verify Endpoints

You ran:

```bash
kubectl get endpoints web-app-nodeport
```

Output:

```text
10.244.0.6:80

10.244.0.7:80

10.244.0.8:80
```

Meaning:

```text
Service correctly found all Pods.
```

---

# NodePort Traffic Flow

Actual flow:

```text
User
 ↓
NodePort 30080
 ↓
Service
 ↓
Endpoints
 ↓
Pods
```

---

# Temporary Solution: Port Forward

You used:

```bash
kubectl port-forward service/web-app-nodeport 8080:80
```

---

Meaning:

```text
EC2 Port 8080
        ↓
Service Port 80
```

---

Initially:

```bash
sudo ss -tulpn | grep 8080
```

showed:

```text
127.0.0.1:8080
```

Only local machine could access.

---

So browser still failed.

---

# Why Browser Failed

Because:

```text
127.0.0.1
=
Localhost Only
```

External users cannot reach:

```text
127.0.0.1
```

---

# Correct Port Forward

You restarted using:

```bash
kubectl port-forward \
--address 0.0.0.0 \
service/web-app-nodeport \
8080:80
```

---

Now:

```bash
ss -tulpn | grep 8080
```

showed:

```text
0.0.0.0:8080
```

Meaning:

```text
All network interfaces.
```

---

# Why It Worked Now

Now browser could access:

```text
http://52.13.41.107:8080
```

because:

```text
kubectl
```

was listening publicly.

---

# Security Group Verification

You checked AWS Security Group.

Rules included:

```text
80
443
22
8080
30080
```

All allowed.

---

Therefore:

```text
Security Group
=
NOT the problem
```

---

# Actual Root Cause

The real issue:

```text
Kind runs inside Docker.

NodePort opened inside Docker container.

EC2 was not directly exposing that port.
```

---

# Important Lab Verification

Question:

```text
Can you see nginx welcome page using NodePort?
```

Answer:

```text
YES
```

Evidence:

Inside Kind container:

```bash
curl http://172.18.0.2:30080
```

returned:

```html
Welcome to nginx!
```

---

And later:

```text
http://52.13.41.107:8080
```

worked through port-forwarding.

---

# NodePort Mental Model

Think:

```text
ClusterIP
=
Internal Door
```

```text
NodePort
=
Building Entrance
```

---

Traffic:

```text
Internet
   ↓
Node IP
   ↓
NodePort
   ↓
Service
   ↓
Pod
```

---

# Commands Learned

Create Service:

```bash
kubectl apply -f nodeport-service.yaml
```

---

View Services:

```bash
kubectl get svc
```

---

Check Endpoints:

```bash
kubectl get endpoints web-app-nodeport
```

---

Check Node:

```bash
kubectl get nodes -o wide
```

---

Enter Kind Node:

```bash
docker exec -it devops-cluster-control-plane bash
```

---

Port Forward:

```bash
kubectl port-forward service/web-app-nodeport 8080:80
```

---

Public Port Forward:

```bash
kubectl port-forward \
--address 0.0.0.0 \
service/web-app-nodeport \
8080:80
```

---

#  Questions

### What is NodePort?

```text
A Service type that exposes an application
through a port on every node.
```

---

### NodePort Range?

```text
30000 - 32767
```

---

### Does NodePort create ClusterIP?

```text
Yes.
```

---

### Can NodePort be accessed externally?

```text
Yes.
```

---

### Why didn't NodePort work directly in your lab?

```text
Because Kind runs inside Docker.

The NodePort existed inside the Docker container.
```

---

# What We Learned In Task 4

```text
NodePort exposes services externally.
```

```text
NodePort builds on ClusterIP.
```

```text
NodePort uses node IP + nodePort.
```

```text
Allowed range:
30000-32767
```

```text
Kind networking can hide NodePorts.
```

```text
Endpoints confirmed backend Pods.
```

```text
Port-forwarding can expose services temporarily.
```

```text
Security Groups were correctly configured.
```

```text
NodePort itself worked correctly.
```

---

# Architecture After Task 4

```text
Internet User
       ↓
NodePort
       ↓
Service
       ↓
Endpoints
       ↓
Pods
```

Now external access is possible.

---



# Part 7 - Task 5: LoadBalancer Service (Cloud External Access)

---

# Problem After NodePort

NodePort solved:

```text
Outside User
     ↓
NodeIP:30080
     ↓
Application
```

Example:

```text
52.13.41.107:30080
```

---

But imagine Production.

Would a company give customers:

```text
52.13.41.107:30080
```

No.

Looks ugly.

Hard to manage.

Not scalable.

---

What if node dies?

```text
Node gone
↓
Application gone
```

Bad.

---

# New Goal

We want:

```text
Customer
     ↓
Public IP
or
Domain Name
     ↓
Application
```

without exposing node details.

---

This is why:

```text
LoadBalancer Service
```

exists.

---

# What Is LoadBalancer?

A LoadBalancer Service asks the cloud provider:

```text
AWS

GCP

Azure
```

to create:

```text
External Load Balancer
```

automatically.

---

# Real Life Example

Imagine a shopping mall.

Customers enter through:

```text
Main Entrance
```

not directly through:

```text
Store #1
Store #2
Store #3
```

---

Load Balancer = Mall Entrance

Pods = Stores

---

Customer:

```text
Customer
   ↓
Load Balancer
   ↓
Service
   ↓
Pods
```

---

# Service YAML Used

File:

```text
loadbalancer-service.yaml
```

---

Content:

```yaml
apiVersion: v1

kind: Service

metadata:
  name: web-app-loadbalancer

spec:
  type: LoadBalancer

  selector:
    app: web-app

  ports:
  - port: 80
    targetPort: 80
```

---

# Read YAML Like English

```text
Use v1 API.

Create a Service.

Call it web-app-loadbalancer.

Make it a LoadBalancer Service.

Find Pods having app=web-app.

Listen on port 80.

Forward traffic to Pod port 80.
```

---

# Full Line By Line Explanation

---

## apiVersion

```yaml
apiVersion: v1
```

Question:

```text
Which API should understand me?
```

Answer:

```text
v1
```

---

## kind

```yaml
kind: Service
```

Question:

```text
What resource should Kubernetes create?
```

Answer:

```text
Service
```

---

## metadata

```yaml
metadata:
```

Question:

```text
Who am I?
```

---

## name

```yaml
name: web-app-loadbalancer
```

Question:

```text
What is my name?
```

Answer:

```text
web-app-loadbalancer
```

---

## spec

```yaml
spec:
```

Question:

```text
What do I want?
```

---

## type

```yaml
type: LoadBalancer
```

Question:

```text
How should users reach me?
```

Answer:

```text
Through a cloud load balancer.
```

---

## selector

```yaml
selector:
  app: web-app
```

Question:

```text
Which Pods belong to me?
```

Answer:

```text
Pods having:

app=web-app
```

---

## ports

```yaml
ports:
```

Question:

```text
Which ports should I expose?
```

---

## port

```yaml
port: 80
```

Question:

```text
Which Service port should I listen on?
```

Answer:

```text
80
```

---

## targetPort

```yaml
targetPort: 80
```

Question:

```text
Which Pod port receives traffic?
```

Answer:

```text
80
```

---

# Apply Service

Command:

```bash
kubectl apply -f loadbalancer-service.yaml
```

Output:

```text
service/web-app-loadbalancer created
```

---

# Check Service

Command:

```bash
kubectl get svc
```

Your output:

```text
web-app-loadbalancer

TYPE:
LoadBalancer

CLUSTER-IP:
10.96.239.199

EXTERNAL-IP:
<pending>

PORT(S):
80:32421/TCP
```

---

# First Thing Most Beginners Notice

They see:

```text
EXTERNAL-IP

<pending>
```

and think:

```text
Something broke.
```

---

Actually:

```text
Everything is working.
```

---

# Why Is EXTERNAL-IP Pending?

Because Kubernetes is waiting for:

```text
Cloud Provider
```

to create a load balancer.

---

But your cluster is:

```text
Kind
```

---

Remember:

```text
Kind
=
Kubernetes IN Docker
```

---

Architecture:

```text
AWS EC2
     ↓
Docker
     ↓
Kind Cluster
```

---

Kind cannot create:

```text
AWS ELB
AWS ALB
AWS NLB
```

because:

```text
No cloud integration exists.
```

---

# What Kubernetes Expected

When you created:

```yaml
type: LoadBalancer
```

Kubernetes expected:

```text
AWS
```

to respond:

```text
Okay.

I created a Load Balancer.

Here is its public IP.
```

---

But Kind replied:

```text
I don't know how.
```

---

Result:

```text
EXTERNAL-IP = <pending>
```

---

# Verification Answer

Question:

```text
What does EXTERNAL-IP show?
```

Answer:

```text
<pending>
```

---

Question:

```text
Why?
```

Answer:

```text
Because Kind is a local cluster.

There is no cloud provider available
to create a real external load balancer.
```

---

# What Happens In AWS EKS?

In EKS:

```yaml
type: LoadBalancer
```

automatically creates:

```text
AWS Load Balancer
```

---

Then:

```bash
kubectl get svc
```

might show:

```text
EXTERNAL-IP

a12345.us-west-2.elb.amazonaws.com
```

or

```text
34.201.xx.xx
```

---

Then users access:

```text
Internet
   ↓
AWS Load Balancer
   ↓
Nodes
   ↓
Pods
```

---

# Important Concept

Most beginners think:

```text
LoadBalancer
=
Completely different Service
```

Wrong.

---

Actually:

```text
LoadBalancer
    ↓
Creates NodePort
    ↓
Creates ClusterIP
```

---

Think:

```text
LoadBalancer
     contains
NodePort
     contains
ClusterIP
```

---

# Service Hierarchy

```text
ClusterIP
```

basic service.

---

```text
NodePort
```

ClusterIP + External Port.

---

```text
LoadBalancer
```

NodePort + Cloud Load Balancer.

---

Visual:

```text
LoadBalancer
│
└── NodePort
      │
      └── ClusterIP
```

---

# Why This Is Important

Even though:

```text
EXTERNAL-IP = <pending>
```

your LoadBalancer Service still has:

```text
ClusterIP
```

and

```text
NodePort
```

working.

---

Your output:

```text
CLUSTER-IP

10.96.239.199
```

---

and:

```text
80:32421/TCP
```

which means:

```text
NodePort = 32421
```

already exists.

---

# Traffic Flow In Real Cloud

```text
Internet
      ↓
Load Balancer
      ↓
NodePort
      ↓
ClusterIP
      ↓
Endpoints
      ↓
Pods
```

---

# Traffic Flow In Your Kind Cluster

```text
LoadBalancer Service
      ↓
Waiting...
      ↓
No Cloud Provider
      ↓
EXTERNAL-IP Pending
```

---

# Can Kind Simulate It?

Some environments can.

Example:

```text
Minikube
```

supports:

```bash
minikube tunnel
```

---

Then:

```text
EXTERNAL-IP
```

gets assigned.

---

But Kind does not automatically do this.

---

# Mental Model

Imagine ordering food.

---

ClusterIP:

```text
Kitchen Staff Only
```

---

NodePort:

```text
Pickup Window
```

---

LoadBalancer:

```text
Home Delivery Service
```

---

Customer doesn't care:

```text
Which cook made food?
```

Customer only knows:

```text
Delivery Address
```

---

Same with LoadBalancer.

User sees:

```text
Public IP
```

not:

```text
Pod IP
Node IP
```

---

# Commands Learned

Create Service:

```bash
kubectl apply -f loadbalancer-service.yaml
```

---

View Services:

```bash
kubectl get svc
```

---

Describe Service:

```bash
kubectl describe service web-app-loadbalancer
```

---

#  Questions

### What does a LoadBalancer Service do?

Answer:

```text
Creates an external load balancer
through the cloud provider.
```

---

### Does LoadBalancer create NodePort?

Answer:

```text
Yes.
```

---

### Does LoadBalancer create ClusterIP?

Answer:

```text
Yes.
```

---

### Why did EXTERNAL-IP show pending?

Answer:

```text
No cloud provider available.

Kind cannot create a real load balancer.
```

---

### Which Service type is most common in production?

Answer:

```text
LoadBalancer
```

(for internet-facing applications)

---

### Can LoadBalancer work without a cloud provider?

Answer:

```text
Not normally.

EXTERNAL-IP remains pending.
```

---

# What We Learned In Task 5

```text
LoadBalancer is the highest Service type.
```

```text
It builds on NodePort.
```

```text
NodePort builds on ClusterIP.
```

```text
LoadBalancer asks cloud providers
to create an external load balancer.
```

```text
Kind cannot create real cloud load balancers.
```

```text
Therefore:

EXTERNAL-IP = <pending>
```

```text
This is expected behavior.
```

---

# Architecture After Task 5

Production:

```text
Internet User
       ↓
Load Balancer
       ↓
NodePort
       ↓
ClusterIP
       ↓
Endpoints
       ↓
Pods
```

---

# Super Important Memory Trick

Remember this ladder:

```text
ClusterIP
     ↓
NodePort
     ↓
LoadBalancer
```

or

```text
Inside Cluster
     ↓
Outside Node
     ↓
Public Internet
```

---



# Part 8 - Task 6: Compare ClusterIP vs NodePort vs LoadBalancer

---

# The Big Question

After learning:

```text
ClusterIP
NodePort
LoadBalancer
```

most beginners ask:

```text
Which one should I use?
```

and

```text
What is the actual difference?
```

This task answers that.

---

# First Understand The Evolution

Think like Pokémon evolution 😄

```text
ClusterIP
    ↓
NodePort
    ↓
LoadBalancer
```

Each new Service type adds more capability.

---

# Evolution #1

## ClusterIP

Provides:

```text
Stable IP
Stable DNS
Load Balancing
```

But only:

```text
Inside Kubernetes Cluster
```

---

Traffic:

```text
Pod
 ↓
Service
 ↓
Pod
```

---

Example:

```text
Frontend Pod
      ↓
Backend Service
      ↓
Backend Pods
```

---

Users outside cluster:

```text
Cannot access
```

---

# Evolution #2

## NodePort

NodePort includes:

```text
ClusterIP
+
External Node Port
```

---

Traffic:

```text
Internet
     ↓
NodeIP:30080
     ↓
Service
     ↓
Pods
```

---

Users outside cluster:

```text
Can access
```

---

Example:

```text
52.13.41.107:30080
```

---

# Evolution #3

## LoadBalancer

LoadBalancer includes:

```text
ClusterIP
+
NodePort
+
Cloud Load Balancer
```

---

Traffic:

```text
Internet
      ↓
Load Balancer
      ↓
NodePort
      ↓
ClusterIP
      ↓
Pods
```

---

Users outside cluster:

```text
Can access
```

without knowing node IP.

---

# One Line Definition

---

## ClusterIP

```text
Internal communication.
```

---

## NodePort

```text
External access through node IP.
```

---

## LoadBalancer

```text
External access through cloud load balancer.
```

---

# Master Comparison Table

| Feature             | ClusterIP | NodePort | LoadBalancer |
| ------------------- | --------- | -------- | ------------ |
| Internal Access     | ✅         | ✅        | ✅            |
| External Access     | ❌         | ✅        | ✅            |
| Stable IP           | ✅         | ✅        | ✅            |
| DNS Name            | ✅         | ✅        | ✅            |
| Uses Node Port      | ❌         | ✅        | ✅            |
| Public IP           | ❌         | ❌        | ✅ (Cloud)    |
| Production Friendly | ❌         | Limited  | ✅            |
| Creates ClusterIP   | N/A       | ✅        | ✅            |
| Creates NodePort    | ❌         | N/A      | ✅            |

---

# Easy Memory Table

| Service      | Think                 |
| ------------ | --------------------- |
| ClusterIP    | Inside Building       |
| NodePort     | Building Gate         |
| LoadBalancer | Main Highway Entrance |

---

# Real Company Example

Imagine:

```text
Amazon Website
```

---

Database:

```text
ClusterIP
```

because users should never access database directly.

---

Internal API:

```text
ClusterIP
```

because only backend should access it.

---

Testing App:

```text
NodePort
```

because developers need external access.

---

Production Website:

```text
LoadBalancer
```

because customers access it.

---

# Your Actual Services

You created:

---

## Service 1

```text
web-app-clusterip
```

Output:

```text
TYPE:
ClusterIP
```

---

Purpose:

```text
Internal Access
```

---

## Service 2

```text
web-app-nodeport
```

Output:

```text
TYPE:
NodePort
```

---

Purpose:

```text
External Access via Node
```

---

## Service 3

```text
web-app-loadbalancer
```

Output:

```text
TYPE:
LoadBalancer
```

---

Purpose:

```text
Production Style External Access
```

---

# Important Statement

Kubernetes documentation often says:

```text
Each type builds on the previous type.
```

This is extremely important.

---

Visual:

```text
LoadBalancer
     ↓
NodePort
     ↓
ClusterIP
```

---

Meaning:

```text
LoadBalancer
```

already contains:

```text
NodePort
```

and

```text
ClusterIP
```

---

# Verification From Your Lab

Command:

```bash
kubectl describe service web-app-loadbalancer
```

Output:

```text
IP:
10.96.239.199
```

---

What is this?

```text
ClusterIP
```

---

Output:

```text
NodePort:
32421/TCP
```

---

What is this?

```text
NodePort
```

---

Output:

```text
Type:
LoadBalancer
```

---

What is this?

```text
LoadBalancer
```

---

# Therefore

Your LoadBalancer Service had:

```text
ClusterIP
```

YES ✅

---

```text
NodePort
```

YES ✅

---

```text
LoadBalancer Configuration
```

YES ✅

---

Exactly as Kubernetes documentation says.

---

# Verification Answer

Question:

```text
Does the LoadBalancer service also have a ClusterIP and NodePort assigned?
```

Answer:

```text
YES
```

Evidence:

```text
ClusterIP:
10.96.239.199
```

and

```text
NodePort:
32421
```

were automatically assigned.

---

# Why Kubernetes Designed It This Way

Imagine if:

```text
LoadBalancer
```

did NOT create:

```text
NodePort
```

Then cloud load balancer wouldn't know where to send traffic.

---

Cloud LB sends traffic to:

```text
NodePort
```

---

NodePort sends traffic to:

```text
Service
```

---

Service sends traffic to:

```text
Pods
```

---

Flow:

```text
Cloud LB
    ↓
NodePort
    ↓
Service
    ↓
Pods
```

---

# Ultimate Service Hierarchy

This is probably the most important diagram in Day 53.

```text
LoadBalancer
│
├── Public Entry
│
└── NodePort
     │
     └── ClusterIP
          │
          └── Endpoints
               │
               └── Pods
```

---

# Another Way To Remember

Imagine a Shopping Mall.

---

Pods:

```text
Shops
```

---

Endpoints:

```text
Shop Addresses
```

---

ClusterIP:

```text
Reception Desk
```

---

NodePort:

```text
Mall Gate
```

---

LoadBalancer:

```text
Highway Exit To Mall
```

---

Customer Flow:

```text
Highway
   ↓
Mall Entrance
   ↓
Reception
   ↓
Shop
```

---

Kubernetes Flow:

```text
LoadBalancer
      ↓
NodePort
      ↓
ClusterIP
      ↓
Endpoints
      ↓
Pods
```

---

# Commands Learned

View Services:

```bash
kubectl get svc
```

---

Detailed Service Info:

```bash
kubectl describe service web-app-loadbalancer
```

---

Wide Output:

```bash
kubectl get svc -o wide
```

---

#  Questions

### Which Service type is default?

Answer:

```text
ClusterIP
```

---

### Which Service type exposes applications externally?

Answer:

```text
NodePort
```

or

```text
LoadBalancer
```

---

### Which Service type is most common in production?

Answer:

```text
LoadBalancer
```

---

### Does LoadBalancer create NodePort?

Answer:

```text
Yes.
```

---

### Does NodePort create ClusterIP?

Answer:

```text
Yes.
```

---

### Can Pods access a LoadBalancer Service internally?

Answer:

```text
Yes.
```

because it also has a ClusterIP.

---

# What We Learned In Task 6

```text
ClusterIP
=
Internal only
```

```text
NodePort
=
External through node
```

```text
LoadBalancer
=
External through cloud LB
```

```text
LoadBalancer includes NodePort.
```

```text
NodePort includes ClusterIP.
```

```text
All Services ultimately route traffic to Pods.
```

```text
Service selection happens through labels.
```

---

# Final Service Mental Model

Whenever you see a Service YAML, ask:

```text
1. Which Pods should I expose?
```

Answer:

```yaml
selector:
```

---

```text
2. Which port should I listen on?
```

Answer:

```yaml
port:
```

---

```text
3. Which Pod port should receive traffic?
```

Answer:

```yaml
targetPort:
```

---

```text
4. How reachable should I be?
```

Answer:

```yaml
type:
```

---

And that's the entire Service story.

---




# Part 9 - Endpoints Deep Dive (The Missing Link)

---

# Why We Need This Topic

Till now you learned:

```text id="ep1"
Deployment → creates Pods
Service → exposes Pods
```

But one big question remains:

```text id="ep2"
How does Service actually know WHICH Pod to send traffic to?
```

Answer:

```text id="ep3"
Endpoints
```

---

# Simple Definition

Endpoints =

```text id="ep4"
Actual Pod IPs behind a Service
```

---

# Real Life Analogy

Think:

```text id="ep5"
Service = Restaurant Reception
```

```text id="ep6"
Endpoints = List of available tables
```

Reception doesn't cook food.

It only sends you to a table.

---

# Your Setup

You created:

```text id="ep7"
Deployment → 3 Pods
```

Pods:

```text id="ep8"
10.244.0.6
10.244.0.7
10.244.0.8
```

---

Service:

```text id="ep9"
web-app-nodeport
web-app-clusterip
web-app-loadbalancer
```

All using selector:

```yaml id="ep10"
app: web-app
```

---

# Key Concept

Service does NOT directly connect to Pods.

It uses:

```text id="ep11"
Endpoints Object
```

---

# What Are Endpoints?

Run:

```bash id="ep12"
kubectl get endpoints web-app-nodeport
```

You saw:

```text id="ep13"
10.244.0.6:80
10.244.0.7:80
10.244.0.8:80
```

---

# Translation

This means:

```text id="ep14"
Service → knows all Pod IPs
```

and will load balance between them.

---

# How Endpoints Are Created

Step by step:

---

## Step 1: Deployment creates Pods

```text id="ep15"
Deployment
   ↓
Pods created
```

---

## Step 2: Pods get labels

```text id="ep16"
app=web-app
```

---

## Step 3: Service selects Pods

```yaml id="ep17"
selector:
  app: web-app
```

---

## Step 4: Kubernetes matches labels

```text id="ep18"
Service finds Pods with matching label
```

---

## Step 5: Kubernetes creates Endpoints

```text id="ep19"
Endpoints = list of matched Pod IPs
```

---

# Final Flow

```text id="ep20"
Deployment
   ↓
Pods
   ↓
Labels (app=web-app)
   ↓
Service selector matches labels
   ↓
Endpoints created
   ↓
Traffic routed to Pods
```

---

# Why Endpoints Matter

Without Endpoints:

```text id="ep21"
Service would not know where Pods are
```

---

With Endpoints:

```text id="ep22"
Service has live map of Pod IPs
```

---

# Your Real Output Explained

You ran:

```bash id="ep23"
kubectl get endpoints web-app-nodeport
```

Got:

```text id="ep24"
10.244.0.6:80, 10.244.0.7:80, 10.244.0.8:80
```

---

Meaning:

```text id="ep25"
Service is actively tracking all 3 Pods
```

---

# Important Insight

Endpoints update automatically.

---

If a Pod dies:

```text id="ep26"
10.244.0.7 dies
```

Endpoints becomes:

```text id="ep27"
10.244.0.6
10.244.0.8
```

---

If a new Pod is created:

```text id="ep28"
10.244.0.9 added
```

Endpoints becomes:

```text id="ep29"
10.244.0.6
10.244.0.8
10.244.0.9
```

---

# This Is The Magic

```text id="ep30"
No manual updates needed
```

---

# Why Kubernetes Uses Endpoints (Not Pods Directly)

Because:

```text id="ep31"
Pods are temporary
```

They can:

* Restart
* Move nodes
* Get new IPs

---

But:

```text id="ep32"
Endpoints are always updated automatically
```

---

# Relationship Diagram

```text id="ep33"
Deployment
   ↓
Pods
   ↓
Labels
   ↓
Service (selector)
   ↓
Endpoints
   ↓
Load balancing
   ↓
Pods
```

---

# kubectl describe Service (Important Insight)

When you run:

```bash id="ep34"
kubectl describe service web-app-nodeport
```

You saw:

```text id="ep35"
Endpoints:
10.244.0.6:80, 10.244.0.7:80, 10.244.0.8:80
```

---

This proves:

```text id="ep36"
Service is NOT guessing Pods
```

It is using:

```text id="ep37"
Endpoints object
```

---

# What Happens When You Curl Service?

Example:

```bash id="ep38"
curl http://web-app-nodeport
```

Flow:

```text id="ep39"
Step 1: DNS resolves Service
Step 2: Service receives request
Step 3: Service checks Endpoints
Step 4: Picks one Pod IP
Step 5: Sends request
Step 6: Response returned
```

---

# Load Balancing Happens Here

Example:

```text id="ep40"
Request 1 → Pod 1
Request 2 → Pod 3
Request 3 → Pod 2
```

This is:

```text id="ep41"
Round Robin (default behavior)
```

---

# Key Mental Model

Think:

```text id="ep42"
Service = Smart Router
Endpoints = Routing Table
Pods = Destinations
```

---

# Very Important Rule

```text id="ep43"
Service never stores Pods
```

It stores:

```text id="ep44"
ONLY Endpoints
```

---

# Why Your Setup Worked Perfectly

Because:

```text id="ep45"
Deployment created Pods
   ↓
Labels matched Service selector
   ↓
Endpoints automatically created
   ↓
Traffic flowed correctly
```

---

# Common Beginner Mistake

If Service shows no endpoints:

```text id="ep46"
No traffic will work
```

Reason:

```text id="ep47"
Selector mismatch
```

---

# Example Failure Case

If:

```yaml id="ep48"
selector:
  app: web
```

But Pods have:

```yaml id="ep49"
app: web-app
```

Then:

```text id="ep50"
Endpoints = EMPTY
```

---

# How To Debug

Always run:

```bash id="ep51"
kubectl get endpoints
```

If empty:

```text id="ep52"
Service is not connected to Pods
```

---

# Commands You Learned

Check Endpoints:

```bash id="ep53"
kubectl get endpoints
```

---

Describe Service:

```bash id="ep54"
kubectl describe service <name>
```

---

Check Pods:

```bash id="ep55"
kubectl get pods -o wide
```

---

#  Questions

### What are Endpoints in Kubernetes?

```text id="ep56"
List of Pod IPs behind a Service
```

---

### Does Service store Pods?

```text id="ep57"
No, it stores Endpoints
```

---

### Who creates Endpoints?

```text id="ep58"
Kubernetes automatically creates them using selectors
```

---

### What happens if Pod restarts?

```text id="ep59"
Endpoints update automatically
```

---

### Why are Endpoints important?

```text id="ep60"
They define where traffic actually goes
```

---

# Final Mental Model (MOST IMPORTANT)

```text id="ep61"
User Request
   ↓
Service
   ↓
Endpoints
   ↓
Pods
   ↓
Container Response
```

---



# Part 10 — FINAL MASTER SUMMARY (One-Page Revision Sheet)

This is your **no-scroll revision sheet**. If you understand this, you understand the entire Day 53 topic.

---

# 1. THE BIG PICTURE (MOST IMPORTANT)

Think Kubernetes like this:

```text
Deployment → creates Pods
Service → exposes Pods
Endpoints → tracks Pod IPs
DNS → gives name to Service
```

Final flow:

```text
User
 ↓
Service (stable access)
 ↓
Endpoints (real Pod IPs)
 ↓
Pods (application)
```

---

# 2. WHY SERVICES EXIST

Pods are:

```text
temporary + changing IPs
```

Problem:

```text
Pod IP changes after restart
```

Solution:

```text
Service = stable access layer
```

Service gives:

* Stable IP (ClusterIP)
* Stable DNS name
* Load balancing
* Pod discovery

---

# 3. SERVICE TYPES (CORE CONCEPT)

## ClusterIP (DEFAULT)

```text
Inside cluster only
```

Use case:

* Backend APIs
* Databases
* Internal services

---

## NodePort

```text
External access via NodeIP:Port
```

Use case:

* Testing
* Development
* Debugging

---

## LoadBalancer

```text
Cloud external access (real production style)
```

Use case:

* Production apps in AWS/GCP/Azure

---

# 4. SERVICE EVOLUTION (VERY IMPORTANT)

```text
LoadBalancer
   ↓
NodePort
   ↓
ClusterIP
```

Meaning:

* LoadBalancer = NodePort + ClusterIP
* NodePort = ClusterIP
* ClusterIP = Base service

---

# 5. SERVICE MENTAL MODEL (CHEF ANALOGY)

```text
Service = Chef receptionist
Pods = Kitchens
Endpoints = Kitchen list
Selector = Recipe filter
```

Service does NOT cook.

It only routes.

---

# 6. SERVICE YAML SKELETON (FULL EXPLANATION)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app
```

👉 Identity of Service

---

```yaml
spec:
```

👉 What Service should do

---

```yaml
type: ClusterIP / NodePort / LoadBalancer
```

👉 How it should be exposed

---

```yaml
selector:
  app: web-app
```

👉 Which Pods to connect

---

```yaml
ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```

Meaning:

| Field      | Meaning       |
| ---------- | ------------- |
| port       | Service port  |
| targetPort | Pod port      |
| nodePort   | External port |

---

# 7. DEPLOYMENT VS SERVICE (VERY IMPORTANT)

## Deployment

```text
Manages Pods
```

## Service

```text
Connects to Pods
```

---

Flow:

```text
Deployment → Pods → Service → Users
```

---

# 8. LABEL + SELECTOR MAGIC

Pods:

```yaml
labels:
  app: web-app
```

Service:

```yaml
selector:
  app: web-app
```

If mismatch:

```text
Service = NO CONNECTION
```

---

# 9. ENDPOINTS (CRITICAL CONCEPT)

Endpoints =

```text
Actual Pod IPs behind Service
```

Check:

```bash
kubectl get endpoints
```

Example:

```text
10.244.0.6:80
10.244.0.7:80
10.244.0.8:80
```

---

# 10. ENDPOINT FLOW

```text
Deployment
   ↓
Pods
   ↓
Labels
   ↓
Service selector
   ↓
Endpoints created
   ↓
Traffic routed
```

---

# 11. DNS IN KUBERNETES

Every Service gets DNS:

```text
<service-name>.<namespace>.svc.cluster.local
```

Example:

```text
web-app-clusterip.default.svc.cluster.local
```

Short form:

```text
web-app-clusterip
```

---

DNS Flow:

```text
Pod → CoreDNS → Service → Endpoints → Pods
```

---

# 12. YOUR LAB SUMMARY

You created:

### Deployment

```text
web-app → 3 nginx pods
```

### Services

```text
ClusterIP
NodePort
LoadBalancer
```

---

# 13. WHAT YOU VERIFIED

## ClusterIP

```text
Works inside cluster only
```

---

## NodePort

```text
Works externally via:
NodeIP:30080
```

---

## LoadBalancer

```text
EXTERNAL-IP = <pending>
(because no cloud LB in local cluster)
```

---

# 14. IMPORTANT REALITY CHECK

## Why LoadBalancer was pending?

Because:

```text
No cloud provider integration (AWS/GCP LB controller missing)
```

---

# 15. FINAL TRAFFIC FLOW (MOST IMPORTANT DIAGRAM)

```text
User Request
    ↓
LoadBalancer (optional)
    ↓
NodePort
    ↓
ClusterIP
    ↓
Service
    ↓
Endpoints
    ↓
Pods
    ↓
Container response
```

---

# 16. DEBUGGING CHEAT SHEET

## Service not working?

Check:

```bash
kubectl get pods
kubectl get svc
kubectl get endpoints
```

---

## Endpoints empty?

Problem:

```text
selector mismatch
```

---

## NodePort not accessible?

Check:

```text
Security Group / firewall / correct IP
```

---

# 17. COMMAND CHEATSHEET

```bash
kubectl get pods -o wide
kubectl get svc
kubectl get endpoints
kubectl describe service <name>
kubectl delete -f file.yaml
```

---

# 18. FINAL ONE-LINE SUMMARY

```text
Service = Stable way to access changing Pods using labels + Endpoints + DNS
```

---

# 19. WHAT YOU NOW KNOW (IMPORTANT)

You understand:

* Pods are temporary
* Services give stability
* Endpoints track real IPs
* DNS makes service discovery easy
* NodePort gives external access
* LoadBalancer is cloud entry point

---




---

# Part 11 —  Questions (REAL WORLD + TRICK QUESTIONS)



---

## 🔹 1. What problem do Kubernetes Services solve?

```text
Pods have dynamic IPs, Services provide stable access.
```

---

## 🔹 2. What happens if Service selector is wrong?

```text
Service will have ZERO Endpoints → no traffic reaches Pods
```

---

## 🔹 3. Difference between port, targetPort, nodePort?

```text
port       → Service port
targetPort → Pod port
nodePort   → External node access port
```

---

## 🔹 4. Why do we need ClusterIP if Pods already have IP?

```text
Pod IP changes when Pod restarts
ClusterIP is stable virtual IP managed by Kubernetes
```

---

## 🔹 5. What happens when Pod dies in a Service?

```text
Endpoint is removed automatically
Traffic stops going to that Pod
```

---

## 🔹 6. What are Endpoints in Kubernetes?

```text
List of Pod IPs currently backing a Service
```

---

## 🔹 7. What is EndpointSlice?

```text
Modern scalable replacement for Endpoints object
```

Used in large clusters.

---

## 🔹 8. Does LoadBalancer create NodePort?

```text
Yes
LoadBalancer → NodePort → ClusterIP
```

---

## 🔹 9. Can NodePort work without ClusterIP?

```text
No, ClusterIP is always created internally
```

---

## 🔹 10. What happens if no Pods match selector?

```text
Service exists but has no Endpoints → no traffic flows
```

---

## 🔹 11. How does Kubernetes load balance traffic?

```text
kube-proxy distributes traffic using iptables or IPVS
```

---

## 🔹 12. Why LoadBalancer shows <pending> in local cluster?

```text
No cloud provider integration exists to provision real LB
```

---

## 🔹 13. How does DNS work in Services?

```text
Service name → CoreDNS → ClusterIP → Endpoints → Pods
```

---

## 🔹 14. Can Pods talk to Service using name?

```text
Yes, via DNS:
http://service-name
```

---

## 🔹 15. What is difference between Service and Ingress?

```text
Service = expose Pods
Ingress = route HTTP/HTTPS traffic intelligently
```

---

# Part 12 — Cheat Sheet (ULTRA QUICK REVISION)

This is your **1-page memory sheet**.

---

# 🔹 CORE IDEA

```text
Service = Stable access to Pods
```

---

# 🔹 SERVICE TYPES

| Type         | Access        | Use         |
| ------------ | ------------- | ----------- |
| ClusterIP    | Internal only | Backend, DB |
| NodePort     | NodeIP:Port   | Testing     |
| LoadBalancer | Public IP     | Production  |

---

# 🔹 YAML SKELETON

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: ClusterIP
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 8080
```

---

# 🔹 FLOW

```text
User → Service → Endpoints → Pods
```

---

# 🔹 IMPORTANT RULES

```text
Selector mismatch → NO TRAFFIC
Pods change → Endpoints auto update
Service never talks directly to Pods
```

---

# 🔹 COMMANDS

```bash
kubectl get svc
kubectl get pods -o wide
kubectl get endpoints
kubectl describe svc <name>
kubectl delete svc <name>
```

---

# 🔹 DNS FORMAT

```text
service-name.namespace.svc.cluster.local
```

Short:

```text
service-name
```

---

# 🔹 DEBUG FLOW

```text
1. kubectl get pods
2. kubectl get svc
3. kubectl get endpoints
4. kubectl describe svc
```

---

# 🔹 TRAFFIC FLOW (MOST IMPORTANT)

```text
Internet
  ↓
LoadBalancer
  ↓
NodePort
  ↓
ClusterIP
  ↓
Endpoints
  ↓
Pods
```

---

# 🧠 FINAL MASTER UNDERSTANDING

```text
Service = stable entry point
Endpoints = live Pod list
Selector = connection logic
DNS = name resolution
```

*********************

# Overview

You have Deployments running multiple Pods, but how do you actually talk to them?

Pods get random IP addresses that change every time they restart. Kubernetes applications therefore need a mechanism that provides a stable way to reach Pods regardless of where they are running or how often they are recreated.

**Services solve this problem.**

A Service gives your Pods a stable network endpoint that remains consistent even when the underlying Pods change.

Today you will learn:

* Why Services are needed
* How Services work internally
* Service discovery using DNS
* Endpoints and Pod selection
* ClusterIP Services
* NodePort Services
* LoadBalancer Services
* ExternalName Services
* Service networking concepts
* Troubleshooting techniques
* Best practices and tips

You will also complete a hands-on lab exposing a Deployment using multiple Service types.

---

# Why Services?

Every Pod gets its own IP address.

This creates two major problems:

### Problem 1: Pod IPs Are Not Stable

When a Pod:

* crashes
* gets rescheduled
* is replaced by a Deployment
* moves to another node

it receives a new IP address.

Applications cannot safely depend on Pod IPs because those addresses may change at any time.

---

### Problem 2: Deployments Run Multiple Pods

A Deployment commonly runs several replicas.

Example:

```text
web-app
├── Pod A (10.244.0.5)
├── Pod B (10.244.0.6)
└── Pod C (10.244.0.7)
```

Which Pod should a client connect to?

Should traffic go to Pod A?

Pod B?

Pod C?

How do clients discover healthy Pods automatically?

---

### How Services Solve These Problems

A Service provides:

* A stable IP address
* A stable DNS name
* Automatic Pod discovery
* Load balancing across Pods

Traffic flows like this:

```text
[Client]
    |
    v
[Service]
    |
    +----> Pod 1
    |
    +----> Pod 2
    |
    +----> Pod 3
```

Clients communicate with the Service.

The Service determines which Pods should receive traffic.

---

# Think of It Like This

Imagine an office.

Pods are workers.

Workers change desks every day.

If customers had to memorize desk numbers, communication would constantly break.

A Service acts like a receptionist.

Customers always speak to the receptionist.

The receptionist always knows which workers are available and forwards requests appropriately.

```text
Customer -> Receptionist -> Worker
```

In Kubernetes:

```text
Application -> Service -> Pod
```

---

# How Services Work

A Service is a Kubernetes resource that:

1. Has a stable virtual IP
2. Has a stable DNS name
3. Selects Pods using labels
4. Maintains a list of Pod endpoints
5. Load balances traffic across healthy Pods

---

## The Service Object

A Service does not run containers.

A Service is simply a networking abstraction.

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app
spec:
  selector:
    app: web-app
  ports:
  - port: 80
    targetPort: 80
```

The selector tells Kubernetes:

```yaml
selector:
  app: web-app
```

Find every Pod with:

```yaml
app: web-app
```

and route traffic to them.

---

# Service Discovery

Kubernetes includes an internal DNS server.

Every Service automatically receives a DNS record.

Format:

```text
<service-name>.<namespace>.svc.cluster.local
```

Example:

```text
web-app.default.svc.cluster.local
```

Within the same namespace you can usually use:

```text
web-app
```

instead of the full DNS name.

---

## Examples

Short name:

```text
web-app
```

Full name:

```text
web-app.default.svc.cluster.local
```

Both resolve to the Service IP.

---

# Endpoints

When a Service selects Pods, Kubernetes creates an Endpoint object.

Endpoints contain the actual Pod IP addresses behind the Service.

Example:

```bash
kubectl get endpoints web-app
```

Output:

```text
NAME      ENDPOINTS
web-app   10.244.0.5:80,10.244.0.6:80,10.244.0.7:80
```

The Service forwards traffic to these addresses.

If Pods are added or removed, Kubernetes automatically updates the Endpoint list.

---

# Service Types

Kubernetes supports four Service types.

| Type         | Accessible From              | Use Case                |
| ------------ | ---------------------------- | ----------------------- |
| ClusterIP    | Inside cluster               | Internal communication  |
| NodePort     | Outside cluster via node IP  | Testing and development |
| LoadBalancer | External cloud load balancer | Production traffic      |
| ExternalName | DNS alias                    | External services       |

---

# Relationship Between Service Types

Each type builds on the previous type.

```text
LoadBalancer
      |
      v
NodePort
      |
      v
ClusterIP
```

Therefore:

A LoadBalancer Service automatically includes:

* ClusterIP
* NodePort
* LoadBalancer configuration

A NodePort Service automatically includes:

* ClusterIP
* NodePort

---

# Challenge Task 1: Deploy the Application

Create:

`app-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  labels:
    app: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
        ports:
        - containerPort: 80
```

Deploy it:

```bash
kubectl apply -f app-deployment.yaml
```

Verify:

```bash
kubectl get pods -o wide
```

Example:

```text
NAME                        READY   STATUS
web-app-xxxxx               1/1     Running
web-app-yyyyy               1/1     Running
web-app-zzzzz               1/1     Running
```

Notice each Pod receives a unique IP.

Those IPs are not guaranteed to remain the same.

That is exactly the problem Services solve.

---

# Challenge Task 2: Create a ClusterIP Service

ClusterIP is the default Service type.

It provides internal access only.

Create:

`clusterip-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app-clusterip
spec:
  type: ClusterIP
  selector:
    app: web-app
  ports:
  - port: 80
    targetPort: 80
```

Field explanations:

```yaml
type: ClusterIP
```

Internal access only.

```yaml
selector:
  app: web-app
```

Selects matching Pods.

```yaml
port: 80
```

Service listening port.

```yaml
targetPort: 80
```

Pod listening port.

Apply:

```bash
kubectl apply -f clusterip-service.yaml
```

Verify:

```bash
kubectl get services
```

Example:

```text
NAME                TYPE        CLUSTER-IP
web-app-clusterip   ClusterIP   10.96.100.50
```

The ClusterIP remains stable even when Pods change.

| Field         | Purpose                                      |
| ------------- | -------------------------------------------- |
| containerPort | Port application listens on inside container |
| targetPort    | Service forwards traffic here                |
| port          | Service exposes this port                    |


---

# Challenge Task 3: Test ClusterIP Connectivity

ClusterIP is reachable only from inside the cluster.

Create a temporary test pod:

```bash
kubectl run test-client \
--image=busybox:latest \
--rm -it \
--restart=Never -- sh
```

Inside the pod:

```bash
wget -qO- http://web-app-clusterip
```

Expected result:

Nginx welcome page.

Example:

```html
Welcome to nginx!
```

The Service automatically forwards traffic to one of the Pods.

Run the command multiple times.

Traffic will be distributed across healthy Pods.


---

# Challenge Task 4: Discover Services with DNS

One of the most powerful features of Kubernetes Services is automatic DNS-based service discovery.

Instead of remembering IP addresses, applications can communicate using Service names.

Kubernetes runs an internal DNS server (usually CoreDNS) that automatically creates DNS records for Services.

The format is:

```text id="1f0ub4"
<service-name>.<namespace>.svc.cluster.local
```

For our Service:

```text id="6jl95m"
web-app-clusterip.default.svc.cluster.local
```

Within the same namespace, you can simply use:

```text id="34yyh0"
web-app-clusterip
```

---

## Test DNS Resolution

Create a temporary test pod:

```bash id="eg7r0v"
kubectl run dns-test \
--image=busybox:latest \
--rm -it \
--restart=Never -- sh
```

Inside the pod:

### Test using the short Service name

```bash id="jlwmn6"
wget -qO- http://web-app-clusterip
```

---

### Test using the full DNS name

```bash id="bn5yvq"
wget -qO- http://web-app-clusterip.default.svc.cluster.local
```

---

### Lookup the DNS entry

```bash id="b32rtt"
nslookup web-app-clusterip
```

Example:

```text id="1vdfux"
Server:    10.96.0.10
Address:   10.96.0.10

Name:      web-app-clusterip
Address:   10.96.100.50
```

---

## Verification

Questions to verify:

* Does the short name resolve?
* Does the full DNS name resolve?
* Does `nslookup` return the same IP shown in `kubectl get services`?
* Can you access the Nginx page using both names?

The answer should be yes for all.

---

## Why DNS Matters

Imagine a microservices application:

```text id="7r31jp"
frontend
backend
payments
orders
inventory
notifications
```

Without DNS:

```text id="nysc18"
frontend -> 10.244.0.7
backend  -> 10.244.0.12
orders   -> 10.244.0.19
```

Every Pod restart would break communication.

With Services:

```text id="hspggk"
frontend -> backend
backend  -> orders
orders   -> inventory
```

Applications communicate using names rather than IP addresses.

This is the foundation of Kubernetes service discovery.

---

# Challenge Task 5: Create a NodePort Service

ClusterIP works only inside the cluster.

What if users outside the cluster need access?

That's where NodePort comes in.

A NodePort Service opens a port on every node.

Traffic flow:

```text id="5d4xmo"
Client
   |
   v
<NodeIP>:30080
   |
   v
NodePort Service
   |
   v
Pods
```

---

## Create nodeport-service.yaml

```yaml id="7sikzn"
apiVersion: v1
kind: Service
metadata:
  name: web-app-nodeport
spec:
  type: NodePort
  selector:
    app: web-app
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```

---

## Field Explanations

### type

```yaml id="r08sm5"
type: NodePort
```

Exposes the Service through a node port.

---

### nodePort

```yaml id="t8sksk"
nodePort: 30080
```

Port exposed on every node.

Valid range:

```text id="ijbkkh"
30000 - 32767
```

If omitted, Kubernetes assigns one automatically.

---

### Traffic Flow

```text id="79u0wq"
Client
   |
   v
NodeIP:30080
   |
   v
Service Port 80
   |
   v
Pod Port 80
```

---

## Apply the Service

```bash id="3d4kph"
kubectl apply -f nodeport-service.yaml
```

Verify:

```bash id="jng70l"
kubectl get services
```

Example:

```text id="jlwmcm"
NAME               TYPE       CLUSTER-IP      PORT(S)
web-app-nodeport   NodePort   10.96.100.51    80:30080/TCP
```

---

# Challenge Task 6: Access the NodePort Service

How you access the Service depends on your Kubernetes environment.

---

## Minikube

Get the URL:

```bash id="avhlmr"
minikube service web-app-nodeport --url
```

Example:

```text id="w5e4e8"
http://192.168.49.2:30080
```

---

## Kind

Find the node IP:

```bash id="p0c1wr"
kubectl get nodes -o wide
```

Example:

```text id="rwvvl4"
NAME                 INTERNAL-IP
kind-control-plane   172.18.0.2
```

Then:

```bash id="9e8x4x"
curl http://172.18.0.2:30080
```

---

## Docker Desktop

```bash id="3yw9x6"
curl http://localhost:30080
```

---

## Browser Test

Open:

```text id="sm8wxz"
http://<node-ip>:30080
```

You should see:

```html id="u5jlwm"
Welcome to nginx!
```

---

## Verification

Confirm:

* Service exists
* NodePort assigned
* Browser loads Nginx
* Curl returns HTML
* External access works

---

# Understanding NodePort

NodePort is commonly used for:

### Development

```text id="v19v2h"
Developer
     |
     v
NodePort Service
```

Easy testing without a cloud load balancer.

---

### Learning Kubernetes

Perfect for:

* labs
* demos
* practice environments

---

### Temporary External Access

Useful for:

* internal testing
* proof of concept deployments
* debugging

---

## NodePort Limitations

NodePort is generally not ideal for production because:

### High Ports

Users access:

```text id="q7pg4j"
http://10.0.0.5:30080
```

instead of:

```text id="o3iztt"
https://example.com
```

---

### Node Dependency

Traffic depends on node accessibility.

---

### Limited Features

No:

* SSL termination
* advanced routing
* path-based routing
* hostname routing

These are typically handled by Ingress controllers.

---

# Challenge Task 7: Create a LoadBalancer Service

LoadBalancer is the most common Service type in cloud environments.

When running on:

* AWS
* Azure
* GCP
* DigitalOcean
* Oracle Cloud

Kubernetes asks the cloud provider to provision a real load balancer.

---

## Create loadbalancer-service.yaml

```yaml id="nup5cg"
apiVersion: v1
kind: Service
metadata:
  name: web-app-loadbalancer
spec:
  type: LoadBalancer
  selector:
    app: web-app
  ports:
  - port: 80
    targetPort: 80
```

---

## Apply

```bash id="x0xyhb"
kubectl apply -f loadbalancer-service.yaml
```

Verify:

```bash id="sihn8e"
kubectl get services
```

Example:

```text id="c5slsn"
NAME                    TYPE           CLUSTER-IP      EXTERNAL-IP
web-app-loadbalancer    LoadBalancer   10.96.100.52    <pending>
```

---

# Why EXTERNAL-IP Shows `<pending>`

Most local Kubernetes environments do not have a cloud provider attached.

Examples:

* Minikube
* Kind
* Docker Desktop
* K3d

Because there is no cloud API available:

```text id="pn1tbw"
AWS ELB
Azure Load Balancer
Google Cloud Load Balancer
```

Kubernetes cannot create an external load balancer.

Therefore:

```text id="9vsm0v"
EXTERNAL-IP = <pending>
```

This is completely normal.

---

## Minikube Tunnel

Minikube can simulate a cloud load balancer.

Run:

```bash id="2h4l5u"
minikube tunnel
```

Open another terminal:

```bash id="tt9s5r"
kubectl get services
```

Example:

```text id="ejyy7d"
NAME                    TYPE           EXTERNAL-IP
web-app-loadbalancer    LoadBalancer   192.168.49.100
```

Now the Service behaves like a cloud LoadBalancer.

---

# LoadBalancer Traffic Flow

```text id="n78qvl"
Internet
    |
    v
Cloud Load Balancer
    |
    v
NodePort
    |
    v
ClusterIP
    |
    v
Pods
```

This illustrates an important Kubernetes concept:

LoadBalancer builds on top of NodePort.

NodePort builds on top of ClusterIP.

---

# Verify LoadBalancer Components

Run:

```bash id="8k9w6z"
kubectl describe service web-app-loadbalancer
```

Look for:

### ClusterIP

```text id="xrb6rf"
ClusterIP: 10.96.100.52
```

---

### NodePort

```text id="tx6dwd"
NodePort: 31234/TCP
```

---

### LoadBalancer Information

```text id="stzpf5"
LoadBalancer Ingress:
```

If using cloud infrastructure or Minikube tunnel.

---

# Challenge Task 8: Compare Service Types Side by Side

Run:

```bash id="4v2wxu"
kubectl get services -o wide
```

Example:

```text id="x14c5o"
NAME                    TYPE
web-app-clusterip       ClusterIP
web-app-nodeport        NodePort
web-app-loadbalancer    LoadBalancer
```

Comparison:

| Type         | Accessible From | Use Case                         |
| ------------ | --------------- | -------------------------------- |
| ClusterIP    | Inside cluster  | Service-to-service communication |
| NodePort     | Node IP         | Development and testing          |
| LoadBalancer | Public IP       | Production traffic               |

---

## Key Insight

Remember:

```text id="w40vci"
LoadBalancer
      |
      v
NodePort
      |
      v
ClusterIP
```

Every LoadBalancer Service also receives:

* ClusterIP
* NodePort

Every NodePort Service also receives:

* ClusterIP

This layered architecture is fundamental to Kubernetes networking.

---




# Service Manifest Deep Dive

So far, you've created Services and used them successfully.

Now let's understand every field in detail.

A typical Service looks like this:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
  labels:
    app: my-app
spec:
  type: ClusterIP
  selector:
    app: my-app
  ports:
  - name: http
    port: 80
    targetPort: 80
    protocol: TCP
```

---

# Understanding Each Section

## apiVersion

```yaml
apiVersion: v1
```

Defines the Kubernetes API version.

Services belong to the core API group.

---

## kind

```yaml
kind: Service
```

Tells Kubernetes that this resource is a Service.

---

## metadata

```yaml
metadata:
  name: my-service
```

Defines:

* resource name
* labels
* annotations

Example:

```yaml
metadata:
  name: web-app
  labels:
    app: web-app
```

---

# The spec Section

Most Service configuration lives inside:

```yaml
spec:
```

This section determines:

* Service type
* Pod selection
* Ports
* Traffic behavior

---

# Selectors

Selectors are how Services discover Pods.

Example:

```yaml
selector:
  app: web-app
```

Kubernetes searches for Pods with:

```yaml
labels:
  app: web-app
```

Example Pod:

```yaml
metadata:
  labels:
    app: web-app
```

The Service automatically includes this Pod.

---

## Why Selectors Matter

If labels don't match:

```yaml
selector:
  app: frontend
```

but Pods contain:

```yaml
labels:
  app: backend
```

the Service finds nothing.

Result:

```bash
kubectl get endpoints service-name
```

returns:

```text
NAME          ENDPOINTS
service-name  <none>
```

No endpoints means no traffic can reach Pods.

---

# Ports Section

Example:

```yaml
ports:
- port: 80
  targetPort: 80
```

These values often confuse beginners.

---

## port

The port exposed by the Service.

Example:

```yaml
port: 80
```

Clients connect to:

```text
http://service-name:80
```

---

## targetPort

The port on the Pod.

Example:

```yaml
targetPort: 80
```

The Service forwards traffic to:

```text
PodIP:80
```

---

# Different port and targetPort Values

They do not have to match.

Example:

```yaml
ports:
- port: 80
  targetPort: 8080
```

Traffic flow:

```text
Client
   |
   v
Service:80
   |
   v
Pod:8080
```

This is common in production systems.

---

# protocol

Default:

```yaml
protocol: TCP
```

Supported values:

```text
TCP
UDP
SCTP
```

Most applications use TCP.

---

# Multiple Ports in a Service

A Service can expose multiple ports.

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app
spec:
  selector:
    app: web-app
  ports:
  - name: http
    port: 80
    targetPort: 80

  - name: https
    port: 443
    targetPort: 443
```

Traffic:

```text
web-app:80
web-app:443
```

Both route to Pods.

---

# Named Ports

Instead of numbers, Kubernetes allows named ports.

Pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-app
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - name: http
      containerPort: 80
```

Service:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app
spec:
  selector:
    app: web-app
  ports:
  - port: 80
    targetPort: http
```

Notice:

```yaml
targetPort: http
```

instead of:

```yaml
targetPort: 80
```

Kubernetes resolves the named port automatically.

---

# Session Affinity

Normally Services load balance requests.

Example:

```text
Request 1 → Pod A
Request 2 → Pod B
Request 3 → Pod C
```

Sometimes applications require users to stay connected to the same Pod.

This is called Sticky Sessions.

---

## Enable Session Affinity

```yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app
spec:
  selector:
    app: web-app

  sessionAffinity: ClientIP

  ports:
  - port: 80
    targetPort: 80
```

Now:

```text
User A → Pod 1
User A → Pod 1
User A → Pod 1

User B → Pod 2
User B → Pod 2
```

Traffic stays consistent.

---

## Configure Timeout

```yaml
sessionAffinityConfig:
  clientIP:
    timeoutSeconds: 10800
```

Example:

```text
10800 seconds
= 3 hours
```

After timeout expires, Kubernetes may rebalance traffic.

---

# ExternalName Services

ExternalName is very different from other Service types.

Unlike ClusterIP, NodePort, and LoadBalancer:

* No Service IP
* No kube-proxy routing
* No Endpoints

Instead:

* Kubernetes creates a DNS alias (CNAME)

---

## Example

```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-db
spec:
  type: ExternalName
  externalName: my-database.example.com
```

Now Pods can use:

```text
external-db
```

instead of:

```text
my-database.example.com
```

---

# Why Use ExternalName?

Imagine:

```text
Application
   |
   v
AWS RDS Database
```

Database DNS:

```text
prod-db.us-east-1.rds.amazonaws.com
```

Long and hard to remember.

Create:

```yaml
kind: Service
type: ExternalName
```

Now applications use:

```text
external-db
```

which is simpler.

---

# Service Networking Deep Dive

Let's see what happens when a request reaches a Service.

Suppose:

```text
Client Pod
   |
   v
web-app Service
   |
   v
3 Backend Pods
```

---

# Detailed Traffic Flow

```text
Client Pod
     |
     v
Service ClusterIP
     |
     v
kube-proxy
     |
     v
Endpoint Selection
     |
     +----> Pod 1
     +----> Pod 2
     +----> Pod 3
```

The Service itself does not forward traffic.

The actual work is done by kube-proxy.

---

# What Is kube-proxy?

kube-proxy runs on every node.

Verify:

```bash
kubectl get pods -n kube-system
```

You should see:

```text
kube-proxy
```

running on each node.

---

# Responsibilities of kube-proxy

kube-proxy:

1. Watches Services
2. Watches Endpoints
3. Updates routing rules
4. Load balances traffic
5. Maintains Service networking

---

# How kube-proxy Learns About Services

It watches the API Server.

When you create:

```yaml
kind: Service
```

the API Server stores it.

kube-proxy detects the change and updates networking rules automatically.

---

# kube-proxy Modes

Historically Kubernetes used:

### Userspace Mode

Older implementation.

```text
Client
   |
   v
kube-proxy process
   |
   v
Pod
```

Slow and mostly obsolete.

---

### iptables Mode

Most common.

```text
Client
   |
   v
iptables
   |
   v
Pod
```

Fast and widely used.

---

### IPVS Mode

Advanced high-performance implementation.

```text
Client
   |
   v
IPVS
   |
   v
Pod
```

Better scalability for large clusters.

---

# Service IP Range

Every cluster reserves IPs for Services.

Common default:

```text
10.96.0.0/16
```

This range is separate from Pod IPs.

---

## Example

Service IP:

```text
10.96.100.50
```

Pod IPs:

```text
10.244.0.5
10.244.0.6
10.244.0.7
```

Notice the different network ranges.

---

# Check Service CIDR

Depending on your cluster:

```bash
kubectl cluster-info dump | grep service-cluster-ip-range
```

May show:

```text
--service-cluster-ip-range=10.96.0.0/16
```

---

# ClusterIP Is Virtual

Important concept:

The ClusterIP does not belong to a Pod.

Example:

```text
10.96.100.50
```

This IP is virtual.

kube-proxy intercepts traffic destined for that IP and redirects it to Pods.

---

# Service Load Balancing

Suppose:

```text
Pod A
Pod B
Pod C
```

Requests arrive:

```text
Request 1 → Pod A
Request 2 → Pod B
Request 3 → Pod C
Request 4 → Pod A
```

Traffic is distributed automatically.

This prevents one Pod from handling all requests.

---

# Service Health Awareness

Endpoints are updated automatically.

Example:

Before failure:

```text
Pod A
Pod B
Pod C
```

Endpoints:

```text
10.244.0.5
10.244.0.6
10.244.0.7
```

---

Pod B crashes.

New endpoints:

```text
10.244.0.5
10.244.0.7
```

Service stops routing traffic to Pod B.

No manual intervention required.

---

# Inspecting Service Endpoints

View endpoints:

```bash
kubectl get endpoints web-app-clusterip
```

Example:

```text
NAME                ENDPOINTS
web-app-clusterip   10.244.0.5:80,10.244.0.6:80,10.244.0.7:80
```

---

# Describe a Service

One of the most useful commands:

```bash
kubectl describe service web-app-clusterip
```

Shows:

* ClusterIP
* Ports
* Selectors
* Endpoints
* Events

Example:

```text
Selector: app=web-app
Endpoints:
10.244.0.5:80
10.244.0.6:80
10.244.0.7:80
```

---



# Troubleshooting Common Issues

Services are one of the most commonly used Kubernetes resources, which also means they are one of the most commonly troubleshooted resources.

When a Service is not working, the issue usually falls into one of these categories:

1. Selector mismatch
2. Missing Endpoints
3. Pod failures
4. DNS problems
5. NodePort access issues
6. LoadBalancer provisioning issues
7. Network configuration problems

The key is learning how to systematically diagnose the issue.

---

# Issue 1: Service Not Routing Traffic to Pods

## Symptoms

The Service exists:

```bash
kubectl get svc
```

but requests fail:

```bash
curl http://web-app-clusterip
```

or

```bash
wget http://web-app-clusterip
```

returns no response.

---

## First Check: Endpoints

```bash
kubectl get endpoints web-app-clusterip
```

Example bad output:

```text
NAME                ENDPOINTS
web-app-clusterip   <none>
```

This means the Service is not connected to any Pods.

---

## Most Common Cause: Selector Mismatch

Service:

```yaml
selector:
  app: frontend
```

Pod:

```yaml
labels:
  app: backend
```

No match.

Therefore:

```text
Service → No Pods
```

---

## Verify Labels

Check Service selector:

```bash
kubectl describe service web-app-clusterip
```

Check Pod labels:

```bash
kubectl get pods --show-labels
```

Ensure they match exactly.

Example:

```yaml
selector:
  app: web-app
```

and

```yaml
labels:
  app: web-app
```

---

## Verify Pod Status

```bash
kubectl get pods
```

Bad:

```text
CrashLoopBackOff
Error
ImagePullBackOff
```

Good:

```text
Running
```

A Service can only route traffic to healthy Pods.

---

# Issue 2: DNS Resolution Fails

## Symptoms

```bash
nslookup web-app-clusterip
```

fails.

Or:

```bash
wget http://web-app-clusterip
```

cannot resolve the hostname.

---

## Check CoreDNS

CoreDNS provides Kubernetes DNS.

Verify:

```bash
kubectl get pods -n kube-system
```

Look for:

```text
coredns
```

Example:

```text
coredns-xxxxx   Running
coredns-yyyyy   Running
```

---

## Verify DNS Resolution

Create a temporary Pod:

```bash
kubectl run dns-test \
--image=busybox \
--rm -it \
--restart=Never -- sh
```

Inside:

```bash
nslookup web-app-clusterip
```

Expected:

```text
Name: web-app-clusterip
Address: 10.96.100.50
```

---

## Use Full DNS Name

Sometimes testing with the full DNS name helps:

```bash
wget http://web-app-clusterip.default.svc.cluster.local
```

---

## Verify DNS Policy

```bash
kubectl describe pod <pod-name>
```

Look for:

```text
dnsPolicy: ClusterFirst
```

This is the normal Kubernetes DNS policy.

---

# Issue 3: NodePort Not Reachable

## Symptoms

Service exists:

```text
TYPE: NodePort
```

but:

```bash
curl http://localhost:30080
```

fails.

---

## Verify NodePort

```bash
kubectl get services
```

Example:

```text
web-app-nodeport   NodePort   80:30080/TCP
```

Ensure the expected NodePort is present.

---

## Check Node IP

```bash
kubectl get nodes -o wide
```

Example:

```text
NAME      INTERNAL-IP
worker-1  10.0.0.10
```

Test:

```bash
curl http://10.0.0.10:30080
```

---

## Minikube Users

Use:

```bash
minikube service web-app-nodeport --url
```

instead of guessing the IP.

---

## Docker Desktop Users

Usually:

```bash
curl http://localhost:30080
```

works.

---

## Firewall Issues

Sometimes the Service is correct but the operating system firewall blocks access.

Verify that:

```text
30080
```

is allowed.

---

# Issue 4: LoadBalancer EXTERNAL-IP Stuck at Pending

## Symptoms

```text
NAME                   TYPE           EXTERNAL-IP
web-app-loadbalancer   LoadBalancer   <pending>
```

---

## Why It Happens

Local clusters:

* Minikube
* Kind
* Docker Desktop
* K3d

do not automatically provision cloud load balancers.

Therefore Kubernetes cannot assign:

```text
Public IP
```

and displays:

```text
<pending>
```

---

## Minikube Solution

Run:

```bash
minikube tunnel
```

Open another terminal:

```bash
kubectl get services
```

You should now see an external IP.

---

## Cloud Environments

If running on:

* AWS
* Azure
* GCP

verify:

* cloud controller manager is running
* permissions are correct
* quota limits are not exceeded

---

# Issue 5: Service Exists but Returns No Response

Check:

```bash
kubectl describe service web-app-clusterip
```

Look for:

```text
Endpoints:
```

If empty:

```text
Endpoints: <none>
```

the Service has nothing to route traffic to.

---

Check:

```bash
kubectl get endpoints web-app-clusterip
```

and

```bash
kubectl get pods --show-labels
```

---

# Useful Debugging Commands

## View Services

```bash
kubectl get svc
```

or

```bash
kubectl get services
```

---

## Detailed Service Information

```bash
kubectl describe service web-app-clusterip
```

---

## View Endpoints

```bash
kubectl get endpoints
```

Specific Service:

```bash
kubectl get endpoints web-app-clusterip
```

If output is:
```bash
<none>
```
then:
```bash
Selector and Pod labels do not match.
```
This is one of the most common Service debugging issues.

---

## View Pods and Labels

```bash
kubectl get pods --show-labels
```

---

## View Service YAML

```bash
kubectl get svc web-app-clusterip -o yaml
```

---

## View Service JSON

```bash
kubectl get svc web-app-clusterip -o json
```

---

# kubectl explain

A very useful command when learning Kubernetes.

View Service documentation:

```bash
kubectl explain service
```

---

View spec documentation:

```bash
kubectl explain service.spec
```

---

View ports documentation:

```bash
kubectl explain service.spec.ports
```

---

# Custom Service Output

Default output can be verbose.

Use custom columns:

```bash
kubectl get services \
-o custom-columns='NAME:.metadata.name,TYPE:.spec.type,CLUSTER-IP:.spec.clusterIP'
```

Example:

```text
NAME                 TYPE           CLUSTER-IP
web-app-clusterip    ClusterIP      10.96.100.50
web-app-nodeport     NodePort       10.96.100.51
```

---

# Port Forwarding

Sometimes you want temporary local access without creating a NodePort.

Use:

```bash
kubectl port-forward service/web-app-clusterip 8080:80
```

Now access:

```text
http://localhost:8080
```

Traffic flow:

```text
localhost:8080
      |
      v
ClusterIP Service
      |
      v
Pods
```

This is extremely useful for testing.

---

# Creating Services Quickly with kubectl expose

Instead of writing YAML manually:

```bash
kubectl expose deployment web-app \
--port=80 \
--target-port=80 \
--name=web-app-service
```

Creates:

```text
ClusterIP Service
```

automatically.

---

## Create a NodePort Service

```bash
kubectl expose deployment web-app \
--port=80 \
--target-port=80 \
--type=NodePort \
--name=web-app-nodeport
```

Kubernetes generates the Service manifest for you.

---

# Best Practices

## Use Labels Consistently

Example:

```yaml
labels:
  app: web-app
```

Keep naming predictable.

---

## Verify Endpoints

Whenever a Service fails:

```bash
kubectl get endpoints
```

should be one of your first checks.

---

## Use ClusterIP by Default

Most Services only need internal communication.

Use:

```yaml
type: ClusterIP
```

unless external access is required.

---

## Avoid Exposing Everything with NodePort

NodePort is useful for:

* learning
* testing
* development

Production systems typically use:

```text
Ingress
LoadBalancer
```

instead.

---

## Name Ports

Good:

```yaml
ports:
- name: http
```

Better readability and easier maintenance.

---

# Challenge Task 9: Cleanup

Delete the Deployment:

```bash
kubectl delete -f app-deployment.yaml
```

Delete ClusterIP Service:

```bash
kubectl delete -f clusterip-service.yaml
```

Delete NodePort Service:

```bash
kubectl delete -f nodeport-service.yaml
```

Delete LoadBalancer Service:

```bash
kubectl delete -f loadbalancer-service.yaml
```

---

## Verify Cleanup

Check Pods:

```bash
kubectl get pods
```

Expected:

```text
No resources found
```

---

Check Services:

```bash
kubectl get services
```

Expected:

```text
NAME         TYPE        CLUSTER-IP
kubernetes   ClusterIP   10.96.0.1
```

Only the built-in Kubernetes Service should remain.

---

# Documentation Requirements


### 1. What Problem Services Solve

Explain:

* Pod IP instability
* Multiple Pod replicas
* Stable networking

---

### 2. Service Manifests

Include:

* ClusterIP Service
* NodePort Service
* LoadBalancer Service

Explain each field.

---

### 3. Service Types Comparison

Include a comparison table.

| Type         | Accessible From | Use Case              |
| ------------ | --------------- | --------------------- |
| ClusterIP    | Internal        | Service communication |
| NodePort     | Node IP         | Testing               |
| LoadBalancer | Public IP       | Production            |

---

### 4. DNS Discovery

Explain:

```text
service.namespace.svc.cluster.local
```

and short names.

---

### 5. Endpoints

Explain:

```bash
kubectl get endpoints
```

and how Services discover Pods.

---

### 6. Screenshots

Include:

```bash
kubectl get services
```

output.

Also include:

* DNS testing
* Service testing
* NodePort access

---

# Notes Section

## What I Learned About Services

* Services provide stable networking.
* Services provide stable DNS names.
* Services load balance traffic.
* Services hide Pod IP changes.

---

## What I Learned About Service Types

### ClusterIP

Internal communication only.

### NodePort

Exposes Services through node ports.

### LoadBalancer

Exposes Services through cloud load balancers.

### ExternalName

Maps a Service name to an external DNS record.

---

## What I Learned About DNS

Every Service automatically receives a DNS record.

Format:

```text
<service-name>.<namespace>.svc.cluster.local
```

---

## What I Learned About Endpoints

Endpoints contain:

```text
Pod IPs
```

currently backing a Service.

---

## What I Learned About Troubleshooting

Always verify:

* Pods
* Labels
* Selectors
* Endpoints
* DNS
* Service configuration

before assuming a networking problem.

---

#  Revision Questions

### Q1. Why are Services needed?

Because Pod IPs are not stable and Deployments run multiple Pods.

---

### Q2. What is ClusterIP?

The default Service type providing internal cluster access.

---

### Q3. What is NodePort?

A Service exposed on every node using a port between:

```text
30000–32767
```

---

### Q4. What is LoadBalancer?

A Service type that provisions an external cloud load balancer.

---

### Q5. What is an Endpoint?

The list of Pod IPs backing a Service.

---

### Q6. How do Services find Pods?

Using label selectors.

---

### Q7. What command shows Service endpoints?

```bash
kubectl get endpoints
```

---

### Q8. What is the DNS format of a Service?

```text
<service-name>.<namespace>.svc.cluster.local
```

---

### Q9. Does a LoadBalancer Service also have a ClusterIP?

Yes.

LoadBalancer includes:

* ClusterIP
* NodePort
* LoadBalancer configuration

---

### Q10. What component implements Service routing?

```text
kube-proxy
```

---

**Services are the foundation of Kubernetes networking. Mastering them is essential before moving to Ingress, Network Policies, StatefulSets, and production-grade Kubernetes architectures.**

---

## NodePort
```bash
Internet
↓
NodeIP:NodePort
↓
Service
↓
Pods
```
## LoadBalancer
```bash
Internet
↓
Cloud Load Balancer
↓
Service
↓
Pods
```
## ExternalName
```bash
Service
↓
External DNS Name
↓
External System
```
These are official Service types.
