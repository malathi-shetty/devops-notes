

# Day 57 – Kubernetes Resource Requests, Limits, and Probes

---

You're right. What I gave earlier was more of a **study guide**.

For DevOps notes that you'll revisit 6 months later, the notes should read like:

* What we learned
* Why it exists
* What happened in our lab
* Commands we ran
* Output we saw
* Common mistakes we hit
* Troubleshooting
* Interview questions
* Real-world usage

In other words:

> "If future me forgets Day 57, can I learn the whole topic again from these notes?"

The answer for the previous version is: **not fully**.

---

# Day 57 – Resource Requests, Limits & Probes (Detailed Master Notes)

---

# 1. Why Kubernetes Needs Resources

When we create a Pod, Kubernetes does not automatically know:

* How much CPU the application needs
* How much memory the application needs
* Whether the application is healthy
* Whether the application is ready to serve traffic

Without this information:

* Scheduler cannot make smart placement decisions
* Node resources may get exhausted
* Kubernetes cannot automatically recover failed applications

To solve this, Kubernetes provides:

### Resource Management

* Requests
* Limits
* QoS Classes

### Health Checking

* Liveness Probe
* Readiness Probe
* Startup Probe

---

# 2. Resource Requests and Limits

---

## What is a Request?

A request is the minimum amount of resources guaranteed for a container.

Example:

```yaml
requests:
  cpu: 100m
  memory: 128Mi
```

Meaning:

```text
CPU    = 0.1 core
Memory = 128 MiB
```

The scheduler uses requests when deciding where a Pod can run.

---

### Think of Requests as

```text
"I need at least this much."
```

---

## What is a Limit?

A limit is the maximum amount of resources a container is allowed to consume.

Example:

```yaml
limits:
  cpu: 250m
  memory: 256Mi
```

Meaning:

```text
CPU can use up to 0.25 core
Memory can use up to 256 MiB
```

The kubelet enforces limits after scheduling.

---

### Think of Limits as

```text
"You cannot exceed this."
```

---

# Requests vs Limits

| Feature           | Request | Limit |
| ----------------- | ------- | ----- |
| Used by Scheduler | Yes     | No    |
| Used by Kubelet   | No      | Yes   |
| Minimum Guarantee | Yes     | No    |
| Maximum Allowed   | No      | Yes   |

---

# CPU Units

CPU is measured in cores.

```text
1 CPU = 1 core
1000m = 1 CPU
500m = 0.5 CPU
100m = 0.1 CPU
```

Examples:

```yaml
cpu: 100m
```

means

```text
0.1 CPU
```

---

# Memory Units

Memory is measured in binary units.

```text
Mi = Mebibyte
Gi = Gibibyte
```

Examples:

```yaml
128Mi
256Mi
1Gi
```

---

# Task 1 Lab

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

Applied:

```bash
kubectl apply -f resource-demo.yaml
```

Verified:

```bash
kubectl describe pod resource-demo
```

Observed:

```text
Requests:
  cpu: 100m
  memory: 128Mi

Limits:
  cpu: 250m
  memory: 256Mi
```

---

# QoS Classes

Kubernetes assigns a Quality of Service class to every Pod.

---

## Guaranteed

Requests equal limits.

Example:

```yaml
requests:
  cpu: 100m
  memory: 128Mi

limits:
  cpu: 100m
  memory: 128Mi
```

Result:

```text
QoS = Guaranteed
```

Highest priority.

---

## Burstable

Requests less than limits.

Example:

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

This is what we observed.

---

## BestEffort

No requests.

No limits.

Example:

```yaml
containers:
- image: nginx
```

Result:

```text
QoS = BestEffort
```

Lowest priority.

---

# Lab Verification

Observed:

```text
QoS Class: Burstable
```

Reason:

```text
requests < limits
```

Answer:

```text
Burstable
```

---

# QoS Eviction Order

If the node runs out of memory:

Eviction order:

```text
BestEffort
↓
Burstable
↓
Guaranteed
```

Interview favorite.

---

# 3. CPU Limits vs Memory Limits

This is one of the most important Kubernetes concepts.

---

## CPU is Compressible

When CPU limit is exceeded:

```text
Container is throttled
```

Container continues running.

No crash.

No restart.

---

Example:

```text
CPU limit = 250m
Container uses 400m
```

Result:

```text
Kernel throttles CPU usage
```

---

## Memory is Incompressible

When memory limit is exceeded:

```text
Container is killed immediately
```

No throttling.

No warning.

Kernel sends SIGKILL.

---

Example:

```text
Limit = 100Mi
Usage = 200Mi
```

Result:

```text
OOMKilled
```

---

# 4. OOMKilled (Out Of Memory)

OOM = Out Of Memory.

Occurs when container exceeds memory limit.

---

# Task 2 Lab

Manifest used:

```yaml
image: polinux/stress
```

Limit:

```yaml
limits:
  memory: 100Mi
```

Command:

```yaml
command: ["stress"]

args:
- "--vm"
- "1"
- "--vm-bytes"
- "200M"
- "--vm-hang"
- "1"
```

Meaning:

```text
Allocate 200 MB
```

Limit:

```text
100 MiB
```

Result:

```text
Memory exceeded
```

---

Observed:

```text
CrashLoopBackOff
```

Observed:

```text
Exit Code: 137
```

Observed:

```text
Restart Count: 3
```

Observed:

```text
Reason: Error
Exit Code: 137
```

---

# Why 137?

Linux:

```text
SIGKILL = 9
```

Exit code formula:

```text
128 + Signal Number
```

Therefore:

```text
128 + 9 = 137
```

Meaning:

```text
OOMKilled
```

---

# Interview Question

Q:

What exit code indicates OOMKilled?

A:

```text
137
```

---

# 5. Scheduler & Pending Pods

Scheduler checks:

```text
Can this node satisfy requests?
```

If not:

```text
Pod stays Pending
```

Forever.

---

# Task 3 Lab

Manifest:

```yaml
requests:
  cpu: 100
  memory: 128Gi
```

Ridiculously large.

---

Result:

```text
STATUS = Pending
```

Observed:

```bash
kubectl describe pod huge-request
```

Event:

```text
0/1 nodes are available:
1 Insufficient cpu,
1 Insufficient memory.
no new claims to deallocate,
preemption: 0/1 nodes are available:
1 Preemption is not helpful for scheduling.
```

---

Verification Answer

```text
Insufficient cpu
Insufficient memory
```

---

# 6. Kubernetes Probes

Probes allow Kubernetes to determine:

```text
Is app alive?
Is app ready?
Has app started?
```

Three probes exist:

```text
Liveness
Readiness
Startup
```

---

# Probe Types

A probe can use:

```text
httpGet
exec
tcpSocket
```

---

# Probe Timing Parameters

Common parameters:

```yaml
initialDelaySeconds
periodSeconds
failureThreshold
successThreshold
timeoutSeconds
```

---

# periodSeconds

How often probe runs.

Example:

```yaml
periodSeconds: 5
```

Probe runs every 5 seconds.

---

# failureThreshold

Number of failures before action.

Example:

```yaml
failureThreshold: 3
```

Three failures required.

---

# 7. Liveness Probe

Purpose:

```text
Detect stuck containers
```

Question:

```text
Is app alive?
```

If probe fails:

```text
Restart container
```

---

# Task 4 Lab

Container:

```text
Creates /tmp/healthy
```

After 30 seconds:

```text
Deletes /tmp/healthy
```

Probe:

```yaml
exec:
  command:
  - cat
  - /tmp/healthy
```

---

Observed:

```text
cat: can't open '/tmp/healthy'
```

Observed:

```text
Container busybox failed liveness probe,
will be restarted
```

Observed:

```text
Restart Count: 13
```

Observed:

```text
CrashLoopBackOff
```

---

Verification Answer

```text
Restart Count = 13
```

---

# Liveness Summary

Failure:

```text
Restart container
```

Purpose:

```text
Self-healing
```

---

# 8. Readiness Probe

Purpose:

```text
Can this Pod receive traffic?
```

Failure:

```text
NO RESTART
```

Only remove Pod from Service endpoints.

---

# First Mistake We Hit

Attempted:

```bash
kubectl expose pod readiness-demo
```

Error:

```text
the pod has no labels and cannot be exposed
```

---

Lesson

Services need labels.

Added:

```yaml
labels:
  app: readiness-demo
```

Fixed.

---

# Task 5 Lab

Readiness probe:

```yaml
httpGet:
  path: /index.html
  port: 80
```

---

Before deleting file:

Pod:

```text
READY 1/1
```

Endpoints:

```text
10.244.0.15:80
```

---

Deleted:

```bash
kubectl exec readiness-demo -- rm /usr/share/nginx/html/index.html
```

---

After deletion:

Pod:

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

---

Verification Answer

Was container restarted?

```text
NO
```

---

# Readiness Summary

Failure:

```text
Remove from Service endpoints
```

No restart.

---

# 9. Startup Probe

Purpose:

```text
Give slow applications time to start
```

While startup probe runs:

```text
Liveness disabled
Readiness disabled
```

---

# Task 6 Lab

Container:

```bash
sleep 20
touch /tmp/started
sleep 600
```

Application startup:

```text
20 seconds
```

---

Probe:

```yaml
startupProbe:
  periodSeconds: 5
  failureThreshold: 12
```

---

Budget Calculation

```text
12 × 5 = 60 seconds
```

Application starts in:

```text
20 seconds
```

Success.

---

Observed:

```text
Startup probe failed:
cat: can't open '/tmp/started'
```

Several times initially.

Expected.

Eventually:

```text
Ready = True
Restart Count = 0
```

---

Challenge Question

What if:

```yaml
failureThreshold: 2
```

Budget:

```text
2 × 5 = 10 seconds
```

Application needs:

```text
20 seconds
```

Result:

```text
Startup probe fails
Container killed
Container restarted
Container killed again
CrashLoopBackOff
```

---

Verification Answer

```text
Container enters CrashLoopBackOff
```

---

# Probe Comparison Table

| Probe     | Purpose              | Failure Result        |
| --------- | -------------------- | --------------------- |
| Liveness  | Is app alive?        | Restart container     |
| Readiness | Can receive traffic? | Remove from endpoints |
| Startup   | Has app started?     | Kill container        |

---

# Important Commands Cheat Sheet

```bash
kubectl apply -f file.yaml

kubectl get pods

kubectl get pods -w

kubectl describe pod <pod-name>

kubectl logs <pod-name>

kubectl exec <pod-name> -- ls

kubectl exec <pod-name> -- rm <file>

kubectl expose pod <pod-name> --port=80 --name=<svc>

kubectl get svc

kubectl get endpoints <svc>

kubectl delete pod <pod-name>

kubectl delete svc <svc>
```

---

# Troubleshooting Notes

### Pod Pending

Check:

```bash
kubectl describe pod
```

Look for:

```text
FailedScheduling
Insufficient cpu
Insufficient memory
```

---

### OOMKilled

Check:

```bash
kubectl describe pod
```

Look for:

```text
Exit Code: 137
```

---

### Readiness Not Working

Check:

```bash
kubectl get endpoints svc-name
```

Empty endpoints:

```text
Probe failing
```

---

### Service Not Creating

Error:

```text
Pod has no labels
```

Fix:

```yaml
labels:
  app: my-app
```

---

### Liveness Restart Loop

Check:

```bash
kubectl describe pod
```

Look for:

```text
Liveness probe failed
```

---

# Day 57 Verification Answers

### Task 1

```text
QoS Class = Burstable
```

### Task 2

```text
Exit Code = 137
```

### Task 3

```text
Insufficient cpu
Insufficient memory
```

### Task 4

```text
Restart Count = 13
```

### Task 5

```text
Container restarted?
NO
```

### Task 6

```text
failureThreshold = 2
→ Startup probe fails
→ Container restarted
→ CrashLoopBackOff
```



***

## Why Resource Management Matters

When Kubernetes schedules a Pod, it needs to know:

* How much CPU the application needs
* How much Memory the application needs
* Whether the application is healthy
* Whether the application is ready to receive traffic

Without resource definitions:

* Scheduler makes poor placement decisions
* Pods may consume excessive resources
* Node stability can suffer
* Kubernetes cannot effectively detect unhealthy applications

This is why we use:

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

Kubernetes Scheduler uses requests to decide:

> "Which node has enough free resources for this Pod?"

Requests are used only during scheduling.

---

## CPU Request

```yaml
cpu: 100m
```

Meaning:

```text
100m = 0.1 CPU Core
500m = 0.5 CPU Core
1000m = 1 CPU Core
```

Examples:

| Value | Meaning  |
| ----- | -------- |
| 100m  | 0.1 CPU  |
| 250m  | 0.25 CPU |
| 500m  | 0.5 CPU  |
| 1000m | 1 CPU    |

---

## Memory Request

```yaml
memory: 128Mi
```

Meaning:

```text
Mi = Mebibytes
Gi = Gibibytes
```

Examples:

| Value | Meaning |
| ----- | ------- |
| 128Mi | 128 MB  |
| 256Mi | 256 MB  |
| 1Gi   | 1024 Mi |

---

# 2. Resource Limits

Limits define the maximum resources a container may use.

Example:

```yaml
resources:
  limits:
    cpu: 250m
    memory: 256Mi
```

Kubelet enforces limits at runtime.

---

## Requests vs Limits

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

Interpretation:

Scheduler guarantees:

```text
CPU = 100m
Memory = 128Mi
```

Container may use:

```text
CPU up to 250m
Memory up to 256Mi
```

---

## Simple Analogy

Request:

> "I need at least this much."

Limit:

> "I cannot exceed this much."

Think of a hotel:

Request:

```text
Reserve one room for me
```

Limit:

```text
You cannot occupy more than 3 rooms
```

---

# Task 1 – Resource Requests and Limits

Manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-demo

spec:
  containers:
  - name: nginx
    image: nginx

    resources:
      requests:
        cpu: 100m
        memory: 128Mi

      limits:
        cpu: 250m
        memory: 256Mi
```

Apply:

```bash
kubectl apply -f resource-demo.yaml
```

Verify:

```bash
kubectl describe pod resource-demo
```

Observe:

```text
Requests:
  cpu: 100m
  memory: 128Mi

Limits:
  cpu: 250m
  memory: 256Mi
```

---

# QoS Classes

QoS = Quality of Service

Determines eviction priority under resource pressure.

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

Highest priority.

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

This is what you observed.

---

## BestEffort

No requests or limits.

Example:

```yaml
containers:
- image: nginx
```

QoS:

```text
BestEffort
```

Lowest priority.

---

## Task 1 Verification

Your Pod:

```text
QoS Class: Burstable
```

Correct answer:

```text
Burstable
```

---

# CPU vs Memory Behavior

This is extremely important.

---

## CPU is Compressible

CPU can be throttled.

If limit:

```yaml
cpu: 250m
```

Application tries:

```text
500m CPU
```

Result:

```text
Container NOT killed
CPU throttled
Application slower
```

---

## Memory is Incompressible

Memory cannot be throttled.

If limit:

```yaml
memory: 100Mi
```

Application uses:

```text
200Mi
```

Result:

```text
Container Killed
OOMKilled
```

No mercy.

---

# Task 2 – OOMKilled

Manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: oom-demo

spec:
  containers:
  - name: stress
    image: polinux/stress

    command: ["stress"]

    args:
    - "--vm"
    - "1"
    - "--vm-bytes"
    - "200M"
    - "--vm-hang"
    - "1"

    resources:
      limits:
        memory: 100Mi

      requests:
        memory: 100Mi
```

---

## What Happens?

Container limit:

```text
100Mi
```

Stress tries:

```text
200M
```

Result:

```text
OOMKilled
```

Observed:

```text
Exit Code: 137
```

---

## Why Exit Code 137?

Formula:

```text
137 = 128 + 9
```

Where:

```text
9 = SIGKILL
```

Kernel kills process.

---

## Task 2 Verification

Answer:

```text
Exit Code = 137
```

---

# Task 3 – Pending Pod

Goal:

Request absurd resources.

Manifest:

```yaml
resources:
  requests:
    cpu: 100
    memory: 128Gi
```

---

## What Happens?

Scheduler checks nodes.

Node capacity:

```text
Much smaller than requested
```

Result:

```text
Pod stays Pending
```

Forever.

---

## Verification

Observed Event:

```text
0/1 nodes are available:
1 Insufficient cpu,
1 Insufficient memory.
```

Meaning:

Scheduler cannot find any node capable of running Pod.

---

# Kubernetes Probes

Probes answer:

```text
Is application alive?
Is application ready?
Has application started?
```

Three probe types:

1. Liveness
2. Readiness
3. Startup

---

# 4. Liveness Probe

Purpose:

Detect dead/stuck containers.

If probe fails:

```text
Restart container
```

---

# Task 4 – Liveness Probe

Container behavior:

```bash
touch /tmp/healthy
sleep 30
rm -f /tmp/healthy
sleep 600
```

---

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

## Timeline

Startup:

```text
/tmp/healthy exists
Probe succeeds
```

After 30s:

```text
File deleted
```

Probe:

```text
Failure 1
Failure 2
Failure 3
```

Kubernetes:

```text
Restart container
```

Observed:

```text
Container busybox failed liveness probe, will be restarted
```

You saw:

```text
Restart Count: 13
```

Meaning:

Probe worked perfectly.

---

# Liveness Summary

Failure Action:

```text
Restart Container
```

---

# 5. Readiness Probe

Purpose:

Determine if Pod can receive traffic.

---

Important:

Readiness failure:

```text
NO restart
```

Instead:

```text
Remove Pod from Service endpoints
```

---

# Task 5 – Readiness Probe

Important correction from our lab.

Initially:

```yaml
path: /
```

Result:

```text
HTTP 403
```

This was NOT the intended lab behavior.

We recreated correctly.

---

Correct Probe

```yaml
readinessProbe:
  httpGet:
    path: /index.html
    port: 80

  periodSeconds: 5
  failureThreshold: 3
```

---

Pod label required:

```yaml
labels:
  app: readiness-demo
```

Without labels:

```bash
kubectl expose pod
```

fails.

You experienced exactly that.

---

Service:

```bash
kubectl expose pod readiness-demo \
--port=80 \
--name=readiness-svc
```

---

Verify Endpoint

Before deletion:

```bash
kubectl get endpoints readiness-svc
```

Output:

```text
10.244.x.x:80
```

---

Break readiness:

```bash
kubectl exec readiness-demo -- rm /usr/share/nginx/html/index.html
```

Now probe gets:

```text
HTTP 404
```

---

Result:

```text
READY 0/1
```

Endpoints:

```text
Empty
```

Restart count:

```text
0
```

Perfect.

---

## Task 5 Verification

Question:

```text
Was the container restarted?
```

Answer:

```text
No
```

---

# 6. Startup Probe

Purpose:

Handle slow-starting applications.

---

Without startup probe:

Liveness starts immediately.

Slow application:

```text
Killed too early
```

---

Startup Probe Solution

Disable:

```text
Liveness
Readiness
```

until startup succeeds.

---

# Task 6 – Startup Probe

Container:

```bash
sleep 20
touch /tmp/started
sleep 600
```

Application needs:

```text
20 seconds
```

to start.

---

Startup Probe

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

Budget:

```text
5 × 12
= 60 seconds
```

Container starts in:

```text
20 seconds
```

Success.

---

Then Liveness Begins

```yaml
livenessProbe:
  exec:
    command:
    - cat
    - /tmp/started
```

---

## What If failureThreshold = 2?

Budget:

```text
5 × 2 = 10 seconds
```

Container needs:

```text
20 seconds
```

Result:

```text
Startup probe fails
Container killed
Restart
Killed again
Restart
```

Eventually:

```text
CrashLoopBackOff
```

---

## Startup Summary

Failure Action:

```text
Kill Container
Restart Container
```

---

# B.  Notes (Questions & Answers)

### Q1. Difference between Requests and Limits?

**Answer:**

Requests are used by the scheduler for Pod placement. Limits are enforced by kubelet at runtime.

---

### Q2. What is QoS?

**Answer:**

Quality of Service classification used by Kubernetes to prioritize Pods during resource pressure.

---

### Q3. QoS Classes?

**Answer:**

* Guaranteed
* Burstable
* BestEffort

---

### Q4. CPU limit exceeded?

**Answer:**

CPU is throttled.

Container continues running.

---

### Q5. Memory limit exceeded?

**Answer:**

Container is OOMKilled.

---

### Q6. What is OOMKilled exit code?

**Answer:**

```text
137
```

---

### Q7. Why 137?

**Answer:**

```text
137 = 128 + SIGKILL(9)
```

---

### Q8. What happens when liveness probe fails?

**Answer:**

Container is restarted.

---

### Q9. What happens when readiness probe fails?

**Answer:**

Pod is removed from Service endpoints.

Container is not restarted.

---

### Q10. What happens when startup probe fails?

**Answer:**

Container is killed and restarted.

---

### Q11. Why use startup probe?

**Answer:**

To allow slow-starting applications enough time before liveness checks begin.

---

### Q12. Can readiness restart containers?

**Answer:**

No.

Only removes Pod from traffic.

---

# C. 1-Minute Revision Sheet

```text
Requests = Scheduling

Limits = Runtime Enforcement

CPU:
1000m = 1 CPU

Memory:
1024Mi = 1Gi

CPU Over Limit:
→ Throttled

Memory Over Limit:
→ OOMKilled

Exit Code:
137

QoS:
Guaranteed = Requests == Limits
Burstable = Requests < Limits
BestEffort = No Requests/Limits

Liveness:
Fail → Restart

Readiness:
Fail → Remove from Service

Startup:
Fail → Kill + Restart

Probe Types:
httpGet
exec
tcpSocket

Task Results:
QoS = Burstable
OOM Exit Code = 137
Pending = Insufficient CPU & Memory
Readiness = No Restart
Startup Threshold 2 = CrashLoopBackOff
```

---

# D. Important Commands Cheat Sheet

### Apply Manifest

```bash
kubectl apply -f file.yaml
```

### Get Pods

```bash
kubectl get pods
```

### Watch Pods

```bash
kubectl get pods -w
```

### Describe Pod

```bash
kubectl describe pod <pod-name>
```

### Logs

```bash
kubectl logs <pod-name>
```

### Execute Command

```bash
kubectl exec <pod-name> -- command
```

Example:

```bash
kubectl exec readiness-demo -- rm /usr/share/nginx/html/index.html
```

### Expose Pod

```bash
kubectl expose pod readiness-demo \
--port=80 \
--name=readiness-svc
```

### Endpoints

```bash
kubectl get endpoints
```

### Delete Pod

```bash
kubectl delete pod <pod-name>
```

### Delete Service

```bash
kubectl delete svc <service-name>
```

---

# E. Troubleshooting & Common Mistakes

## Mistake 1

### Pod exposed without labels

Error:

```text
the pod has no labels and cannot be exposed
```

Fix:

```yaml
metadata:
  labels:
    app: readiness-demo
```

---

## Mistake 2

### Wrong readiness path

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

Then deleting file causes:

```text
404
```

which correctly demonstrates readiness failure.

---

## Mistake 3

### Expecting readiness to restart container

Wrong:

```text
Readiness failure restarts container
```

Correct:

```text
Readiness failure removes endpoint only
```

---

## Mistake 4

### Confusing CPU and Memory behavior

Wrong:

```text
CPU limit exceeded → killed
```

Correct:

```text
CPU → throttled
Memory → OOMKilled
```

---

## Mistake 5

### Forgetting to check Events

Always run:

```bash
kubectl describe pod <pod-name>
```

Events section often tells the exact issue:

```text
FailedScheduling
OOMKilled
Probe failures
BackOff
```

---

## Mistake 6

### Startup probe budget too small

Example:

```yaml
periodSeconds: 5
failureThreshold: 2
```

Budget:

```text
10 seconds
```

Application startup:

```text
20 seconds
```

Result:

```text
CrashLoopBackOff
```

---








***

### 1. Resource Requests & Limits

You observed:

```yaml
requests:
  cpu: 100m
  memory: 128Mi

limits:
  cpu: 250m
  memory: 256Mi
```

And:

```bash
QoS Class: Burstable
```

Reason:

```text
requests < limits
```

QoS Summary:

| QoS Class  | Condition          |
| ---------- | ------------------ |
| Guaranteed | requests == limits |
| Burstable  | requests < limits  |
| BestEffort | no requests/limits |

---

### 2. OOMKilled Lab Result

You observed:

```bash
Exit Code: 137
```

and

```bash
Reason: Error
Exit Code: 137
```

Even though `kubectl describe` showed:

```text
Reason: Error
```

Exit code 137 proves the container was killed by the kernel due to memory exhaustion.



```text
OOMKilled = SIGKILL = 9
128 + 9 = 137
```

---

### 3. Pending Pod Lab Result

Actual scheduler event:

```text
0/1 nodes are available:
1 Insufficient cpu,
1 Insufficient memory.
no new claims to deallocate,
preemption: 0/1 nodes are available:
1 Preemption is not helpful for scheduling.
```

This should be included because the challenge specifically asked:

> What event message does the scheduler produce?

Answer:

```text
Insufficient cpu
Insufficient memory
```

---

### 4. Liveness Probe Lab Result

Your pod repeatedly restarted.

You observed:

```bash
Restart Count: 13
```

and:

```text
Liveness probe failed:
cat: can't open '/tmp/healthy'
```

and:

```text
Container busybox failed liveness probe,
will be restarted
```

 Question:

> What happens when a liveness probe fails?

Answer:

```text
Container is restarted.
```

---

### 5. Readiness Probe Lab Result

This is important because you initially hit a problem.

#### First Attempt

Service exposure failed:

```bash
kubectl expose pod readiness-demo
```

Error:

```text
the pod has no labels and cannot be exposed
```

Lesson:

```text
Service selectors require labels.
```

You fixed it by adding:

```yaml
labels:
  app: readiness-demo
```

---

#### Second Attempt (Successful)

Before deleting file:

```bash
READY 1/1
```

Endpoint:

```bash
10.244.0.15:80
```

After:

```bash
kubectl exec readiness-demo -- rm /usr/share/nginx/html/index.html
```

Results:

```bash
READY 0/1
```

Endpoint became:

```bash
ENDPOINTS <none>
```

Restart count:

```bash
0
```

Most Important Verification:

> Was the container restarted?

Answer:

```text
No.
```

Readiness removes pod from Service endpoints only.

---

### 6. Startup Probe Lab Result

Your configuration:

```yaml
periodSeconds: 5
failureThreshold: 12
```

Budget:

```text
12 × 5 = 60 seconds
```

Container startup time:

```text
20 seconds
```

Therefore startup probe succeeded.

---

Challenge Verification:

> What would happen if failureThreshold were 2 instead of 12?

Calculation:

```text
2 × 5 = 10 seconds
```

Container needs:

```text
20 seconds
```

Result:

```text
Startup probe would fail before application starts.
```

Kubernetes would:

```text
Kill container
Restart container
Kill again
Restart again
...
```

Eventually:

```text
CrashLoopBackOff
```

---

## Additional Important Concept (Often Asked)

### CPU vs Memory Limits

CPU:

```text
Compressible Resource
```

When CPU limit exceeded:

```text
Container is throttled.
```

Container survives.

---

Memory:

```text
Incompressible Resource
```

When memory limit exceeded:

```text
Container is killed immediately.
```

Container survives only if restarted.

---

## Additional Important Concept (QoS and Eviction)

If node is under memory pressure:

Eviction order:

```text
BestEffort  → first
Burstable   → second
Guaranteed  → last
```



---

## Additional Important Concept (Probe Differences)

| Probe     | Purpose                    | Failure Result        |
| --------- | -------------------------- | --------------------- |
| Liveness  | Is app alive?              | Restart container     |
| Readiness | Can app receive traffic?   | Remove from endpoints |
| Startup   | Has app finished starting? | Kill container        |

---

## Things You Correctly Verified During Lab

### Task 1

```text
QoS Class = Burstable
```

---

### Task 2

```text
Exit Code = 137
```

---

### Task 3

```text
Insufficient cpu
Insufficient memory
```

---

### Task 4

```text
Restart Count = 13
```

(or whatever restart count was visible at verification time)

---

### Task 5

```text
Container restarted?
NO
```

---

### Task 6

```text
failureThreshold=2
⇒ startup probe fails
⇒ restart loop
⇒ CrashLoopBackOff
```



***

# Real Lab Results 

## Task 1 — Resource Requests & Limits

Manifest:

```yaml
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
requests < limits
```

Verification Answer:

```text
QoS Class = Burstable
```

---

## Task 2 — OOMKilled

Container attempted:

```text
200M memory
```

Limit:

```text
100Mi
```

Observed:

```text
Exit Code: 137
```

Observed from describe output:

```text
State: Terminated
Reason: Error
Exit Code: 137
```

Verification Answer:

```text
OOMKilled container exit code = 137
```

Why?

```text
SIGKILL = 9
128 + 9 = 137
```

---

## Task 3 — Pending Pod

Requested:

```yaml
cpu: 100
memory: 128Gi
```

Observed Scheduler Event:

```text
0/1 nodes are available:
1 Insufficient cpu,
1 Insufficient memory.
no new claims to deallocate,
preemption: 0/1 nodes are available:
1 Preemption is not helpful for scheduling.
```

Verification Answer:

```text
Insufficient cpu
Insufficient memory
```

---

## Task 4 — Liveness Probe

Observed:

```text
Liveness probe failed:
cat: can't open '/tmp/healthy'
```

Observed:

```text
Container busybox failed liveness probe,
will be restarted
```

Observed Restart Count:

```text
Restart Count: 13
```

Verification Answer:

```text
Liveness failure restarts the container.
```

---

## Task 5 — Readiness Probe

### First Attempt

Observed Error:

```text
the pod has no labels and cannot be exposed
```

Reason:

```text
Service selectors require labels.
```

Fix:

```yaml
labels:
  app: readiness-demo
```

---

### Successful Attempt

Before deleting file:

```text
READY 1/1
```

Endpoints:

```text
10.244.0.15:80
```

Command:

```bash
kubectl exec readiness-demo -- rm /usr/share/nginx/html/index.html
```

After probe failure:

```text
READY 0/1
```

Endpoints:

```text
<none>
```

Restart Count:

```text
0
```

Verification Answer:

```text
Container was NOT restarted.
```

Readiness only removes the Pod from Service endpoints.

---

## Task 6 — Startup Probe

Configuration:

```yaml
periodSeconds: 5
failureThreshold: 12
```

Startup Budget:

```text
12 × 5 = 60 seconds
```

Application Startup Time:

```text
20 seconds
```

Result:

```text
Startup probe succeeded.
```

Verification Question:

```text
What if failureThreshold = 2 ?
```

Calculation:

```text
2 × 5 = 10 seconds
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
CrashLoopBackOff
```

---

# Production  Nuggets

### CPU vs Memory

CPU:

```text
Compressible Resource
```

When exceeded:

```text
Throttled
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

### QoS Eviction Order

When node memory pressure occurs:

```text
BestEffort  → Evicted First
Burstable   → Evicted Next
Guaranteed  → Evicted Last
```



---

### Probe Summary

| Probe     | Purpose                   | Failure Action        |
| --------- | ------------------------- | --------------------- |
| Liveness  | Is app alive?             | Restart container     |
| Readiness | Can app serve traffic?    | Remove from endpoints |
| Startup   | Has app finished startup? | Kill container        |


***

Requests affect scheduling.

Limits affect runtime enforcement.

Scheduler ignores limits.

Example:
```bash
requests:
  cpu: 100m

limits:
  cpu: 4
```

Scheduler only reserves:
```bash
100m
```
not:
```bash
4 CPU
```
