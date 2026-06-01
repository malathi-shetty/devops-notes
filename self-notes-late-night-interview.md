# Tell Me About Yourself

Practice 5 times.

---
```bash
 Background

 Current Learning

 Why This Role
```

---

# Explain Your Project

Practice 5 times.

Remember:

```text
Project
Goal
Technology
My Work
Learning
```

### What was the project?

```bash

Why was it done?

What did you use?

What did you do?

What did you learn?
```

---

# GitHub Copilot

### What is it?

> GitHub Copilot is an AI-powered coding assistant.

### Why is it used?

> It helps developers generate code, documentation, tests, and explanations faster.

### Example

> For example, Copilot can generate a GitHub Actions workflow or Kubernetes YAML file from a simple prompt.

---

# Prompt Engineering

### What is it?

> Prompt engineering is the practice of writing effective instructions for AI systems.

### Why is it used?

> Better prompts produce better AI responses.

### Example

Bad prompt:

```text
Create workflow
```

Good prompt:

```text
Create GitHub Actions workflow to build a Maven project and run tests on every push.
```

---

# Custom Instructions

### What are they?

> Custom Instructions are rules that guide how Copilot generates responses.

### Why are they used?

> To make generated code follow team standards.

### Example

```text
Use Java 17
Use TestNG
Use Page Object Model
Avoid Thread.sleep
```

---

# AI Agent

### What is it?

> An AI Agent is a system that can perform multiple tasks toward a goal.

### Why is it used?

> To automate complex workflows.

### Example

> An AI Agent can generate code, create tests, update files, and write documentation automatically.

---

# CI/CD

### What is it?

> CI/CD stands for Continuous Integration and Continuous Delivery or Deployment.

### Why is it used?

> To automate software build, testing, and deployment processes.

### Example

> When code is pushed to GitHub, tests run automatically and the application is deployed.

---

# GitHub Actions

### What is it?

> GitHub Actions is GitHub's CI/CD automation platform.

### Why is it used?

> To automate workflows such as build, test, and deployment.

### Example

> A workflow can run automatically whenever code is pushed to a repository.

---

# Docker

### What is it?

> Docker is a containerization platform.

### Why is it used?

> To package applications and dependencies into containers.

### Example

> A Java application can be packaged into a Docker image and run consistently anywhere.

---

# Kubernetes

### What is it?

> Kubernetes is a container orchestration platform.

### Why is it used?

> To deploy, scale, and manage containers.

### Example

> If a container crashes, Kubernetes can automatically restart it.

---

# Deployment

### What is it?

> A Deployment is a Kubernetes resource that manages Pods.

### Why is it used?

> To ensure the required number of application instances are running.

### Example

> If one Pod fails, Kubernetes creates a new Pod automatically.

---

# Service

### What is it?

> A Service is a Kubernetes resource that exposes Pods.

### Why is it used?

> To provide stable network access to applications.

### Example

> Users connect to the Service instead of connecting directly to Pods.

---

# Namespace

### What is it?

> A Namespace is a logical partition in a Kubernetes cluster.

### Why is it used?

> To organize and isolate resources.

### Example

> Separate namespaces can be created for development, testing, and production.

---

# Docker vs Kubernetes

### Docker

> Creates and runs containers.

### Kubernetes

> Manages containers at scale.

### Example

> Docker creates the application container. Kubernetes manages multiple containers across servers.

---

# How Would Copilot Help In Your Project?

### What would it do?

> Copilot can explain YAML files, generate workflows, create Kubernetes manifests, generate documentation, and troubleshoot errors.

### Why is it useful?

> It reduces manual effort and improves productivity.

### Example

> I can ask Copilot to generate a Deployment YAML or explain a GitHub Actions workflow.

---

# One-Line Rescue Answers

If your mind goes blank:

### GitHub Copilot

> AI coding assistant.

### GitHub Actions

> CI/CD automation platform.

### Docker

> Containerization platform.

### Kubernetes

> Container orchestration platform.

### Deployment

> Manages Pods.

### Service

> Exposes Pods.

### Namespace

> Organizes resources.

### CI/CD

> Automates build, test, and deployment.



---




#### Write a simple Dockerfile

Example:

```dockerfile
FROM openjdk:17

COPY app.jar app.jar

ENTRYPOINT ["java","-jar","app.jar"]
```

You don't need to memorize complicated Dockerfiles.

Just know:

```text
FROM    -> base image
COPY    -> copy file
RUN     -> execute command
CMD/ENTRYPOINT -> start application
```

---

#### Explain a simple GitHub Actions YAML

Example:

```yaml
name: Build

on:
  push:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
```

Know:

```text
name      -> workflow name
on        -> trigger
jobs      -> work to execute
runs-on   -> runner
steps     -> actions performed
```

---

#### Kubernetes YAML

Example:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: my-app

spec:
  replicas: 2
```

Know:

```text
apiVersion
kind
metadata
spec
replicas
```

---



#### OOP Concepts

Because your background includes Java and testing, they may ask:

### What is Encapsulation?

> Wrapping data and methods into a single unit and controlling access using access modifiers.

### What is Inheritance?

> One class acquiring properties and methods of another class.

### What is Polymorphism?

> Same method name, different behavior.

### What is Abstraction?

> Hiding implementation details and showing only essential functionality.

---

###  Java OOP

Use:

#### What is it?

#### Why is it used?

#### Example

Example:

### Encapsulation

**What?**

> Binding data and methods together.

**Why?**

> To protect data.

**Example?**

```java
private String name;

public void setName(String name){
   this.name=name;
}
```

---



### Dockerfile

```dockerfile
FROM openjdk:17
COPY app.jar app.jar
ENTRYPOINT ["java","-jar","app.jar"]
```

### GitHub Actions

```yaml
name: Build

on:
  push:
```

### Deployment

```yaml
kind: Deployment
replicas: 2
```

That's enough to discuss them.

---



> "Can you write a Dockerfile?"

Write:

```dockerfile
FROM openjdk:17

COPY app.jar app.jar

ENTRYPOINT ["java","-jar","app.jar"]
```

and explain:

```text
FROM = base image
COPY = copy application
ENTRYPOINT = run application
```

---



> "Can you write a GitHub Actions workflow?"

Write:

```yaml
name: Build

on:
  push:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
```

and explain each section.

---


