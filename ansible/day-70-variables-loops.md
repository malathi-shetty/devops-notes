# Day 70 Master Notes — Part 1

# Ansible Variables (Teach It Like You're 10)

---

# Big Picture

Imagine you're building 100 houses.

Without variables:

```text
House 1 = Blue
House 2 = Blue
House 3 = Blue
House 4 = Blue
...
House 100 = Blue
```

If the customer says:

```text
Change Blue → Red
```

You must edit 100 places.

Painful.

---

With variables:

```text
color = Blue

House color = color
```

Now change:

```text
color = Red
```

Everything updates automatically.

This is exactly why Ansible uses Variables.

---

# What is a Variable?

A variable is simply:

```text
A named container that stores a value.
```

Example:

```yaml
app_name: terraweek-app
```

Think:

```text
app_name
    ↓
terraweek-app
```

---

# Why Variables Matter

Without variables:

```yaml
path: /opt/terraweek-app
```

written everywhere.

If app changes:

```text
terraweek-app
        ↓
my-app
```

You edit many files.

---

With variables:

```yaml
app_name: terraweek-app
```

Use:

```yaml
path: "/opt/{{ app_name }}"
```

Now changing one variable updates everything.

---

# Variable Mental Model

```text
Variable
   ↓
Value

app_name
   ↓
terraweek-app
```

Whenever Ansible sees:

```yaml
{{ app_name }}
```

it replaces it with:

```text
terraweek-app
```

before running.

---

# The Variable Demo Playbook

Your playbook:

```yaml
vars:
  app_name: terraweek-app
  app_port: 8080
  app_dir: "/opt/{{ app_name }}"
```

---

# Understanding Each Variable

## app_name

```yaml
app_name: terraweek-app
```

Stores application name.

Think:

```text
app_name
   ↓
terraweek-app
```

---

## app_port

```yaml
app_port: 8080
```

Stores application port.

Think:

```text
Users
   ↓
Port 8080
   ↓
Application
```

---

## app_dir

```yaml
app_dir: "/opt/{{ app_name }}"
```

This is a dynamic variable.

---

Ansible sees:

```yaml
app_name: terraweek-app
```

Then calculates:

```yaml
app_dir: /opt/terraweek-app
```

Automatically.

---

# Variable Interpolation

Fancy word.

Simple meaning:

```text
Put one variable inside another.
```

Example:

```yaml
app_dir: "/opt/{{ app_name }}"
```

---

Process:

```text
Step 1

app_name
   ↓
terraweek-app

Step 2

/opt/{{ app_name }}

Step 3

/opt/terraweek-app
```

Done.

---

# ASCII Flow

```text
app_name
   ↓
terraweek-app

Used inside

app_dir

   ↓

/opt/{{ app_name }}

   ↓

/opt/terraweek-app
```

---

# Debug Task

Your playbook:

```yaml
debug:
  msg: "Deploying {{ app_name }} on port {{ app_port }} to {{ app_dir }}"
```

Variables:

```yaml
app_name = terraweek-app
app_port = 8080
app_dir = /opt/terraweek-app
```

Final output:

```text
Deploying terraweek-app
on port 8080
to /opt/terraweek-app
```

---

# What Debug Does

Think:

```text
print()
```

in Python.

Or:

```text
echo
```

in Linux.

Its purpose:

```text
Show variable values
for troubleshooting.
```

---

# Creating Application Directory

Task:

```yaml
file:
  path: "{{ app_dir }}"
  state: directory
```

Ansible replaces:

```yaml
{{ app_dir }}
```

with:

```text
/opt/terraweek-app
```

Then executes:

```text
Create folder:
/opt/terraweek-app
```

---

# What You Verified

You checked:

```bash
ls -ld /opt/terraweek-app
```

Output:

```text
drwxr-xr-x
```

Meaning:

```text
Directory exists
```

So variable resolution worked correctly.

---

# Package Installation Using Variables

Playbook:

```yaml
packages:
  - git
  - curl
  - wget
```

Think:

```text
packages

 ├── git
 ├── curl
 └── wget
```

---

Task:

```yaml
name: "{{ packages }}"
```

Ansible expands:

```text
Install:

git
curl
wget
```

---

# Real World Example

Instead of:

```yaml
name: git
```

```yaml
name: curl
```

```yaml
name: wget
```

You create one list:

```yaml
packages:
  - git
  - curl
  - wget
```

Much cleaner.

---

# Amazon Linux Issue You Found

Playbook wanted:

```yaml
curl
```

But server already had:

```text
curl-minimal
```

Installed.

Result:

```text
Conflict
```

---

# Why This Happened

Amazon Linux 2023 ships with:

```text
curl-minimal
```

instead of full:

```text
curl
```

Think:

```text
curl-minimal
       and
curl

fight for same files
```

Package manager stops.

---

# Important Learning

The problem was NOT:

```text
Variables
```

The problem WAS:

```text
Package dependency conflict
```

Huge interview point.

---

# Command Line Variable Override

This was the most important part.

Command:

```bash
ansible-playbook variables-demo.yml \
-e "app_name=my-custom-app app_port=9090"
```

---

# What is -e ?

```text
Extra Variables
```

Think:

```text
Boss walks in at runtime and says:

Don't use these values.

Use THESE instead.
```

---

Playbook says:

```yaml
app_name: terraweek-app
```

Boss says:

```bash
-e "app_name=my-custom-app"
```

Ansible obeys boss.

---

# What Happened Internally

Before:

```text
app_name
   ↓
terraweek-app
```

After:

```text
app_name
   ↓
my-custom-app
```

---

# Recalculation Magic

Original:

```yaml
app_dir: "/opt/{{ app_name }}"
```

Since:

```text
app_name changed
```

Ansible recalculated:

```text
/opt/my-custom-app
```

automatically.

---

# ASCII Visualization

Before

```text
app_name
    ↓
terraweek-app

app_dir
    ↓
/opt/terraweek-app
```

After Override

```text
app_name
    ↓
my-custom-app

app_dir
    ↓
/opt/my-custom-app
```

---

# Verification

Debug Output:

```text
Deploying my-custom-app
on port 9090
to /opt/my-custom-app
```

This proved override worked.

---

# Directory Verification

You checked:

```bash
ls -ld /opt/my-custom-app
```

Directory existed.

Proof:

```text
Playbook used overridden values.
```

---

# Variable Precedence (First Introduction)

When multiple values exist:

```text
Which one wins?
```

This is called:

```text
Variable Precedence
```

---

Example

Playbook:

```yaml
app_name: terraweek-app
```

CLI:

```bash
-e "app_name=my-custom-app"
```

Winner:

```text
my-custom-app
```

---

# Memory Trick

Think:

```text
Playbook = Employee

CLI Extra Vars = CEO
```

Employee says:

```text
terraweek-app
```

CEO says:

```text
my-custom-app
```

Who wins?

```text
CEO
```

Always.

---

# Rule Learned Today

```text
Extra Vars (-e)
override
Playbook Variables
```

---

# Interview Questions

### What is a variable in Ansible?

A variable stores reusable values that can be referenced throughout a playbook.

---

### What syntax is used to access variables?

```yaml
{{ variable_name }}
```

Example:

```yaml
{{ app_name }}
```

---

### What is variable interpolation?

Using one variable inside another.

Example:

```yaml
app_dir: "/opt/{{ app_name }}"
```

---

### What does debug module do?

Prints variables and messages during playbook execution.

---

### What does -e mean?

```text
Extra Variables
```

Used to pass variables at runtime.

---

### Do CLI variables override playbook variables?

Yes.

```text
-e
has higher precedence
than vars:
```

---

# 60-Second Revision Sheet

```text
VARIABLE
=
Reusable named value

{{ variable }}
=
Reference variable

app_name
=
terraweek-app

Interpolation
=
Variable inside variable

app_dir:
/opt/{{ app_name }}

↓

/opt/terraweek-app

Debug
=
Print variable values

-e
=
Extra Variables

CLI variables
override
playbook variables

Precedence:

Extra Vars (-e)
       ↓
Playbook Vars
```

---

# One-Line Summary

```text
Ansible Variables allow us to write values once, reuse them everywhere, dynamically build configurations, and override them at runtime using -e, making automation flexible and maintainable.
```

***

# Day 70 Master Notes — Part 2

# group_vars, host_vars & Variable Precedence

# (The Real Power of Ansible Variables)

---

# Why Task 2 Exists

In Task 1 we learned:

```yaml
vars:
  app_name: terraweek-app
```

Works great.

But imagine:

```text
100 Servers
```

---

Would you write:

```yaml
vars:
```

inside every playbook?

No.

That becomes a nightmare.

---

Real companies have:

```text
Web Servers
Database Servers
Application Servers
Cache Servers
Monitoring Servers
```

Each needs different values.

This is why:

```text
group_vars
host_vars
```

exist.

---

# The Problem Before group_vars

Imagine:

```text
web-server-1
web-server-2
web-server-3
web-server-4
web-server-5
```

All use:

```yaml
http_port: 80
```

Without group_vars:

```text
Copy
Paste
Copy
Paste
Copy
Paste
```

everywhere.

---

Then manager says:

```text
Change port 80
to port 8080
```

Now edit many files.

Bad design.

---

# The Solution

Store shared variables once.

```text
group_vars
```

Then all servers inherit them.

---

# Simple Analogy

Imagine a school.

---

Principal creates rule:

```text
All Class 5 students
wear Blue Uniform
```

No need to tell every student.

---

Class rule:

```text
Blue Uniform
```

↓

Applies to all students.

---

This is:

```text
group_vars
```

---

Now imagine:

One student is class monitor.

Principal says:

```text
This student wears
Red Badge
```

Only that student gets it.

This is:

```text
host_vars
```

---

# The Directory Structure

You created:

```text
ansible/

├── inventory.ini

├── group_vars/
│   ├── all.yml
│   ├── web.yml
│   └── db.yml

├── host_vars/
│   └── web-server.yml

└── playbooks/
    └── site.yml
```

---

# Visual Diagram

```text
                 Inventory

                     │

      ┌──────────────┼──────────────┐

      │              │              │

     web            app            db

      │                             │

      │                             │

 group_vars/web.yml         group_vars/db.yml

      │                             │

      │                             │

 web-server                 db-server

      │

 host_vars/web-server.yml
```

---

# Inventory Refresher

Your inventory:

```ini
[web]
web-server

[app]
app-server

[db]
db-server
```

Think:

```text
Groups
   ↓
Contain Hosts
```

---

ASCII

```text
web group

 └── web-server

app group

 └── app-server

db group

 └── db-server
```

---

# group_vars/all.yml

File:

```yaml
app_env: development

common_packages:
  - vim
  - htop
  - tree

ntp_server: pool.ntp.org
```

---

# Meaning

This file applies to:

```text
EVERY HOST
```

---

Think:

```text
Company Rule
```

Everyone follows it.

---

ASCII

```text
group_vars/all.yml

        │

        ▼

 ┌─────────────┐
 │ web-server  │
 ├─────────────┤
 │ app-server  │
 ├─────────────┤
 │ db-server   │
 └─────────────┘
```

---

# What You Verified

Output:

```text
Environment: development
```

appeared on:

```text
web-server
app-server
db-server
```

Proof:

```text
all.yml
applied everywhere
```

---

# group_vars/web.yml

File:

```yaml
http_port: 80

max_connections: 1000

web_packages:
  - nginx
```

---

Meaning:

```text
Only hosts
inside web group
get these values
```

---

ASCII

```text
group_vars/web.yml

       │

       ▼

web-server

Gets:

http_port
max_connections
web_packages
```

---

# What Did NOT Get It?

```text
app-server
db-server
```

---

Because they are not inside:

```text
[web]
```

group.

---

# group_vars/db.yml

File:

```yaml
db_port: 3306

db_packages:
  - mysql-server
```

---

Meaning:

```text
Only DB hosts
receive these variables
```

---

ASCII

```text
group_vars/db.yml

      │

      ▼

 db-server

Gets:

db_port
db_packages
```

---

# host_vars

Now comes the interesting part.

---

File:

```text
host_vars/web-server.yml
```

---

Contains:

```yaml
max_connections: 2000

custom_message:
  This is the primary web server
```

---

Question:

Didn't we already have:

```yaml
max_connections: 1000
```

inside:

```yaml
group_vars/web.yml
```

?

YES.

This was intentional.

---

# Why?

To learn:

```text
Variable Precedence
```

---

# Host Variable Meaning

Think:

```text
Group Rule:
Everyone gets 1000
```

But manager says:

```text
Special server gets 2000
```

---

Which value wins?

```text
2000
```

---

Because:

```text
Host Specific
```

is more specific.

---

# Real Life Analogy

School rule:

```text
All students
must arrive by 8 AM
```

---

But Principal says:

```text
Class Monitor
arrives at 7 AM
```

---

Who follows which rule?

```text
Normal Students → 8 AM

Monitor → 7 AM
```

Special rule wins.

---

# Visual Representation

```text
group_vars/web.yml

max_connections = 1000


host_vars/web-server.yml

max_connections = 2000
```

---

Conflict detected.

Who wins?

---

```text
host_vars
```

---

Final:

```text
max_connections = 2000
```

---

# What You Verified

Output:

```text
HTTP port: 80
Max connections: 2000
```

---

This proves:

```text
host_vars
overrode
group_vars
```

---

# Another Host Variable

```yaml
custom_message:
  This is the primary web server
```

Only existed in:

```text
host_vars/web-server.yml
```

---

Output:

```text
This is the primary web server
```

---

Proof:

```text
Host-specific variable loaded successfully.
```

---

# How Ansible Searches Variables

Think of Ansible as detective.

When it needs:

```text
max_connections
```

it searches.

---

Step 1

```text
host_vars
```

Found?

YES

```text
2000
```

Stop searching.

---

Step 2

Never needed.

---

ASCII

```text
Need variable:

max_connections

      │

      ▼

host_vars ?

YES

2000

DONE
```

---

Another Example

Need:

```text
http_port
```

---

Search:

```text
host_vars ?
```

Not found.

---

Search:

```text
group_vars/web.yml
```

Found.

```text
80
```

Use it.

---

ASCII

```text
http_port

     │

     ▼

host_vars ?

No

     ▼

group_vars ?

Yes

     ▼

80
```

---

# Playbook Flow

Your playbook:

```yaml
hosts: all
```

---

Task:

```yaml
msg: "Environment: {{ app_env }}"
```

---

Ansible looked:

```text
host_vars ?
```

No.

```text
group_vars/all.yml ?
```

Yes.

---

Used:

```text
development
```

---

Output:

```text
Environment: development
```

---

# CLI Override Test

Most important verification.

You ran:

```bash
ansible-playbook playbooks/site.yml \
-e "app_env=production"
```

---

Remember:

group_vars had:

```yaml
app_env: development
```

---

But output became:

```text
Environment: production
```

---

Why?

Because:

```text
-e
has higher precedence
```

---

# Visual Battle

```text
group_vars

app_env=development
```

versus

```bash
-e "app_env=production"
```

Winner:

```text
production
```

---

# CEO Analogy

Remember from Part 1.

---

group_vars:

```text
Department Manager
```

host_vars:

```text
Regional Manager
```

CLI:

```text
CEO
```

---

Who wins?

```text
CEO
```

Always.

---

# Variable Precedence Hierarchy

What you proved in Task 2

```text
Extra Vars (-e)

       ↓

host_vars

       ↓

group_vars

       ↓

Playbook Vars
```

---

Memory Version

```text
CEO

 ↓

Special Employee

 ↓

Department

 ↓

Default Rule
```

---

# Why Precedence Exists

Imagine:

```text
All servers use Port 80
```

But:

```text
One special server uses 8080
```

---

Without precedence:

```text
Chaos
```

---

With precedence:

```text
Most Specific
Rule Wins
```

---

# Real Production Example

Company:

```text
100 Web Servers
```

---

group_vars/web.yml

```yaml
http_port: 80
```

Applies everywhere.

---

But one VIP server:

```yaml
host_vars/web-prod-1.yml

http_port: 8080
```

---

Result:

```text
99 Servers → 80

1 VIP Server → 8080
```

Perfect.

---

# Common Use Cases

## group_vars/all.yml

Store:

```text
Timezone
NTP Server
Environment
Common Packages
DNS
```

---

## group_vars/web.yml

Store:

```text
Nginx Settings
HTTP Port
TLS Config
Workers
```

---

## group_vars/db.yml

Store:

```text
DB Port
DB Memory
DB Packages
Replication Settings
```

---

## host_vars

Store:

```text
Unique IPs
Special Ports
Host-Specific Limits
Custom Messages
```

---

# What You Learned

Task 2 wasn't about files.

Task 2 was about:

```text
Centralized Configuration
```

and

```text
Variable Hierarchy
```

which is how real companies manage thousands of servers.

---

# Interview Questions

### What is group_vars?

Variables shared by a group of hosts.

---

### What is host_vars?

Variables specific to a single host.

---

### What does group_vars/all.yml do?

Applies variables to every host in inventory.

---

### Which has higher precedence?

```text
host_vars
```

or

```text
group_vars
```

Answer:

```text
host_vars
```

---

### Do CLI variables override host_vars?

Yes.

```text
-e
wins
```

---

### Why use group_vars?

To avoid duplication and manage shared configuration centrally.

---

### Why use host_vars?

To customize individual servers without affecting the whole group.

---

# 60-Second Revision Sheet

```text
group_vars
=
Variables for a group

host_vars
=
Variables for one host

group_vars/all.yml
=
Applies everywhere

group_vars/web.yml
=
Only web group

group_vars/db.yml
=
Only db group

host_vars/web-server.yml
=
Only web-server

Host Specific
beats
Group Specific

CLI (-e)
beats
Everything

Precedence:

Extra Vars (-e)

      ↓

host_vars

      ↓

group_vars

      ↓

Playbook Vars
```

---

# One-Line Summary

```text
group_vars provide shared settings for groups of servers, host_vars provide unique settings for individual servers, and Ansible's variable precedence ensures the most specific configuration wins while allowing runtime overrides through -e.
```

***

# Day 70 Master Notes — Part 3

# Ansible Facts (How Ansible Becomes a Detective 🔍)

---

# Big Idea

Until now Ansible only used:

```text
Variables
```

that YOU provided.

Example:

```yaml
app_port: 8080
```

Ansible didn't know anything.

It simply obeyed.

---

# New Problem

Imagine you manage:

```text
100 Servers
```

Some are:

```text
Ubuntu
Amazon Linux
CentOS
RHEL
Debian
```

---

Question:

How will Ansible know:

```text
Which package manager?

apt ?

yum ?

dnf ?
```

---

How will it know:

```text
RAM?
CPU?
IP?
Hostname?
```

---

Do we manually write everything?

```text
No.
```

That would be impossible.

---

# The Solution

Ansible collects:

```text
FACTS
```

---

# What Is A Fact?

A fact is:

```text
Information about a server
automatically discovered by Ansible.
```

---

Think:

```text
Server
   ↓
Ansible Investigates
   ↓
Collects Information
```

---

# Detective Analogy

Imagine a detective arrives.

Before asking questions he notes:

```text
House Number
Owner
Car
Address
```

---

Ansible does the same.

Before running tasks it learns:

```text
Hostname
OS
RAM
CPU
IP
Disk
Network
```

---

# ASCII Visualization

```text
        SERVER

           │

           ▼

     Ansible Setup

           │

           ▼

  ┌─────────────────┐

  │ OS              │
  │ RAM             │
  │ CPU             │
  │ IP Address      │
  │ Hostname        │
  │ Disk Info       │
  │ Interfaces      │

  └─────────────────┘
```

---

# The setup Module

The module responsible is:

```bash
ansible web-server -m setup
```

---

Think:

```text
setup
=
Fact Collector
```

---

ASCII

```text
Server

   │

   ▼

setup module

   │

   ▼

Thousands of facts
```

---

# Why Output Is Huge

When you ran:

```bash
ansible web-server -m setup
```

you saw:

```text
Massive JSON Output
```

Why?

Because Ansible discovered:

```text
OS
CPU
RAM
IP
DNS
Network
Disks
Kernel
Architecture
Users
Environment
Mounts
```

and much more.

---

# Real Mental Model

```text
setup

=

Server Resume
```

Everything about that machine.

---

# Why We Use Filters

Looking through thousands of lines is painful.

Instead:

```bash
ansible web-server \
-m setup \
-a "filter=ansible_distribution"
```

---

Now Ansible shows only:

```text
OS Info
```

---

Think:

```text
Google Search

instead of

Reading whole internet
```

---

# Important Fact #1

# ansible_distribution

Command:

```bash
ansible web-server \
-m setup \
-a "filter=ansible_distribution"
```

Output:

```text
Amazon
```

---

Meaning:

```text
Which Linux Distribution?
```

Examples:

```text
Amazon
Ubuntu
CentOS
Debian
RHEL
```

---

# Why Important?

Because package managers differ.

Ubuntu:

```bash
apt
```

Amazon:

```bash
dnf
```

---

ASCII

```text
OS ?

   │

   ├── Ubuntu → apt

   └── Amazon → dnf
```

---

# Real Example

```yaml
when: ansible_distribution == "Amazon"
```

Run task only on:

```text
Amazon Linux
```

---

# Important Fact #2

# ansible_distribution_version

Command:

```bash
ansible web-server \
-m setup \
-a "filter=ansible_distribution_version"
```

Output:

```text
2023
```

---

Meaning:

```text
Which version?
```

Example:

```text
Ubuntu 20.04
Ubuntu 22.04
Amazon 2023
```

---

# Why Important?

Sometimes software only works on:

```text
Specific OS Version
```

---

Example:

```yaml
when:
  ansible_distribution_version == "2023"
```

---

# Important Fact #3

# ansible_default_ipv4.address

Command:

```bash
ansible web-server \
-m setup \
-a "filter=ansible_default_ipv4"
```

Output:

```text
172.31.x.x
```

---

Meaning:

```text
Primary IP Address
```

---

Think:

```text
Server Home Address
```

---

ASCII

```text
Server

   │

   ▼

172.31.43.32
```

---

# Why Important?

Used for:

```text
Load Balancers
Monitoring
Clusters
Configuration Files
```

---

Real Example

```yaml
server_ip:
  {{ ansible_default_ipv4.address }}
```

---

# Important Fact #4

# ansible_memtotal_mb

Command:

```bash
ansible web-server \
-m setup \
-a "filter=ansible_memtotal_mb"
```

Output:

```text
911
```

Meaning:

```text
911 MB RAM
```

---

Think:

```text
How much memory
does this machine have?
```

---

# Why Important?

Imagine:

```text
Database Server
```

Needs:

```text
Minimum 2 GB RAM
```

---

Ansible can check.

```yaml
when:
  ansible_memtotal_mb >= 2048
```

---

ASCII

```text
RAM

911 MB

↓

Too Small

Skip Database Install
```

---

# Important Fact #5

# ansible_hostname

Command:

```bash
ansible web-server \
-m setup \
-a "filter=ansible_hostname"
```

Output:

```text
web-server
```

---

Meaning:

```text
Machine Name
```

---

Think:

```text
Human Name

↓

Server Name
```

---

# Why Important?

Useful for:

```text
Logging
Reports
Monitoring
Naming
```

---

# What You Created

Playbook:

```yaml
- name: Facts demo
  hosts: all

  tasks:

    - name: Show OS info

      debug:
        msg: >
          Hostname:
          {{ ansible_hostname }}

          OS:
          {{ ansible_distribution }}
          {{ ansible_distribution_version }}

          RAM:
          {{ ansible_memtotal_mb }}MB

          IP:
          {{ ansible_default_ipv4.address }}
```

---

# What Happened?

Ansible automatically filled:

```text
Hostname
OS
RAM
IP
```

for every server.

---

Example Output

```text
Hostname: web-server

OS: Amazon 2023

RAM: 911MB

IP: 172.31.43.32
```

---

# Magic Behind The Scenes

You never wrote:

```yaml
hostname: web-server
```

---

You never wrote:

```yaml
ip: 172.31.43.32
```

---

Ansible discovered it automatically.

That is:

```text
Fact Gathering
```

---

# Network Interfaces

Second task:

```yaml
debug:
  var: ansible_interfaces
```

---

Meaning:

```text
Show all network adapters
```

---

Think:

```text
Laptop

WiFi
Ethernet

Server

eth0
ens5
lo
docker0
```

---

Example Output

```text
ansible_interfaces:
 - lo
 - ens5
```

---

# Why Useful?

Used for:

```text
Firewall Rules
Monitoring
Networking
Clusters
```

---

# Fact Gathering Process

Every playbook starts.

---

Step 1

```text
Connect to Host
```

↓

Step 2

```text
Gather Facts
```

↓

Step 3

```text
Store as Variables
```

↓

Step 4

```text
Run Tasks
```

---

ASCII

```text
Playbook Start

      │

      ▼

Gather Facts

      │

      ▼

Create Variables

      │

      ▼

Run Tasks
```

---

# Why Facts Are Powerful

Without facts:

```text
Hardcoded Automation
```

---

With facts:

```text
Dynamic Automation
```

---

Example

Bad:

```yaml
yum:
  name: nginx
```

Only works on:

```text
RHEL Family
```

---

Better:

```yaml
when:
  ansible_os_family == "RedHat"
```

Use yum.

---

Else:

```yaml
when:
  ansible_os_family == "Debian"
```

Use apt.

---

One playbook.

Many operating systems.

---

# Real Production Example

Company has:

```text
50 Ubuntu Servers
50 Amazon Servers
```

---

Single playbook:

```yaml
when:
  ansible_distribution == "Ubuntu"
```

↓

Install using:

```bash
apt
```

---

Else:

```yaml
when:
  ansible_distribution == "Amazon"
```

↓

Install using:

```bash
dnf
```

---

Automation becomes:

```text
Self-Aware
```

---

# Five Facts You Identified

---

## 1. ansible_distribution

```text
Operating System
```

Used for:

```text
OS-specific tasks
```

---

## 2. ansible_distribution_version

```text
OS Version
```

Used for:

```text
Version-specific automation
```

---

## 3. ansible_default_ipv4.address

```text
Primary IP
```

Used for:

```text
Networking
Clusters
Monitoring
```

---

## 4. ansible_memtotal_mb

```text
RAM
```

Used for:

```text
Capacity checks
Performance tuning
```

---

## 5. ansible_hostname

```text
Host Name
```

Used for:

```text
Reports
Logs
Monitoring
```

---

# Common Interview Questions

### What are Ansible Facts?

Facts are automatically discovered information about a managed host.

---

### Which module gathers facts?

```text
setup
```

module.

---

### When are facts gathered?

By default:

```text
Before tasks execute
```

---

### How can you view facts?

```bash
ansible web-server -m setup
```

---

### How can you filter facts?

```bash
ansible web-server \
-m setup \
-a "filter=ansible_distribution"
```

---

### What is ansible_default_ipv4.address?

Primary IPv4 address of the host.

---

### Why are facts useful?

They allow playbooks to make intelligent decisions dynamically.

---

# Memory Tricks

---

## Facts

Think:

```text
Fact

=

Server Biography
```

---

## setup Module

Think:

```text
setup

=

Detective
```

---

## ansible_distribution

Think:

```text
Server Nationality
```

---

## ansible_hostname

Think:

```text
Server Name
```

---

## ansible_memtotal_mb

Think:

```text
Server Brain Capacity
```

---

## ansible_default_ipv4.address

Think:

```text
Server Home Address
```

---

# 60-Second Revision Sheet

```text
FACT
=
Automatic server information

Collected by:
setup module

Command:

ansible host -m setup

Useful Facts:

ansible_distribution
=
OS

ansible_distribution_version
=
OS Version

ansible_hostname
=
Host Name

ansible_memtotal_mb
=
RAM

ansible_default_ipv4.address
=
IP Address

Flow:

Connect
  ↓
Gather Facts
  ↓
Store Variables
  ↓
Run Tasks

Benefits:

No Hardcoding
Dynamic Decisions
OS-Aware Automation
```

---

# One-Line Summary

```text
Ansible Facts are automatically discovered system details collected by the setup module, allowing playbooks to adapt dynamically to each server's OS, RAM, CPU, IP address, and configuration without hardcoding values.
```

***

# Day 70 Master Notes — Part 4

# Ansible Conditionals (`when`)

# How Ansible Learns To Think 🤔

---

# Big Picture

Until now Ansible behaved like a robot.

You said:

```yaml
Install nginx
```

Ansible replied:

```text
Okay.
```

---

You said:

```yaml
Create directory
```

Ansible replied:

```text
Okay.
```

---

No thinking.

No decisions.

Just execution.

---

# The Problem

Imagine:

```text
100 Servers
```

Some are:

```text
Web Servers
Database Servers
Application Servers
```

---

Question:

Should nginx be installed on:

```text
Database Server?
```

No.

---

Should MySQL be installed on:

```text
Web Server?
```

No.

---

Without conditions:

```text
Everything runs everywhere
```

Disaster.

---

# The Solution

Teach Ansible:

```text
IF something is true
DO this task

ELSE
SKIP it
```

This is called:

```text
Conditionals
```

---

# The when Statement

Main keyword:

```yaml
when:
```

Think:

```text
when
=
IF
```

---

Python:

```python
if age > 18:
```

---

Ansible:

```yaml
when: age > 18
```

Same idea.

---

# Real Life Analogy

Mom says:

```text
If it's raining

Take umbrella
```

---

Condition:

```text
Raining ?
```

---

YES

```text
Take umbrella
```

---

NO

```text
Skip umbrella
```

---

Ansible works exactly like this.

---

# ASCII Visualization

```text
Condition

   │

   ▼

True ?

 ├── YES → Run Task

 └── NO  → Skip Task
```

---

# Your First Conditional

Example:

```yaml
- name: Install Nginx

  package:
    name: nginx
    state: present

  when:
    inventory_hostname in groups['web']
```

---

Translation:

```text
Only install nginx
if host belongs
to web group
```

---

# Understanding inventory_hostname

Think:

```text
Current Server Name
```

Example:

```text
web-server
app-server
db-server
```

---

When Ansible runs:

```text
web-server
```

Current value:

```text
inventory_hostname
=
web-server
```

---

# Understanding groups

Inventory:

```ini
[web]
web-server

[db]
db-server

[app]
app-server
```

---

Ansible internally creates:

```text
groups['web']

↓

[web-server]
```

---

ASCII

```text
groups

 ├── web
 │     └── web-server
 │
 ├── db
 │     └── db-server
 │
 └── app
       └── app-server
```

---

# Condition Evaluation

Condition:

```yaml
inventory_hostname
in
groups['web']
```

---

For web-server:

```text
web-server
in
[web-server]

YES
```

Task runs.

---

For db-server:

```text
db-server
in
[web-server]

NO
```

Task skipped.

---

# Visual Flow

```text
Current Host

web-server

      │

      ▼

Is it inside
groups['web'] ?

      │

      ▼

YES

      │

      ▼

Install nginx
```

---

Database Server Flow

```text
Current Host

db-server

      │

      ▼

Inside web group ?

      │

      ▼

NO

      │

      ▼

Skip Task
```

---

# What You Verified

Output:

```text
web-server
changed
```

---

Output:

```text
db-server
skipping
```

---

Output:

```text
app-server
skipping
```

---

Proof:

```text
Condition worked correctly.
```

---

# Why Skipped Is Good

New engineers panic:

```text
TASK SKIPPED
```

---

They think:

```text
Something failed
```

---

Wrong.

---

Skipped means:

```text
Condition was FALSE
```

and Ansible intentionally ignored task.

---

Example:

```text
Failed
=
Bad

Skipped
=
Expected
```

---

# OS-Based Conditions

One of the most important uses.

---

Remember Facts:

```yaml
ansible_distribution
```

---

Example:

```yaml
when:
  ansible_distribution == "Amazon"
```

---

Meaning:

```text
Run only
on Amazon Linux
```

---

# How It Works

Fact gathered:

```text
Amazon
```

---

Condition:

```yaml
ansible_distribution == "Amazon"
```

---

Result:

```text
Amazon == Amazon

TRUE
```

Run task.

---

# Ubuntu Example

Fact:

```text
Ubuntu
```

Condition:

```yaml
ansible_distribution == "Amazon"
```

---

Result:

```text
Ubuntu == Amazon

FALSE
```

Skip task.

---

# ASCII

```text
OS ?

    │

    ▼

Amazon ?

 ├── YES → Run

 └── NO  → Skip
```

---

# Why This Matters

Different Linux distributions use:

```text
Different Package Managers
```

---

Ubuntu:

```bash
apt
```

---

Amazon:

```bash
dnf
```

---

RHEL:

```bash
yum
```

---

Without conditions:

```text
Playbook breaks
```

---

With conditions:

```text
Playbook adapts
```

---

# Real Production Example

```yaml
- name: Install package on Ubuntu

  apt:
    name: nginx

  when:
    ansible_distribution == "Ubuntu"
```

---

```yaml
- name: Install package on Amazon

  dnf:
    name: nginx

  when:
    ansible_distribution == "Amazon"
```

---

Same playbook.

Different behavior.

---

# Memory-Based Conditions

Fact:

```yaml
ansible_memtotal_mb
```

---

Example:

```yaml
when:
  ansible_memtotal_mb >= 2048
```

---

Meaning:

```text
Only run if RAM
is at least 2GB
```

---

Example Evaluation

Server RAM:

```text
911 MB
```

Condition:

```text
911 >= 2048
```

Result:

```text
FALSE
```

Skip.

---

# Why Useful?

Database installation.

---

Bad server:

```text
512 MB RAM
```

Database would crash.

---

Condition prevents mistake.

---

ASCII

```text
RAM Check

911 MB

      │

      ▼

>= 2048 ?

      │

      ▼

NO

      │

      ▼

Skip Database Install
```

---

# Variable-Based Conditions

Variable:

```yaml
app_env: production
```

---

Condition:

```yaml
when:
  app_env == "production"
```

---

Meaning:

```text
Run only in production
```

---

Example

Production:

```text
production == production

TRUE
```

Run.

---

Development:

```text
development == production

FALSE
```

Skip.

---

# Comparison Operators

Just like programming.

---

Equal

```yaml
==
```

Example:

```yaml
when:
  app_env == "production"
```

---

Not Equal

```yaml
!=
```

Example:

```yaml
when:
  app_env != "development"
```

---

Greater Than

```yaml
>
```

Example:

```yaml
when:
  ansible_memtotal_mb > 1000
```

---

Less Than

```yaml
<
```

Example:

```yaml
when:
  ansible_memtotal_mb < 512
```

---

Greater Than Or Equal

```yaml
>=
```

---

Less Than Or Equal

```yaml
<=
```

---

# AND Logic

Sometimes one condition isn't enough.

---

Example:

```yaml
when:
  ansible_distribution == "Amazon"
  and
  ansible_memtotal_mb >= 1024
```

---

Meaning:

```text
Must be Amazon Linux

AND

Must have enough RAM
```

---

ASCII

```text
Amazon ?

   YES

RAM > 1024 ?

   YES

RUN
```

---

If either becomes NO:

```text
Skip
```

---

# AND Truth Table

```text
Condition A   Condition B   Result

TRUE          TRUE          RUN

TRUE          FALSE         SKIP

FALSE         TRUE          SKIP

FALSE         FALSE         SKIP
```

---

# OR Logic

Example:

```yaml
when:
  ansible_distribution == "Amazon"
  or
  ansible_distribution == "Ubuntu"
```

---

Meaning:

```text
Amazon OR Ubuntu
```

Either works.

---

ASCII

```text
Amazon ?

   YES

RUN


Ubuntu ?

   YES

RUN
```

---

# OR Truth Table

```text
Condition A   Condition B   Result

TRUE          TRUE          RUN

TRUE          FALSE         RUN

FALSE         TRUE          RUN

FALSE         FALSE         SKIP
```

---

# Multiple Conditions (List Style)

Cleaner syntax:

```yaml
when:
  - ansible_distribution == "Amazon"
  - ansible_memtotal_mb >= 1024
```

---

Ansible interprets as:

```text
AND
```

between lines.

---

Meaning:

```text
Both must be true
```

---

# The Reporting Playbook Logic

You created reporting tasks that displayed:

```text
Hostname
OS
RAM
IP
```

---

Then used conditions to decide:

```text
Should additional tasks run?
```

based on facts.

---

This is the beginning of:

```text
Self-Aware Automation
```

---

# How Ansible Thinks Internally

Example:

```yaml
when:
  ansible_distribution == "Amazon"
```

---

Ansible performs:

```text
Step 1

Get Fact

↓

Amazon

Step 2

Compare

↓

Amazon == Amazon

Step 3

TRUE

Step 4

Run Task
```

---

# Decision Engine Diagram

```text
Gather Facts

      │

      ▼

Evaluate Condition

      │

      ▼

TRUE ?

 ├── YES → Execute

 └── NO  → Skip
```

---

# Common Mistakes

---

## Mistake 1

Using Single Equals

Wrong:

```yaml
when:
  app_env = "production"
```

---

Correct:

```yaml
when:
  app_env == "production"
```

---

## Mistake 2

Wrong Fact Name

Wrong:

```yaml
ansible_memory
```

---

Correct:

```yaml
ansible_memtotal_mb
```

---

## Mistake 3

Case Sensitivity

Wrong:

```yaml
when:
  ansible_distribution == "amazon"
```

---

Fact:

```text
Amazon
```

---

Result:

```text
FALSE
```

---

Correct:

```yaml
when:
  ansible_distribution == "Amazon"
```

---

# Interview Questions

### What does `when` do?

Controls task execution based on a condition.

---

### What happens if condition is false?

Task is skipped.

---

### Is skipped a failure?

No.

Skipped means condition evaluated to false.

---

### Can facts be used in conditions?

Yes.

Example:

```yaml
when:
  ansible_distribution == "Amazon"
```

---

### Can variables be used?

Yes.

Example:

```yaml
when:
  app_env == "production"
```

---

### Can multiple conditions be used?

Yes.

Using:

```yaml
and
or
```

or list syntax.

---

### What is the most common use of conditionals?

OS-specific automation and environment-specific execution.

---

# 60-Second Revision Sheet

```text
when
=
IF statement

TRUE
=
Run Task

FALSE
=
Skip Task

Skipped
≠
Failed

Use Facts:

ansible_distribution

ansible_hostname

ansible_memtotal_mb

Examples:

when:
  app_env == "production"

when:
  ansible_distribution == "Amazon"

AND

when:
  - ansible_distribution == "Amazon"
  - ansible_memtotal_mb >= 1024

OR

when:
  ansible_distribution == "Ubuntu"
  or
  ansible_distribution == "Amazon"

Flow:

Gather Facts
     ↓
Evaluate Condition
     ↓
TRUE → Run
FALSE → Skip
```

---

# One-Line Summary

```text
Conditionals (`when`) allow Ansible to make intelligent decisions by evaluating variables and facts, ensuring tasks run only on the right servers, operating systems, environments, or hardware configurations instead of blindly executing everywhere.
```

***

# Day 70 Master Notes — Part 5

# Ansible Loops (`loop`)

# How To Make Ansible Repeat Work Without Repeating Code 🔄

---

# Big Picture

Imagine your manager says:

```text
Install:

git
curl
wget
vim
htop
```

---

New engineers often do:

```yaml
- name: Install git
  package:
    name: git

- name: Install curl
  package:
    name: curl

- name: Install wget
  package:
    name: wget

- name: Install vim
  package:
    name: vim

- name: Install htop
  package:
    name: htop
```

---

Works?

```text
YES
```

Good?

```text
NO
```

---

Why?

Because:

```text
Same task
Repeated many times
```

---

# The Problem

Imagine:

```text
100 Packages
```

Would you write:

```yaml
100 Tasks
```

?

No.

That becomes:

```text
Huge
Messy
Hard to Maintain
```

---

# The Solution

Use:

```yaml
loop:
```

Think:

```text
Repeat this task
for every item
in a list
```

---

# Real Life Analogy

Imagine a teacher.

Students:

```text
Rahul
Amit
Priya
Neha
```

---

Without loop:

```text
Call Rahul

Call Amit

Call Priya

Call Neha
```

---

With loop:

```text
For each student

Call student
```

---

Same idea.

---

# What Is A Loop?

A loop tells Ansible:

```text
Run one task
multiple times
using different values
```

---

ASCII

```text
Task

   │

   ▼

Item 1

Item 2

Item 3

Item 4
```

---

# Your First Loop

Example:

```yaml
packages:

  - git
  - curl
  - wget
```

---

Task:

```yaml
- name: Install packages

  package:
    name: "{{ item }}"
    state: present

  loop:
    "{{ packages }}"
```

---

# What Is item?

Most important concept.

---

Think:

```text
Current thing
inside the loop
```

---

Ansible automatically creates:

```text
item
```

---

Iteration 1

```text
item = git
```

---

Iteration 2

```text
item = curl
```

---

Iteration 3

```text
item = wget
```

---

# Internal Execution

Your code:

```yaml
name: "{{ item }}"
```

---

First Run

```yaml
name: git
```

---

Second Run

```yaml
name: curl
```

---

Third Run

```yaml
name: wget
```

---

# ASCII Visualization

```text
packages

 ├── git
 ├── curl
 └── wget

      │

      ▼

Loop Starts

Iteration 1

item=git

Install git

Iteration 2

item=curl

Install curl

Iteration 3

item=wget

Install wget
```

---

# Why This Is Better

Without Loop

```yaml
Task 1
Task 2
Task 3
Task 4
Task 5
```

---

With Loop

```yaml
One Task
```

---

Benefits:

```text
Less Code

Less Errors

Easy Updates

Easy Maintenance
```

---

# Verification

You checked installed packages.

Example:

```bash
rpm -qa | grep git
```

---

Output showed:

```text
git installed
```

---

Same for:

```text
curl
wget
```

---

Proof:

```text
Loop processed
every item successfully.
```

---

# Directory Creation Loop

Next example.

---

Variables:

```yaml
directories:

  - /opt/app
  - /opt/logs
  - /opt/config
```

---

Task:

```yaml
- name: Create directories

  file:
    path: "{{ item }}"
    state: directory

  loop:
    "{{ directories }}"
```

---

# What Happens?

Iteration 1

```text
item=/opt/app
```

Create:

```text
/opt/app
```

---

Iteration 2

```text
item=/opt/logs
```

Create:

```text
/opt/logs
```

---

Iteration 3

```text
item=/opt/config
```

Create:

```text
/opt/config
```

---

# Visual Flow

```text
directories

 ├── /opt/app
 ├── /opt/logs
 └── /opt/config

      │

      ▼

Loop

      │

      ▼

Create Each Directory
```

---

# Why Companies Love Loops

Imagine:

```text
50 Directories
```

Without loops:

```text
50 Tasks
```

---

With loops:

```text
1 Task
```

---

Huge difference.

---

# User Creation Loop

Very common interview example.

---

Variables:

```yaml
users:

  - devops
  - developer
  - tester
```

---

Task:

```yaml
- name: Create users

  user:
    name: "{{ item }}"
    state: present

  loop:
    "{{ users }}"
```

---

# Execution

Iteration 1

```text
item=devops
```

Create user:

```text
devops
```

---

Iteration 2

```text
item=developer
```

Create user:

```text
developer
```

---

Iteration 3

```text
item=tester
```

Create user:

```text
tester
```

---

ASCII

```text
users

 ├── devops
 ├── developer
 └── tester

      │

      ▼

Create Each User
```

---

# Verification

Command:

```bash
cat /etc/passwd
```

---

Found:

```text
devops

developer

tester
```

---

Proof:

```text
Loop worked.
```

---

# Loop With Debug

One of the best ways to learn.

---

Task:

```yaml
- name: Show items

  debug:
    msg: "{{ item }}"

  loop:
    "{{ packages }}"
```

---

Output:

```text
git

curl

wget
```

---

Why Useful?

Because you can see:

```text
Exactly what
item contains
during each iteration.
```

---

# Looping Over Facts

Facts can also be looped.

---

Example:

```yaml
debug:
  msg: "{{ item }}"

loop:
  "{{ ansible_interfaces }}"
```

---

Suppose:

```text
ansible_interfaces

 ├── lo
 └── ens5
```

---

Output:

```text
lo

ens5
```

---

# Real Use Case

Network auditing.

---

Show every interface.

```text
lo

ens5

docker0

eth1
```

---

Useful for:

```text
Monitoring

Security

Networking
```

---

# Loop + Condition

Very powerful combination.

---

Example:

```yaml
- name: Install package

  package:
    name: "{{ item }}"

  loop:
    "{{ packages }}"

  when:
    ansible_distribution == "Amazon"
```

---

Meaning:

```text
Loop through packages

BUT

Only on Amazon Linux
```

---

ASCII

```text
Amazon ?

 YES

   ▼

Loop Starts

git

curl

wget

Install All


NO

Skip Entire Task
```

---

# Loop Execution Order

Important interview concept.

---

Ansible does:

```text
Check Condition
       ↓
Condition TRUE ?
       ↓
Start Loop
       ↓
Process Items
```

---

Not:

```text
Loop First

Condition Later
```

---

# Common Mistake #1

Wrong:

```yaml
loop:
  packages
```

---

Ansible treats:

```text
packages
```

as plain text.

---

Correct:

```yaml
loop:
  "{{ packages }}"
```

---

# Common Mistake #2

Forgetting item

Wrong:

```yaml
package:
  name: packages
```

---

This tries installing:

```text
Package literally called:

packages
```

---

Correct:

```yaml
package:
  name: "{{ item }}"
```

---

# Common Mistake #3

Indentation

Wrong indentation causes:

```text
YAML Error
```

---

Always keep:

```yaml
loop:
```

aligned with:

```yaml
package:
```

or

```yaml
debug:
```

---

# How Loop Thinks Internally

Variables:

```yaml
packages:

 - git
 - curl
 - wget
```

---

Ansible creates:

```text
Iteration 1

item=git
```

Run task.

---

Then:

```text
Iteration 2

item=curl
```

Run task.

---

Then:

```text
Iteration 3

item=wget
```

Run task.

---

Then:

```text
Finished
```

---

ASCII Engine

```text
List

 ├── git
 ├── curl
 └── wget

      │

      ▼

Take Item

      │

      ▼

Run Task

      │

      ▼

Next Item

      │

      ▼

Run Task

      │

      ▼

End List
```

---

# Why Loops Matter In DevOps

Real examples:

---

## Package Installation

```text
Install 50 Packages
```

---

## User Creation

```text
Create 100 Users
```

---

## Directory Creation

```text
Create 30 Directories
```

---

## Firewall Rules

```text
Open 20 Ports
```

---

## Kubernetes Nodes

```text
Configure 10 Nodes
```

---

Without loops:

```text
Huge Playbooks
```

---

With loops:

```text
Tiny Playbooks
```

---

# Old Style vs New Style

Older Ansible:

```yaml
with_items:
```

---

Modern Ansible:

```yaml
loop:
```

---

Interview Answer:

```text
loop is preferred

with_items is legacy
```

---

# Interview Questions

### What is a loop?

A mechanism to execute the same task multiple times using different values.

---

### What variable is automatically available inside loops?

```text
item
```

---

### What does item represent?

Current element being processed.

---

### Can loops be used with packages?

Yes.

---

### Can loops be used with users and directories?

Yes.

---

### Can loops be combined with conditions?

Yes.

Using:

```yaml
when:
```

---

### Which is preferred?

```text
loop
```

over

```text
with_items
```

---

# Memory Tricks

---

## loop

Think:

```text
Repeat Button
```

---

## item

Think:

```text
Current Object
```

---

## List

Think:

```text
Queue
```

---

Example:

```text
Queue

git
curl
wget
```

---

Loop processes:

```text
One

By

One
```

---

# 60-Second Revision Sheet

```text
Loop
=
Repeat Task

item
=
Current Value

Example:

packages

 - git
 - curl
 - wget

Task:

package:
  name: "{{ item }}"

Loop Executes:

git
curl
wget

Benefits:

Less Code

Easy Maintenance

Reusable

Common Uses:

Packages

Users

Directories

Firewall Rules

Modern Syntax:

loop

Legacy Syntax:

with_items

Flow:

List
 ↓
Item
 ↓
Run Task
 ↓
Next Item
 ↓
Finish
```

---

# One-Line Summary

```text
Ansible loops allow a single task to process multiple items by iterating through a list using the item variable, dramatically reducing repetitive code and making automation scalable, cleaner, and easier to maintain.
```

***

# Day 70 Master Notes — Part 6

# Register Variables & End-to-End Server Reporting Project

# How Ansible Remembers Things During Execution 🧠

---

# Big Picture

Until now Ansible could:

```text
Use Variables      ✅

Use Facts          ✅

Use Conditions     ✅

Use Loops          ✅
```

---

But there was a problem.

Imagine Ansible runs:

```bash
df -h
```

and receives:

```text
Filesystem      Size Used Avail

/dev/xvda1       8G   3G   5G
```

---

Question:

Can Ansible remember this output?

Without register:

```text
NO
```

Output appears.

Then disappears.

---

# The Problem

Imagine:

```text
Step 1

Check Disk Space
```

↓

```text
Step 2

Send Alert If Low
```

---

How can Step 2 know what happened in Step 1?

It needs memory.

---

# The Solution

```yaml
register:
```

---

# What Is register?

Think:

```text
register

=

Save output
into a variable
```

---

Real Life Analogy

Imagine a detective.

---

He asks:

```text
What is your name?
```

---

Person replies:

```text
Rahul
```

---

Detective writes it down.

Notebook:

```text
Name = Rahul
```

---

Later he can use it.

---

Ansible does the same.

---

# ASCII Mental Model

```text
Command

   │

   ▼

Output

   │

   ▼

register

   │

   ▼

Variable

   │

   ▼

Reuse Later
```

---

# First Register Example

Task:

```yaml
- name: Check hostname

  command: hostname

  register: hostname_output
```

---

What Happens?

Ansible runs:

```bash
hostname
```

---

Server returns:

```text
web-server
```

---

Ansible stores:

```text
hostname_output
```

---

Think:

```text
hostname_output

↓

web-server
```

---

# Important Concept

register does NOT store only text.

It stores:

```text
Complete Result Object
```

---

Think:

```text
Mini Report
```

containing lots of information.

---

# Internal Structure

Example:

```yaml
hostname_output:
```

contains:

```text
stdout

stderr

rc

changed

failed
```

---

ASCII

```text
hostname_output

 ├── stdout
 ├── stderr
 ├── rc
 ├── changed
 └── failed
```

---

# Understanding stdout

Most important field.

---

Example:

```bash
hostname
```

Output:

```text
web-server
```

---

Stored as:

```yaml
hostname_output.stdout
```

---

Think:

```text
stdout

=

Actual Output
```

---

# Using Registered Data

Task:

```yaml
debug:
  msg: "{{ hostname_output.stdout }}"
```

---

Output:

```text
web-server
```

---

Flow:

```text
Command

↓

Register

↓

stdout

↓

Debug
```

---

# Why stdout Matters

Most automation uses:

```text
stdout
```

because that's the useful result.

---

# Understanding stderr

Example:

```bash
wrong-command
```

Linux returns:

```text
command not found
```

---

Stored in:

```yaml
result.stderr
```

---

Think:

```text
stderr

=

Error Output
```

---

# Understanding rc

Very important interview question.

---

rc means:

```text
Return Code
```

---

Linux convention:

```text
0 = Success

Non-Zero = Failure
```

---

Example:

```bash
hostname
```

returns:

```text
rc = 0
```

---

Example:

```bash
wrong-command
```

returns:

```text
rc = 127
```

---

ASCII

```text
rc

0

↓

Success


Non-Zero

↓

Problem
```

---

# Why rc Is Useful

You can make decisions.

Example:

```yaml
when:
  disk_check.rc == 0
```

---

Meaning:

```text
Only continue
if command succeeded
```

---

# Real Example: Disk Check

Task:

```yaml
- name: Check disk

  command: df -h

  register: disk_output
```

---

Ansible runs:

```bash
df -h
```

---

Stores result.

---

Later:

```yaml
debug:
  msg: "{{ disk_output.stdout }}"
```

---

Output:

```text
Filesystem
Size
Used
Available
```

---

# Why Register Is Powerful

Without register:

```text
Run Command

See Output

Forget Output
```

---

With register:

```text
Run Command

Save Output

Reuse Output

Make Decisions
```

---

# The Server Reporting Project

This was the major project of Day 70.

---

Goal:

Create:

```text
Automatic Server Report
```

for every server.

---

Report should include:

```text
Hostname

OS

RAM

IP

Disk Information
```

---

Instead of manually logging into servers.

---

# Step 1

Gather Facts

---

Remember:

```yaml
ansible_hostname

ansible_distribution

ansible_memtotal_mb

ansible_default_ipv4.address
```

---

These already provide:

```text
Hostname

OS

RAM

IP
```

---

# Step 2

Collect Disk Information

Facts don't give the exact report format wanted.

So:

```yaml
command: df -h
```

---

Register output:

```yaml
register: disk_info
```

---

Now Ansible remembers:

```text
Disk Usage
```

---

# Visual Flow

```text
Facts

 ├── Hostname
 ├── OS
 ├── RAM
 └── IP

Command

 └── df -h

         │

         ▼

Register

         │

         ▼

disk_info
```

---

# Step 3

Generate Report

Task:

```yaml
debug:
  msg: |
    Hostname: {{ ansible_hostname }}

    OS: {{ ansible_distribution }}

    RAM: {{ ansible_memtotal_mb }}

    IP: {{ ansible_default_ipv4.address }}

    Disk:

    {{ disk_info.stdout }}
```

---

# What Happened?

Ansible merged:

```text
Facts
```

and

```text
Registered Data
```

into one report.

---

# ASCII Report

```text
=================================

Hostname: web-server

OS: Amazon Linux 2023

RAM: 911 MB

IP: 172.31.43.32

Disk:

/dev/xvda1  8G  3G  5G

=================================
```

---

# Why This Is Important

Imagine:

```text
500 Servers
```

---

Without Ansible:

```text
SSH

Run Commands

Copy Output

Create Report
```

500 times.

---

Nightmare.

---

With Ansible:

```text
Run Playbook Once
```

Get:

```text
500 Reports
```

Automatically.

---

# How Everything Connected

This project intentionally combined:

---

## Variables

Used for:

```text
Configuration Values
```

---

## Facts

Used for:

```text
Hostname

RAM

OS

IP
```

---

## Conditions

Used for:

```text
Run tasks only
on matching systems
```

---

## Loops

Used for:

```text
Multiple packages

Multiple users

Multiple directories
```

---

## Register

Used for:

```text
Store runtime output
```

---

# Full Automation Flow

```text
Playbook Starts

      │

      ▼

Gather Facts

      │

      ▼

Run Commands

      │

      ▼

Register Results

      │

      ▼

Apply Conditions

      │

      ▼

Generate Report

      │

      ▼

Display Output
```

---

# Real DevOps Example

Imagine production servers.

---

Need:

```text
Disk Usage

Memory Usage

Running Services

CPU Load
```

---

Commands run:

```bash
df -h

free -m

systemctl

uptime
```

---

Each registered:

```yaml
register:
```

---

Combined into:

```text
Health Report
```

---

Exactly the same concept.

---

# Register + Condition

Very common pattern.

---

Task:

```yaml
- name: Check service

  command: systemctl is-active nginx

  register: nginx_status
```

---

Then:

```yaml
when:
  nginx_status.stdout == "active"
```

---

Meaning:

```text
Only continue
if nginx is running
```

---

ASCII

```text
Command

↓

active

↓

register

↓

Condition

↓

TRUE

↓

Run Next Task
```

---

# Register + Loop

Another advanced pattern.

---

Loop:

```yaml
packages:

 - git
 - curl
 - wget
```

---

Task:

```yaml
package:
  name: "{{ item }}"

loop:
  "{{ packages }}"

register: package_result
```

---

Now Ansible stores:

```text
Result Of Every Iteration
```

---

Useful for:

```text
Auditing

Reporting

Troubleshooting
```

---

# Common Mistakes

---

## Mistake 1

Using register variable before it exists.

Wrong:

```yaml
debug:
  msg: "{{ disk_info.stdout }}"
```

before:

```yaml
register: disk_info
```

---

Result:

```text
Undefined Variable Error
```

---

## Mistake 2

Forgetting stdout

Wrong:

```yaml
{{ disk_info }}
```

---

Produces huge structure.

---

Usually use:

```yaml
{{ disk_info.stdout }}
```

---

## Mistake 3

Using rc incorrectly

Wrong:

```yaml
when:
  result.rc
```

---

Better:

```yaml
when:
  result.rc == 0
```

---

# Interview Questions

### What does register do?

Stores task output into a variable.

---

### Does register create a normal variable?

Yes, but it contains a detailed result object.

---

### What is stdout?

Standard output produced by a command.

---

### What is stderr?

Error output produced by a command.

---

### What is rc?

Return code.

```text
0 = Success

Non-Zero = Failure
```

---

### Why use register?

To capture runtime data and use it later in the playbook.

---

### Can register be used with conditions?

Yes.

Very common.

---

### Can register be used with loops?

Yes.

Stores results from loop iterations.

---

# Memory Tricks

---

## register

Think:

```text
Notebook
```

Ansible writes information for later use.

---

## stdout

Think:

```text
Answer
```

returned by command.

---

## stderr

Think:

```text
Error Message
```

---

## rc

Think:

```text
Exam Result

0 = Pass

Non-Zero = Fail
```

---

# 60-Second Revision Sheet

```text
register
=
Store Output

Command
 ↓
Register
 ↓
Variable

Important Fields:

stdout
=
Normal Output

stderr
=
Error Output

rc
=
Return Code

0
=
Success

Non-Zero
=
Failure

Common Pattern:

command
 ↓
register
 ↓
when
 ↓
decision

Project:

Facts
+
Register
+
Conditions
+
Loops
=
Server Report
```

---

# Day 70 Final Architecture

```text
VARIABLES
     │
     ▼
FACTS
     │
     ▼
CONDITIONS
     │
     ▼
LOOPS
     │
     ▼
REGISTER
     │
     ▼
AUTOMATION REPORTING
```

---

# One-Line Summary

```text
Register variables allow Ansible to capture and remember command results during playbook execution, enabling dynamic decision-making, reporting, validation, and advanced automation workflows that combine variables, facts, loops, and conditionals into real-world operational solutions.
```

***

# Day 70 — Part 7

# Interview Notes (High-Value Q&A)

# Ansible Variables → Facts → Conditions → Loops → Register

---

# Section 1: Variables

---

## Q1. What are variables in Ansible?

Variables are placeholders used to store values that can be reused throughout a playbook.

Example:

```yaml
app_name: nginx
```

Usage:

```yaml
{{ app_name }}
```

---

## Q2. Why do we use variables?

To avoid hardcoding values.

Instead of:

```yaml
name: nginx
```

Use:

```yaml
name: "{{ app_name }}"
```

Benefits:

```text
Reusable
Maintainable
Flexible
```

---

## Q3. What are different ways to define variables?

```text
vars section

group_vars

host_vars

inventory

extra vars (-e)

facts

registered variables
```

---

## Q4. How do you access a variable?

Using Jinja2 syntax:

```yaml
{{ variable_name }}
```

Example:

```yaml
{{ app_env }}
```

---

## Q5. What happens if a variable is not defined?

Ansible throws:

```text
Undefined Variable Error
```

---

# Section 2: group_vars & host_vars

---

## Q6. What is group_vars?

Variables shared by a group of hosts.

Example:

```text
group_vars/web.yml
```

Applies to:

```text
All web servers
```

---

## Q7. What is host_vars?

Variables specific to one host.

Example:

```text
host_vars/web-server.yml
```

Applies only to:

```text
web-server
```

---

## Q8. What is group_vars/all.yml?

Variables available to every host in inventory.

Think:

```text
Global Variables
```

---

## Q9. Which has higher precedence?

```text
host_vars

beats

group_vars
```

---

## Q10. Which has highest precedence?

```bash
-e
```

Extra vars.

Example:

```bash
ansible-playbook site.yml \
-e "app_env=prod"
```

---

## Q11. Why use group_vars?

Centralized configuration management.

Example:

```text
100 web servers

Same HTTP port
Same packages
Same settings
```

Store once.

---

# Section 3: Facts

---

## Q12. What are Ansible Facts?

Automatically discovered information about managed hosts.

Examples:

```text
Hostname

OS

IP Address

Memory

CPU
```

---

## Q13. Which module gathers facts?

```text
setup
```

module.

---

## Q14. How can you view facts?

```bash
ansible web-server \
-m setup
```

---

## Q15. How can you filter facts?

```bash
ansible web-server \
-m setup \
-a "filter=ansible_distribution"
```

---

## Q16. What is ansible_distribution?

Linux distribution name.

Example:

```text
Ubuntu

Amazon

CentOS

RHEL
```

---

## Q17. What is ansible_distribution_version?

Operating system version.

Example:

```text
22.04

20.04

2023
```

---

## Q18. What is ansible_hostname?

Hostname of the managed server.

---

## Q19. What is ansible_default_ipv4.address?

Primary IP address of the server.

---

## Q20. What is ansible_memtotal_mb?

Total RAM available on the host.

---

## Q21. Why are facts useful?

Facts make automation dynamic.

Instead of hardcoding:

```text
Ubuntu
```

Ansible discovers:

```text
Actual OS
```

and adapts automatically.

---

# Section 4: Conditionals

---

## Q22. What is a conditional?

A mechanism that allows tasks to run only when a condition is true.

Keyword:

```yaml
when:
```

---

## Q23. What happens if condition is false?

Task becomes:

```text
Skipped
```

---

## Q24. Is skipped considered failure?

No.

```text
Skipped
=
Expected behavior
```

---

## Q25. Give a simple conditional example.

```yaml
when:
  app_env == "production"
```

---

## Q26. Can facts be used inside when?

Yes.

Example:

```yaml
when:
  ansible_distribution == "Amazon"
```

---

## Q27. Why use conditionals?

To execute tasks only on appropriate servers.

Example:

```text
Install nginx only on web servers.
```

---

## Q28. How do you perform AND logic?

```yaml
when:
  ansible_distribution == "Amazon"
  and
  ansible_memtotal_mb >= 1024
```

---

## Q29. How do you perform OR logic?

```yaml
when:
  ansible_distribution == "Amazon"
  or
  ansible_distribution == "Ubuntu"
```

---

## Q30. What does this mean?

```yaml
when:
  inventory_hostname
  in groups['web']
```

Answer:

```text
Run only on hosts
belonging to web group.
```

---

# Section 5: Loops

---

## Q31. What is a loop?

A loop executes the same task multiple times using different values.

Keyword:

```yaml
loop:
```

---

## Q32. Why use loops?

To eliminate repetitive tasks.

Instead of:

```text
50 tasks
```

Use:

```text
1 task + loop
```

---

## Q33. What is item?

Special loop variable.

Represents:

```text
Current value
being processed
```

---

## Q34. Example of item?

List:

```yaml
packages:
  - git
  - curl
  - wget
```

Iterations:

```text
item=git

item=curl

item=wget
```

---

## Q35. How do you install multiple packages?

```yaml
package:
  name: "{{ item }}"

loop:
  "{{ packages }}"
```

---

## Q36. Can loops create users?

Yes.

Example:

```yaml
user:
  name: "{{ item }}"
```

---

## Q37. Can loops create directories?

Yes.

Using:

```yaml
file:
```

module.

---

## Q38. Can loops be combined with conditions?

Yes.

Example:

```yaml
loop:
  "{{ packages }}"

when:
  ansible_distribution == "Amazon"
```

---

## Q39. What is the modern replacement for with_items?

```text
loop
```

---

## Q40. Which is preferred?

```text
loop
```

---

# Section 6: Register Variables

---

## Q41. What does register do?

Stores task output into a variable.

---

## Q42. Why use register?

To capture runtime information and reuse it later.

---

## Q43. Example?

```yaml
command: hostname

register: hostname_output
```

---

## Q44. What is stdout?

Actual command output.

Example:

```yaml
hostname_output.stdout
```

---

## Q45. What is stderr?

Error output produced by command execution.

---

## Q46. What is rc?

Return code.

```text
0 = Success

Non-Zero = Failure
```

---

## Q47. Why is rc important?

Used to determine if command execution succeeded.

Example:

```yaml
when:
  result.rc == 0
```

---

## Q48. Can registered variables be used in conditions?

Yes.

Example:

```yaml
when:
  nginx_status.stdout == "active"
```

---

## Q49. Can register be used with loops?

Yes.

Ansible stores results for every iteration.

---

## Q50. What is the most common mistake with register?

Forgetting:

```yaml
.stdout
```

and printing the entire result structure.

---

# Scenario-Based Questions

---

## Q51. You have 100 web servers. Where should common nginx settings be stored?

Answer:

```text
group_vars/web.yml
```

---

## Q52. One web server needs a different port. Where should that value be stored?

Answer:

```text
host_vars/server-name.yml
```

---

## Q53. You need to install packages only on Amazon Linux servers. What would you use?

Answer:

```yaml
when:
  ansible_distribution == "Amazon"
```

---

## Q54. You need to install 20 packages. Which feature should be used?

Answer:

```text
loop
```

---

## Q55. You need to save output from:

```bash
df -h
```

for later processing.

Answer:

```yaml
register:
```

---

## Q56. You need to check if nginx is running before executing another task.

Answer:

```yaml
register: nginx_status

when:
  nginx_status.stdout == "active"
```

---

## Q57. You need OS, RAM, hostname, and IP information automatically.

Answer:

```text
Facts
```

collected by:

```text
setup module
```

---

# Most Important Interview Question

## Q58. Explain the complete flow of Day 70.

```text
Variables
     ↓

group_vars / host_vars
     ↓

Facts
     ↓

Conditionals
     ↓

Loops
     ↓

Register
     ↓

Dynamic Automation
```

---

### Sample Interview Answer

> Ansible variables provide reusable configuration values. group_vars and host_vars allow centralized and host-specific configuration management. Facts automatically collect system information like OS, RAM, hostname, and IP. Conditionals (`when`) use those variables and facts to make decisions. Loops eliminate repetitive tasks by processing multiple items with a single task. Register variables capture runtime command output for later use. Together, these features enable dynamic, scalable, and intelligent automation across large server environments.

---

# Day 70 Interview Cheat Sheet

```text
Variables
=
Reusable values

group_vars
=
Group settings

host_vars
=
Host settings

Facts
=
Auto-discovered server info

setup
=
Fact gathering module

when
=
Conditional execution

loop
=
Repeat task

item
=
Current loop value

register
=
Store output

stdout
=
Command output

stderr
=
Error output

rc
=
Return code

0
=
Success

host_vars
>
group_vars

-e
=
Highest precedence
```

---

# One Interview Line That Impresses

```text
Ansible becomes truly powerful when variables, facts, conditionals, loops, and register variables are combined to create self-aware automation that adapts dynamically to each server instead of relying on hardcoded configurations.
```

***

# Day 70 — Ultimate 1-Minute Revision Sheet

# Variables → Facts → Conditionals → Loops → Register

---

# 1. Variables

```text
Purpose:
Store reusable values

Example:

app_name: nginx

Usage:

{{ app_name }}
```

Think:

```text
Variable
=
Label on a Box
```

Store once.

Use many times.

---

# 2. Variable Sources

```text
Playbook Vars

group_vars

host_vars

Facts

Register Variables

Extra Vars (-e)
```

---

# 3. group_vars vs host_vars

```text
group_vars
=
Variables for a group

host_vars
=
Variables for one host
```

Example:

```text
group_vars/web.yml

http_port=80
```

Applies to:

```text
All Web Servers
```

---

Example:

```text
host_vars/web-server.yml

http_port=8080
```

Applies to:

```text
Only web-server
```

---

# Memory Trick

```text
group_vars

↓

Department Rule


host_vars

↓

Special Employee Rule
```

---

# 4. Variable Precedence

```text
Highest

Extra Vars (-e)

       ↓

host_vars

       ↓

group_vars

       ↓

Playbook Vars

Lowest
```

---

# 5. Facts

```text
Facts
=
Automatically discovered
server information
```

Collected by:

```text
setup module
```

Command:

```bash
ansible host -m setup
```

---

# Important Facts

```text
ansible_distribution
=
OS

ansible_distribution_version
=
OS Version

ansible_hostname
=
Hostname

ansible_default_ipv4.address
=
IP Address

ansible_memtotal_mb
=
RAM
```

---

# Memory Trick

```text
Facts

=

Server Biography
```

---

# 6. Conditionals

Keyword:

```yaml
when:
```

Meaning:

```text
IF Statement
```

---

Example

```yaml
when:
  ansible_distribution == "Amazon"
```

Meaning:

```text
Run only on
Amazon Linux
```

---

Flow

```text
Condition

   │

   ▼

TRUE ?

 ├── YES → Run

 └── NO  → Skip
```

---

# AND

```yaml
when:
  ansible_distribution == "Amazon"
  and
  ansible_memtotal_mb >= 1024
```

Both must be true.

---

# OR

```yaml
when:
  ansible_distribution == "Amazon"
  or
  ansible_distribution == "Ubuntu"
```

One must be true.

---

# Remember

```text
Skipped

≠

Failed
```

Skipped means:

```text
Condition False
```

---

# 7. Loops

Keyword:

```yaml
loop:
```

Purpose:

```text
Repeat Task
```

---

Example

```yaml
packages:

 - git
 - curl
 - wget
```

---

Task

```yaml
package:
  name: "{{ item }}"

loop:
  "{{ packages }}"
```

---

Execution

```text
item=git

item=curl

item=wget
```

---

# Memory Trick

```text
loop

=

Repeat Button


item

=

Current Object
```

---

# Common Uses

```text
Packages

Users

Directories

Firewall Rules
```

---

# Modern vs Legacy

```text
loop
=
Preferred

with_items
=
Legacy
```

---

# 8. Register Variables

Keyword:

```yaml
register:
```

Purpose:

```text
Store Output
```

---

Example

```yaml
command: hostname

register: host_result
```

---

Access Output

```yaml
{{ host_result.stdout }}
```

---

# Important Fields

```text
stdout
=
Normal Output

stderr
=
Error Output

rc
=
Return Code
```

---

# Return Codes

```text
0
=
Success

Non-Zero
=
Failure
```

---

Example

```yaml
when:
  result.rc == 0
```

Meaning:

```text
Continue only
if command succeeded
```

---

# Register Flow

```text
Command

   │

   ▼

Output

   │

   ▼

register

   │

   ▼

Variable

   │

   ▼

Reuse Later
```

---

# 9. End-to-End Reporting Project

Collected:

```text
Hostname

OS

RAM

IP

Disk Usage
```

Using:

```text
Facts

+

Register Variables
```

---

Flow

```text
Gather Facts

      │

      ▼

Run Command

      │

      ▼

Register Output

      │

      ▼

Generate Report
```

---

# Complete Day 70 Architecture

```text
                 VARIABLES

                      │

                      ▼

         group_vars / host_vars

                      │

                      ▼

                   FACTS

                      │

                      ▼

                CONDITIONS

                    when

                      │

                      ▼

                   LOOPS

                    loop

                      │

                      ▼

                  REGISTER

                      │

                      ▼

            DYNAMIC AUTOMATION

                      │

                      ▼

             SERVER REPORTING
```

---

# 30-Second Interview Revision

```text
Variables
=
Reusable values

group_vars
=
Group configuration

host_vars
=
Host configuration

Facts
=
Auto-discovered server data

setup
=
Fact gathering module

when
=
Conditional execution

loop
=
Repeat task

item
=
Current loop value

register
=
Store command output

stdout
=
Actual output

stderr
=
Error output

rc
=
Return code

0
=
Success

host_vars > group_vars

-e
=
Highest precedence
```

---

# Entire Day 70 In One Sentence

```text
Day 70 teaches how Ansible stores configuration using variables, discovers server information using facts, makes decisions with conditionals, eliminates repetition using loops, captures runtime results with register variables, and combines all of them to build intelligent, scalable, self-aware automation and server reporting workflows.
```

---

# Master Memory Diagram

```text
                ANSIBLE DAY 70

┌───────────────────────────────────────┐
│ Variables = Store Values              │
└───────────────────────────────────────┘
                  │
                  ▼
┌───────────────────────────────────────┐
│ group_vars = Group Settings           │
│ host_vars  = Host Settings            │
└───────────────────────────────────────┘
                  │
                  ▼
┌───────────────────────────────────────┐
│ Facts = Discover Server Info          │
│ OS | RAM | IP | Hostname              │
└───────────────────────────────────────┘
                  │
                  ▼
┌───────────────────────────────────────┐
│ when = Make Decisions                 │
│ IF True → Run                         │
│ IF False → Skip                       │
└───────────────────────────────────────┘
                  │
                  ▼
┌───────────────────────────────────────┐
│ loop = Repeat Tasks                   │
│ item = Current Value                  │
└───────────────────────────────────────┘
                  │
                  ▼
┌───────────────────────────────────────┐
│ register = Remember Output            │
│ stdout | stderr | rc                  │
└───────────────────────────────────────┘
                  │
                  ▼
┌───────────────────────────────────────┐
│ Dynamic Automation & Reporting        │
└───────────────────────────────────────┘
```

***********************


# Day 70 – Variables, Facts, Conditionals, Loops and Registers

---

# Task 1: Variables in Playbooks

## Create variables-demo.yml

```yaml
---
- name: Variable demo
  hosts: all
  become: true

  vars:
    app_name: terraweek-app
    app_port: 8080
    app_dir: "/opt/{{ app_name }}"
    packages:
      - git
      - curl
      - wget

  tasks:
    - name: Print app details
      debug:
        msg: "Deploying {{ app_name }} on port {{ app_port }} to {{ app_dir }}"

    - name: Create application directory
      file:
        path: "{{ app_dir }}"
        state: directory
        mode: '0755'

    - name: Install required packages
      yum:
        name: "{{ packages }}"
        state: present
```

---

## Variable Resolution

```yaml
app_dir: "/opt/{{ app_name }}"
```

resolves automatically to:

```text
/opt/terraweek-app
```

because Ansible resolves:

```yaml
{{ app_name }}
```

before executing tasks.

---

## Verify Directory Creation

```bash
ls -ld /opt/terraweek-app
```

Expected:

```text
drwxr-xr-x ...
```

---

## Verify Package Installation

```bash
rpm -q git curl wget
```

or

```bash
which git curl wget
```

---

## Amazon Linux 2023 Package Conflict

Issue:

```text
curl-minimal conflicts with curl
```

Reason:

Amazon Linux 2023 already contains:

```text
curl-minimal
```

Removing:

```yaml
curl
```

from the package list resolves the issue.

Alternative:

```yaml
- name: Install required packages
  package:
    name: "{{ packages }}"
    state: present
```

---

## Override Variables from CLI

Command:

```bash
ansible-playbook variables-demo.yml \
-e "app_name=my-custom-app app_port=9090"
```

Variables become:

```yaml
app_name: my-custom-app
app_port: 9090
```

Since:

```yaml
app_dir: "/opt/{{ app_name }}"
```

Ansible recalculates:

```text
/opt/my-custom-app
```

Expected output:

```text
TASK [Print app details]

ok: [web-server] => {
    "msg": "Deploying my-custom-app on port 9090 to /opt/my-custom-app"
}
```

---

## Verification

Check:

```bash
ls -ld /opt/my-custom-app
```

Expected:

```text
drwxr-xr-x ...
```

---

## CLI Variable Precedence

Playbook values:

```yaml
app_name: terraweek-app
app_port: 8080
```

CLI values:

```bash
-e "app_name=my-custom-app app_port=9090"
```

Final values:

```text
app_name = my-custom-app
app_port = 9090
```

Result:

| Source           | app_name      |
| ---------------- | ------------- |
| Playbook         | terraweek-app |
| CLI (-e)         | my-custom-app |
| Final Value Used | my-custom-app |

Conclusion:

```text
CLI variables (-e) override playbook variables.
```

---

## Task 1 Verification Summary

Verified:

* app_name resolved to terraweek-app
* app_dir resolved dynamically to /opt/terraweek-app
* Directory /opt/terraweek-app created successfully
* git installed successfully
* wget installed successfully
* curl conflict documented
* CLI override tested successfully

Result:

```bash
ansible-playbook variables-demo.yml \
-e "app_name=my-custom-app app_port=9090"
```

Output:

```text
Deploying my-custom-app on port 9090 to /opt/my-custom-app
```

Conclusion:

```text
Extra vars (-e) have higher precedence than playbook variables.
```

---



Absolutely. For DevOps/Ansible learning, don't memorize the YAML. Understand **what each line is doing and why**.

Let's take your playbook and explain it like an interviewer would expect.

---

# Full Playbook

```yaml
---
- name: Variable demo
  hosts: all
  become: true

  vars:
    app_name: terraweek-app
    app_port: 8080
    app_dir: "/opt/{{ app_name }}"
    packages:
      - git
      - curl
      - wget

  tasks:
    - name: Print app details
      debug:
        msg: "Deploying {{ app_name }} on port {{ app_port }} to {{ app_dir }}"

    - name: Create application directory
      file:
        path: "{{ app_dir }}"
        state: directory
        mode: '0755'

    - name: Install required packages
      yum:
        name: "{{ packages }}"
        state: present
```

---

# Line 1

```yaml
---
```

### What is this?

YAML document separator.

Think of it as:

```text
Start of YAML file
```

Not mandatory in every file but recommended.

---

# Line 2

```yaml
- name: Variable demo
```

### What is this?

Play name.

Displayed when playbook runs.

Output:

```text
PLAY [Variable demo]
```

### Why use it?

Easy identification.

Large companies may have hundreds of playbooks.

---

# Line 3

```yaml
hosts: all
```

### Meaning

Run this playbook on:

```text
all servers
```

from inventory.

Example inventory:

```ini
[web]
server1

[db]
server2
```

Then:

```yaml
hosts: all
```

means

```text
server1
server2
```

---

# Line 4

```yaml
become: true
```

### Meaning

Run commands as:

```bash
sudo
```

Equivalent:

```bash
sudo mkdir /opt/app
```

instead of

```bash
mkdir /opt/app
```

### Why?

Normal user cannot:

```text
Install packages
Create system directories
Modify system files
```

---

# Variables Section

```yaml
vars:
```

### Meaning

Declare variables.

Similar to Java:

```java
String appName="terraweek-app";
```

---

# Variable 1

```yaml
app_name: terraweek-app
```

Store:

```text
terraweek-app
```

inside variable:

```text
app_name
```

---

# Variable 2

```yaml
app_port: 8080
```

Store:

```text
8080
```

inside variable:

```text
app_port
```

---

# Variable 3

```yaml
app_dir: "/opt/{{ app_name }}"
```

This is important.

### Ansible sees:

```yaml
app_name: terraweek-app
```

So it replaces:

```yaml
{{ app_name }}
```

with:

```text
terraweek-app
```

Result:

```text
/opt/terraweek-app
```

---

# Variable 4

```yaml
packages:
```

Creating a list.

```yaml
packages:
  - git
  - curl
  - wget
```

Equivalent:

```java
List<String> packages =
["git","curl","wget"];
```

---

# Tasks Section

```yaml
tasks:
```

### Meaning

Actual work starts here.

Think:

```text
Step 1
Step 2
Step 3
```

---

# Task 1

```yaml
- name: Print app details
```

Task name.

Shown during execution.

Output:

```text
TASK [Print app details]
```

---

# Debug Module

```yaml
debug:
```

Used to print values.

Like:

```java
System.out.println();
```

---

# Message

```yaml
msg: "Deploying {{ app_name }} on port {{ app_port }} to {{ app_dir }}"
```

Variables replaced.

Before:

```yaml
{{ app_name }}
{{ app_port }}
{{ app_dir }}
```

After:

```text
Deploying terraweek-app on port 8080 to /opt/terraweek-app
```

Output:

```text
ok: [server1] =>

msg:
Deploying terraweek-app on port 8080 to /opt/terraweek-app
```

---

# Task 2

```yaml
- name: Create application directory
```

Task title.

---

# File Module

```yaml
file:
```

Used for:

```text
Creating directories
Deleting directories
Creating files
Changing permissions
```

---

# Path

```yaml
path: "{{ app_dir }}"
```

Variable value:

```text
/opt/terraweek-app
```

Final:

```yaml
path: /opt/terraweek-app
```

---

# State

```yaml
state: directory
```

Meaning:

```text
Ensure this path is a directory
```

Equivalent Linux:

```bash
mkdir -p /opt/terraweek-app
```

---

# Mode

```yaml
mode: '0755'
```

Permissions.

```text
Owner = rwx = 7
Group = r-x = 5
Others = r-x = 5
```

Equivalent:

```bash
chmod 755 /opt/terraweek-app
```

Visualization:

```text
Owner   Group   Others

rwx     r-x     r-x
111     101     101

7       5       5
```

---

# Task 3

```yaml
- name: Install required packages
```

Task title.

---

# Yum Module

```yaml
yum:
```

Package installation module.

Equivalent Linux:

```bash
yum install
```

or

```bash
dnf install
```

depending on OS.

---

# Package Names

```yaml
name: "{{ packages }}"
```

Variable contains:

```yaml
packages:
  - git
  - curl
  - wget
```

Ansible expands to:

```yaml
name:
  - git
  - curl
  - wget
```

---

# State Present

```yaml
state: present
```

Meaning:

```text
Package must exist
```

If installed:

```text
Do nothing
```

If not installed:

```text
Install it
```

This is called:

```text
Idempotency
```

Very important Ansible interview question.

---

# Internal Flow Diagram

```text
Playbook Starts
       |
       V
Load Inventory
       |
       V
Connect to Server
       |
       V
Load Variables
       |
       +--> app_name = terraweek-app
       |
       +--> app_port = 8080
       |
       +--> app_dir = /opt/terraweek-app
       |
       V
Task 1
Print Message
       |
       V
Task 2
Create Directory
/opt/terraweek-app
       |
       V
Task 3
Install Packages
git
curl
wget
       |
       V
Playbook Complete
```

### Interview Question

If I run:

```bash
ansible-playbook variables-demo.yml \
-e "app_name=my-app"
```

Can you predict:

1. What will `app_name` become?
2. What will `app_dir` become?
3. What message will `debug` print?

Try answering before I explain CLI variable precedence.

***








## Part B – Task 2: Using `group_vars` and `host_vars`

---

# Objective

Learn how Ansible automatically loads variables based on:

```text
Group
Host
```

instead of defining everything inside the playbook.

---

# Why Use group_vars and host_vars?

Suppose:

```text
10 Web Servers
5 Database Servers
```

All web servers use:

```text
Port = 8080
Environment = Production
```

All database servers use:

```text
Port = 3306
Environment = Production
```

Instead of repeating variables in every playbook, store them separately.

---

# Directory Structure

```text
ansible-project/
│
├── inventory.ini
│
├── group_vars/
│   ├── web.yml
│   └── db.yml
│
├── host_vars/
│   ├── web-server.yml
│   └── db-server.yml
│
└── group-vars-demo.yml
```

---

# Inventory File

## inventory.ini

```ini
[web]
web-server

[db]
db-server
```

---

# Create Group Variables

## group_vars/web.yml

```yaml
app_port: 8080
environment: production
app_type: web
```

---

## group_vars/db.yml

```yaml
db_port: 3306
db_engine: mysql
environment: production
```

---

# Create Host Variables

## host_vars/web-server.yml

```yaml
server_owner: devops-team
server_location: mumbai
```

---

## host_vars/db-server.yml

```yaml
server_owner: database-team
server_location: pune
```

---

# Create Playbook

## group-vars-demo.yml

```yaml
---
- name: Group Vars Demo
  hosts: all

  tasks:

    - name: Show server information
      debug:
        msg:
          - "Host: {{ inventory_hostname }}"
          - "Owner: {{ server_owner }}"
          - "Location: {{ server_location }}"
```

---

# Run Playbook

```bash
ansible-playbook -i inventory.ini group-vars-demo.yml
```

---

# Expected Output

For web server:

```text
Host: web-server
Owner: devops-team
Location: mumbai
```

For database server:

```text
Host: db-server
Owner: database-team
Location: pune
```

---

# How Ansible Finds Variables

When running:

```bash
ansible-playbook group-vars-demo.yml
```

Ansible checks:

```text
1. Inventory
2. group_vars
3. host_vars
4. Playbook vars
5. Extra vars
```

automatically.

---

# Variable Loading Flow

```text
Playbook Starts
       |
       V
Read Inventory
       |
       +--> web-server
       |
       +--> db-server
       |
       V
Load group_vars
       |
       +--> web.yml
       |
       +--> db.yml
       |
       V
Load host_vars
       |
       +--> web-server.yml
       |
       +--> db-server.yml
       |
       V
Execute Tasks
```

---

# Display Group Variables

Modify playbook:

```yaml
---
- name: Group Variable Demo
  hosts: web

  tasks:

    - name: Show group variables
      debug:
        msg:
          - "App Port: {{ app_port }}"
          - "Environment: {{ environment }}"
          - "Application Type: {{ app_type }}"
```

---

# Run

```bash
ansible-playbook -i inventory.ini group-vars-demo.yml
```

---

# Output

```text
App Port: 8080
Environment: production
Application Type: web
```

---

# Database Group Example

```yaml
---
- name: Database Variable Demo
  hosts: db

  tasks:

    - name: Show database variables
      debug:
        msg:
          - "Database Port: {{ db_port }}"
          - "Database Engine: {{ db_engine }}"
          - "Environment: {{ environment }}"
```

---

# Output

```text
Database Port: 3306
Database Engine: mysql
Environment: production
```

---

# Variable Precedence Example

Suppose:

## group_vars/web.yml

```yaml
environment: production
```

---

## host_vars/web-server.yml

```yaml
environment: testing
```

---

When playbook runs:

```yaml
- debug:
    msg: "{{ environment }}"
```

Output:

```text
testing
```

Reason:

```text
host_vars
has higher precedence than
group_vars
```

---

# Precedence Order

```text
Lowest Priority

group_vars
      ↓
host_vars
      ↓
playbook vars
      ↓
extra vars (-e)

Highest Priority
```

---

# Verification Commands

Check inventory:

```bash
ansible-inventory -i inventory.ini --list
```

---

View merged host variables:

```bash
ansible-inventory -i inventory.ini --host web-server
```

---

Expected:

```json
{
  "app_port": 8080,
  "environment": "production",
  "server_owner": "devops-team",
  "server_location": "mumbai"
}
```

---

# Task 2 Summary

Created:

```text
group_vars/web.yml
group_vars/db.yml
host_vars/web-server.yml
host_vars/db-server.yml
```

Verified:

```text
Group variables loaded automatically
Host variables loaded automatically
Host variables override group variables
Inventory lookup works successfully
```

***

# Part C – Task 3: Facts and Gathering System Information

---

# Objective

Learn how Ansible automatically collects information about managed hosts using:

```text
Facts
```

Facts provide details about:

```text
Operating System
IP Address
Hostname
Memory
CPU
Disk
Kernel
Architecture
Network Interfaces
```

---

# What are Facts?

Facts are system information collected automatically by Ansible before tasks run.

Example:

```text
Hostname = web-server
OS = Amazon Linux
RAM = 4 GB
IP = 172.31.10.20
CPU = 2 vCPU
```

Ansible stores these values in variables called:

```text
ansible_facts
```

---

# Verify Fact Gathering

Create:

## facts-demo.yml

```yaml
---
- name: Facts Demo
  hosts: all

  tasks:

    - name: Show hostname
      debug:
        msg: "{{ ansible_hostname }}"

    - name: Show operating system
      debug:
        msg: "{{ ansible_distribution }}"

    - name: Show IP address
      debug:
        msg: "{{ ansible_default_ipv4.address }}"
```

---

# Run Playbook

```bash
ansible-playbook -i inventory.ini facts-demo.yml
```

---

# Expected Output

```text
TASK [Show hostname]

ok: [web-server] => {
    "msg": "ip-172-31-10-20"
}
```

---

```text
TASK [Show operating system]

ok: [web-server] => {
    "msg": "Amazon"
}
```

---

```text
TASK [Show IP address]

ok: [web-server] => {
    "msg": "172.31.10.20"
}
```

---

# Display All Facts

Create:

```yaml
---
- name: Display all facts
  hosts: all

  tasks:

    - name: Print all facts
      debug:
        var: ansible_facts
```

---

# Run

```bash
ansible-playbook facts-demo.yml
```

---

# Sample Output

```text
ansible_facts:
  architecture: x86_64
  hostname: ip-172-31-10-20
  distribution: Amazon
  distribution_version: "2023"
  kernel: 6.1.0
  memory_mb:
    real:
      total: 3800
```

---

# Useful Facts

## Hostname

```yaml
{{ ansible_hostname }}
```

Example:

```text
ip-172-31-10-20
```

---

## FQDN

```yaml
{{ ansible_fqdn }}
```

Example:

```text
ip-172-31-10-20.ec2.internal
```

---

## Operating System

```yaml
{{ ansible_distribution }}
```

Example:

```text
Amazon
```

---

## OS Version

```yaml
{{ ansible_distribution_version }}
```

Example:

```text
2023
```

---

## Kernel Version

```yaml
{{ ansible_kernel }}
```

Example:

```text
6.1.0
```

---

## Architecture

```yaml
{{ ansible_architecture }}
```

Example:

```text
x86_64
```

---

## Memory

```yaml
{{ ansible_memtotal_mb }}
```

Example:

```text
3800
```

---

## Processor Count

```yaml
{{ ansible_processor_vcpus }}
```

Example:

```text
2
```

---

## IP Address

```yaml
{{ ansible_default_ipv4.address }}
```

Example:

```text
172.31.10.20
```

---

# Create System Report

```yaml
---
- name: System Information Report
  hosts: all

  tasks:

    - name: Print system details
      debug:
        msg:
          - "Hostname: {{ ansible_hostname }}"
          - "OS: {{ ansible_distribution }}"
          - "Version: {{ ansible_distribution_version }}"
          - "Kernel: {{ ansible_kernel }}"
          - "Memory: {{ ansible_memtotal_mb }} MB"
          - "IP: {{ ansible_default_ipv4.address }}"
```

---

# Output

```text
Hostname: ip-172-31-10-20
OS: Amazon
Version: 2023
Kernel: 6.1.0
Memory: 3800 MB
IP: 172.31.10.20
```

---

# Facts Gathering Process

```text
Playbook Starts
       |
       V
Connect to Host
       |
       V
Run Setup Module
       |
       V
Collect Facts
       |
       +--> Hostname
       |
       +--> OS
       |
       +--> Memory
       |
       +--> CPU
       |
       +--> IP
       |
       V
Store in ansible_facts
       |
       V
Execute Tasks
```

---

# Setup Module

Ansible collects facts using:

```yaml
setup:
```

---

Example:

```yaml
---
- name: Gather Facts
  hosts: all

  tasks:

    - name: Collect facts
      setup:
```

---

# Run Ad-Hoc Command

```bash
ansible all -m setup
```

---

# Display Only Hostname Facts

```bash
ansible all -m setup -a "filter=ansible_hostname"
```

---

# Output

```text
ansible_hostname: ip-172-31-10-20
```

---

# Display Only Network Facts

```bash
ansible all -m setup -a "filter=ansible_default_ipv4"
```

---

# Output

```text
ansible_default_ipv4:
  address: 172.31.10.20
```

---

# Disable Fact Gathering

By default:

```yaml
gather_facts: true
```

---

Disable:

```yaml
---
- name: No Facts Example
  hosts: all
  gather_facts: false

  tasks:

    - name: Print message
      debug:
        msg: "Facts disabled"
```

---

# Why Disable Facts?

If a playbook:

```text
Does not need OS details
Does not need IP information
Does not need hardware information
```

Fact gathering can be skipped to improve execution speed.

---

# Example

```yaml
---
- name: Install Git
  hosts: all
  gather_facts: false

  tasks:

    - name: Install git
      yum:
        name: git
        state: present
```

---

# Verification Commands

Show all facts:

```bash
ansible all -m setup
```

---

Show hostname:

```bash
ansible all -m setup -a "filter=ansible_hostname"
```

---

Show operating system:

```bash
ansible all -m setup -a "filter=ansible_distribution*"
```

---

Show IP address:

```bash
ansible all -m setup -a "filter=ansible_default_ipv4"
```

---

# Task 3 Summary

Verified:

```text
Facts gathered automatically
Hostname retrieved successfully
Operating system retrieved successfully
IP address retrieved successfully
Memory information retrieved successfully
CPU information retrieved successfully
Setup module tested successfully
Fact filtering tested successfully
gather_facts: false tested successfully
```

***

# Part D – Task 4: Conditionals (`when` Statements)

---

# Objective

Learn how to execute tasks only when specific conditions are met using:

```yaml
when:
```

Conditionals allow Ansible to make decisions based on:

```text
Operating System
Hostname
Variable Values
Facts
Registered Output
Custom Conditions
```

---

# What is a Conditional?

A conditional tells Ansible:

```text
Run this task only if the condition is true
```

Example:

```text
If OS = Amazon Linux
Install httpd

Else
Skip the task
```

---

# Basic Example

## condition-demo.yml

```yaml
---
- name: Conditional Demo
  hosts: all

  vars:
    install_package: true

  tasks:

    - name: Install Git
      yum:
        name: git
        state: present
      when: install_package
```

---

# How It Works

Variable:

```yaml
install_package: true
```

Condition:

```yaml
when: install_package
```

Result:

```text
TRUE
```

Task executes.

---

# Output

```text
TASK [Install Git]

changed: [web-server]
```

---

# If Variable Becomes False

```yaml
install_package: false
```

---

Result:

```text
TASK [Install Git]

skipping: [web-server]
```

---

# Conditional Using Equality

```yaml
---
- name: Equality Example
  hosts: all

  vars:
    environment: production

  tasks:

    - name: Deploy Application
      debug:
        msg: "Deploying to Production"
      when: environment == "production"
```

---

# Result

Condition:

```text
production == production
```

Result:

```text
TRUE
```

Task runs.

---

# Output

```text
Deploying to Production
```

---

# If Environment Changes

```yaml
environment: testing
```

Condition:

```text
testing == production
```

Result:

```text
FALSE
```

Task skipped.

---

# Conditional Using Facts

## Install Package Only on Amazon Linux

```yaml
---
- name: OS Based Installation
  hosts: all

  tasks:

    - name: Install Apache
      yum:
        name: httpd
        state: present
      when: ansible_distribution == "Amazon"
```

---

# How It Works

Fact:

```yaml
{{ ansible_distribution }}
```

might return:

```text
Amazon
```

Condition:

```text
Amazon == Amazon
```

Result:

```text
TRUE
```

Task executes.

---

# Output

```text
TASK [Install Apache]

changed: [web-server]
```

---

# Example Using OS Version

```yaml
---
- name: OS Version Check
  hosts: all

  tasks:

    - name: Print Message
      debug:
        msg: "Amazon Linux 2023 Detected"
      when: ansible_distribution_version == "2023"
```

---

# Example Using Hostname

```yaml
---
- name: Hostname Check
  hosts: all

  tasks:

    - name: Run on web-server only
      debug:
        msg: "Web Server Detected"
      when: inventory_hostname == "web-server"
```

---

# Output

For:

```text
web-server
```

Task runs.

For:

```text
db-server
```

Task skipped.

---

# Multiple Conditions Using AND

```yaml
---
- name: Multiple Conditions
  hosts: all

  tasks:

    - name: Deploy Application
      debug:
        msg: "Deployment Started"
      when:
        - ansible_distribution == "Amazon"
        - ansible_distribution_version == "2023"
```

---

# Evaluation

Condition 1:

```text
Amazon == Amazon
```

TRUE

Condition 2:

```text
2023 == 2023
```

TRUE

Result:

```text
TRUE AND TRUE
```

Task executes.

---

# Output

```text
Deployment Started
```

---

# Multiple Conditions Using OR

```yaml
---
- name: OR Condition Example
  hosts: all

  tasks:

    - name: Supported OS
      debug:
        msg: "OS Supported"
      when:
        ansible_distribution == "Amazon" or
        ansible_distribution == "RedHat"
```

---

# Evaluation

```text
Amazon == Amazon
```

TRUE

Result:

```text
TRUE OR FALSE
```

TRUE

Task executes.

---

# Using Numeric Comparison

```yaml
---
- name: Memory Check
  hosts: all

  tasks:

    - name: Sufficient Memory
      debug:
        msg: "Server has enough memory"
      when: ansible_memtotal_mb > 2000
```

---

# Example

Fact:

```text
ansible_memtotal_mb = 3800
```

Condition:

```text
3800 > 2000
```

TRUE

Task executes.

---

# Output

```text
Server has enough memory
```

---

# Using List Membership

```yaml
---
- name: Group Membership Example
  hosts: all

  tasks:

    - name: Web Server Task
      debug:
        msg: "Running on Web Group"
      when: "'web' in group_names"
```

---

# Example

Host belongs to:

```text
[web]
web-server
```

Then:

```text
group_names = [web]
```

Condition:

```text
web in group_names
```

TRUE

Task executes.

---

# Output

```text
Running on Web Group
```

---

# Conditional Flow

```text
Task Starts
      |
      V
Check Condition
      |
      |
  TRUE? ---------> YES ---------> Run Task
      |
      |
      NO
      |
      V
Skip Task
```

---

# Using Variables

```yaml
---
- name: Environment Check
  hosts: all

  vars:
    env: production

  tasks:

    - name: Deploy Application
      debug:
        msg: "Production Deployment"
      when: env == "production"
```

---

# Output

```text
Production Deployment
```

---

# Skip Example

```yaml
---
- name: Skip Example
  hosts: all

  vars:
    deploy_app: false

  tasks:

    - name: Deploy Application
      debug:
        msg: "Deploying Application"
      when: deploy_app
```

---

# Output

```text
TASK [Deploy Application]

skipping: [web-server]
```

---

# Verification Commands

Run playbook:

```bash
ansible-playbook condition-demo.yml
```

---

Verbose mode:

```bash
ansible-playbook condition-demo.yml -v
```

---

Check facts used in conditions:

```bash
ansible all -m setup -a "filter=ansible_distribution*"
```

---

Check memory fact:

```bash
ansible all -m setup -a "filter=ansible_memtotal_mb"
```

---

# Task 4 Summary

Verified:

```text
Basic when condition tested
Boolean conditions tested
Equality comparison tested
Fact-based conditions tested
Hostname-based conditions tested
AND conditions tested
OR conditions tested
Memory comparison tested
Group membership condition tested
Task skipping behavior verified
```

***

# Part E – Task 5: Loops (`loop` and `with_items`)

---

# Objective

Learn how to execute the same task multiple times using:

```yaml
loop:
```

Instead of writing:

```yaml
Task 1
Task 2
Task 3
Task 4
```

Ansible can repeat the task automatically.

---

# Why Use Loops?

Without loops:

```yaml
- name: Install git
  yum:
    name: git
    state: present

- name: Install curl
  yum:
    name: curl
    state: present

- name: Install wget
  yum:
    name: wget
    state: present
```

Same task repeated multiple times.

---

# Better Approach

```yaml
- name: Install packages
  yum:
    name: "{{ item }}"
    state: present
  loop:
    - git
    - curl
    - wget
```

---

# How Loop Works

Loop values:

```yaml
loop:
  - git
  - curl
  - wget
```

Execution:

```text
Iteration 1
item = git

Iteration 2
item = curl

Iteration 3
item = wget
```

---

# Result

Internally Ansible performs:

```yaml
yum:
  name: git
  state: present
```

Then:

```yaml
yum:
  name: curl
  state: present
```

Then:

```yaml
yum:
  name: wget
  state: present
```

---

# Package Installation Example

## loop-demo.yml

```yaml
---
- name: Loop Demo
  hosts: all
  become: true

  tasks:

    - name: Install Packages
      yum:
        name: "{{ item }}"
        state: present
      loop:
        - git
        - wget
        - unzip
```

---

# Run

```bash
ansible-playbook loop-demo.yml
```

---

# Output

```text
TASK [Install Packages]

changed: [web-server] => (item=git)

changed: [web-server] => (item=wget)

changed: [web-server] => (item=unzip)
```

---

# Loop with Variables

## Define Variable

```yaml
vars:
  packages:
    - git
    - wget
    - unzip
```

---

## Use Loop

```yaml
- name: Install Packages
  yum:
    name: "{{ item }}"
    state: present
  loop: "{{ packages }}"
```

---

# Variable Resolution

Variable:

```yaml
packages:
  - git
  - wget
  - unzip
```

Loop becomes:

```yaml
loop:
  - git
  - wget
  - unzip
```

---

# Output

```text
(item=git)
(item=wget)
(item=unzip)
```

---

# Creating Multiple Directories

Without Loop:

```yaml
- file:
    path: /app/logs
    state: directory

- file:
    path: /app/data
    state: directory

- file:
    path: /app/config
    state: directory
```

---

# With Loop

```yaml
- name: Create Directories
  file:
    path: "{{ item }}"
    state: directory
    mode: '0755'
  loop:
    - /app/logs
    - /app/data
    - /app/config
```

---

# Execution

```text
Iteration 1
/app/logs

Iteration 2
/app/data

Iteration 3
/app/config
```

---

# Output

```text
changed: [web-server] => (item=/app/logs)

changed: [web-server] => (item=/app/data)

changed: [web-server] => (item=/app/config)
```

---

# Creating Multiple Users

```yaml
---
- name: User Creation
  hosts: all
  become: true

  tasks:

    - name: Create Users
      user:
        name: "{{ item }}"
        state: present
      loop:
        - devops
        - tester
        - developer
```

---

# Execution

```text
useradd devops

useradd tester

useradd developer
```

---

# Output

```text
(item=devops)

(item=tester)

(item=developer)
```

---

# Loop with Debug

```yaml
---
- name: Debug Loop Example
  hosts: all

  tasks:

    - name: Print Values
      debug:
        msg: "{{ item }}"
      loop:
        - Linux
        - Docker
        - Kubernetes
```

---

# Output

```text
Linux

Docker

Kubernetes
```

---

# Loop Through Facts

```yaml
---
- name: Loop Example
  hosts: all

  tasks:

    - name: Print IP Addresses
      debug:
        msg: "{{ item }}"
      loop: "{{ ansible_all_ipv4_addresses }}"
```

---

# Example Output

```text
172.31.10.20

10.0.0.5
```

---

# Loop with Dictionary

## Variable

```yaml
vars:
  users:
    devops: admin
    tester: qa
```

---

## Task

```yaml
- name: Print User Roles
  debug:
    msg: "{{ item.key }} -> {{ item.value }}"
  loop: "{{ users | dict2items }}"
```

---

# Conversion

Dictionary:

```yaml
devops: admin
tester: qa
```

Converted to:

```yaml
- key: devops
  value: admin

- key: tester
  value: qa
```

---

# Output

```text
devops -> admin

tester -> qa
```

---

# Legacy Method (`with_items`)

Older Ansible versions used:

```yaml
- name: Install Packages
  yum:
    name: "{{ item }}"
    state: present
  with_items:
    - git
    - wget
    - unzip
```

---

# Modern Approach

Preferred:

```yaml
loop:
```

instead of:

```yaml
with_items:
```

---

# Loop Execution Flow

```text
Task Starts
      |
      V
Read Loop List
      |
      +--> git
      |
      +--> wget
      |
      +--> unzip
      |
      V
Iteration 1
Install git
      |
      V
Iteration 2
Install wget
      |
      V
Iteration 3
Install unzip
      |
      V
Task Complete
```

---

# Verification Commands

Verify packages:

```bash
rpm -q git wget unzip
```

---

Verify directories:

```bash
ls -ld /app/*
```

---

Verify users:

```bash
cat /etc/passwd
```

---

Check created users:

```bash
id devops

id tester

id developer
```

---

# Common Real-World Uses of Loops

```text
Install multiple packages

Create multiple users

Create multiple directories

Deploy multiple files

Start multiple services

Configure multiple servers

Print multiple values

Process facts and variables
```

---

# Task 5 Summary

Verified:

```text
Loop keyword tested
Package installation with loop tested
Variable-based loop tested
Directory creation loop tested
User creation loop tested
Debug loop tested
Fact-based loop tested
Dictionary loop tested
with_items tested
Loop execution flow verified
```

***

# Part F – Task 6: Registers (`register`) and Capturing Command Output

---

# Objective

Learn how to capture the output of a task and store it in a variable using:

```yaml id="o7g3n2"
register:
```

This allows later tasks to use:

```text id="nlyi9k"
Command Output
Return Code
Standard Output
Standard Error
Task Status
```

---

# Why Use Register?

Suppose you run:

```bash id="t8gwxq"
hostname
```

Output:

```text id="9uqqdv"
web-server
```

You may want to use this output later.

Register stores it in a variable.

---

# Basic Example

## register-demo.yml

```yaml id="a8x85n"
---
- name: Register Demo
  hosts: all

  tasks:

    - name: Get Hostname
      command: hostname
      register: hostname_output

    - name: Print Hostname
      debug:
        var: hostname_output.stdout
```

---

# How It Works

Task:

```yaml id="sp7h3h"
command: hostname
```

runs:

```bash id="34n4uh"
hostname
```

Output:

```text id="d8v8l6"
web-server
```

Stored in:

```yaml id="vh79xa"
hostname_output
```

---

# Output

```text id="zr34te"
TASK [Print Hostname]

ok: [web-server] => {
    "hostname_output.stdout": "web-server"
}
```

---

# Understanding Register Variable

```yaml id="wrlx7u"
register: hostname_output
```

Creates:

```text id="2b75xk"
hostname_output
```

which contains multiple values.

---

# Display Entire Register Object

```yaml id="huws6o"
---
- name: Show Register Data
  hosts: all

  tasks:

    - name: Get Hostname
      command: hostname
      register: hostname_output

    - name: Show Everything
      debug:
        var: hostname_output
```

---

# Sample Output

```text id="c5huhz"
hostname_output:
  changed: true
  cmd:
    - hostname
  rc: 0
  stdout: web-server
  stderr:
  start:
  end:
```

---

# Common Register Fields

## stdout

```yaml id="cvb4w7"
hostname_output.stdout
```

Example:

```text id="g2fcr4"
web-server
```

---

## stderr

```yaml id="n9tz8u"
hostname_output.stderr
```

Stores error output.

---

## rc

```yaml id="wlazto"
hostname_output.rc
```

Return code.

---

# Return Codes

```text id="4r0bxf"
0 = Success

1 = Failure

Other values = Error Codes
```

---

# Example

```yaml id="7og78s"
---
- name: Check Return Code
  hosts: all

  tasks:

    - name: Execute Command
      command: hostname
      register: result

    - name: Show Return Code
      debug:
        msg: "{{ result.rc }}"
```

---

# Output

```text id="x1a3jp"
0
```

---

# Capturing Date Command

```yaml id="85tbxv"
---
- name: Date Example
  hosts: all

  tasks:

    - name: Get Current Date
      command: date
      register: current_date

    - name: Show Date
      debug:
        msg: "{{ current_date.stdout }}"
```

---

# Output

```text id="p33s55"
Tue Jul 08 10:15:20 UTC 2025
```

---

# Capturing Uptime

```yaml id="9ce7i2"
---
- name: Uptime Example
  hosts: all

  tasks:

    - name: Get Uptime
      command: uptime
      register: uptime_result

    - name: Print Uptime
      debug:
        msg: "{{ uptime_result.stdout }}"
```

---

# Example Output

```text id="9pwqqm"
10:30:15 up 5 days, 2 users, load average: 0.10
```

---

# Register with Condition

```yaml id="oqvvf7"
---
- name: Register with Condition
  hosts: all

  tasks:

    - name: Check Hostname
      command: hostname
      register: host_result

    - name: Production Server Found
      debug:
        msg: "Production Server"
      when: "'prod' in host_result.stdout"
```

---

# Flow

```text id="zbm7ic"
Run hostname
      |
      V
Store output
      |
      V
Check output
      |
      V
Execute condition
```

---

# Register with Failed Command

```yaml id="0v9xsi"
---
- name: Failure Example
  hosts: all

  tasks:

    - name: Invalid Command
      command: xyzabc
      register: result
      ignore_errors: true

    - name: Show Error
      debug:
        var: result.stderr
```

---

# Example Output

```text id="7gmx72"
xyzabc: command not found
```

---

# Register Package Check

```yaml id="gg4vvd"
---
- name: Package Check
  hosts: all

  tasks:

    - name: Check Git
      command: rpm -q git
      register: git_check
      ignore_errors: true

    - name: Show Result
      debug:
        msg: "{{ git_check.stdout }}"
```

---

# Output If Installed

```text id="kt49ww"
git-2.39.3
```

---

# Output If Missing

```text id="55y9g8"
package git is not installed
```

---

# Register Multiple Commands

```yaml id="wl8r96"
---
- name: Multiple Registers
  hosts: all

  tasks:

    - name: Hostname
      command: hostname
      register: host_info

    - name: Date
      command: date
      register: date_info

    - name: Display Information
      debug:
        msg:
          - "Host: {{ host_info.stdout }}"
          - "Date: {{ date_info.stdout }}"
```

---

# Output

```text id="igvst6"
Host: web-server

Date: Tue Jul 08 10:20:00 UTC 2025
```

---

# Register Execution Flow

```text id="p0qjzt"
Run Command
      |
      V
Generate Output
      |
      V
Store Output
      |
      V
Register Variable
      |
      +--> stdout
      |
      +--> stderr
      |
      +--> rc
      |
      V
Use Later Tasks
```

---

# Real-World Example

Check if Apache is installed.

```yaml id="4c83mo"
---
- name: Apache Validation
  hosts: all

  tasks:

    - name: Check Apache
      command: rpm -q httpd
      register: apache_check
      ignore_errors: true

    - name: Apache Installed
      debug:
        msg: "Apache Present"
      when: apache_check.rc == 0

    - name: Apache Missing
      debug:
        msg: "Apache Not Installed"
      when: apache_check.rc != 0
```

---

# Execution Logic

```text id="t3mdx7"
rpm -q httpd
       |
       |
    Installed?
       |
   YES -----> rc = 0
       |
       V
Apache Present

--------------------------------

Not Installed
       |
       V
rc != 0
       |
       V
Apache Not Installed
```

---

# Verification Commands

Run playbook:

```bash id="a2dj29"
ansible-playbook register-demo.yml
```

---

Display complete register output:

```yaml id="g37jtw"
debug:
  var: result
```

---

Display stdout:

```yaml id="mpc0qq"
debug:
  var: result.stdout
```

---

Display stderr:

```yaml id="akfshq"
debug:
  var: result.stderr
```

---

Display return code:

```yaml id="oqzz1m"
debug:
  var: result.rc
```

---

# Task 6 Summary

Verified:

```text id="yfz81k"
register keyword tested
stdout retrieval tested
stderr retrieval tested
return code retrieval tested
date command capture tested
uptime command capture tested
package validation tested
conditional execution using register tested
multiple register variables tested
error handling tested
```

***

Sure. Here is the missing section rewritten in the same notes format so you can copy-paste directly.

# Part G – Real-World Project: Server Health Report

---

# Objective

Combine:

```text
Variables
Facts
Conditionals
Registers
Debug
Copy Module
File Creation
```

into one real-world project.

---

# Project Goal

Collect:

```text
Server Information
Operating System
IP Address
RAM Details
Disk Usage
Running Services
```

Generate:

```text
Server Health Report
```

for each server.

---

# Create Playbook

## server-report.yml

```yaml
---
- name: Server Health Report
  hosts: all
  become: true

  tasks:

    - name: Check disk space
      command: df -h /
      register: disk_result

    - name: Check memory
      command: free -m
      register: memory_result

    - name: Check running services
      shell: systemctl list-units --type=service --state=running | head -20
      register: services_result

    - name: Generate report
      debug:
        msg:
          - "========== {{ inventory_hostname }} =========="
          - "OS: {{ ansible_distribution }} {{ ansible_distribution_version }}"
          - "IP: {{ ansible_default_ipv4.address }}"
          - "RAM: {{ ansible_memtotal_mb }}MB"
          - "Disk: {{ disk_result.stdout_lines[1] }}"
          - "Running services (first 20): {{ services_result.stdout_lines | length }}"

    - name: Flag if disk is critically low
      debug:
        msg: "ALERT: Check disk space on {{ inventory_hostname }}"
      when: "'9[0-9]%' in disk_result.stdout or '100%' in disk_result.stdout"

    - name: Save report to file
      copy:
        content: |
          Server: {{ inventory_hostname }}
          OS: {{ ansible_distribution }} {{ ansible_distribution_version }}
          IP: {{ ansible_default_ipv4.address }}
          RAM: {{ ansible_memtotal_mb }}MB
          Disk: {{ disk_result.stdout }}
          Checked at: {{ ansible_date_time.iso8601 }}
        dest: "/tmp/server-report-{{ inventory_hostname }}.txt"
```

---

# Run Playbook

```bash
ansible-playbook -i inventory.ini server-report.yml
```

---

# Task 1 – Check Disk Space

```yaml
- name: Check disk space
  command: df -h /
  register: disk_result
```

Runs:

```bash
df -h /
```

Stores output in:

```text
disk_result
```

---

# Example Output

```text
Filesystem      Size Used Avail Use%
/dev/xvda1       20G  10G   10G  50%
```

---

# Task 2 – Check Memory

```yaml
- name: Check memory
  command: free -m
  register: memory_result
```

Runs:

```bash
free -m
```

Stores output in:

```text
memory_result
```

---

# Example Output

```text
total used free

3800 1200 2600
```

---

# Task 3 – Check Running Services

```yaml
- name: Check running services
  shell: systemctl list-units --type=service --state=running | head -20
  register: services_result
```

Stores:

```text
First 20 running services
```

inside:

```text
services_result
```

---

# Example Output

```text
amazon-ssm-agent.service
chronyd.service
crond.service
sshd.service
```

---

# Task 4 – Generate Report

```yaml
- name: Generate report
  debug:
    msg:
      - "========== {{ inventory_hostname }} =========="
      - "OS: {{ ansible_distribution }} {{ ansible_distribution_version }}"
      - "IP: {{ ansible_default_ipv4.address }}"
      - "RAM: {{ ansible_memtotal_mb }}MB"
      - "Disk: {{ disk_result.stdout_lines[1] }}"
      - "Running services (first 20): {{ services_result.stdout_lines | length }}"
```

Displays collected information.

---

# Example Output

```text
========== web-server ==========
OS: Amazon 2023
IP: 172.31.10.20
RAM: 3800MB
Disk: /dev/xvda1 20G 10G 10G 50%
Running services (first 20): 20
```

---

# Task 5 – Disk Space Alert

```yaml
- name: Flag if disk is critically low
  debug:
    msg: "ALERT: Check disk space on {{ inventory_hostname }}"
  when: "'9[0-9]%' in disk_result.stdout or '100%' in disk_result.stdout"
```

Condition:

```text
Disk Usage >= 90%
```

Display alert.

---

# Example Output

```text
ALERT: Check disk space on web-server
```

---

# Task 6 – Save Report to File

```yaml
- name: Save report to file
  copy:
    content: |
      Server: {{ inventory_hostname }}
      OS: {{ ansible_distribution }} {{ ansible_distribution_version }}
      IP: {{ ansible_default_ipv4.address }}
      RAM: {{ ansible_memtotal_mb }}MB
      Disk: {{ disk_result.stdout }}
      Checked at: {{ ansible_date_time.iso8601 }}
    dest: "/tmp/server-report-{{ inventory_hostname }}.txt"
```

Creates:

```text
/tmp/server-report-web-server.txt
/tmp/server-report-db-server.txt
```

---

# Verify Report File

Check generated files:

```bash
ls -l /tmp/server-report-*
```

---

View report:

```bash
cat /tmp/server-report-web-server.txt
```

---

# Sample Report

```text
Server: web-server
OS: Amazon 2023
IP: 172.31.10.20
RAM: 3800MB
Disk: /dev/xvda1 20G 10G 10G 50%
Checked at: 2025-07-08T10:30:15Z
```

---

# Verification Checklist

```text
OS Match
IP Match
RAM Match
Disk Usage Match
Report File Created
Condition Tested
Register Values Verified
Facts Retrieved Successfully
```

---

# Project Flow Diagram

```text
Playbook Starts
       |
       V
Gather Facts
       |
       V
Check Disk Space
       |
       V
Check Memory
       |
       V
Check Running Services
       |
       V
Store Results in Registers
       |
       +--> disk_result
       |
       +--> memory_result
       |
       +--> services_result
       |
       V
Generate Report
       |
       V
Check Disk Threshold
       |
       +--> Alert if > 90%
       |
       V
Save Report File
       |
       V
Playbook Complete
```



