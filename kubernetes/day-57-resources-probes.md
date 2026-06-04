# Day 57 – Kubernetes Resource Requests, Limits, and Probes

---

# A. Core Concepts (Theory)

## Why Resource Management Matters

When Kubernetes schedules a Pod, it needs to know:

* How much CPU the application requires
* How much Memory the application requires
* Whether the application is healthy
* Whether the application is ready to receive traffic

Without resource definitions:

* Scheduler makes poor placement decisions
* Pods may consume excessive resources
* Node stability can suffer
* Kubernetes cannot effectively detect unhealthy applications

Kubernetes provides:

1. Resource Requests
2. Resource Limits
3. Liveness Probes
4. Readiness Probes
5. Startup Probes

---

# 1. Resource Requests

Requests define the minimum resources a Pod requires.

Example:

```yaml
resources:
  requests:
    cpu: 100m
    memory: 128Mi
```

Scheduler uses requests to decide:

```text
Which node has enough free resources
to run this Pod?
```

Requests affect:

```text
Scheduling
```

---

## CPU Requests

```text
100m = 0.1 CPU

250m = 0.25 CPU

500m = 0.5 CPU

1000m = 1 CPU Core
```

Important:

```text
100m ≠ 100 CPUs

100m = 0.1 CPU
100 = 100 CPU Cores
```

---

## Memory Requests

```text
Mi = Mebibyte

Gi = Gibibyte
```

Examples:

```text
128Mi ≈ 128 MB

256Mi ≈ 256 MB

1Gi = 1024Mi
```

---

# 2. Resource Limits

Limits define the maximum resources a container may consume.

Example:

```yaml
resources:
  limits:
    cpu: 250m
    memory: 256Mi
```

Limits affect:

```text
Runtime Enforcement
```

Kubelet enforces limits while containers are running.

---

# Requests vs Limits

Example:

```yaml
resources:
  requests:
    cpu: 100m
    memory: 128Mi

  limits:
    cpu: 250m
    memory: 256Mi
```

Scheduler guarantees:

```text
CPU = 100m

Memory = 128Mi
```

Container may consume:

```text
CPU up to 250m

Memory up to 256Mi
```

---

## Simple Analogy

Request:

```text
I need at least this much.
```

Limit:

```text
I cannot exceed this much.
```

Hotel Analogy:

Request:

```text
Reserve one room for me.
```

Limit:

```text
You cannot occupy more than
three rooms.
```

---

# Scheduler Uses Requests Only

Very Important  Question

Scheduler considers:

```text
Requests
```

Scheduler ignores:

```text
Limits
```

Example:

```yaml
requests:
  cpu: 100m

limits:
  cpu: 4
```

Scheduler reserves:

```text
100m CPU
```

NOT:

```text
4 CPUs
```

---

# Requests Without Limits

Example:

```yaml
resources:
  requests:
    cpu: 100m
    memory: 128Mi
```

Result:

```text
Minimum resources guaranteed.
```

However:

```text
Container may consume
additional resources
if available.
```

Risk:

```text
Noisy Neighbor Problem
```

One container may negatively impact other workloads.

---

# Limits Without Requests

Example:

```yaml
resources:
  limits:
    cpu: 500m
    memory: 512Mi
```

If requests are omitted:

```text
Kubernetes may use limits
as requests.
```

Very common  question.

---

# 3. QoS Classes

QoS = Quality of Service

Determines eviction priority when a node is under resource pressure.

---

## Guaranteed

Requests = Limits

Example:

```yaml
requests:
  cpu: 100m
  memory: 128Mi

limits:
  cpu: 100m
  memory: 128Mi
```

QoS:

```text
Guaranteed
```

Highest protection.

---

## Burstable

Requests < Limits

Example:

```yaml
requests:
  cpu: 100m

limits:
  cpu: 250m
```

QoS:

```text
Burstable
```

---

## BestEffort

No requests.

No limits.

Example:

```yaml
containers:
- image: nginx
```

QoS:

```text
BestEffort
```

Lowest protection.

---

# QoS Eviction Order

When node memory pressure occurs:

```text
BestEffort
→ Evicted First

Burstable
→ Evicted Next

Guaranteed
→ Evicted Last
```

Guaranteed Pods receive the highest protection.

---

# 4. CPU vs Memory Behavior

Extremely Important  Topic

CPU:

```text
Compressible Resource
```

When CPU limit exceeded:

```text
Container throttled

Container survives

Application slower
```

---

Memory:

```text
Incompressible Resource
```

When Memory limit exceeded:

```text
Container killed

OOMKilled

Exit Code 137
```

---

# OOMKilled

Formula:

```text
137 = 128 + 9
```

Where:

```text
9 = SIGKILL
```

Meaning:

```text
Kernel forcefully terminated
the process.
```

Depending on runtime:

```text
Reason: OOMKilled
```

or

```text
Reason: Error
Exit Code: 137
```

Both may indicate memory exhaustion.

---

# 5. Kubernetes Probes

Probes answer:

```text
Is application alive?

Is application ready?

Has application started?
```

Probe Types:

```text
httpGet

exec

tcpSocket
```

Three Probe Categories:

1. Liveness Probe
2. Readiness Probe
3. Startup Probe




***



# B. Kubernetes Probes

---

# 1. Liveness Probe

## Purpose

Liveness Probe checks:

```text
Is the application alive?
```

It is used to detect:

* Dead applications
* Stuck applications
* Hung processes

---

## Failure Action

If the probe fails repeatedly:

```text
Container Restarted
```

Kubernetes assumes:

```text
Application is unhealthy.
```

and restarts the container.

---

## Example

```yaml
livenessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy

  periodSeconds: 5
  failureThreshold: 3
```

Meaning:

```text
Run every 5 seconds.

After 3 consecutive failures,
restart the container.
```

---

# Liveness Summary

Question:

```text
What happens when a liveness probe fails?
```

Answer:

```text
Container is restarted.
```

---

# 2. Readiness Probe

## Purpose

Readiness Probe checks:

```text
Can the application receive traffic?
```

A Pod may be:

```text
Running
```

but not yet:

```text
Ready
```

to serve requests.

---

## Failure Action

Readiness failure:

```text
NO restart
```

Instead:

```text
Pod removed from
Service Endpoints.
```

Traffic stops flowing to that Pod.

---

## Example

```yaml
readinessProbe:
  httpGet:
    path: /index.html
    port: 80

  periodSeconds: 5
  failureThreshold: 3
```

---

# Readiness Summary

Question:

```text
What happens when a readiness probe fails?
```

Answer:

```text
Pod removed from
Service Endpoints.

Container NOT restarted.
```

---

# 3. Startup Probe

## Purpose

Startup Probe is designed for:

```text
Slow-starting applications
```

Examples:

* Large Java applications
* Spring Boot applications
* Legacy enterprise applications
* Applications performing lengthy initialization

---

## Problem Without Startup Probe

Liveness Probe begins checking immediately.

Slow startup:

```text
Application still starting
```

Liveness sees:

```text
Application not ready
```

Result:

```text
Container restarted too early
```

---

# Startup Probe Solution

Startup Probe delays:

```text
Liveness Checks

Readiness Checks
```

until startup succeeds.

This prevents premature restarts.

---

## Example

```yaml
startupProbe:
  exec:
    command:
    - cat
    - /tmp/started

  periodSeconds: 5
  failureThreshold: 12
```

Startup Budget:

```text
5 × 12
= 60 seconds
```

Application gets:

```text
60 seconds
```

to finish startup.

---

## Failure Action

If Startup Probe repeatedly fails:

```text
Container killed
```

then:

```text
Container restarted
```

Eventually:

```text
CrashLoopBackOff
```

may occur.

---

# Startup Summary

Question:

```text
What happens when a startup probe fails?
```

Answer:

```text
Container restarted.
```

---

# C. Real Lab Results

These are actual observations from the hands-on lab.

---

# Task 1 – Resource Requests & Limits

Manifest:

```yaml
resources:
  requests:
    cpu: 100m
    memory: 128Mi

  limits:
    cpu: 250m
    memory: 256Mi
```

Observed:

```text
QoS Class: Burstable
```

Reason:

```text
Requests < Limits
```

Verification Answer:

```text
QoS = Burstable
```

---

# Task 2 – OOMKilled

Container attempted:

```text
200M Memory
```

Limit:

```text
100Mi
```

Observed:

```text
State: Terminated

Reason: Error

Exit Code: 137
```

Verification Answer:

```text
OOMKilled Exit Code = 137
```

Explanation:

```text
137 = 128 + 9

9 = SIGKILL
```

Kernel terminated the process because memory exceeded the limit.

---

# Task 3 – Pending Pod

Requested:

```yaml
resources:
  requests:
    cpu: 100
    memory: 128Gi
```

Important:

```text
100 = 100 CPU Cores
```

NOT:

```text
100m
```

Observed Scheduler Event:

```text
0/1 nodes are available:

1 Insufficient cpu

1 Insufficient memory

no new claims to deallocate

preemption:
0/1 nodes are available:

1 Preemption is not helpful for scheduling
```

Verification Answer:

```text
Insufficient cpu

Insufficient memory
```

Meaning:

```text
Scheduler could not find
a suitable node.
```

Result:

```text
Pod remained Pending.
```

---

# Task 4 – Liveness Probe

Container Behavior:

```bash
touch /tmp/healthy

sleep 30

rm -f /tmp/healthy

sleep 600
```

Probe:

```yaml
livenessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy

  periodSeconds: 5

  failureThreshold: 3
```

Observed:

```text
Liveness probe failed:

cat: can't open '/tmp/healthy'
```

Observed:

```text
Container busybox failed
liveness probe,
will be restarted
```

Observed:

```text
Restart Count: 13
```

Verification Answer:

```text
Liveness failure
restarts the container.
```

---

# Task 5 – Readiness Probe

## First Attempt

Observed Error:

```text
the pod has no labels
and cannot be exposed
```

Reason:

```text
Service selectors
require labels.
```

Fix:

```yaml
metadata:
  labels:
    app: readiness-demo
```

---

## Path Correction

Initially:

```yaml
path: /
```

Observed:

```text
403 Forbidden
```

This was not the intended lab behavior.

Correct Probe:

```yaml
path: /index.html
```

After deleting file:

```text
404 Not Found
```

which correctly demonstrated readiness failure.

---

## Successful Attempt

Before deleting file:

```text
READY 1/1
```

Endpoints:

```text
10.244.x.x:80
```

Command:

```bash
kubectl exec readiness-demo -- rm /usr/share/nginx/html/index.html
```

After deletion:

```text
READY 0/1
```

Observed Endpoints:

```text
<none>
```

Observed Restart Count:

```text
0
```

Verification Answer:

```text
Container was NOT restarted.
```

Readiness only removes the Pod from Service endpoints.

```bash
READY 0/1 means:
Pod is running but not ready to receive traffic.
```

---

# Task 6 – Startup Probe

Configuration:

```yaml
startupProbe:
  exec:
    command:
    - cat
    - /tmp/started

  periodSeconds: 5

  failureThreshold: 12
```

Startup Budget:

```text
12 × 5
= 60 seconds
```

Application Startup Time:

```text
20 seconds
```

Result:

```text
Startup Probe Succeeded
```

---

## Verification Question

Question:

```text
What if failureThreshold = 2 ?
```

Calculation:

```text
2 × 5
= 10 seconds
```

Application requires:

```text
20 seconds
```

Result:

```text
Startup probe fails

Container killed

Container restarted

Container killed again

Container restarted again
```

Eventually:

```text
CrashLoopBackOff
```

Verification Answer:

```text
failureThreshold=2

→ Startup Probe Fails

→ Restart Loop

→ CrashLoopBackOff
```

***



# D. Troubleshooting & Common Mistakes

---

# Mistake 1

## Pod Exposed Without Labels

Error:

```text
the pod has no labels
and cannot be exposed
```

Reason:

```text
Services use selectors.

Selectors require labels.
```

Fix:

```yaml
metadata:
  labels:
    app: readiness-demo
```

---

# Mistake 2

## Wrong Readiness Path

Using:

```yaml
path: /
```

may return:

```text
403 Forbidden
```

and fail for the wrong reason.

Lab intended:

```yaml
path: /index.html
```

Then deleting the file causes:

```text
404 Not Found
```

which correctly demonstrates readiness failure.

---

# Mistake 3

## Expecting Readiness Probe to Restart Containers

Wrong:

```text
Readiness failure
restarts the container.
```

Correct:

```text
Readiness failure
removes Pod from
Service endpoints only.
```

---

# Mistake 4

## Confusing CPU and Memory Behavior

Wrong:

```text
CPU limit exceeded
→ Container killed
```

Correct:

```text
CPU limit exceeded
→ CPU throttled
```

Wrong:

```text
Memory limit exceeded
→ Throttled
```

Correct:

```text
Memory limit exceeded
→ OOMKilled
```

---

# Mistake 5

## Forgetting to Check Events

Always run:

```bash
kubectl describe pod <pod-name>
```

Events often reveal the exact problem:

```text
FailedScheduling

OOMKilled

Probe Failures

BackOff

ImagePullBackOff
```

---

# Mistake 6

## Startup Probe Budget Too Small

Example:

```yaml
periodSeconds: 5
failureThreshold: 2
```

Budget:

```text
10 seconds
```

Application Startup:

```text
20 seconds
```

Result:

```text
Startup Probe Fails

Container Restarted

CrashLoopBackOff
```

---

# E.  Questions & Answers

### Q1. What is a Resource Request?

Answer:

```text
Minimum resources required
by a Pod.

Used by the Scheduler.
```

---

### Q2. What is a Resource Limit?

Answer:

```text
Maximum resources a container
can consume.

Enforced by Kubelet.
```

---

### Q3. Difference Between Requests and Limits?

Answer:

```text
Requests = Scheduling

Limits = Runtime Enforcement
```

---

### Q4. Which Resource Values Does the Scheduler Use?

Answer:

```text
Requests Only
```

Scheduler ignores limits.

---

### Q5. What Happens If Requests Are Not Specified?

Answer:

```text
No guaranteed resource reservation.

If limits exist,
Kubernetes may use limits
as requests.
```

---

### Q6. What is QoS?

Answer:

```text
Quality of Service

Used to determine
eviction priority.
```

---

### Q7. What Are the QoS Classes?

Answer:

```text
Guaranteed

Burstable

BestEffort
```

---

### Q8. What Happens When CPU Limit Is Exceeded?

Answer:

```text
CPU throttled.

Container survives.
```

---

### Q9. What Happens When Memory Limit Is Exceeded?

Answer:

```text
OOMKilled

Container terminated.
```

---

### Q10. What Is OOMKilled Exit Code?

Answer:

```text
137
```

---

### Q11. Why Exit Code 137?

Answer:

```text
137 = 128 + 9

9 = SIGKILL
```

---

### Q12. What Happens When a Liveness Probe Fails?

Answer:

```text
Container restarted.
```

---

### Q13. What Happens When a Readiness Probe Fails?

Answer:

```text
Pod removed from
Service endpoints.

No restart.
```

---

### Q14. What Happens When a Startup Probe Fails?

Answer:

```text
Container restarted.
```

Repeated failures may cause:

```text
CrashLoopBackOff
```

---

### Q15. Why Use a Startup Probe?

Answer:

```text
Allows slow-starting applications
enough time to initialize before
liveness checks begin.
```

---

### Q16. Can Readiness Restart Containers?

Answer:

```text
No.
```

It only controls traffic routing.

---

### Q17. What Is the Noisy Neighbor Problem?

Answer:

```text
One container consumes excessive
resources and negatively impacts
other workloads on the same node.
```

---

### Q18. Why Are Resource Requests Important?

Answer:

```text
Guarantee minimum resources
and help scheduling decisions.
```

---

### Q19. Why Are Resource Limits Important?

Answer:

```text
Prevent containers from consuming
unlimited node resources.
```

---

### Q20. What Probe Types Are Available?

Answer:

```text
httpGet

exec

tcpSocket
```

---

# F. Commands Cheat Sheet

## Apply Manifest

```bash
kubectl apply -f file.yaml
```

---

## Get Pods

```bash
kubectl get pods
```

---

## Watch Pods

```bash
kubectl get pods -w
```

---

## Describe Pod

```bash
kubectl describe pod <pod-name>
```

---

## View Logs

```bash
kubectl logs <pod-name>
```

---

## Execute Commands

```bash
kubectl exec <pod-name> -- command
```

Example:

```bash
kubectl exec readiness-demo -- rm /usr/share/nginx/html/index.html
```

---

## Expose Pod

```bash
kubectl expose pod readiness-demo \
--port=80 \
--name=readiness-svc
```

---

## View Endpoints

```bash
kubectl get endpoints
```

---

## Delete Pod

```bash
kubectl delete pod <pod-name>
```

---

## Delete Service

```bash
kubectl delete svc <service-name>
```

---

# G. Production Nuggets

## Scheduler Uses Requests Only

```text
Requests affect scheduling.

Limits affect runtime enforcement.
```

Scheduler ignores limits.

---

Example:

```yaml
requests:
  cpu: 100m

limits:
  cpu: 4
```

Scheduler reserves:

```text
100m CPU
```

not:

```text
4 CPUs
```

---

## CPU vs Memory

CPU:

```text
Compressible Resource
```

When exceeded:

```text
CPU Throttled
```

Container survives.

---

Memory:

```text
Incompressible Resource
```

When exceeded:

```text
OOMKilled
```

Container terminated.

---

## Requests Without Limits

```yaml
resources:
  requests:
    cpu: 100m
    memory: 128Mi
```

Result:

```text
Guaranteed minimum resources.
```

However:

```text
Container may consume
additional resources.
```

Risk:

```text
Noisy Neighbor Problem
```

---

## Limits Without Requests

```yaml
resources:
  limits:
    cpu: 500m
    memory: 512Mi
```

If requests omitted:

```text
Kubernetes may use limits
as requests.
```

---

## QoS Eviction Order

Under node memory pressure:

```text
BestEffort
→ Evicted First

Burstable
→ Evicted Next

Guaranteed
→ Evicted Last
```
```text
Resource Requests
→ Guarantee minimum resources

Resource Limits
→ Restrict maximum resource usage
```

---

## Probe Failure Actions

| Probe     | Purpose                | Failure Action      |
| --------- | ---------------------- | ------------------- |
| Liveness  | Is app alive?          | Restart Container   |
| Readiness | Can app serve traffic? | Remove From Service |
| Startup   | Has app started?       | Restart Container   |

---

# H. 1-Minute Revision Sheet

```text
Requests = Scheduling

Limits = Runtime Enforcement

Scheduler Uses Requests Only

CPU:
1000m = 1 CPU

Memory:
1024Mi = 1Gi

CPU = Compressible

Memory = Incompressible

CPU Limit Exceeded
→ Throttled

Memory Limit Exceeded
→ OOMKilled

OOM Exit Code
→ 137

137 = 128 + 9

QoS

Guaranteed
= Requests == Limits

Burstable
= Requests < Limits

BestEffort
= No Requests/Limits

QoS Eviction Order

BestEffort
→ Burstable
→ Guaranteed

Liveness Failure
→ Restart

Readiness Failure
→ Remove From Service

Startup Failure
→ Restart

Probe Types

httpGet
exec
tcpSocket

Lab Results

QoS = Burstable

OOM Exit Code = 137

Pending =
Insufficient CPU
Insufficient Memory

Readiness =
No Restart

failureThreshold=2
→ CrashLoopBackOff
```

***


# Differences & Comparisons – Day 57

---

# 1. Requests vs Limits

| Feature                | Requests                           | Limits                           |
| ---------------------- | ---------------------------------- | -------------------------------- |
| Purpose                | Minimum resources guaranteed       | Maximum resources allowed        |
| Used By                | Scheduler                          | Kubelet                          |
| When Used              | Before Pod placement               | While container is running       |
| CPU Example            | 100m                               | 250m                             |
| Memory Example         | 128Mi                              | 256Mi                            |
| Guarantees             | Scheduler reserves capacity        | Runtime enforcement              |
| If Usage Exceeds Value | Allowed if resources are available | CPU throttled / Memory OOMKilled |
| Keyword                | Scheduling                         | Enforcement                      |
| If Missing             | No guaranteed reservation          | No upper boundary                |
| Day 57 Example         | CPU=100m Memory=128Mi              | CPU=250m Memory=256Mi            |

### Day 57 Lab

```yaml
requests:
  cpu: 100m
  memory: 128Mi

limits:
  cpu: 250m
  memory: 256Mi
```

Result:

```text
QoS Class: Burstable
```

### Easy Trick

```text
Requests = Reservation

Limits = Restriction
```

---

# 2. Requests-Only vs Limits-Only

## Requests Only

```yaml
resources:
  requests:
    cpu: 100m
    memory: 128Mi
```

Result:

```text
Minimum resources guaranteed.
```

Risk:

```text
Container may consume additional resources
if available on the node.
```

Potential Issue:

```text
Noisy Neighbor Problem
```

---

## Limits Only

```yaml
resources:
  limits:
    cpu: 500m
    memory: 512Mi
```

Result:

```text
Maximum usage restricted.
```

Important:

```text
If requests are omitted,
Kubernetes may treat limits
as requests.
```

---

# 3. Scheduler Uses Requests Only

 Question:

> Does Kubernetes Scheduler use Requests or Limits?

Answer:

```text
Requests Only
```

Example:

```yaml
requests:
  cpu: 100m

limits:
  cpu: 4
```

Scheduler reserves:

```text
100m CPU
```

NOT:

```text
4 CPUs
```

---

# 4. CPU vs Memory Limits

| Feature             | CPU                  | Memory                           |
| ------------------- | -------------------- | -------------------------------- |
| Resource Type       | Compressible         | Incompressible                   |
| Exceed Limit        | Throttled            | OOMKilled                        |
| Container Survives? | Yes                  | No                               |
| Enforcement         | CPU Quota / cgroup   | OOM Killer                       |
| Common Result       | Slow Application     | OOMKilled (often Exit Code 137)  |
| Keyword             | CPU Throttling       | OOMKilled                        |
| Kubernetes Action   | Slows container down | Terminates container             |
| Day 57 Example      | Not tested           | Stress container allocating 200M |

### CPU Example

```yaml
limits:
  cpu: 250m
```

Application attempts:

```text
500m CPU
```

Result:

```text
Container survives.

CPU is throttled.

Application becomes slower.
```

---

### Memory Example

```yaml
limits:
  memory: 100Mi
```

Application attempts:

```text
200Mi
```

Result:

```text
OOMKilled
```

###  Questions

What happens when CPU limit is exceeded?

```text
Container is throttled.
```

What happens when Memory limit is exceeded?

```text
Container is OOMKilled.
```

---

# 5. QoS Classes Comparison

| QoS Class  | Requests | Limits  | Condition             | Eviction Priority | Example                |
| ---------- | -------- | ------- | --------------------- | ----------------- | ---------------------- |
| Guaranteed | Equal    | Equal   | Requests = Limits     | Last              | 100m / 100m            |
| Burstable  | Present  | Present | Requests < Limits     | Middle            | 100m / 250m            |
| BestEffort | None     | None    | No requests or limits | First             | No resources specified |

---

### Guaranteed

```yaml
requests:
  cpu: 200m
  memory: 256Mi

limits:
  cpu: 200m
  memory: 256Mi
```

Result:

```text
QoS = Guaranteed
```

---

### Burstable (Day 57)

```yaml
requests:
  cpu: 100m
  memory: 128Mi

limits:
  cpu: 250m
  memory: 256Mi
```

Result:

```text
QoS = Burstable
```

---

### BestEffort

```yaml
containers:
- image: nginx
```

Result:

```text
QoS = BestEffort
```

---

# 6. Node Memory Pressure Eviction Order

When a node experiences memory pressure:

| Eviction Order | QoS Class  |
| -------------- | ---------- |
| 1st Evicted    | BestEffort |
| 2nd Evicted    | Burstable  |
| Last Evicted   | Guaranteed |

 Question:

> Which Pods are evicted first?

Answer:

```text
BestEffort Pods
```

Important:

```text
Guaranteed Pods receive the highest protection.

BestEffort Pods are sacrificed first.
```

---

# 7. OOMKilled vs CrashLoopBackOff

| Feature    | OOMKilled                   | CrashLoopBackOff             |
| ---------- | --------------------------- | ---------------------------- |
| Meaning    | Container exceeded memory   | Container repeatedly failing |
| Root Cause | Memory exhaustion           | Any repeated failure         |
| Exit Code  | Usually 137                 | Varies                       |
| Restarted? | Yes                         | Yes                          |
| Visible In | Last State                  | Pod Status                   |
| Example    | Stress Pod with 100Mi limit | Probe failures, bad command  |

### Day 57 Lab

Observed:

```text
Exit Code: 137
```

Observed:

```text
Reason: Error
```

Meaning:

```text
Container exceeded memory limit.

Kernel terminated the process.
```

---

# 8. Pending Pod vs Running Pod

| Feature              | Running Pod | Pending Pod |
| -------------------- | ----------- | ----------- |
| Scheduled On Node?   | Yes         | No          |
| Node Assigned?       | Yes         | No          |
| Container Started?   | Yes         | No          |
| Resources Available? | Yes         | No          |

### Day 57 Pending Example

Request:

```yaml
cpu: 100
memory: 128Gi
```

Result:

```text
STATUS = Pending
```

Scheduler Event:

```text
Insufficient cpu

Insufficient memory
```

Actual Event:

```text
0/1 nodes are available:
1 Insufficient cpu,
1 Insufficient memory.
```

Important:

```text
Pending does NOT always mean insufficient resources.

Other causes include:

- PVC binding issues
- Taints/Tolerations
- Node Selectors
- Affinity Rules
```

---

# 9. Liveness vs Readiness vs Startup Probe

| Probe     | Question Asked         | Failure Action      |
| --------- | ---------------------- | ------------------- |
| Liveness  | Is app alive?          | Restart container   |
| Readiness | Can app serve traffic? | Remove from Service |
| Startup   | Has app started yet?   | Restart container   |

---

# 10. Probe Failure Behavior

| Probe     | Restarts Container? | Removes Endpoints? | Used During Startup? |
| --------- | ------------------- | ------------------ | -------------------- |
| Liveness  | Yes                 | No                 | No                   |
| Readiness | No                  | Yes                | No                   |
| Startup   | Yes                 | No                 | Yes                  |

Important:

```text
Once Startup Probe succeeds,
it stops running permanently.
```

---

# 11. Day 57 Probe Results

| Task                     | Result                         |
| ------------------------ | ------------------------------ |
| Liveness Probe           | Container restarted repeatedly |
| Restart Count Seen       | 13                             |
| Readiness Probe          | Pod became READY 0/1           |
| Service Endpoint         | Removed                        |
| Readiness Restart Count  | 0                              |
| Startup Probe            | Succeeded after 20 seconds     |
| Startup Failure Scenario | CrashLoopBackOff               |

---

# 12. Liveness vs Readiness

| Feature                   | Liveness          | Readiness              |
| ------------------------- | ----------------- | ---------------------- |
| Checks                    | Is app alive?     | Can app serve traffic? |
| Failure Action            | Restart container | Remove endpoint        |
| Traffic Stops?            | Eventually        | Immediately            |
| Container Restarted?      | Yes               | No                     |
| Service Endpoint Removed? | No                | Yes                    |
| Day 57 Result             | Restart Count 13  | Restart Count 0        |

---

# 13. Service Endpoint Behavior

| Pod State     | Endpoint Present? | Endpoint      |
| ------------- | ----------------- | ------------- |
| Pod Ready     | Yes               | 10.244.x.x:80 |
| Pod Not Ready | No                | <none>        |

### Day 57 Lab

Before readiness failure:

```text
10.244.x.x:80
```

After readiness failure:

```text
ENDPOINTS <none>
```

Meaning:

```text
Service stopped routing traffic
to the Pod.
```

---

# 14. Exit Codes to Remember

| Exit Code | Meaning                        |
| --------- | ------------------------------ |
| 0         | Success                        |
| 1         | Generic Failure                |
| 125       | Docker Error                   |
| 126       | Cannot Execute                 |
| 127       | Command Not Found              |
| 137       | SIGKILL / OOMKilled            |
| 143       | SIGTERM                        |
| 128+n     | Process terminated by signal n |

Most Important:

```text
137 = 128 + 9

SIGKILL = 9
```

---

# 15. Mistakes Encountered During Lab

| Issue                        | Cause                      | Fix                        |
| ---------------------------- | -------------------------- | -------------------------- |
| Service could not be exposed | Pod had no labels          | Added app label            |
| Readiness probe returned 403 | Wrong probe path           | Used /index.html           |
| Endpoint empty initially     | Pod not Ready              | Wait for readiness success |
| OOM Demo showed Error        | Generic Kubernetes reason  | Check Exit Code 137        |
| Pending Pod                  | Resource request too large | Reduce requests            |

### First Readiness Mistake

Wrong:

```yaml
metadata:
  name: readiness-demo
```

Correct:

```yaml
metadata:
  name: readiness-demo
  labels:
    app: readiness-demo
```

Lesson:

```text
Services select Pods using labels.

No labels = No Service selector.
```

---

# 16. Day 57 Verification Answers

| Task   | Verification Question       | Answer                                |
| ------ | --------------------------- | ------------------------------------- |
| Task 1 | QoS Class?                  | Burstable                             |
| Task 2 | OOMKilled Exit Code?        | 137                                   |
| Task 3 | Scheduler Message?          | Insufficient cpu, Insufficient memory |
| Task 4 | Container Restarted?        | Yes                                   |
| Task 4 | Restart Count Seen?         | 13                                    |
| Task 5 | Container Restarted?        | No                                    |
| Task 5 | Endpoint Removed?           | Yes                                   |
| Task 6 | What if failureThreshold=2? | CrashLoopBackOff                      |

```

***

# Liveness vs Readiness vs Startup Probes

This is one of the most important Kubernetes interview topics.

| Feature              | Liveness Probe                        | Readiness Probe                         | Startup Probe                               |
| -------------------- | ------------------------------------- | --------------------------------------- | ------------------------------------------- |
| Purpose              | Checks if application is alive        | Checks if application can serve traffic | Checks if application has finished starting |
| Question Answered    | "Is the app still running correctly?" | "Can the app receive requests?"         | "Has the app started yet?"                  |
| Failure Action       | Restart container                     | Remove Pod from Service endpoints       | Kill and restart container                  |
| Container Restarted? | ✅ Yes                                 | ❌ No                                    | ✅ Yes                                       |
| Traffic Sent to Pod? | Usually No (container restarting)     | ❌ No                                    | ❌ No until startup succeeds                 |
| Used For             | Detecting stuck/hung applications     | Load balancing and traffic control      | Slow-starting applications                  |
| Runs During Startup? | ❌ Disabled by startup probe           | ❌ Disabled by startup probe             | ✅ Yes                                       |
| Typical Types        | exec, httpGet, tcpSocket              | exec, httpGet, tcpSocket                | exec, httpGet, tcpSocket                    |
| Day 57 Example       | `cat /tmp/healthy`                    | HTTP GET `/index.html`                  | `cat /tmp/started`                          |

---

# 1. Liveness Probe

### Purpose

Checks whether the application is still healthy after it has started.

If the application becomes stuck, deadlocked, or unhealthy:

```text
Liveness Probe Fails
       ↓
Kubernetes Restarts Container
```

### Day 57 Example

Container created:

```bash
touch /tmp/healthy
```

After 30 seconds:

```bash
rm /tmp/healthy
```

Probe:

```yaml
livenessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  periodSeconds: 5
  failureThreshold: 3
```

### What Happened

File removed:

```text
/tmp/healthy missing
```

Probe failed:

```text
cat: can't open '/tmp/healthy'
```

Kubernetes:

```text
Container busybox failed liveness probe,
will be restarted
```

Observed:

```text
Restart Count: 13
```

### Interview Answer

**What happens when a liveness probe fails?**

```text
Kubernetes restarts the container.
```

---

# 2. Readiness Probe

### Purpose

Checks whether the application is ready to receive traffic.

The container may be running but not ready.

Example:

```text
Application starting
Database unavailable
Cache warming
Dependency unavailable
```

In such cases:

```text
Don't restart container.
Just stop sending traffic.
```

### Day 57 Example

Probe:

```yaml
readinessProbe:
  httpGet:
    path: /index.html
    port: 80
```

Initial State:

```text
READY 1/1
```

Service Endpoint:

```text
10.244.0.15:80
```

You deleted:

```bash
kubectl exec readiness-demo -- rm /usr/share/nginx/html/index.html
```

Probe Result:

```text
HTTP 404
```

Pod Status:

```text
READY 0/1
```

Endpoints:

```text
ENDPOINTS <none>
```

Restart Count:

```text
0
```

### What Happened

```text
Readiness failed
↓
Pod removed from Service
↓
No traffic sent
↓
Container continues running
```

### Interview Answer

**What happens when a readiness probe fails?**

```text
Pod is removed from Service endpoints.
Container is NOT restarted.
```

---

# 3. Startup Probe

### Purpose

Used for slow-starting applications.

Examples:

```text
Java applications
Spring Boot
Large ML models
Database initialization
```

Without startup probe:

```text
Application still starting
↓
Liveness probe fails
↓
Container restarted
↓
Application never starts
```

Startup probe prevents this.

### Day 57 Example

Container:

```bash
sleep 20
touch /tmp/started
sleep 600
```

Probe:

```yaml
startupProbe:
  exec:
    command:
    - cat
    - /tmp/started
  periodSeconds: 5
  failureThreshold: 12
```

Budget:

```text
12 × 5 = 60 seconds
```

Application Needs:

```text
20 seconds
```

Result:

```text
Startup succeeds
```

Then:

```text
Startup Probe Success
↓
Liveness Probe Enabled
↓
Readiness Probe Enabled
```

---

## What if failureThreshold = 2 ?

Configuration:

```yaml
failureThreshold: 2
periodSeconds: 5
```

Budget:

```text
2 × 5 = 10 seconds
```

Application Needs:

```text
20 seconds
```

Result:

```text
Startup probe fails
↓
Container killed
↓
Restarted
↓
Fails again
↓
CrashLoopBackOff
```

### Interview Answer

**What happens if startup probe threshold is too low?**

```text
Container may be killed before the application finishes starting,
causing CrashLoopBackOff.
```

---

# Visual Flow

### Liveness

```text
Application Running
       ↓
Liveness Fails
       ↓
Restart Container
```

---

### Readiness

```text
Application Running
       ↓
Readiness Fails
       ↓
Remove From Service Endpoints
       ↓
No Traffic
```

---

### Startup

```text
Application Starting
       ↓
Startup Probe Running
       ↓
Success
       ↓
Enable Liveness + Readiness
```

---

# One-Line Memory Trick

```text
Liveness  = Restart Me
Readiness = Route Traffic To Me
Startup   = Give Me Time To Start
```

---

# Interview Quick Answer

| Probe     | One-Line Meaning            |
| --------- | --------------------------- |
| Liveness  | "Am I alive?"               |
| Readiness | "Can I take traffic?"       |
| Startup   | "Have I finished starting?" |

### Failure Results

| Probe Failure | Result                     |
| ------------- | -------------------------- |
| Liveness      | Restart container          |
| Readiness     | Remove from endpoints      |
| Startup       | Kill and restart container |

---



# Liveness vs Readiness vs Startup Probes

This is one of the most commonly asked Kubernetes interview topics.

---

## Why Kubernetes Needs Probes

A container being **Running** does not necessarily mean the application is healthy.

Examples:

* Application process is stuck
* Application cannot serve requests
* Application is still starting
* Application is deadlocked
* Application lost database connection

Without probes:

```text
Pod Status = Running
Application = Broken
```

Kubernetes would never know.

Probes allow Kubernetes to continuously check application health.

---

# 1. Liveness Probe

## Purpose

Answers:

```text
Is the application alive?
```

If answer is:

```text
No
```

Kubernetes:

```text
Restarts the container
```

---

## Your Lab

Container created:

```text
/tmp/healthy
```

After:

```text
30 seconds
```

File removed:

```text
rm /tmp/healthy
```

Probe:

```yaml
livenessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  periodSeconds: 5
  failureThreshold: 3
```

---

## What Happened

Probe checks every:

```text
5 seconds
```

File removed after:

```text
30 seconds
```

Failures:

```text
1st failure
2nd failure
3rd failure
```

Kubernetes:

```text
Container busybox failed liveness probe,
will be restarted
```

Observed:

```text
Restart Count: 13
```

---

## Liveness Timeline

```text
Container starts
      ↓
Healthy
      ↓
Probe succeeds
      ↓
File deleted
      ↓
Probe fails
      ↓
3 consecutive failures
      ↓
Container killed
      ↓
Container restarted
```

---

## Interview Question

### What happens when liveness probe fails?

Answer:

```text
Container is restarted.
```

---

# 2. Readiness Probe

## Purpose

Answers:

```text
Can this application receive traffic?
```

Not:

```text
Is it alive?
```

---

## Important

Readiness failure:

```text
DOES NOT restart container
```

It only removes Pod from:

```text
Service endpoints
```

---

## Your Lab

Before failure:

```bash
kubectl get endpoints readiness-svc
```

Output:

```text
10.244.0.15:80
```

Pod receiving traffic.

---

You removed:

```bash
kubectl exec readiness-demo -- rm /usr/share/nginx/html/index.html
```

Probe:

```yaml
readinessProbe:
  httpGet:
    path: /index.html
    port: 80
```

---

## Result

Pod:

```text
READY 0/1
```

Service:

```text
ENDPOINTS <none>
```

Restart count:

```text
0
```

Container:

```text
Still running
```

---

## Readiness Timeline

```text
Container starts
      ↓
Probe succeeds
      ↓
Added to Service endpoints
      ↓
Receives traffic
      ↓
Probe fails
      ↓
Removed from endpoints
      ↓
No traffic
      ↓
Container keeps running
```

---

## Interview Question

### What happens when readiness probe fails?

Answer:

```text
Pod is removed from Service endpoints.
Container is NOT restarted.
```

---

# 3. Startup Probe

## Purpose

Answers:

```text
Has the application finished starting?
```

Useful for:

```text
Spring Boot
Java apps
Large enterprise apps
ML workloads
Database initialization
```

Applications that need:

```text
30 seconds
60 seconds
2 minutes
```

to start.

---

## Problem Without Startup Probe

Suppose:

```text
Application startup = 45 seconds
```

Liveness probe:

```text
Checks after 10 seconds
```

Application not ready yet.

Probe fails.

Kubernetes thinks:

```text
Application is broken
```

Kills container.

Container never finishes startup.

---

## Startup Probe Solution

While startup probe runs:

```text
Liveness Probe Disabled
Readiness Probe Disabled
```

Only startup probe matters.

---

## Your Lab

Application startup:

```yaml
sleep 20
touch /tmp/started
```

Startup probe:

```yaml
startupProbe:
  exec:
    command:
    - cat
    - /tmp/started
  periodSeconds: 5
  failureThreshold: 12
```

---

## Startup Budget Calculation

Formula:

```text
periodSeconds × failureThreshold
```

Your lab:

```text
5 × 12
```

Result:

```text
60 seconds
```

Kubernetes allows:

```text
60 seconds
```

for startup.

Application needs:

```text
20 seconds
```

Therefore:

```text
Startup succeeds
```

---

## What If failureThreshold = 2 ?

Budget:

```text
5 × 2
```

Result:

```text
10 seconds
```

Application needs:

```text
20 seconds
```

Startup probe fails before app starts.

Result:

```text
Container killed
Restarted
Killed again
Restarted again
```

Eventually:

```text
CrashLoopBackOff
```

---

## Interview Question

### What happens if startup probe keeps failing?

Answer:

```text
Container is killed and restarted.
```

---

# Probe Comparison Table

| Feature                | Liveness              | Readiness                     | Startup                    |
| ---------------------- | --------------------- | ----------------------------- | -------------------------- |
| Purpose                | Is app alive?         | Can app receive traffic?      | Has app finished startup?  |
| Failure Action         | Restart container     | Remove from Service endpoints | Kill and restart container |
| Restarts Container?    | Yes                   | No                            | Yes                        |
| Affects Traffic?       | Indirectly            | Yes                           | During startup             |
| Used For               | Deadlocks, stuck apps | Traffic routing               | Slow startups              |
| Runs Continuously?     | Yes                   | Yes                           | Only during startup        |
| Disables Other Probes? | No                    | No                            | Yes                        |
| Lab Example            | `/tmp/healthy`        | `/index.html`                 | `/tmp/started`             |

---

# Probe Types

Kubernetes supports 3 probe mechanisms.

---

## 1. exec

Runs a command inside container.

Example:

```yaml
livenessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
```

Used in:

```text
Your liveness lab
Your startup lab
```

---

## 2. httpGet

Makes HTTP request.

Example:

```yaml
readinessProbe:
  httpGet:
    path: /
    port: 80
```

Used in:

```text
Your readiness lab
```

---

## 3. tcpSocket

Checks whether TCP port is open.

Example:

```yaml
livenessProbe:
  tcpSocket:
    port: 3306
```

Common for:

```text
MySQL
Redis
PostgreSQL
```

---

# Probe Timing Parameters

---

## initialDelaySeconds

Wait before first probe.

Example:

```yaml
initialDelaySeconds: 20
```

Meaning:

```text
Wait 20 seconds
Then start probing
```

---

## periodSeconds

How often probe runs.

Example:

```yaml
periodSeconds: 5
```

Meaning:

```text
Every 5 seconds
```

---

## timeoutSeconds

Maximum wait time for response.

Example:

```yaml
timeoutSeconds: 2
```

Meaning:

```text
Fail if no response within 2 seconds
```

---

## successThreshold

How many successes required.

Mostly used with readiness probes.

Example:

```yaml
successThreshold: 2
```

Meaning:

```text
Need 2 successful checks
```

before becoming Ready.

---

## failureThreshold

Number of failures before action.

Example:

```yaml
failureThreshold: 3
```

Meaning:

```text
3 consecutive failures
```

before Kubernetes reacts.

---

# Production Best Practices

### Liveness Probe

Use for:

```text
Deadlocks
Hung applications
Frozen processes
```

Do NOT make it too aggressive.

---

### Readiness Probe

Use for:

```text
Database connectivity
Cache connectivity
Application warm-up
```

Most important probe in production.

---

### Startup Probe

Use when application startup is slow.

Examples:

```text
Spring Boot
Kafka Connect
Elasticsearch
Large Java applications
```

---

# Quick Memory Trick

### Liveness

```text
Alive?
```

Fail:

```text
Restart
```

---

### Readiness

```text
Ready?
```

Fail:

```text
Remove from traffic
```

---

### Startup

```text
Started?
```

Fail:

```text
Kill and restart
```

---

# One-Line Interview Answer

```text
Liveness checks whether the application is alive and restarts it on failure. Readiness checks whether the application can serve traffic and removes it from Service endpoints on failure without restarting it. Startup checks whether the application has completed startup; until it succeeds, liveness and readiness probes remain disabled.
```

***



### Scheduler Doesn't Care About Limits

Many beginners think:

```text
Scheduler checks Limits
```

Reality:

```text
Scheduler checks Requests
```

Example:

```yaml
requests:
  cpu: 100m

limits:
  cpu: 4
```

Scheduler reserves:

```text
100m CPU
```

Not:

```text
4 CPUs
```

💡 I spent a long time thinking Limits affected scheduling.

---



### CPU and Memory Are Not Equal

Most people assume:

```text
Resource limit exceeded
=
Container dies
```

Reality:

```text
CPU Over Limit
→ Throttled

Memory Over Limit
→ OOMKilled
```

Easy Memory Trick:

```text
CPU = Compressible

Memory = Incompressible
```

---



### Readiness Probe Doesn't Check Health

This surprises many people.

People think:

```text
Readiness
=
Application Health
```

Actually:

```text
Readiness
=
Should traffic be sent here?
```

When Readiness fails:

```text
Pod keeps running

No restart

Traffic stops
```

---



### Startup Probe Solves Restart Loops

Without Startup Probe:

```text
Application needs 30s

Liveness starts at 5s

Container gets killed
```

Result:

```text
Restart
Kill
Restart
Kill
CrashLoopBackOff
```

Startup Probe gives the application time to breathe.

---



### OOMKilled Is Usually a Configuration Problem

Many people debug application code first.

Often the real issue is:

```text
Memory Limit Too Small
```

Example:

```yaml
limits:
  memory: 100Mi
```

Application uses:

```text
200Mi
```

Result:

```text
OOMKilled
Exit Code 137
```

Not necessarily an application bug.

---



### BestEffort Pods Are the First Sacrifice

Node runs out of memory.

Kubernetes chooses victims.

Eviction order:

```text
BestEffort
↓

Burstable
↓

Guaranteed
```

Meaning:

```text
No Requests + No Limits
=
Highest Risk
```

---


### Readiness Can Save Your Users

A Pod can be:

```text
Running
```

but still be:

```text
Not Ready
```

That is completely valid.

Kubernetes can:

```text
Keep Pod Running

Stop Sending Traffic
```

until the application is actually ready.

This is one of the smartest design decisions in Kubernetes.

---



### Kubernetes Doesn't Ask "Is the Pod Running?"

It asks three separate questions:

```text
Liveness
↓
Is it alive?

Readiness
↓
Can it receive traffic?

Startup
↓
Has it finished starting?
```

That small distinction explains almost every probe-related interview question.

---





