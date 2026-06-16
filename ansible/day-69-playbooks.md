# DAY 69 MASTER NOTES (PART 1)

# Ansible Playbooks Fundamentals

---

# Chapter 1: Why Ansible Exists

Imagine your school has:

```text
100 Classrooms
```

Every classroom needs:

```text
✓ Fan ON
✓ Lights ON
✓ Whiteboard Clean
✓ Projector Ready
```

Without Ansible:

```text
You walk into Room 1
Do work

You walk into Room 2
Do work

You walk into Room 3
Do work

...
100 Times
```

Painful.

---

With Ansible:

```text
You write instructions once.

"Turn on fans
Turn on lights
Clean board"
```

and every classroom follows it.

ASCII:

```text
                    YOU

                     │

                     ▼

               Ansible Playbook

                     │

      ┌──────────────┼──────────────┐

      ▼              ▼              ▼

  Server 1       Server 2       Server 3

      ▼              ▼              ▼

  Same Work      Same Work      Same Work
```

Think:

```text
Teacher → Gives Homework Once

Students → Do It Everywhere
```

Teacher = Ansible

Students = Servers

---

# Real DevOps Problem

Company has:

```text
10 Servers
```

Need to install:

```text
Nginx
```

Manual way:

```bash
ssh server1
sudo yum install nginx

ssh server2
sudo yum install nginx

ssh server3
sudo yum install nginx
```

Repeat.

Repeat.

Repeat.

---

Ansible way:

```yaml
Install Nginx Everywhere
```

Run once:

```bash
ansible-playbook install-nginx.yml
```

Done.

---

# Big Definition

### What is Ansible?

Ansible is a tool that automates server management.

Simple version:

```text
Ansible = Robot System Administrator
```

It can:

```text
Install Software

Start Services

Create Files

Edit Files

Configure Servers

Deploy Applications
```

Automatically.

---

# Chapter 2: What is a Playbook?

Imagine Mom writes:

```text
1. Clean Room
2. Fold Clothes
3. Arrange Books
4. Take Out Trash
```

This whole instruction sheet is:

```text
PLAYBOOK
```

---

Ansible Playbook:

```yaml
---
- name: Install Nginx
```

is simply:

```text
A list of instructions
for servers.
```

---

Think:

```text
Recipe Book
```

Example:

```text
Step 1 → Cut Vegetables

Step 2 → Add Oil

Step 3 → Cook
```

Recipe = Playbook

Steps = Tasks

---

ASCII

```text
Playbook

│

├── Task 1

├── Task 2

├── Task 3

└── Task 4
```

---

# Real Definition

A Playbook is:

```text
A YAML file containing automation instructions.
```

Usually:

```text
something.yml
```

Example:

```text
install-nginx.yml

essential-modules.yml

nginx-config.yml
```

---

# Why YAML?

Humans should read it easily.

Bad:

```json
{
  "install":"nginx"
}
```

Looks technical.

---

Good:

```yaml
install nginx
```

Almost English.

---

# Chapter 3: Understanding YAML

Ansible Playbooks use YAML.

---

## Rule 1

Indentation matters.

Correct:

```yaml
tasks:
  - name: Install Nginx
```

Wrong:

```yaml
tasks:
- name: Install Nginx
```

or

```yaml
tasks:
      - name: Install Nginx
```

in random places.

---

Think:

```text
Folders inside folders
```

ASCII:

```text
House

 └── Room

      └── Cupboard

            └── Shelf
```

Indentation shows relationships.

---

## Rule 2

Dash means list item

```yaml
tasks:

  - task1

  - task2

  - task3
```

ASCII:

```text
Shopping List

□ Milk

□ Bread

□ Eggs
```

Each item starts with:

```text
-
```

---

## Rule 3

Key : Value

```yaml
name: nginx
```

Means:

```text
name = nginx
```

Think:

```text
Student Name : Rahul

Age : 10
```

---

# Chapter 4: Anatomy of a Playbook

Your playbook:

```yaml
---
- name: Install and start Nginx on web servers

  hosts: web

  become: true

  tasks:

    - name: Install Nginx

      yum:
        name: nginx
        state: present
```

Looks scary.

Let's decode it.

---

ASCII View

```text
Playbook

│

└── Play

      │

      ├── Hosts

      ├── Become

      └── Tasks

             │

             ├── Task 1

             ├── Task 2

             └── Task 3
```

---

# Chapter 5: What is a Play?

Most Important Interview Question.

---

Imagine:

```text
Annual School Function
```

Whole event:

```text
PLAY
```

Inside:

```text
Dance

Song

Speech

Prize Distribution
```

These are:

```text
TASKS
```

---

Ansible Version

```yaml
- name: Install and start Nginx
```

This entire block is:

```text
PLAY
```

---

A Play answers:

```text
Which servers?

What tasks?

What permissions?
```

---

Memory Trick

```text
PLAY = Big Container
```

Contains many tasks.

---

ASCII

```text
PLAY

│

├── Task

├── Task

├── Task

└── Task
```

---

# Chapter 6: What is a Task?

Task = One Unit of Work

---

Example:

```yaml
- name: Install Nginx
```

One task.

---

Another:

```yaml
- name: Start Nginx
```

Another task.

---

Think:

Morning Routine

```text
Wake Up

Brush Teeth

Take Bath

Eat Breakfast
```

Each activity:

```text
TASK
```

Whole routine:

```text
PLAY
```

---

ASCII

```text
Morning Routine

│

├── Wake Up

├── Brush Teeth

├── Bath

└── Breakfast
```

---

Same:

```text
Server Configuration

│

├── Install Nginx

├── Start Service

├── Copy File

└── Enable Service
```

---

Interview Answer

```text
Play = Collection of Tasks

Task = Single Action
```

---

# Chapter 7: What Are Hosts?

Huge Concept.

---

Imagine:

Teacher says:

```text
All Students Stand Up
```

Who listens?

```text
Entire Class
```

---

Teacher says:

```text
Only Girls Stand Up
```

Who listens?

```text
Only Girls
```

---

Same in Ansible.

---

Example:

```yaml
hosts: web
```

Means:

```text
Only Web Servers
```

---

Example:

```yaml
hosts: db
```

Means:

```text
Only Database Servers
```

---

Example:

```yaml
hosts: all
```

Means:

```text
EVERY SERVER
```

---

ASCII

```text
Inventory

│

├── web

│     ├── web1

│     └── web2

│

├── app

│     └── app1

│

└── db

      └── db1
```

---

```yaml
hosts: web
```

Runs only here:

```text
web1

web2
```

---

```yaml
hosts: all
```

Runs everywhere.

---

# Chapter 8: Inventory

Imagine attendance register.

Teacher needs:

```text
Who are my students?
```

---

Ansible needs:

```text
Which servers exist?
```

Answer:

```text
Inventory
```

---

Example

```ini
[web]
web-server

[app]
app-server

[db]
db-server
```

---

Meaning

```text
web group

  └── web-server


app group

  └── app-server


db group

  └── db-server
```

---

Without Inventory

Ansible says:

```text
I don't know
where servers are.
```

Error:

```text
Could not match supplied host pattern
```

Exactly the error you saw during Day 69.

---

# Chapter 9: Why You Got The Inventory Error

Your Playbook:

```yaml
hosts: web
```

Ansible asked:

```text
Where is web?
```

Inventory missing.

Result:

```text
No inventory was parsed
```

Then:

```text
Could not match supplied host pattern
```

Because:

```text
web group
did not exist
```

---

Once you created:

```ini
[web]
web-server
```

Problem solved.

---

# Chapter 10: Mental Model of Entire Flow

Before Day 69, think like this:

```text
Step 1

Inventory

Who exists?

        ↓

Step 2

Playbook

What should happen?

        ↓

Step 3

Play

Which hosts?

        ↓

Step 4

Tasks

What actions?

        ↓

Step 5

Modules

How to perform them?

        ↓

Servers Updated
```

ASCII:

```text
Inventory
    │
    ▼
Playbook
    │
    ▼
Play
    │
    ▼
Tasks
    │
    ▼
Modules
    │
    ▼
Servers
```

---

# 60-Second Revision

```text
Ansible
=
Automation Tool

Playbook
=
Instruction File

Play
=
Collection of Tasks

Task
=
Single Action

Hosts
=
Target Servers

Inventory
=
Server List

YAML
=
Language Used

hosts:web
=
Only Web Servers

hosts:all
=
Every Server

Inventory Missing
=
Host Pattern Error

Play
=
Big Container

Task
=
Small Work
```

---

## Memory Story (Exam/Interview Trick)

Imagine:

```text
Teacher (Ansible)

has

Attendance Register (Inventory)

and

Homework Sheet (Playbook)

Teacher chooses

Class 5A (hosts:web)

and gives

Tasks

to students.

Students perform work.

Done.
```

If you remember this story, you'll remember:

```text
Inventory
Playbook
Play
Hosts
Tasks
```

forever.

***

# DAY 69 MASTER NOTES (PART 2)

# become + Modules + Nginx Playbook Deep Dive

### Teach a 10-Year-Old Version + Analogies + ASCII + Why Before How

---

# Chapter 11: What is `become: true`?

One of the most important Ansible concepts.

Many beginners memorize:

```yaml
become: true
```

but don't understand it.

Let's fix that.

---

## Real Life Analogy

Imagine you're inside a company.

Normal Employee:

```text
Can enter:
✓ Office
✓ Cafeteria

Cannot enter:
✗ Server Room
✗ CEO Room
```

---

Administrator:

```text
Can enter:
✓ Office
✓ Cafeteria
✓ Server Room
✓ CEO Room
```

Because admin has special permissions.

---

Linux Works Exactly The Same

Normal User:

```bash
ec2-user
```

can do some things.

But cannot do:

```bash
Install packages

Modify system files

Start services

Stop services
```

---

Example

Try:

```bash
yum install nginx
```

Result:

```text
Permission Denied
```

---

Need:

```bash
sudo yum install nginx
```

Now it works.

---

Ansible Equivalent

Linux:

```bash
sudo
```

Ansible:

```yaml
become: true
```

---

Memory Trick

```text
sudo
=
become: true
```

---

ASCII

```text
Normal User

     │

     ▼

Permission Denied

------------------

become: true

     │

     ▼

Root User

     │

     ▼

Full Access
```

---

# Why Day 69 Needed become

Your playbook:

```yaml
- name: Install and start Nginx
  hosts: web

  become: true
```

Why?

Because these tasks require root access:

```yaml
yum:
  name: nginx
```

Installing packages.

---

```yaml
service:
  name: nginx
```

Managing services.

---

```yaml
copy:
  dest: /usr/share/nginx/html/index.html
```

Writing inside system folders.

---

Without become:

```text
FAILED

Permission Denied
```

---

# Play Level vs Task Level become

Huge Interview Question.

---

## Play Level

```yaml
- name: Configure Servers

  hosts: all

  become: true

  tasks:
```

Meaning:

```text
Every task uses sudo.
```

---

ASCII

```text
Play

│

├── Task 1 → sudo

├── Task 2 → sudo

├── Task 3 → sudo
```

---

## Task Level

```yaml
- name: Install Nginx
  become: true

  yum:
    name: nginx
```

Meaning:

```text
Only this task uses sudo.
```

---

ASCII

```text
Task 1 → Normal User

Task 2 → sudo

Task 3 → Normal User
```

---

Memory Trick

```text
Play Level
=
Everything

Task Level
=
Only One Task
```

---

# Chapter 12: What is a Module?

Most Important Ansible Building Block.

---

Imagine:

Ansible = Robot

Robot needs tools.

---

Hammer:

```text
For Nails
```

Screwdriver:

```text
For Screws
```

Scissors:

```text
For Cutting
```

---

Ansible Tools:

```text
yum

service

copy

file

command

shell

lineinfile
```

These are called:

```text
MODULES
```

---

Definition

A Module is a pre-built piece of code that performs one specific task.

---

Think:

```text
Mobile Apps
```

Calculator App:

```text
Calculates
```

Camera App:

```text
Takes Photos
```

---

Ansible Modules:

```text
yum
=
Install Packages

service
=
Manage Services

copy
=
Copy Files

file
=
Manage Files/Folders
```

---

ASCII

```text
Ansible

 │

 ├── yum

 ├── service

 ├── copy

 ├── file

 ├── command

 ├── shell

 └── lineinfile
```

---

# Chapter 13: Deep Dive into yum Module

The first module used in Day 69.

---

Analogy

Think:

```text
Google Play Store
```

You want:

```text
WhatsApp
```

Store installs it.

---

Linux Equivalent

Want:

```text
Nginx
```

yum installs it.

---

Example

```yaml
yum:
  name: nginx
  state: present
```

---

Translation

```text
Dear Server,

Please ensure nginx exists.

If missing:
Install it.

If already installed:
Do nothing.
```

---

ASCII

```text
Desired State

nginx Installed

        │

        ▼

Check System

        │

 ┌──────┴──────┐

 ▼             ▼

Exists      Missing

 ▼             ▼

Do Nothing   Install
```

---

# state: present

Huge Concept.

---

Not:

```text
Install nginx
```

Instead:

```text
Make sure nginx exists.
```

---

Ansible Thinks In

```text
DESIRED STATE
```

not commands.

---

Human:

```bash
Install nginx
```

Ansible:

```text
nginx should exist
```

---

Why?

Because Ansible wants:

```text
Idempotency
```

---

# What Happened During Your First Run

Initial State:

```text
Nginx Missing
```

---

Ansible Check:

```text
Need Nginx
```

---

Action:

```text
Install Nginx
```

---

Result:

```text
changed
```

---

ASCII

```text
Before

nginx ❌

After

nginx ✅

Result

changed
```

---

# What Happened During Second Run

Before:

```text
nginx ✅
```

---

Desired:

```text
nginx ✅
```

---

Already matches.

---

Result:

```text
ok
```

---

ASCII

```text
Desired
=
Reality

No Work Needed

Result

ok
```

---

This is why your second run showed:

```text
changed=0
```

---

# Chapter 14: Deep Dive into service Module

Installing software is NOT enough.

---

Real Life

Buying Car:

```text
Car Exists
```

Doesn't mean:

```text
Car Running
```

Need:

```text
Start Engine
```

---

Same For Nginx

Install:

```yaml
yum:
  name: nginx
```

Means:

```text
Nginx Exists
```

---

NOT:

```text
Nginx Running
```

---

Need:

```yaml
service:
  name: nginx
  state: started
```

---

Translation

```text
Ensure Nginx Is Running
```

---

ASCII

```text
Install Nginx

      │

      ▼

Exists

      │

      ▼

Start Service

      │

      ▼

Running
```

---

# state: started

Meaning:

```text
Must Be Running
```

---

If Running Already

```text
Do Nothing
```

---

If Stopped

```text
Start It
```

---

Again:

```text
Idempotent
```

---

# enabled: true

Another important concept.

---

Imagine:

You start your laptop.

Every day:

```text
Chrome Opens Automatically
```

Because:

```text
Auto Start Enabled
```

---

Same idea.

---

```yaml
enabled: true
```

Means:

```text
Start Automatically
After Reboot
```

---

Without:

```text
Server Reboot

↓

Nginx Stops

↓

Website Down
```

---

With:

```text
Server Reboot

↓

Nginx Starts Automatically

↓

Website Works
```

---

ASCII

```text
enabled:true

Server Restart

      │

      ▼

Nginx Starts

Automatically
```

---

# Chapter 15: Deep Dive into copy Module

Third module used in Task 1.

---

Think:

```text
Photocopy Machine
```

Source:

```text
Original Page
```

Destination:

```text
New Page
```

---

Ansible Version

```yaml
copy:
```

means:

```text
Copy Something
```

---

Your Playbook

```yaml
copy:
  content: "<h1>Deployed by Ansible</h1>"
  dest: /usr/share/nginx/html/index.html
```

---

Translation

```text
Create this HTML page
inside the web server.
```

---

# What is content?

Instead of copying a file:

```yaml
src: file.txt
```

you directly write content.

---

Example

```yaml
content: Hello World
```

creates:

```text
Hello World
```

inside destination file.

---

ASCII

```text
content

      │

      ▼

Write Text

      │

      ▼

Create File
```

---

# What is dest?

Destination.

---

Think:

```text
Amazon Delivery
```

Package:

```text
content
```

Address:

```text
dest
```

---

Example

```yaml
dest:
/usr/share/nginx/html/index.html
```

Means:

```text
Put file here.
```

---

# Why index.html?

When browser opens:

```text
http://server-ip
```

Nginx automatically searches:

```text
index.html
```

---

ASCII

```text
Browser

   │

   ▼

Nginx

   │

   ▼

index.html

   │

   ▼

Show Page
```

---

# What You Actually Built

Your Playbook:

```yaml
Install Nginx

Start Nginx

Create Web Page
```

---

ASCII

```text
Playbook

 │

 ├── Install Nginx

 │

 ├── Start Nginx

 │

 └── Create index.html
```

---

Result

```text
Browser

      │

      ▼

Public IP

      │

      ▼

Nginx

      │

      ▼

index.html

      │

      ▼

Deployed by Ansible
```

---

# Chapter 16: Why curl Was Used

You ran:

```bash
curl http://PUBLIC_IP
```

---

Think:

```text
Mini Browser
```

Without GUI.

---

ASCII

```text
curl

   │

   ▼

Request Web Page

   │

   ▼

Show HTML
```

---

Expected

```html
<h1>Deployed by Ansible - TerraWeek Server</h1>
```

---

Meaning:

```text
Nginx Working

Page Working

Deployment Successful
```

---

# Day 69 Task 1 Complete Flow

```text
Write Playbook

        │

        ▼

Inventory

        │

        ▼

Ansible Playbook Run

        │

        ▼

yum Module

Install Nginx

        │

        ▼

service Module

Start Nginx

        │

        ▼

copy Module

Create index.html

        │

        ▼

Open Browser

        │

        ▼

Website Works
```

---

# 60-Second Revision

```text
become:true
=
sudo

Module
=
Tool Used By Ansible

yum
=
Install Packages

state:present
=
Must Exist

service
=
Manage Services

state:started
=
Must Be Running

enabled:true
=
Auto Start On Boot

copy
=
Create/Copy Files

content
=
File Content

dest
=
Destination Path

curl
=
Mini Browser

First Run
=
changed

Second Run
=
ok

Reason
=
Idempotency
```

---

## Memory Story

```text
Teacher (Ansible)

gets Administrator Badge
(become:true)

uses Tools
(modules)

Tool 1
=
yum
(install Nginx)

Tool 2
=
service
(start Nginx)

Tool 3
=
copy
(create webpage)

Students
(servers)

follow instructions

Website becomes live.
```

***

# DAY 69 MASTER NOTES (PART 3)

# file Module + command Module + shell Module + register + debug + lineinfile

### Teach a 10-Year-Old Version + Real-Life Analogies + ASCII + Deep Understanding

---

# Before We Start

By now you know:

```text
Ansible = Teacher

Inventory = Student List

Playbook = Homework Sheet

Tasks = Individual Work

Modules = Tools
```

In Part 3 we learn some of the most-used modules in real DevOps projects.

These modules are used daily by DevOps Engineers.

---

# Chapter 17: file Module

One of the most powerful modules.

Think:

```text
file module
=
File Manager
```

---

## Real Life Analogy

Imagine you own a cupboard.

You can:

```text
Create Drawer

Delete Drawer

Lock Drawer

Rename Drawer

Change Permissions
```

---

Linux Filesystem Works The Same.

You can:

```text
Create File

Delete File

Create Folder

Delete Folder

Change Permissions
```

---

Ansible Tool For This:

```yaml
file:
```

---

# Why Do We Need file Module?

Suppose application needs:

```text
/opt/app/logs
```

folder.

But folder doesn't exist.

Application crashes.

---

Instead of manually creating:

```bash
mkdir -p /opt/app/logs
```

Ansible can do it automatically.

---

# Create Directory

```yaml
- name: Create logs directory

  file:
    path: /opt/app/logs
    state: directory
```

---

Translation

```text
Dear Server,

Please ensure

/opt/app/logs

exists as a directory.
```

---

ASCII

```text
Before

/opt/app
    │
    └── logs ❌

After

/opt/app
    │
    └── logs ✅
```

---

# state: directory

Means:

```text
Must Exist As Folder
```

---

If Folder Exists

```text
Do Nothing
```

---

If Missing

```text
Create It
```

---

Again:

```text
Idempotent
```

---

# Delete File

```yaml
file:
  path: /tmp/test.txt
  state: absent
```

---

Translation

```text
Make sure this file
does NOT exist.
```

---

ASCII

```text
Before

test.txt ✅

After

test.txt ❌
```

---

# state: absent

Huge Interview Question.

---

Not:

```text
Delete File
```

Instead:

```text
Desired State

File Should Not Exist
```

---

Ansible Thinking:

```text
Present
or

Absent
```

not

```text
Create
Delete
```

---

Memory Trick

```text
present
=
Must Exist

absent
=
Must Not Exist
```

---

# Change Permissions

Example:

```yaml
file:
  path: /opt/app
  mode: '0755'
```

---

Think:

```text
House Permissions
```

Who can enter?

Who can modify?

Who can only view?

---

Linux permissions work similarly.

---

ASCII

```text
Owner

Group

Others
```

---

More on permissions later.

For now:

```text
file module
=
Filesystem Manager
```

---

# Chapter 18: command Module

One of the most misunderstood modules.

---

Real Life

Imagine:

You have a robot.

You tell robot:

```text
Open Door
```

Robot opens door.

Simple.

---

Ansible Version

```yaml
command:
```

means

```text
Run Linux Command
```

---

Example

```yaml
- name: Check hostname

  command: hostname
```

---

Translation

```text
Run:

hostname
```

on remote server.

---

Output

```text
web-server
```

---

ASCII

```text
Ansible

   │

   ▼

hostname

   │

   ▼

Server Executes

   │

   ▼

Result Returned
```

---

# Another Example

```yaml
command: date
```

Runs:

```bash
date
```

---

Output

```text
Mon Jun 15
```

---

# Why command Module Exists

Not every task has a dedicated module.

Example:

There is no:

```yaml
moon_module:
```

for every possible action.

Sometimes:

```text
Just Run Command
```

---

Use:

```yaml
command:
```

---

# Important Limitation

command module is SAFE.

But limited.

---

Cannot understand:

```bash
|

>

>>

&&

<
```

These are shell operators.

---

Example

This FAILS:

```yaml
command: cat file.txt | grep nginx
```

---

Why?

Because command module does NOT use a shell.

---

Think:

```text
Robot understands

"Open Door"

but not

"Open Door AND
Turn Light ON"
```

---

ASCII

```text
command

   │

   ▼

Direct Execution

No Shell
```

---

This leads us to:

```text
shell module
```

---

# Chapter 19: shell Module

Most Interviewed Topic.

---

Think:

command module:

```text
Simple Calculator
```

---

shell module:

```text
Scientific Calculator
```

More powerful.

---

Example

```yaml
shell: cat file.txt | grep nginx
```

Works.

---

Why?

Because shell module uses:

```text
Linux Shell
```

underneath.

---

ASCII

```text
Ansible

   │

   ▼

Shell

   │

   ▼

Linux Bash

   │

   ▼

Command Executes
```

---

# Shell Operators Supported

Example

Pipe:

```bash
cat file.txt | grep nginx
```

---

Redirect

```bash
echo hello > file.txt
```

---

Append

```bash
echo hello >> file.txt
```

---

Logical AND

```bash
mkdir test && cd test
```

---

All work.

---

# Command vs Shell

Most Important Interview Table

| Feature            | command | shell            |
| ------------------ | ------- | ---------------- |
| Safer              | ✅       | ❌                |
| Faster             | ✅       | ❌                |
| Uses Shell         | ❌       | ✅                |
| Supports Pipe      | ❌       | ✅                |
| Supports Redirect  | ❌       | ✅                |
| Supports Variables | Limited | ✅                |
| Preferred          | ✅       | Only When Needed |

---

Memory Trick

```text
command

=
Simple
Safe

shell

=
Powerful
Flexible
```

---

Interview Answer

```text
Use command whenever possible.

Use shell only when shell features are required.
```

---

# Chapter 20: register

One of the coolest features.

---

Think:

Exam Results.

Teacher asks:

```text
What's your score?
```

You write it down.

---

That written result becomes:

```text
Stored Information
```

---

register does the same thing.

---

Example

```yaml
- name: Get hostname

  command: hostname

  register: server_name
```

---

Translation

```text
Run hostname

Store result inside

server_name
```

---

ASCII

```text
hostname

   │

   ▼

web-server

   │

   ▼

Stored In

server_name
```

---

# Why Register?

Without register:

```text
Result Appears

Then Disappears
```

---

With register:

```text
Result Saved

Can Reuse Later
```

---

Think:

```text
Notebook
```

You save information.

---

# Example

```yaml
command: date

register: today
```

Now:

```text
today
```

contains:

```text
Current Date
```

---

# Chapter 21: debug Module

What if you want to see what's inside register?

Use:

```yaml
debug:
```

---

Think:

```text
Open Notebook

Read Stored Information
```

---

Example

```yaml
- debug:
    var: server_name
```

---

Translation

```text
Show me
what is inside

server_name
```

---

ASCII

```text
register

   │

   ▼

Stored Result

   │

   ▼

debug

   │

   ▼

Display Result
```

---

# Real Flow

```yaml
- command: hostname

  register: host_result

- debug:
    var: host_result
```

---

ASCII

```text
hostname

   │

   ▼

web-server

   │

   ▼

register

   │

   ▼

host_result

   │

   ▼

debug

   │

   ▼

Display Output
```

---

# Why DevOps Engineers Use This

During troubleshooting.

Example:

```text
Did command run?

What output came?

What failed?
```

---

register + debug

are debugging superheroes.

---

# Chapter 22: lineinfile Module

One of the most practical modules.

---

Think:

School Notebook.

You want to change:

```text
Math
```

to

```text
Mathematics
```

without rewriting the whole notebook.

---

Ansible Equivalent:

```yaml
lineinfile:
```

---

Purpose:

```text
Add

Replace

Update

One Specific Line
```

inside a file.

---

# Example

File:

```text
Port=80
```

Need:

```text
Port=8080
```

---

Playbook

```yaml
lineinfile:
  path: /etc/app.conf

  regexp: '^Port='

  line: 'Port=8080'
```

---

Translation

```text
Find line starting with

Port=

Replace it with

Port=8080
```

---

ASCII

```text
Before

Port=80

After

Port=8080
```

---

# Why Not Use copy?

Good Question.

---

copy:

```text
Replaces Entire File
```

---

lineinfile:

```text
Changes One Line
```

---

Think:

```text
copy
=
Replace Entire Book

lineinfile
=
Correct One Sentence
```

---

# Real DevOps Usage

Common Tasks:

```text
Change Port

Enable Feature

Modify Config

Update IP

Change Hostname
```

without replacing full file.

---

# Master Flow of Part 3

```text
file
│
├── Create Folder
├── Delete File
└── Change Permissions


command
│
└── Run Simple Linux Commands


shell
│
├── Pipes
├── Redirects
└── Complex Commands


register
│
└── Store Output


debug
│
└── Show Output


lineinfile
│
└── Modify One Line
```

---

# Real DevOps Mental Model

```text
Need Folder?
      │
      ▼
file Module

Need Simple Command?
      │
      ▼
command Module

Need Pipe or Redirect?
      │
      ▼
shell Module

Need To Save Output?
      │
      ▼
register

Need To View Output?
      │
      ▼
debug

Need To Change One Line?
      │
      ▼
lineinfile
```

---

# 60-Second Revision

```text
file
=
Manage Files & Folders

directory
=
Create Folder

absent
=
Delete Resource

command
=
Run Simple Commands

shell
=
Run Shell Commands

command
=
Safer

shell
=
More Powerful

register
=
Store Output

debug
=
Print Output

lineinfile
=
Modify One Line

copy
=
Replace Whole File

lineinfile
=
Modify One Line Only
```

---

## Memory Story

```text
Teacher (Ansible)

uses

file
→ Build Classroom

command
→ Ask Question

shell
→ Ask Complex Question

register
→ Write Answer In Notebook

debug
→ Read Notebook

lineinfile
→ Correct One Sentence
```

If you remember this story, you'll never confuse:

```text
file
command
shell
register
debug
lineinfile
```

again.

***

# DAY 69 MASTER NOTES (PART 4)

# Handlers + Notify + Check Mode + Diff Mode + Verbosity

### Teach a 10-Year-Old Version + Analogies + ASCII + Deep Understanding

---

# Chapter 23: The Problem Before Handlers Existed

Imagine a school.

Every time a student changes one word in a notebook:

```text
Teacher Restarts Entire Class
```

Sounds ridiculous.

---

Example:

```text
Student changes:
Math → Mathematics
```

Teacher:

```text
"EVERYONE LEAVE"

"COME BACK"

"RESTART CLASS"
```

Waste of time.

---

Same problem happens on servers.

---

Suppose:

```yaml
copy:
  content: New Website Content
```

changes a website file.

Many people do:

```yaml
- copy:
    ...

- service:
    name: nginx
    state: restarted
```

---

Result:

```text
Restart Nginx

EVERY RUN
```

Even when nothing changed.

---

ASCII

```text
Run Playbook

     │

     ▼

File Changed?

     │

 ┌───┴───┐

 ▼       ▼

YES      NO

 ▼       ▼

Restart  Restart ❌
```

Bad design.

---

# Why Restarts Are Expensive

Think:

```text
Restarting Nginx
```

like:

```text
Turning Off Car

Then

Starting Car Again
```

---

Possible Issues:

```text
Temporary Downtime

Lost Connections

Slower Execution

Unnecessary Work
```

---

DevOps Rule:

```text
Only Restart
When Needed
```

---

This is why:

```text
HANDLERS
```

exist.

---

# Chapter 24: What is a Handler?

Most Important Day 69 Concept.

---

Definition:

```text
A Handler is a task that runs ONLY when notified.
```

---

Real Life Analogy

Fire Alarm.

Normal Situation:

```text
Students Study
```

Alarm:

```text
Silent
```

---

Fire Detected:

```text
Alarm Triggered
```

---

ASCII

```text
Fire?

 │

 ├── No
 │
 ▼

Do Nothing

 │

 └── Yes
      │
      ▼

Alarm Sounds
```

---

Handlers work exactly the same way.

---

Normal Task:

```text
Changes Something
```

Handler:

```text
Waits Quietly
```

Only runs if notified.

---

# Handler Structure

Example:

```yaml
handlers:

  - name: Restart Nginx

    service:
      name: nginx
      state: restarted
```

---

Translation:

```text
I know HOW to restart Nginx.

But I will wait.

Call me only if needed.
```

---

ASCII

```text
Handler

   │

   ▼

Sleeping 😴

   │

   ▼

Waiting For Notification
```

---

# Chapter 25: What is notify?

Handler cannot start itself.

Someone must call it.

That is:

```yaml
notify:
```

---

Think:

```text
Door Bell
```

---

Delivery Person:

```text
Presses Bell
```

---

Inside House:

```text
Owner Reacts
```

---

ASCII

```text
notify

    │

    ▼

Handler Activated
```

---

Example

```yaml
- name: Update Website

  copy:
    content: New Content

  notify:
    - Restart Nginx
```

---

Translation

```text
If copy task changes file

then

Notify Restart Nginx
```

---

# Complete Flow

```yaml
tasks:

  - name: Update Website

    copy:
      content: Hello

    notify:
      - Restart Nginx

handlers:

  - name: Restart Nginx

    service:
      name: nginx
      state: restarted
```

---

ASCII

```text
Update Website

      │

      ▼

Changed?

      │

 ┌────┴────┐

 ▼         ▼

YES        NO

 ▼         ▼

Notify     Nothing

 ▼

Restart Nginx
```

---

# Most Important Handler Behavior

Interview Favorite.

---

Suppose:

```yaml
Task 1
notify Restart Nginx

Task 2
notify Restart Nginx

Task 3
notify Restart Nginx
```

---

Question:

```text
How many restarts happen?
```

Many beginners answer:

```text
3
```

Wrong.

---

Actual Answer:

```text
1
```

---

Why?

Ansible collects notifications.

Runs handler once.

At end of play.

---

ASCII

```text
Task 1 Changed
      │
      ▼
 Notify

Task 2 Changed
      │
      ▼
 Notify

Task 3 Changed
      │
      ▼
 Notify

================

Handler Runs ONCE
```

---

Huge Optimization.

---

# Chapter 26: Handler Execution Timing

Another Interview Favorite.

---

Question:

```text
When do handlers run?
```

Answer:

```text
End Of Play
```

---

Example

```yaml
Task 1

Task 2

Task 3

Handler
```

Execution:

```text
Task 1

Task 2

Task 3

Then

Handler
```

---

ASCII

```text
Play

│

├── Task 1

├── Task 2

├── Task 3

│

└── Handler Runs Here
```

---

Not immediately.

---

# Chapter 27: Check Mode (--check)

Huge DevOps Feature.

---

Imagine:

Before painting house:

```text
You Want Preview
```

---

Without Check Mode

```text
Paint Immediately
```

---

With Check Mode

```text
Show Me
What Would Change
```

---

ASCII

```text
Normal Run

     │

     ▼

Changes Applied

----------------

Check Mode

     │

     ▼

Preview Only
```

---

Command

```bash
ansible-playbook site.yml --check
```

---

Translation

```text
Do NOT change anything.

Just tell me what would happen.
```

---

Why Useful?

Production Servers.

You want safety.

---

Example

```text
Install Nginx

Create File

Restart Service
```

Check Mode shows:

```text
Would Install Nginx

Would Create File

Would Restart Service
```

without actually doing it.

---

Memory Trick

```text
--check

=

Dry Run
```

---

# Chapter 28: Diff Mode (--diff)

Think:

Microsoft Word Track Changes.

---

You want to know:

```text
Exactly What Changed
```

---

Command

```bash
ansible-playbook site.yml --diff
```

---

Example

Before

```text
Port=80
```

After

```text
Port=8080
```

---

Diff Shows:

```text
- Port=80
+ Port=8080
```

---

ASCII

```text
Old Version

     │

     ▼

Compare

     │

     ▼

New Version

     │

     ▼

Show Difference
```

---

Why DevOps Engineers Love It

Without Diff:

```text
Something Changed
```

---

With Diff:

```text
Exactly What Changed
```

---

# Check + Diff Together

Power Combination.

---

Command

```bash
ansible-playbook site.yml --check --diff
```

---

Translation

```text
Do Not Change Anything

But Show Me

Exactly What Would Change
```

---

Production Best Practice.

---

# Chapter 29: Verbosity

Sometimes Playbook Fails.

You need more information.

---

Think:

Doctor Investigation.

---

Patient Says:

```text
I Feel Sick
```

Not enough.

---

Doctor asks:

```text
More Details
```

---

Same with Ansible.

---

Normal Run

```bash
ansible-playbook site.yml
```

Gives basic information.

---

Need more details?

Use:

```bash
-v
```

---

# Verbosity Levels

Level 1

```bash
-v
```

Small extra details.

---

Level 2

```bash
-vv
```

More details.

---

Level 3

```bash
-vvv
```

Very detailed.

---

Level 4

```bash
-vvvv
```

Everything.

SSH included.

---

ASCII

```text
Normal

   │

   ▼

-v

   │

   ▼

-vv

   │

   ▼

-vvv

   │

   ▼

-vvvv
```

More and More Details.

---

# When To Use Each?

Daily Use:

```bash
-v
```

---

Troubleshooting:

```bash
-vv
```

---

Serious Debugging:

```bash
-vvv
```

---

Deep SSH Problems:

```bash
-vvvv
```

---

Memory Trick

```text
More V

=

More Visibility
```

---

# Chapter 30: Complete Execution Flow

What happens when you run:

```bash
ansible-playbook install-nginx.yml
```

---

Step 1

Read Inventory

```text
Which Servers?
```

---

Step 2

Read Playbook

```text
What Work?
```

---

Step 3

Connect Using SSH

```text
Login To Servers
```

---

Step 4

Gather Facts (if enabled)

```text
OS

Memory

IP

CPU
```

---

Step 5

Run Tasks

```text
yum

service

copy
```

---

Step 6

Detect Changes

```text
Changed?

Or

Already Correct?
```

---

Step 7

Send Notifications

```text
notify
```

---

Step 8

Run Handlers

```text
Restart Nginx
```

if required.

---

Step 9

Generate Report

Example:

```text
ok=5

changed=2

failed=0
```

---

ASCII

```text
Inventory
    │
    ▼
Playbook
    │
    ▼
SSH Connection
    │
    ▼
Tasks
    │
    ▼
Changes?
    │
    ▼
Notify
    │
    ▼
Handlers
    │
    ▼
Summary Report
```

---

# Most Important Interview Questions

### Q1: What is a Handler?

```text
A special task that runs only when notified.
```

---

### Q2: Why use Handlers?

```text
To avoid unnecessary service restarts.
```

---

### Q3: What does notify do?

```text
Triggers a handler when a task changes.
```

---

### Q4: When does a handler execute?

```text
At the end of the play.
```

---

### Q5: Three tasks notify same handler. How many executions?

```text
One.
```

---

### Q6: What is Check Mode?

```text
Preview changes without applying them.
```

Command:

```bash
ansible-playbook site.yml --check
```

---

### Q7: What is Diff Mode?

```text
Shows exact file/configuration differences.
```

Command:

```bash
ansible-playbook site.yml --diff
```

---

### Q8: What is -vvv?

```text
Increases verbosity for troubleshooting.
```

---

# 60-Second Revision

```text
Handler
=
Special Task

notify
=
Trigger Handler

Handler Runs
=
Only If Changed

Multiple Notifications
=
One Execution

Handler Timing
=
End Of Play

--check
=
Dry Run

--diff
=
Show Changes

-v
=
More Details

-vv
=
More Details

-vvv
=
Debug Level

-vvvv
=
SSH Debugging

Execution Flow

Inventory
↓
Playbook
↓
SSH
↓
Tasks
↓
Notify
↓
Handlers
↓
Report
```

---

## Memory Story

```text
Teacher (Ansible)

checks Homework.

If student changes notebook:

Teacher rings bell
(notify)

School Alarm
(handler)

waits quietly.

At end of day,

Alarm reacts once.

Before making changes,

Teacher can do:

--check
(Preview)

--diff
(See exact changes)

-vvv
(Get more details)
```

***

# DAY 69 MASTER NOTES (PART 5)

# Complete Mental Model + Troubleshooting + Interview Guide + Ultra Revision

### The Final Connection of Everything You Learned

---

# Chapter 31: The Entire Day 69 Story

Most students learn Ansible like this:

```text
Inventory

Playbook

Task

Module

Handler
```

as separate topics.

Wrong approach.

---

A DevOps Engineer should see:

```text
ONE COMPLETE SYSTEM
```

---

Imagine:

You are a school principal.

You want:

```text
Every Classroom

✓ Clean

✓ Fan ON

✓ Lights ON

✓ Projector Working
```

---

What do you need?

### Step 1

Need classroom list.

```text
Inventory
```

---

### Step 2

Need instructions.

```text
Playbook
```

---

### Step 3

Need work items.

```text
Tasks
```

---

### Step 4

Need tools.

```text
Modules
```

---

### Step 5

Need permissions.

```text
become:true
```

---

### Step 6

Need restart only if something changed.

```text
notify

handler
```

---

### Step 7

Need verification.

```text
curl
```

---

ASCII

```text
                INVENTORY

                     │

                     ▼

                PLAYBOOK

                     │

                     ▼

                   PLAY

                     │

                     ▼

                  TASKS

                     │

                     ▼

                 MODULES

                     │

                     ▼

              SERVER CHANGES

                     │

                     ▼

                 NOTIFY

                     │

                     ▼

                HANDLER

                     │

                     ▼

              SERVICE RESTART

                     │

                     ▼

                VERIFICATION
```

---

# Chapter 32: Complete Nginx Deployment Flow

This is what actually happened in Day 69.

---

Initial State

```text
Server Running

Nginx ❌

Website ❌

index.html ❌
```

---

Playbook Starts

```yaml
hosts: web
```

---

Ansible Checks Inventory

```ini
[web]
web-server
```

Found server.

---

SSH Connection

```text
Control Node

     │

SSH

     ▼

Managed Node
```

---

Task 1

```yaml
yum:
  name: nginx
  state: present
```

Result:

```text
Nginx Installed
```

---

Task 2

```yaml
service:
  name: nginx
  state: started
```

Result:

```text
Nginx Running
```

---

Task 3

```yaml
copy:
  content:
```

Result:

```text
index.html Created
```

---

Task 4

```yaml
notify:
```

if file changed.

---

Handler

```yaml
service:
  state: restarted
```

Result:

```text
Nginx Reloaded
```

---

Verification

```bash
curl http://server-ip
```

Result:

```html
<h1>Deployed By Ansible</h1>
```

---

Final State

```text
Server Running

Nginx ✅

Website ✅

index.html ✅
```

---

# Chapter 33: All Modules Comparison Table

This single table is enough for many interviews.

| Module     | Purpose              | Example          |
| ---------- | -------------------- | ---------------- |
| yum        | Install Packages     | nginx            |
| service    | Manage Services      | start nginx      |
| copy       | Create/Copy Files    | index.html       |
| file       | Manage Files/Folders | create directory |
| command    | Run Linux Command    | hostname         |
| shell      | Run Shell Command    | grep | awk       |
| lineinfile | Modify One Line      | Port=8080        |
| debug      | Print Values         | show output      |
| register   | Store Output         | save hostname    |

---

# How To Decide Which Module?

---

Need package?

```text
yum
```

---

Need service?

```text
service
```

---

Need folder?

```text
file
```

---

Need simple command?

```text
command
```

---

Need pipes?

```text
shell
```

---

Need save output?

```text
register
```

---

Need show output?

```text
debug
```

---

Need edit one line?

```text
lineinfile
```

---

ASCII

```text
Need Package?
      │
      ▼
     yum

Need Service?
      │
      ▼
   service

Need Folder?
      │
      ▼
     file

Need Pipe?
      │
      ▼
    shell

Need One Line Change?
      │
      ▼
  lineinfile
```

---

# Chapter 34: Most Common Beginner Mistakes

These are exactly what Day 69 beginners usually hit.

---

## Mistake 1

Inventory Missing

Playbook:

```yaml
hosts: web
```

Inventory:

```text
Missing
```

Error:

```text
Could not match supplied host pattern
```

---

Reason:

```text
Ansible Cannot Find Host Group
```

---

Fix:

```ini
[web]
web-server
```

---

# Mistake 2

Forgot become:true

Task:

```yaml
yum:
  name: nginx
```

Error:

```text
Permission Denied
```

---

Fix:

```yaml
become: true
```

---

# Mistake 3

Using command Instead Of shell

Wrong:

```yaml
command:
  cat file | grep nginx
```

---

Error:

```text
Pipe Not Understood
```

---

Correct:

```yaml
shell:
  cat file | grep nginx
```

---

# Mistake 4

Restarting Service Every Run

Wrong:

```yaml
copy:

service:
  state: restarted
```

---

Problem:

```text
Unnecessary Restart
```

---

Correct:

```yaml
notify

handler
```

---

# Mistake 5

Using copy For Small Change

Wrong:

```text
Replace Entire File
```

---

Better:

```yaml
lineinfile
```

---

# Chapter 35: Understanding Ansible Output

Example

```text
ok=4

changed=2

failed=0

unreachable=0
```

---

What does each mean?

---

## ok

```text
Task Checked

Nothing Needed
```

Example:

```text
Nginx Already Installed
```

---

## changed

```text
Task Modified Something
```

Example:

```text
Installed Nginx

Created File
```

---

## failed

```text
Task Failed
```

Example:

```text
Syntax Error

Permission Error
```

---

## unreachable

```text
Cannot Connect Server
```

Example:

```text
SSH Failure
```

---

ASCII

```text
ok
=
Already Correct

changed
=
Modified

failed
=
Broken

unreachable
=
Cannot Connect
```

---

# Chapter 36: Ansible Interview Questions

## Beginner Level

---

What is Ansible?

```text
Configuration Management
and Automation Tool
```

---

What is Inventory?

```text
List Of Managed Hosts
```

---

What is Playbook?

```text
YAML File Containing Tasks
```

---

What is a Play?

```text
Collection Of Tasks
```

---

What is a Task?

```text
Single Unit Of Work
```

---

What is a Module?

```text
Reusable Automation Tool
```

---

What does become:true do?

```text
Privilege Escalation

Equivalent To sudo
```

---

Difference Between Play And Task?

```text
Play = Group Of Tasks

Task = Single Action
```

---

# Intermediate Level

Difference Between command and shell?

```text
command:
No Shell

shell:
Uses Shell
Supports Pipes
```

---

What is register?

```text
Stores Task Output
```

---

What is debug?

```text
Displays Variables
and Output
```

---

What is lineinfile?

```text
Modify Single Line
Without Replacing Entire File
```

---

What is Idempotency?

```text
Repeated Execution

Same Final State
```

---

Example:

```text
Install Nginx Once

Run Again

No Changes
```

---

# Advanced Level

Why Handlers?

```text
Avoid Unnecessary Restarts
```

---

When Do Handlers Execute?

```text
End Of Play
```

---

Three Tasks Notify Same Handler.

How Many Restarts?

```text
One
```

---

Purpose Of Check Mode?

```text
Preview Changes
```

---

Purpose Of Diff Mode?

```text
Show Exact Differences
```

---

# Chapter 37: The Golden DevOps Mental Model

This one diagram connects everything.

---

ASCII

```text
Developer

    │

    ▼

Writes Playbook

    │

    ▼

Inventory Chooses Hosts

    │

    ▼

SSH Connection

    │

    ▼

Play Starts

    │

    ▼

Tasks Execute

    │

    ▼

Modules Perform Work

    │

    ▼

Changes Detected

    │

    ▼

notify

    │

    ▼

Handlers Execute

    │

    ▼

Service Updated

    │

    ▼

Verification

    │

    ▼

Success
```

---

# ONE PAGE MASTER REVISION SHEET

```text
Ansible
=
Automation Tool

Inventory
=
Server List

Playbook
=
YAML Instructions

Play
=
Collection Of Tasks

Task
=
Single Action

Module
=
Reusable Tool

become:true
=
sudo

yum
=
Install Package

service
=
Manage Service

copy
=
Create/Copy File

file
=
Manage Files/Folders

command
=
Simple Commands

shell
=
Complex Commands

register
=
Store Output

debug
=
Show Output

lineinfile
=
Modify One Line

notify
=
Trigger Handler

handler
=
Run Only If Changed

Idempotency
=
Same Result Every Run

--check
=
Dry Run

--diff
=
Show Difference

-v
=
More Logs

ok
=
No Change

changed
=
Modified

failed
=
Task Failed

unreachable
=
SSH Problem
```

---

# 60-SECOND ULTRA REVISION

```text
Inventory
tells Ansible where to work.

Playbook
tells Ansible what to do.

Play
contains tasks.

Tasks use modules.

yum installs software.

service starts software.

copy creates files.

file manages folders.

command runs simple commands.

shell runs complex commands.

register stores output.

debug shows output.

lineinfile edits one line.

become:true means sudo.

notify triggers handlers.

handlers restart services only when needed.

Idempotency means
same desired state every run.

--check previews changes.

--diff shows exact changes.

-vvv helps debugging.
```

---

# DAY 69 IN ONE SENTENCE

```text
Ansible uses an Inventory to find servers,
a Playbook to define desired state,
Tasks and Modules to perform work,
Handlers to react only when changes occur,
and Idempotency to ensure repeated runs stay safe and predictable.
```

***************************

# Day 69 – Ansible Playbooks

# Part A – Task 1: First Ansible Playbook (Install and Manage Nginx)

---

# Objective

The goal of this task is to:

* Create the first Ansible playbook
* Install Nginx automatically
* Start and enable the Nginx service
* Deploy a custom web page
* Understand idempotency
* Learn how inventory and managed hosts work
* Troubleshoot connectivity and package-manager issues

---

# Step 1 – Check the Target Operating System

Before writing a playbook, verify the operating system of the managed node.

SSH into the target EC2 instance:

```bash
cat /etc/os-release
```

Possible outputs:

### Ubuntu

```text
NAME="Ubuntu"
```

Uses:

```bash
apt
```

### Amazon Linux / RHEL / CentOS

```text
NAME="Amazon Linux"
```

Uses:

```bash
yum
```

or

```bash
dnf
```

---

# Why This Matters

Package management modules are OS-specific.

### Ubuntu

```yaml
apt:
  name: nginx
  state: present
```

### Amazon Linux

```yaml
yum:
  name: nginx
  state: present
```

Using the wrong module causes playbook failures.

---

# Step 2 – Create Project Directory

On the Ansible control node:

```bash
mkdir -p ~/ansible/day-69
cd ~/ansible/day-69
```

---

# Step 3 – Create Playbook

Create:

```bash
nano install-nginx.yml
```

---

# Initial Ubuntu Version

```yaml
---
- name: Install and start Nginx on web servers
  hosts: web
  become: true

  tasks:

    - name: Install Nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Start and enable Nginx
      service:
        name: nginx
        state: started
        enabled: true

    - name: Create a custom index page
      copy:
        content: "<h1>Deployed by Ansible - TerraWeek Server</h1>"
        dest: /var/www/html/index.html
```

---

# Playbook Breakdown

```yaml
---
```

YAML document start.

---

```yaml
- name: Install and start Nginx on web servers
```

Play name.

---

```yaml
hosts: web
```

Target inventory group.

---

```yaml
become: true
```

Run tasks using sudo privileges.

---

```yaml
tasks:
```

Collection of actions to perform.

---

```yaml
apt:
```

Module used to install packages.

---

```yaml
service:
```

Module used to manage services.

---

```yaml
copy:
```

Module used to copy files or content to managed nodes.

---

# Step 4 – Syntax Check

Always validate before execution.

```bash
ansible-playbook --syntax-check install-nginx.yml
```

Expected:

```text
playbook: install-nginx.yml
```

No errors means YAML syntax is valid.

---

# Step 5 – First Execution

Run:

```bash
ansible-playbook install-nginx.yml
```

Expected:

```text
TASK [Install Nginx]
changed

TASK [Start and enable Nginx]
changed

TASK [Create a custom index page]
changed
```

---

Expected recap:

```text
PLAY RECAP

web-server : ok=3 changed=3 failed=0
```

---

# Step 6 – Test Idempotency

Run again:

```bash
ansible-playbook install-nginx.yml
```

Expected:

```text
TASK [Install Nginx]
ok

TASK [Start and enable Nginx]
ok

TASK [Create a custom index page]
ok
```

---

Expected recap:

```text
PLAY RECAP

web-server : ok=3 changed=0 failed=0
```

---

# What is Idempotency?

Idempotency means:

> Running the same playbook repeatedly produces the same desired state.

---

## First Run

Nginx not installed.

```text
Install package
```

Nginx service not running.

```text
Start service
```

Web page not present.

```text
Create page
```

Result:

```text
changed=3
```

---

## Second Run

Everything already exists.

Nothing needs modification.

Result:

```text
changed=0
```

---

# Learning

Ansible does not repeat work unnecessarily.

This behavior is called:

```text
Idempotency
```

One of the most important Ansible concepts.

---

# Inventory Problem Encountered

The playbook targeted:

```yaml
hosts: web
```

But Ansible reported:

```text
No inventory was parsed
Could not match supplied host pattern, ignoring: web
```

---

# Why It Happened

Ansible knew only:

```text
localhost
```

No inventory file existed.

Therefore:

```text
web
```

group could not be found.

---

# Solution – Create Inventory

Create:

```bash
nano inventory.ini
```

---

Example:

```ini
[web]
13.233.xx.xx ansible_user=ubuntu
```

Ubuntu server example.

---

Amazon Linux example:

```ini
[web]
13.233.xx.xx ansible_user=ec2-user
```

---

With PEM key:

```ini
[web]
13.233.xx.xx ansible_user=ec2-user ansible_ssh_private_key_file=~/.ssh/ansible-lab.pem
```

---

# Run Using Inventory

```bash
ansible-playbook -i inventory.ini install-nginx.yml
```

---

# Verify Inventory

```bash
ansible-inventory -i inventory.ini --list
```

---

List hosts:

```bash
ansible web -i inventory.ini --list-hosts
```

---

Expected:

```text
hosts (1):
  web-server
```

---

# Default Inventory Configuration

Create:

```bash
nano ansible.cfg
```

Add:

```ini
[defaults]
inventory = inventory.ini
```

---

Now Ansible automatically uses:

```text
inventory.ini
```

---

# Placeholder Hostname Problem

Inventory initially contained:

```ini
[web]
your-server-ip
```

Ansible attempted:

```text
ssh your-server-ip
```

and failed.

---

Error:

```text
Could not resolve hostname your-server-ip
```

---

# Fix

Replace placeholder with actual IP:

```ini
[web]
13.233.xxx.xxx ansible_user=ec2-user
```

---

# Connectivity Testing

Verify:

```bash
ansible web -m ping
```

Expected:

```json
{
  "ping": "pong"
}
```

---

# Reusing Previous Inventory

Instead of creating a new inventory, the previous inventory from earlier days was reused.

Example:

```ini
[web]
web-server ansible_host=16.146.52.72

[app]
app-server ansible_host=35.92.55.203

[db]
db-server ansible_host=16.144.35.155

[all:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=/home/malathi/.ssh/ansible-lab.pem
```

---

# Dynamic IP Challenge

AWS EC2 public IPs change when instances are stopped and started.

This caused previously working inventories to become invalid.

---

# Symptoms

```text
ssh: connect to host <IP> port 22: Connection timed out
```

---

# Possible Causes

### Instance stopped

### Instance terminated

### Public IP changed

### Security group blocking SSH

### Wrong inventory entries

---

# AWS Verification

Check:

### EC2 state

```text
Running
```

---

### Public IPs

Compare with inventory.

---

### Security Group

Verify:

```text
SSH
TCP 22
```

is allowed.

---

# Manual SSH Test

```bash
ssh -i ~/.ssh/ansible-lab.pem ec2-user@<IP>
```

---

If SSH fails:

```text
Ansible will fail too
```

because Ansible uses SSH.

---

# Infrastructure Was Deleted

It was later discovered that previous EC2 instances had been deleted.

Therefore:

```text
Inventory IPs were invalid.
```

---

# Recovery Options

## Option 1 – Create New EC2

Recommended.

Launch:

```text
Amazon Linux 2023
```

with:

```text
SSH (22)
HTTP (80)
```

open.

---

## Option 2 – Use Localhost

Inventory:

```ini
[web]
localhost ansible_connection=local
```

Useful for learning.

---

# Terraform-Based Recovery

Since infrastructure had originally been created using Terraform:

```bash
terraform apply
```

could recreate everything.

---

Commands:

```bash
terraform init
terraform plan
terraform apply
```

---

Then retrieve new IPs:

```bash
terraform output
```

---

# Major Discovery

After connectivity was restored, playbook execution failed again.

Error:

```text
No such file or directory: b'apt-get'
```

---

# Root Cause

The instance was:

```text
Amazon Linux
```

not:

```text
Ubuntu
```

---

Amazon Linux uses:

```bash
yum
```

or

```bash
dnf
```

instead of:

```bash
apt
```

---

# Updated Playbook

```yaml
---
- name: Install and start Nginx on web servers
  hosts: web
  become: true

  tasks:

    - name: Install Nginx
      yum:
        name: nginx
        state: present

    - name: Start and enable Nginx
      service:
        name: nginx
        state: started
        enabled: true

    - name: Create a custom index page
      copy:
        content: "<h1>Deployed by Ansible - TerraWeek Server</h1>"
        dest: /usr/share/nginx/html/index.html
```

---

# Python Interpreter Warning

Warning:

```text
Platform linux on host web-server is using the discovered Python interpreter
```

---

Fix:

```ini
[all:vars]
ansible_python_interpreter=/usr/bin/python3
```

---

Verify path:

```bash
ansible web -m command -a "which python3"
```

---

# Successful Run

Output:

```text
TASK [Install Nginx]
changed

TASK [Start and enable Nginx]
changed

TASK [Create a custom index page]
changed
```

---

Recap:

```text
web-server : ok=4 changed=3 failed=0
```

---

# Verifying Nginx

Check service:

```bash
sudo systemctl status nginx
```

Expected:

```text
Active: active (running)
```

---

Check locally:

```bash
curl localhost
```

Expected:

```html
<h1>Deployed by Ansible - TerraWeek Server</h1>
```

---

# HTTP Access Problem

External:

```bash
curl http://PUBLIC_IP
```

hung indefinitely.

---

# Root Cause

AWS Security Group did not allow:

```text
HTTP
Port 80
```

---

# Required Rules

```text
SSH   22
HTTP  80
```

---

Example:

```text
SSH   TCP 22   My IP
HTTP  TCP 80   0.0.0.0/0
```

---

# DevOps Learning

Installing software is not enough.

A service becomes usable only when:

### Installed

### Started

### Configured

### Network access allowed

All four are required.

---

# Final Verification

```bash
curl http://PUBLIC_IP
```

Expected:

```html
<h1>Deployed by Ansible - TerraWeek Server</h1>
```

---

# Task 1 Summary

### Created first Ansible playbook

### Learned inventory configuration

### Learned idempotency

### Fixed SSH connectivity issues

### Rebuilt infrastructure using Terraform

### Converted playbook from apt to yum

### Installed and started Nginx

### Deployed a custom web page

### Opened HTTP access through AWS Security Groups

### Verified successful deployment

---

# Master in 60 Seconds

✅ Playbook = Collection of tasks

✅ Inventory = List of managed hosts

✅ Ansible uses SSH to connect

✅ `become: true` = sudo privileges

✅ `yum` for Amazon Linux

✅ `apt` for Ubuntu

✅ First run = `changed`

✅ Second run = `ok`

✅ This behavior is called **idempotency**

✅ Installing a service is different from exposing it

✅ AWS Security Groups must allow required ports

**End of Part A**

***

# Day 69 – Ansible Playbooks

# Part B – Task 2: Understanding Playbook Structure

---

# Objective

The goal of this task is to understand:

* What a playbook is
* What a play is
* What a task is
* What a module is
* How plays, tasks, and modules relate to each other
* The purpose of `become: true`
* What happens when a task fails
* How multiple plays can exist in a single playbook

This task focuses on understanding Ansible architecture rather than creating infrastructure.

---

# Visual Overview

```text
Playbook
│
├── Play
│   │
│   ├── Hosts
│   ├── Become
│   └── Tasks
│        │
│        ├── Task 1
│        │    └── Module
│        │
│        ├── Task 2
│        │    └── Module
│        │
│        └── Task 3
│             └── Module
│
└── Play
     │
     └── Tasks
```

---

# Annotated Playbook

Using the Nginx playbook from Task 1:

```yaml
---
# YAML document start

- name: Install and start Nginx on web servers

  # PLAY
  # A play targets a group of hosts
  # and executes one or more tasks

  hosts: web

  # Inventory group

  become: true

  # Run tasks with sudo privileges

  tasks:

    # List of tasks

    - name: Install Nginx

      # TASK
      # One unit of work

      yum:

        # MODULE
        # Package installation module

        name: nginx
        state: present

    - name: Start and enable Nginx

      service:

        name: nginx
        state: started
        enabled: true

    - name: Create a custom index page

      copy:

        content: "<h1>Deployed by Ansible - TerraWeek Server</h1>"
        dest: /usr/share/nginx/html/index.html
```

---

# What is a Playbook?

A playbook is an Ansible file written in YAML that defines automation tasks.

Think of it as:

```text
Automation Blueprint
```

A playbook can contain:

```text
One Play
or
Multiple Plays
```

---

# Example

```yaml
---
- name: Configure web servers
  hosts: web

  tasks:
    - name: Install Nginx
      yum:
        name: nginx
        state: present
```

This entire file is:

```text
Playbook
```

---

# What is a Play?

A play defines:

```text
Which hosts?
```

and

```text
What tasks?
```

---

# Example

```yaml
- name: Configure web servers
  hosts: web
```

This is a play.

---

# Responsibilities of a Play

A play specifies:

### Target hosts

```yaml
hosts: web
```

---

### Privilege escalation

```yaml
become: true
```

---

### Tasks to execute

```yaml
tasks:
```

---

# Simple Analogy

```text
Play = Project
Task = Activity
Module = Tool
```

Example:

```text
Build House (Play)

├── Lay Foundation (Task)
├── Build Walls (Task)
├── Paint House (Task)

Tools Used:
Shovel
Hammer
Paint Brush
```

---

# What is a Task?

A task is the smallest executable unit in Ansible.

Each task performs:

```text
One action
```

---

# Example

```yaml
- name: Install Nginx
  yum:
    name: nginx
    state: present
```

Task:

```text
Install Nginx
```

---

Another example:

```yaml
- name: Start Nginx
  service:
    name: nginx
    state: started
```

Task:

```text
Start Service
```

---

# Rule

One task should do:

```text
One logical operation
```

---

# What is a Module?

Modules are the actual programs that perform work.

Think of modules as:

```text
Ansible Tools
```

---

# Common Modules

### yum

Install packages

```yaml
yum:
  name: nginx
  state: present
```

---

### dnf

Package management on newer systems

```yaml
dnf:
  name: nginx
  state: present
```

---

### service

Manage services

```yaml
service:
  name: nginx
  state: started
```

---

### copy

Copy files

```yaml
copy:
  src: app.conf
  dest: /etc/app.conf
```

---

### file

Create files/directories

```yaml
file:
  path: /opt/myapp
  state: directory
```

---

### command

Run commands

```yaml
command: df -h
```

---

### shell

Run shell commands

```yaml
shell: ps aux | wc -l
```

---

### lineinfile

Modify lines inside files

```yaml
lineinfile:
  path: /etc/environment
  line: TZ=Asia/Kolkata
```

---

# Relationship Between Play, Task, and Module

```text
Playbook
│
└── Play
     │
     └── Task
          │
          └── Module
```

Example:

```text
Playbook
│
└── Configure Web Server
     │
     ├── Install Nginx
     │    └── yum
     │
     ├── Start Service
     │    └── service
     │
     └── Copy Page
          └── copy
```

---

# Question 1

## What is the difference between a Play and a Task?

### Play

```text
Defines:
Which hosts?
Which tasks?
```

Contains:

```text
One or more tasks
```

---

### Task

```text
Performs:
One specific action
```

Uses:

```text
One module
```

---

# Comparison Table

| Play                    | Task            |
| ----------------------- | --------------- |
| Targets hosts           | Performs action |
| Contains tasks          | Uses module     |
| Larger unit             | Smallest unit   |
| Defines execution scope | Executes work   |

---

# Quick Memory Trick

```text
Play = Where?
Task = What?
Module = How?
```

---

# Question 2

## Can a Playbook Contain Multiple Plays?

### Yes

A playbook can contain multiple plays.

---

# Example

```yaml
---
- name: Configure web servers
  hosts: web

  tasks:

    - name: Install Nginx
      yum:
        name: nginx
        state: present

- name: Configure database servers
  hosts: db

  tasks:

    - name: Install MySQL
      yum:
        name: mysql
        state: present
```

---

# Execution Flow

```text
Play 1
Configure web servers

        ↓

Install Nginx

        ↓

Play 2
Configure database servers

        ↓

Install MySQL
```

---

# Benefits of Multiple Plays

Different server roles can be configured within one playbook.

Example:

```text
Web Servers
Application Servers
Database Servers
```

all handled in one file.

---

# Question 3

## What Does `become: true` Do?

### Purpose

Provides:

```text
Privilege Escalation
```

Equivalent to:

```bash
sudo
```

---

# Without Become

```yaml
- name: Install Nginx
  yum:
    name: nginx
    state: present
```

May fail:

```text
Permission Denied
```

because package installation requires root access.

---

# With Become

```yaml
become: true
```

Ansible automatically uses sudo.

---

# Play-Level Become

```yaml
- name: Configure web servers
  hosts: web
  become: true
```

Every task receives root privileges.

---

Execution:

```text
Task 1 → sudo
Task 2 → sudo
Task 3 → sudo
Task 4 → sudo
```

---

# Task-Level Become

```yaml
- name: Install Nginx
  become: true

  yum:
    name: nginx
    state: present
```

Only that task runs with elevated privileges.

---

Execution:

```text
Task 1 → sudo
Task 2 → normal user
Task 3 → normal user
```

---

# When to Use Task-Level Become

When only a few tasks require root access.

---

# When to Use Play-Level Become

When most tasks need root access.

Most infrastructure playbooks use:

```yaml
become: true
```

at play level.

---

# Question 4

## What Happens When a Task Fails?

Default behavior:

```text
Ansible stops running
remaining tasks on that host.
```

---

# Example

```yaml
tasks:

  - name: Install Nginx
    yum:
      name: nginx
      state: present

  - name: Run Invalid Command
    command: invalid_command

  - name: Create File
    file:
      path: /tmp/test
      state: touch
```

---

# Execution

```text
Install Nginx
     OK

Run Invalid Command
     FAILED

Create File
     SKIPPED
```

---

# Why?

Ansible assumes something went wrong.

Continuing could create an inconsistent state.

---

# Host-Specific Behavior

Suppose:

```text
web-server
app-server
db-server
```

---

If:

```text
web-server fails
```

Ansible stops tasks on:

```text
web-server
```

only.

---

Other hosts continue:

```text
app-server
db-server
```

---

# Example

```text
web-server → FAILED
app-server → OK
db-server → OK
```

---

# Ignore Errors

Can be overridden using:

```yaml
ignore_errors: true
```

Example:

```yaml
- name: Run Invalid Command
  command: invalid_command
  ignore_errors: true
```

---

Result:

```text
FAILED
but continue execution
```

---

# Learning from Real Lab Experience

While running Day 69 tasks:

```text
web-server → nginx installed
app-server → nginx missing
db-server → nginx missing
```

---

Service task:

```yaml
service:
  name: nginx
  state: started
```

produced:

```text
Could not find requested service nginx
```

on:

```text
app-server
db-server
```

---

Result:

```text
app-server → failed
db-server → failed
```

while:

```text
web-server → continued
```

---

This demonstrated real-world task failure behavior.

---

# Key Takeaways

### Playbook

```text
Complete automation file
```

---

### Play

```text
Targets hosts
Contains tasks
```

---

### Task

```text
Single unit of work
```

---

### Module

```text
Performs actual action
```

---

### become: true

```text
Provides sudo privileges
```

---

### Multiple Plays

```text
Supported in one playbook
```

---

### Task Failure

```text
Stops remaining tasks on that host
```

unless:

```yaml
ignore_errors: true
```

is used.

---

# Master in 60 Seconds

```text
Playbook = Entire YAML automation file

Play = Which servers?

Task = What action?

Module = How action is performed?

become: true = sudo access

One playbook can contain multiple plays

One play contains multiple tasks

One task usually uses one module

Task failure stops execution on that host

ignore_errors: true allows continuation
```

***

# Day 69 – Ansible Playbooks

# Part C – Task 3: Essential Ansible Modules

---

# Objective

The purpose of this task is to understand the most commonly used Ansible modules that are used daily in DevOps and Infrastructure Automation.

By the end of this task, you should understand:

* What a module is
* Why modules are preferred over shell commands
* When to use each module
* Real-world DevOps use cases
* Idempotent behavior of modules
* Differences between commonly confused modules

---

# What is a Module?

A module is a reusable unit of code that performs a specific task on a managed node.

Think of a module as:

```text
Ansible's built-in tool
```

Examples:

```text
Install package
Start service
Create file
Copy file
Run command
Create directory
Manage users
Manage groups
```

All these operations are performed using modules.

---

# Architecture

```text
Playbook
│
└── Play
     │
     └── Task
          │
          └── Module
```

Example:

```yaml
- name: Install Nginx
  yum:
    name: nginx
    state: present
```

Task:

```text
Install Nginx
```

Module:

```text
yum
```

---

# Why Modules Instead of Shell Commands?

Without modules:

```yaml
- name: Install Nginx
  shell: yum install nginx -y
```

Works.

But:

```text
Not Idempotent
Harder to maintain
Less readable
Less portable
```

---

# With Modules

```yaml
- name: Install Nginx
  yum:
    name: nginx
    state: present
```

Benefits:

```text
Idempotent
Readable
Structured
Safe
Platform aware
```

---

# Rule

Always prefer:

```text
Module
```

over:

```text
Shell Command
```

whenever possible.

---

# Module 1 – yum

---

# Purpose

Install, remove, and manage packages on:

```text
Amazon Linux
RHEL
CentOS
Rocky Linux
AlmaLinux
```

---

# Install Package

```yaml
- name: Install Nginx
  yum:
    name: nginx
    state: present
```

---

# Execution Logic

If package absent:

```text
Install package
```

---

If package already installed:

```text
Do nothing
```

---

Result:

```text
changed=0
```

on subsequent runs.

---

# Remove Package

```yaml
- name: Remove Nginx
  yum:
    name: nginx
    state: absent
```

---

# Update Package

```yaml
- name: Update Nginx
  yum:
    name: nginx
    state: latest
```

---

# States

| State   | Meaning           |
| ------- | ----------------- |
| present | Ensure installed  |
| absent  | Ensure removed    |
| latest  | Upgrade to latest |

---

# Real DevOps Example

```yaml
- name: Install Git
  yum:
    name: git
    state: present
```

Used before:

```text
Git clone
Jenkins jobs
CI/CD pipelines
```

---

# Module 2 – apt

---

# Purpose

Package management for:

```text
Ubuntu
Debian
```

---

# Example

```yaml
- name: Install Nginx
  apt:
    name: nginx
    state: present
    update_cache: yes
```

---

# Why update_cache?

Equivalent to:

```bash
apt update
```

before installation.

---

# Common States

```text
present
absent
latest
```

Same concept as yum.

---

# Difference

```text
Ubuntu      → apt
Amazon Linux → yum/dnf
```

---

# Module 3 – service

---

# Purpose

Manage services.

Examples:

```text
Nginx
Apache
Docker
Jenkins
MySQL
```

---

# Start Service

```yaml
- name: Start Nginx
  service:
    name: nginx
    state: started
```

---

# Stop Service

```yaml
- name: Stop Nginx
  service:
    name: nginx
    state: stopped
```

---

# Restart Service

```yaml
- name: Restart Nginx
  service:
    name: nginx
    state: restarted
```

---

# Enable Service

```yaml
- name: Enable Nginx
  service:
    name: nginx
    enabled: true
```

Equivalent:

```bash
systemctl enable nginx
```

---

# Combined Example

```yaml
- name: Enable and Start Nginx
  service:
    name: nginx
    state: started
    enabled: true
```

---

# States

| State     | Meaning         |
| --------- | --------------- |
| started   | Ensure running  |
| stopped   | Ensure stopped  |
| restarted | Restart service |
| reloaded  | Reload config   |

---

# Real DevOps Example

After deployment:

```yaml
- name: Restart Application
  service:
    name: myapp
    state: restarted
```

---

# Module 4 – copy

---

# Purpose

Copy files or content to managed hosts.

---

# Copy Local File

```yaml
- name: Copy Config File
  copy:
    src: nginx.conf
    dest: /etc/nginx/nginx.conf
```

---

# Copy Inline Content

```yaml
- name: Create HTML Page
  copy:
    content: "<h1>Hello from Ansible</h1>"
    dest: /usr/share/nginx/html/index.html
```

---

# Day 69 Example

```yaml
- name: Create Custom Page
  copy:
    content: "<h1>Deployed by Ansible</h1>"
    dest: /usr/share/nginx/html/index.html
```

---

# Why Useful?

Commonly used for:

```text
Configuration files
Application files
Environment files
Startup scripts
Web pages
```

---

# Module 5 – file

---

# Purpose

Manage:

```text
Files
Directories
Permissions
Ownership
Links
```

---

# Create Directory

```yaml
- name: Create App Directory
  file:
    path: /opt/myapp
    state: directory
```

---

# Create Empty File

```yaml
- name: Create File
  file:
    path: /tmp/app.log
    state: touch
```

---

# Delete File

```yaml
- name: Remove File
  file:
    path: /tmp/app.log
    state: absent
```

---

# Set Permissions

```yaml
- name: Set Permissions
  file:
    path: /opt/myapp
    mode: '0755'
```

---

# Common States

| State     | Meaning      |
| --------- | ------------ |
| file      | Regular file |
| directory | Directory    |
| touch     | Create file  |
| absent    | Delete       |

---

# Real DevOps Example

Create application directory before deployment.

```yaml
- name: Create Deployment Folder
  file:
    path: /opt/app
    state: directory
```

---

# Module 6 – command

---

# Purpose

Execute commands directly.

---

# Example

```yaml
- name: Check Disk Space
  command: df -h
```

---

# Another Example

```yaml
- name: Show Uptime
  command: uptime
```

---

# Important Characteristic

```text
No Shell Processing
```

Meaning:

```text
No Pipes
No Redirects
No Wildcards
```

---

# Works

```yaml
command: uptime
```

---

# Fails

```yaml
command: ps aux | wc -l
```

because:

```text
Pipe requires shell
```

---

# Use Case

Simple command execution.

---

# Module 7 – shell

---

# Purpose

Run commands through shell interpreter.

---

# Example

```yaml
- name: Count Processes
  shell: ps aux | wc -l
```

---

# Example

```yaml
- name: Disk Usage
  shell: df -h | grep xvda
```

---

# Supports

```text
Pipes
Redirects
Wildcards
Variables
Shell Operators
```

---

# Examples

```yaml
shell: cat file.txt | grep nginx
```

```yaml
shell: echo hello > test.txt
```

```yaml
shell: ls *.log
```

---

# Warning

Shell is powerful but:

```text
Less predictable
Less idempotent
Higher risk
```

Use only when necessary.

---

# command vs shell

| command      | shell                |
| ------------ | -------------------- |
| Safer        | More powerful        |
| No shell     | Uses shell           |
| No pipes     | Pipes allowed        |
| No redirects | Redirects allowed    |
| Preferred    | Use only if required |

---

# Module 8 – lineinfile

---

# Purpose

Modify specific lines in files.

---

# Example

```yaml
- name: Add Timezone
  lineinfile:
    path: /etc/environment
    line: TZ=Asia/Kolkata
```

---

# Result

Before:

```text
PATH=/usr/bin
```

After:

```text
PATH=/usr/bin
TZ=Asia/Kolkata
```

---

# Why Important?

Avoids replacing the entire file.

Changes only:

```text
Specific line
```

---

# Common Uses

```text
Environment variables
SSH configuration
Application settings
OS configuration
```

---

# Real DevOps Example

Disable root SSH login.

```yaml
- name: Disable Root Login
  lineinfile:
    path: /etc/ssh/sshd_config
    regexp: '^PermitRootLogin'
    line: 'PermitRootLogin no'
```

---

# Most Frequently Used DevOps Modules

```text
yum
apt
service
copy
file
command
shell
lineinfile
```

These modules cover a large percentage of daily infrastructure automation tasks.

---

# Real Infrastructure Example

Deploy Web Server

```text
Install Package
      ↓
Start Service
      ↓
Create Directory
      ↓
Copy Config
      ↓
Update Config Line
      ↓
Restart Service
```

---

Modules Used:

```text
yum
service
file
copy
lineinfile
service
```

---

# Learning from Day 69

While building the Nginx deployment:

```text
yum
service
copy
```

were used together.

---

Workflow:

```text
Install Nginx
      ↓
Start Nginx
      ↓
Create Web Page
      ↓
Verify Access
```

---

This demonstrated how multiple modules cooperate to achieve a complete deployment.

---

# Key Takeaways

### Module

```text
Reusable Ansible tool
```

---

### yum

```text
Package management for Amazon Linux/RHEL
```

---

### apt

```text
Package management for Ubuntu/Debian
```

---

### service

```text
Manage services
```

---

### copy

```text
Copy files/content
```

---

### file

```text
Manage files and directories
```

---

### command

```text
Run simple commands
```

---

### shell

```text
Run shell commands with pipes and redirects
```

---

### lineinfile

```text
Modify specific lines in files
```

---

# Master in 60 Seconds

```text
Module = Ansible tool

yum = Install packages on Amazon Linux

apt = Install packages on Ubuntu

service = Start/Stop/Restart services

copy = Copy files/content

file = Create/Delete files/directories

command = Run simple commands

shell = Run commands with pipes

lineinfile = Edit one line in a file

Prefer modules over shell commands

Modules are idempotent and safer
```

***

# Day 69 – Ansible Playbooks

# Part D – Task 4: Handlers

---

# Objective

The goal of this task is to understand:

* What handlers are
* Why handlers are needed
* How handlers work internally
* Difference between a normal task and a handler
* notify mechanism
* Real-world DevOps use cases
* Handler execution flow
* Multiple notifications
* Common interview questions

Handlers are one of the most important Ansible concepts because they help prevent unnecessary service restarts.

---

# Problem Without Handlers

Consider a playbook:

```yaml
- name: Copy Nginx Config
  copy:
    src: nginx.conf
    dest: /etc/nginx/nginx.conf

- name: Restart Nginx
  service:
    name: nginx
    state: restarted
```

---

# First Run

```text
Copy Config      → changed
Restart Service  → executed
```

Everything looks fine.

---

# Second Run

Nothing changed.

But:

```text
Copy Config      → ok
Restart Service  → executed
```

Nginx still restarts.

---

# Why Is This Bad?

Restarting services unnecessarily can cause:

```text
Temporary downtime
Performance impact
User disruption
Unnecessary resource usage
```

---

# Example

Imagine:

```text
Nginx
Jenkins
MySQL
Redis
Docker
```

restarting every playbook run.

That would be inefficient.

---

# Solution

Use:

```text
Handlers
```

---

# What Is a Handler?

A handler is a special task that runs only when notified.

Think of it as:

```text
Conditional Task
```

---

# Rule

Normal Task:

```text
Runs every playbook execution
```

Handler:

```text
Runs only when triggered
```

---

# Architecture

```text
Task
 │
 │ changed?
 ▼
YES
 │
 ▼
notify
 │
 ▼
Handler
 │
 ▼
Execute Once
```

---

# First Handler Example

```yaml
---
- name: Configure Nginx
  hosts: web
  become: true

  tasks:

    - name: Copy Config
      copy:
        src: nginx.conf
        dest: /etc/nginx/nginx.conf

      notify:
        - Restart Nginx

  handlers:

    - name: Restart Nginx
      service:
        name: nginx
        state: restarted
```

---

# Breakdown

Task:

```yaml
copy:
```

copies configuration.

---

If file changes:

```text
changed = true
```

---

Ansible sends notification:

```yaml
notify:
  - Restart Nginx
```

---

Handler:

```yaml
handlers:
```

receives notification.

---

Then:

```yaml
service:
  name: nginx
  state: restarted
```

executes.

---

# Execution Flow – First Run

File does not exist yet.

```text
Copy Config
     ↓
changed
     ↓
notify
     ↓
Restart Nginx
```

Output:

```text
TASK [Copy Config]
changed

RUNNING HANDLER [Restart Nginx]
changed
```

---

# Execution Flow – Second Run

File already exists.

```text
Copy Config
     ↓
ok
     ↓
No notify
     ↓
No handler
```

Output:

```text
TASK [Copy Config]
ok
```

Handler does not run.

---

# Major Benefit

```text
Restart only when required
```

This is one of the biggest reasons handlers exist.

---

# notify Keyword

The keyword connecting tasks and handlers is:

```yaml
notify:
```

Example:

```yaml
notify:
  - Restart Nginx
```

---

# Important Rule

The handler name must match exactly.

---

Task:

```yaml
notify:
  - Restart Nginx
```

---

Handler:

```yaml
- name: Restart Nginx
```

---

Mismatch:

```yaml
notify:
  - Restart Apache
```

Handler:

```yaml
- name: Restart Nginx
```

---

Result:

```text
Handler not found
```

---

# Handler Section

Handlers are defined separately.

Structure:

```yaml
tasks:

  ...

handlers:

  ...
```

---

Example

```yaml
tasks:

  - name: Copy Config
    copy:
      src: nginx.conf
      dest: /etc/nginx/nginx.conf

    notify:
      - Restart Nginx

handlers:

  - name: Restart Nginx
    service:
      name: nginx
      state: restarted
```

---

# Multiple Tasks Can Notify One Handler

Example:

```yaml
tasks:

  - name: Copy Main Config
    copy:
      src: nginx.conf
      dest: /etc/nginx/nginx.conf
    notify:
      - Restart Nginx

  - name: Copy Virtual Host
    copy:
      src: app.conf
      dest: /etc/nginx/conf.d/app.conf
    notify:
      - Restart Nginx
```

---

Both tasks notify:

```text
Restart Nginx
```

---

# Question

How many restarts happen?

---

Answer

```text
One Restart
```

---

# Why?

Ansible automatically deduplicates handler execution.

---

Execution

```text
Task 1 → changed
Task 2 → changed

Notify Restart Nginx
Notify Restart Nginx

Handler Queue
      ↓
Restart Nginx

Executed Once
```

---

# This Is Extremely Important

Without handlers:

```text
Restart #1
Restart #2
```

---

With handlers:

```text
Single Restart
```

---

# Real DevOps Example

Deploying an application.

Tasks:

```text
Copy application config
Copy environment file
Copy nginx config
```

---

Each task may notify:

```text
Restart Application
```

---

Result:

```text
One restart
```

instead of:

```text
Three restarts
```

---

# Multiple Handlers

A task can notify multiple handlers.

Example:

```yaml
notify:
  - Restart Nginx
  - Restart Application
```

---

Handlers:

```yaml
handlers:

  - name: Restart Nginx
    service:
      name: nginx
      state: restarted

  - name: Restart Application
    service:
      name: myapp
      state: restarted
```

---

Execution:

```text
Copy Config
     ↓
changed
     ↓
Notify
     ↓
Restart Nginx
Restart Application
```

---

# Handler Execution Timing

One of the most commonly asked interview questions.

---

Question

When do handlers execute?

Immediately?

```text
No
```

---

Answer

```text
Handlers run at the end of the play.
```

---

Example

```yaml
tasks:

  - Task A
  - Task B
  - Task C

handlers:

  - Restart Nginx
```

---

Execution Order

```text
Task A
Task B
Task C

END OF PLAY

Restart Nginx
```

---

# Why End of Play?

Efficiency.

Suppose:

```text
Task 1 changes config
Task 2 changes config
Task 3 changes config
```

---

Without delay:

```text
Restart
Restart
Restart
```

---

With handlers:

```text
Task 1
Task 2
Task 3

Restart Once
```

---

# force_handlers

Normally:

```text
Task Failure
```

may prevent handlers from running.

---

Example

```text
Task 1 → changed
Task 2 → failed
```

Handler may not execute.

---

To force execution:

```yaml
force_handlers: true
```

---

Example

```yaml
- hosts: web
  force_handlers: true
```

---

This ensures notified handlers still run.

---

# Common Services Managed by Handlers

```text
Nginx
Apache
Docker
Jenkins
MySQL
PostgreSQL
Redis
Tomcat
Application Services
```

---

# Day 69 Nginx Example

Imagine updating:

```text
index.html
```

---

Task:

```yaml
- name: Update Website
  copy:
    src: index.html
    dest: /usr/share/nginx/html/index.html

  notify:
    - Restart Nginx
```

---

Handler:

```yaml
handlers:

  - name: Restart Nginx
    service:
      name: nginx
      state: restarted
```

---

Behavior

### First Run

```text
File changed
Restart Nginx
```

---

### Second Run

```text
No changes
No restart
```

---

Exactly what we want.

---

# Handler Lifecycle Diagram

```text
Task Executes
      │
      ▼
Did Change Occur?
      │
 ┌────┴────┐
 │         │
NO        YES
 │         │
 ▼         ▼
End      Notify
          │
          ▼
     Handler Queue
          │
          ▼
     End of Play
          │
          ▼
     Execute Handler
```

---

# Normal Task vs Handler

| Normal Task            | Handler                               |
| ---------------------- | ------------------------------------- |
| Runs every time        | Runs only when notified               |
| Executes immediately   | Executes at end of play               |
| No notification needed | Requires notify                       |
| May run repeatedly     | Runs once even if notified many times |
| Used for work          | Used for reactions                    |

---

# Real Interview Questions

### What is a Handler?

```text
A special task that executes only when notified by another task.
```

---

### Why Use Handlers?

```text
To avoid unnecessary service restarts and improve efficiency.
```

---

### Which Keyword Triggers a Handler?

```yaml
notify:
```

---

### When Are Handlers Executed?

```text
At the end of the play.
```

---

### Can Multiple Tasks Notify the Same Handler?

```text
Yes.
```

---

### If Five Tasks Notify One Handler?

```text
Handler runs once.
```

---

### Can One Task Notify Multiple Handlers?

```text
Yes.
```

---

### What Happens If No Change Occurs?

```text
No notification.
No handler execution.
```

---

# Key Takeaways

### Handler

```text
Special task triggered by notifications
```

---

### notify

```text
Connects task to handler
```

---

### Runs Only On Change

```text
Improves efficiency
```

---

### Executes At End Of Play

```text
Prevents repeated restarts
```

---

### Multiple Notifications

```text
Handler still executes once
```

---

### Common Use

```text
Restart services after configuration changes
```

---

# Master in 60 Seconds

```text
Handler = Special task

notify = Trigger handler

Handler runs only when change occurs

No change = No handler

Handlers execute at end of play

Multiple notifications = One execution

Common use:
Restart Nginx
Restart Jenkins
Restart Docker
Restart MySQL

Purpose:
Avoid unnecessary service restarts
```

***

# Day 69 – Ansible Playbooks

# Part E – Task 5: Check Mode, Diff Mode, and Verbosity

---

# Objective

The goal of this task is to understand:

* What Check Mode is
* What Diff Mode is
* What Verbosity is
* How to safely test playbooks
* How to preview changes before execution
* How to troubleshoot playbook execution
* Real-world DevOps usage

These features are heavily used in production environments because they reduce risk before making infrastructure changes.

---

# Why Do We Need These Features?

Imagine a playbook that:

```text
Installs packages
Updates configuration files
Restarts services
Creates users
Modifies permissions
```

Running it directly on production can be risky.

Questions we may have:

```text
What will change?
Will any files be modified?
Will any service restart?
What commands are executing?
```

Ansible provides special execution modes to answer these questions safely.

---

# Feature 1 – Check Mode

---

# What Is Check Mode?

Check Mode is also known as:

```text
Dry Run
Simulation Mode
```

It shows:

```text
What Ansible WOULD do
```

without actually making changes.

---

# Real-Life Analogy

Before applying Terraform:

```bash
terraform plan
```

You preview changes.

Then:

```bash
terraform apply
```

You execute them.

---

Similarly:

```bash
ansible-playbook --check
```

previews changes.

---

# Example Playbook

```yaml
---
- name: Install Nginx
  hosts: web
  become: true

  tasks:

    - name: Install Nginx
      yum:
        name: nginx
        state: present
```

---

# Normal Execution

```bash
ansible-playbook install-nginx.yml
```

Result:

```text
Package installed
```

---

# Check Mode

```bash
ansible-playbook install-nginx.yml --check
```

Result:

```text
Would install package
No actual change
```

---

# Important

Check Mode:

```text
Shows intended actions
Makes no modifications
```

---

# Typical Output

```text
TASK [Install Nginx]
changed
```

But package remains untouched.

---

# Verification

Before:

```bash
rpm -qa | grep nginx
```

Output:

```text
No package found
```

---

Run:

```bash
ansible-playbook install-nginx.yml --check
```

---

Check again:

```bash
rpm -qa | grep nginx
```

Still:

```text
No package found
```

---

# Why Use Check Mode?

Before production deployment:

```text
Validate playbook behavior
Review expected changes
Avoid accidental modifications
```

---

# Common Use Cases

```text
Production servers
Large environments
Compliance reviews
Change approvals
Infrastructure audits
```

---

# Check Mode Flow

```text
Playbook
    │
    ▼
Check Mode
    │
    ▼
Evaluate Tasks
    │
    ▼
Show Changes
    │
    ▼
Do Not Execute
```

---

# Feature 2 – Diff Mode

---

# What Is Diff Mode?

Diff Mode shows:

```text
Exactly what changed
```

inside files.

---

# Example

Current file:

```html
<h1>Hello World</h1>
```

---

Playbook updates file:

```html
<h1>Deployed by Ansible</h1>
```

---

Without Diff Mode

Output:

```text
TASK [Copy File]
changed
```

---

Question:

```text
What changed?
```

Unknown.

---

# With Diff Mode

Run:

```bash
ansible-playbook site.yml --diff
```

---

Output:

```diff
--- before
+++ after

- <h1>Hello World</h1>
+ <h1>Deployed by Ansible</h1>
```

---

Now the exact modification is visible.

---

# Example Using copy Module

```yaml
- name: Deploy Page
  copy:
    content: "<h1>Deployed by Ansible</h1>"
    dest: /usr/share/nginx/html/index.html
```

---

Run:

```bash
ansible-playbook install-nginx.yml --diff
```

---

Possible Output

```diff
--- before
+++ after

- Old Website
+ Deployed by Ansible
```

---

# Why Diff Mode Matters

Without Diff:

```text
File changed
```

---

With Diff:

```text
Which line changed?
What was removed?
What was added?
```

---

# Common Uses

```text
Configuration management
Security reviews
Compliance audits
Application deployments
```

---

# Check + Diff Together

Very common command:

```bash
ansible-playbook site.yml --check --diff
```

---

Meaning:

```text
Preview changes
Show exact file differences
Do not modify anything
```

---

# Production Best Practice

Always run:

```bash
ansible-playbook site.yml --check --diff
```

before:

```bash
ansible-playbook site.yml
```

---

# Feature 3 – Verbosity

---

# What Is Verbosity?

Verbosity provides:

```text
More detailed execution logs
```

---

Normal Run

```bash
ansible-playbook site.yml
```

Output:

```text
TASK [Install Nginx]
ok

TASK [Start Service]
ok
```

---

Sometimes this information is insufficient.

Need:

```text
SSH details
Module execution details
Variables
Connection information
Debug information
```

---

# Verbosity Levels

---

# Level 1

```bash
ansible-playbook site.yml -v
```

Provides:

```text
Additional execution details
```

---

# Level 2

```bash
ansible-playbook site.yml -vv
```

Provides:

```text
Connection information
```

---

# Level 3

```bash
ansible-playbook site.yml -vvv
```

Provides:

```text
SSH debugging
```

---

# Level 4

```bash
ansible-playbook site.yml -vvvv
```

Provides:

```text
Maximum troubleshooting output
```

---

# Quick Reference

| Command | Purpose                 |
| ------- | ----------------------- |
| -v      | Basic details           |
| -vv     | More connection details |
| -vvv    | SSH troubleshooting     |
| -vvvv   | Full debugging          |

---

# Real Example

Suppose:

```text
SSH connection failing
```

---

Normal Output

```text
UNREACHABLE
```

Not very helpful.

---

Run:

```bash
ansible-playbook site.yml -vvvv
```

---

Now you can see:

```text
SSH command executed
SSH key used
Authentication attempt
Connection failure reason
```

---

# Day 69 Troubleshooting Example

Earlier issue:

```text
Could not resolve hostname your-server-ip
```

---

Using:

```bash
ansible-playbook install-nginx.yml -vvvv
```

would reveal:

```text
SSH connection details
Inventory resolution
Host lookup failure
```

making troubleshooting easier.

---

# Combining Features

---

# Preview + Diff

```bash
ansible-playbook site.yml --check --diff
```

---

# Debug Execution

```bash
ansible-playbook site.yml -vvv
```

---

# Full Investigation

```bash
ansible-playbook site.yml --check --diff -vvvv
```

---

This command provides:

```text
Preview changes
Show file differences
Display maximum debug information
```

---

# Real DevOps Deployment Workflow

Before deployment:

```bash
ansible-playbook deploy.yml --check --diff
```

Review:

```text
Packages
Files
Configurations
Service changes
```

---

If acceptable:

```bash
ansible-playbook deploy.yml
```

---

If issues occur:

```bash
ansible-playbook deploy.yml -vvvv
```

Troubleshoot.

---

# Execution Comparison

---

# Normal Run

```text
Execute Changes
```

Command:

```bash
ansible-playbook site.yml
```

---

# Check Mode

```text
Preview Changes
```

Command:

```bash
ansible-playbook site.yml --check
```

---

# Diff Mode

```text
Show File Differences
```

Command:

```bash
ansible-playbook site.yml --diff
```

---

# Verbosity

```text
Show Detailed Logs
```

Command:

```bash
ansible-playbook site.yml -vvvv
```

---

# Architecture Diagram

```text
Playbook
    │
    ├── Normal Run
    │      │
    │      └── Execute
    │
    ├── Check Mode
    │      │
    │      └── Preview
    │
    ├── Diff Mode
    │      │
    │      └── Show Differences
    │
    └── Verbosity
           │
           └── Debug Details
```

---

# Interview Questions

---

## What Is Check Mode?

```text
A dry-run mode that shows what changes Ansible would make without actually modifying the target system.
```

---

## What Is Diff Mode?

```text
A mode that displays the exact file-level differences before and after changes.
```

---

## Which Option Enables Check Mode?

```bash
--check
```

---

## Which Option Enables Diff Mode?

```bash
--diff
```

---

## Which Option Enables Maximum Verbosity?

```bash
-vvvv
```

---

## Can Check Mode and Diff Mode Be Used Together?

```text
Yes
```

Example:

```bash
ansible-playbook site.yml --check --diff
```

---

## Why Use Verbosity?

```text
To troubleshoot execution, connection issues, SSH failures, module behavior, and debugging problems.
```

---

# Key Takeaways

### Check Mode

```text
Preview changes
No execution
```

---

### Diff Mode

```text
Show file modifications
```

---

### Verbosity

```text
Display detailed logs
```

---

### Production Practice

```bash
ansible-playbook site.yml --check --diff
```

before deployment.

---

### Troubleshooting Practice

```bash
ansible-playbook site.yml -vvvv
```

for maximum diagnostics.

---

# Master in 60 Seconds

```text
--check = Dry run

--diff = Show file differences

-v = More logs

-vv = More connection info

-vvv = SSH debugging

-vvvv = Maximum debugging

Best Practice:

ansible-playbook site.yml --check --diff

Use before production deployment

Use -vvvv for troubleshooting
```

***


# Day 69 – Ansible Playbooks

# Part F – Documentation, Conclusions, Revision Notes & Interview Preparation

---

# Task 6 – Documentation and Observations

---

# Why Documentation Matters

Many engineers focus only on:

```text
Write Playbook
Run Playbook
Complete Task
```

But in real DevOps environments documentation is equally important.

A deployment is not complete until it can be:

```text
Understood
Repeated
Troubleshot
Maintained
Audited
```

---

# Typical Documentation Includes

```text
Objective
Architecture
Prerequisites
Inventory Details
Playbooks Used
Commands Executed
Problems Encountered
Solutions Applied
Validation Steps
Lessons Learned
```

---

# Day 69 Documentation Example

---

## Objective

```text
Create and execute Ansible Playbooks.

Learn:
Inventory
Tasks
Modules
Handlers
Check Mode
Diff Mode
Verbosity
```

---

## Environment

```text
Control Node:
Ansible Server

Managed Node:
Amazon Linux EC2
```

---

## Inventory Used

```ini
[web]
web-server ansible_host=<IP>

[all:vars]
ansible_user=ec2-user
ansible_ssh_private_key_file=~/.ssh/ansible-lab.pem
```

---

## Playbook Used

```yaml
install-nginx.yml
```

Purpose:

```text
Install Nginx
Start Nginx
Enable Nginx
Deploy HTML Page
```

---

## Validation Commands

Connectivity:

```bash
ansible web -m ping
```

---

Inventory:

```bash
ansible-inventory --list
```

---

Playbook Syntax:

```bash
ansible-playbook --syntax-check install-nginx.yml
```

---

Execution:

```bash
ansible-playbook install-nginx.yml
```

---

Check Mode:

```bash
ansible-playbook install-nginx.yml --check
```

---

Diff Mode:

```bash
ansible-playbook install-nginx.yml --diff
```

---

Verbosity:

```bash
ansible-playbook install-nginx.yml -vvvv
```

---

Web Verification:

```bash
curl http://PUBLIC_IP
```

---

# Problems Encountered During Lab

---

# Problem 1

Inventory Not Found

Error:

```text
Could not match supplied host pattern
```

---

Cause:

```text
Inventory file missing
```

---

Solution:

```text
Create inventory.ini
Configure ansible.cfg
```

---

# Problem 2

Placeholder Hostname

Error:

```text
Could not resolve hostname your-server-ip
```

---

Cause:

```text
Example value not replaced
```

---

Solution:

```text
Use actual EC2 public IP
```

---

# Problem 3

SSH Failure

Error:

```text
Connection timed out
```

---

Cause:

```text
Stopped instance
Deleted instance
Wrong IP
Security group issue
```

---

Solution:

```text
Validate infrastructure
Validate SSH
Update inventory
```

---

# Problem 4

Package Manager Error

Error:

```text
No such file or directory: apt-get
```

---

Cause:

```text
Amazon Linux
```

instead of:

```text
Ubuntu
```

---

Solution:

```yaml
yum:
  name: nginx
  state: present
```

---

# Problem 5

HTTP Access Failure

Error:

```text
curl http://PUBLIC_IP
```

hung indefinitely.

---

Cause:

```text
Port 80 blocked
```

---

Solution:

```text
Allow HTTP in Security Group
```

---

# Most Important Lessons Learned

---

## Lesson 1

Infrastructure First

Before Ansible:

```text
Instance must exist
SSH must work
```

---

## Lesson 2

Inventory Is Critical

Without inventory:

```text
No hosts
No automation
```

---

## Lesson 3

Ansible Uses SSH

If:

```bash
ssh user@server
```

fails

then:

```text
Ansible fails too
```

---

## Lesson 4

Correct Module Matters

```text
Ubuntu → apt

Amazon Linux → yum/dnf
```

---

## Lesson 5

Security Groups Matter

Application deployment is incomplete unless network access is configured.

---

## Lesson 6

Idempotency Is Fundamental

Running the same playbook multiple times:

```text
Same Result
No Unnecessary Changes
```

---

## Lesson 7

Handlers Improve Efficiency

Restart services only when configuration changes occur.

---

## Lesson 8

Check Mode Prevents Surprises

Always preview changes before production deployment.

---

# End-to-End Deployment Flow

```text
Create Infrastructure
         │
         ▼
Create Inventory
         │
         ▼
Test SSH Connectivity
         │
         ▼
Write Playbook
         │
         ▼
Syntax Check
         │
         ▼
Run Playbook
         │
         ▼
Verify Service
         │
         ▼
Verify Application
         │
         ▼
Open Security Group Ports
         │
         ▼
Access Application
```

---

# High-Level Ansible Architecture

```text
                    CONTROL NODE
              (Ansible Installed Here)

                         │
                         │ SSH
                         │
         ┌───────────────┼───────────────┐
         │               │               │
         ▼               ▼               ▼

     WEB SERVER      APP SERVER      DB SERVER
     Managed Host    Managed Host    Managed Host

         │               │               │
         └───────────────┼───────────────┘

              Tasks Executed

      yum
      service
      copy
      file
      command
      shell
      lineinfile
```

---

# Low-Level Playbook Execution Flow

```text
Playbook Starts
        │
        ▼

Read Inventory
        │
        ▼

Resolve Hosts
        │
        ▼

Establish SSH Connection
        │
        ▼

Gather Facts
        │
        ▼

Execute Task 1
        │
        ▼

Execute Task 2
        │
        ▼

Execute Task 3
        │
        ▼

Any Change?
        │
   ┌────┴────┐
   │         │
  NO        YES
   │         │
   ▼         ▼
 End      Notify Handler
              │
              ▼

       End of Play
              │
              ▼

      Execute Handlers
              │
              ▼

          Recap
```

---

# Day 69 Interview Questions

---

## What Is a Playbook?

```text
A YAML file that defines automation tasks to be executed on managed hosts.
```

---

## What Is a Play?

```text
A collection of tasks executed on a specified group of hosts.
```

---

## What Is a Task?

```text
A single unit of work performed by a module.
```

---

## What Is a Module?

```text
A reusable Ansible component that performs a specific operation.
```

Examples:

```text
yum
apt
service
copy
file
```

---

## What Is Inventory?

```text
A file containing managed hosts and groups.
```

---

## What Is Idempotency?

```text
Running the same playbook repeatedly results in the same desired state without unnecessary changes.
```

---

## What Does become: true Mean?

```text
Execute tasks with elevated privileges (sudo).
```

---

## What Is a Handler?

```text
A special task that runs only when notified by another task.
```

---

## What Is notify?

```text
Keyword used to trigger handlers.
```

---

## What Is Check Mode?

```text
Dry-run mode that previews changes without executing them.
```

---

## What Is Diff Mode?

```text
Shows exact file-level changes.
```

---

## What Is Verbosity?

```text
Provides detailed execution and troubleshooting logs.
```

---

## Difference Between command and shell?

```text
command:
No shell processing
No pipes
Safer

shell:
Supports pipes
Supports redirects
More flexible
```

---

# Complete Day 69 Summary

Day 69 introduced the foundation of Ansible automation.

Topics covered:

```text
Playbooks
Inventory
Plays
Tasks
Modules
Become
Idempotency
Handlers
Notify
Check Mode
Diff Mode
Verbosity
Troubleshooting
Nginx Deployment
AWS Security Groups
Infrastructure Validation
```

The day demonstrated a complete automation lifecycle:

```text
Infrastructure
      ↓
Connectivity
      ↓
Automation
      ↓
Validation
      ↓
Troubleshooting
      ↓
Production Readiness
```

---

# Master in 60 Seconds (Day 69 Final Revision)

```text
Playbook = Automation File

Inventory = List of Managed Hosts

Play = Which Servers?

Task = What Action?

Module = How Action Is Performed?

become: true = sudo

yum = Amazon Linux Packages

apt = Ubuntu Packages

service = Manage Services

copy = Deploy Files

file = Manage Files/Directories

command = Simple Commands

shell = Commands with Pipes

lineinfile = Modify Specific Lines

Idempotency = Same Result Every Run

Handler = Special Task

notify = Trigger Handler

Handler Runs Only On Change

--check = Dry Run

--diff = Show Differences

-vvvv = Maximum Debugging

Ansible Uses SSH

No SSH = No Automation

Deployment Success Requires:
Infrastructure +
Configuration +
Network Access
```


***

# DAY 69 MASTER NOTES (PART 6 - GOLD EDITION)

# Architecture + Agentless + Ad-Hoc Commands + Ping + Facts + Idempotency Deep Dive

### The Missing 10% That Separates Beginners From Real DevOps Engineers

---

# Chapter 38: Control Node vs Managed Node

Before learning playbooks, understand:

```text
Who Gives Instructions?

Who Receives Instructions?
```

---

## Real Life Analogy

Imagine:

```text
School Principal
```

and

```text
100 Students
```

---

Principal:

```text
Creates Rules

Creates Homework

Checks Results
```

---

Students:

```text
Receive Instructions

Perform Work
```

---

Ansible Works The Same.

---

ASCII

```text
           Control Node

        (Principal)

               │

               ▼

       SSH Instructions

               │

 ┌─────────────┼─────────────┐

 ▼             ▼             ▼

Server1      Server2      Server3

(Student)    (Student)    (Student)
```

---

# Control Node

Definition:

```text
Machine Where Ansible Is Installed
```

---

Contains:

```text
Inventory

Playbooks

Modules

Configuration Files
```

---

Example:

```text
Ubuntu EC2

Laptop

Jenkins Server
```

can be Control Node.

---

ASCII

```text
Control Node

│

├── Ansible Installed

├── Inventory

├── Playbooks

└── Modules
```

---

# Managed Node

Definition:

```text
Servers Controlled By Ansible
```

---

Examples:

```text
Web Server

Application Server

Database Server
```

---

Managed Nodes:

```text
Do NOT Need Ansible Installed
```

Huge point.

---

Memory Trick

```text
Control Node
=
Brain

Managed Node
=
Body
```

---

# Chapter 39: Why Ansible Is Agentless

One of Ansible's biggest advantages.

---

## What Is An Agent?

Think:

```text
Spy
```

living inside every server.

---

Many automation tools install:

```text
Agent Software
```

on every server.

---

ASCII

```text
Tool

 │

 ▼

Agent Installed

 │

 ▼

Server
```

---

Problems:

```text
Extra Software

Extra Maintenance

Version Mismatches

Extra Resources
```

---

# Ansible Approach

No Agent.

---

Only needs:

```text
SSH

Python
```

on Linux.

---

ASCII

```text
Control Node

      │

      ▼

SSH

      │

      ▼

Managed Node
```

---

Nothing else.

---

Benefits

```text
Easy Setup

Less Maintenance

Less Resource Usage

Faster Adoption
```

---

Interview Answer

```text
Why Is Ansible Popular?

Because It Uses Agentless Architecture.
```

---

# Chapter 40: Ansible Architecture Deep Dive

This is the complete picture.

---

ASCII

```text
                 Playbook

                     │

                     ▼

               Ansible Engine

              (Control Node)

                     │

                     ▼

                Inventory

                     │

                     ▼

        ┌────────────┼────────────┐

        ▼            ▼            ▼

     Server1      Server2      Server3

        ▼            ▼            ▼

    Execute      Execute      Execute

      Tasks        Tasks        Tasks
```

---

Actual Flow

```text
Inventory

↓

Playbook

↓

SSH Connection

↓

Module Execution

↓

Results Returned

↓

Report Generated
```

---

# Chapter 41: Ad-Hoc Commands

Most overlooked topic.

---

Think:

```text
Quick One-Time Command
```

---

Example:

Teacher says:

```text
Everyone Stand Up
```

Done.

No notebook.

No lesson plan.

---

That is:

```text
Ad-Hoc Command
```

---

Example

```bash
ansible web -m ping
```

---

Meaning:

```text
Run ping module

Against web group
```

---

ASCII

```text
Ad-Hoc

     │

     ▼

One Command

     ▼

One Action
```

---

# Playbook vs Ad-Hoc

---

Ad-Hoc

```text
One-Time Work
```

Example:

```bash
ansible web -m ping
```

---

Playbook

```text
Reusable Automation
```

Example:

```yaml
Install Nginx

Start Nginx

Create Website
```

---

ASCII

```text
Ad-Hoc

   One Bullet

----------------

Playbook

   Full Movie
```

---

Interview Answer

```text
Ad-Hoc
=
One-Time Task

Playbook
=
Reusable Automation Workflow
```

---

# Chapter 42: Ansible Ping Module

Very Common Interview Trap.

---

Question:

```text
What Does Ansible Ping Do?
```

Most beginners answer:

```text
ICMP Ping
```

Wrong.

---

It does NOT run:

```bash
ping google.com
```

---

Instead it checks:

```text
SSH Connectivity

Python Availability

Module Execution
```

---

Command

```bash
ansible web -m ping
```

---

Success

```json
{
  "ping": "pong"
}
```

---

ASCII

```text
Control Node

      │

      ▼

SSH Connect?

      │

      ▼

Python Available?

      │

      ▼

Return

pong
```

---

Memory Trick

```text
Network Ping

≠

Ansible Ping
```

---

# Chapter 43: Gather Facts

One of the most important Ansible features.

---

Imagine:

Before assigning homework,

Teacher checks:

```text
Student Name

Class

Age

Roll Number
```

---

Ansible does something similar.

---

Before running tasks:

```text
Collect Server Information
```

---

Called:

```text
Facts
```

---

# Default Behavior

```yaml
gather_facts: true
```

(default)

---

Collected Information

```text
Hostname

OS

CPU

RAM

IP Address

Architecture
```

---

ASCII

```text
Server

   │

   ▼

Gather Facts

   │

   ├── Hostname

   ├── OS

   ├── CPU

   ├── RAM

   └── IP
```

---

# Example Facts

Hostname

```yaml
{{ ansible_hostname }}
```

---

OS

```yaml
{{ ansible_os_family }}
```

---

IP

```yaml
{{ ansible_default_ipv4.address }}
```

---

# Why Facts Matter

Suppose:

```text
Ubuntu Server
```

Needs:

```bash
apt
```

---

RHEL Server

Needs:

```bash
yum
```

---

Ansible learns OS through facts.

---

ASCII

```text
Gather Facts

      │

      ▼

Ubuntu?

      │

      ▼

Use apt

----------------

RHEL?

      │

      ▼

Use yum
```

---

# Disable Facts

Sometimes faster.

```yaml
gather_facts: false
```

---

Useful when:

```text
No Fact Variables Needed
```

---

# Chapter 44: Desired State Philosophy

This is Ansible's most important mindset.

---

Most Humans Think:

```text
Run Commands
```

---

Ansible Thinks:

```text
Achieve Desired State
```

---

Human:

```bash
start nginx
```

---

Ansible:

```yaml
state: started
```

---

Human:

```bash
install nginx
```

---

Ansible:

```yaml
state: present
```

---

ASCII

```text
Traditional Admin

      │

      ▼

Run Command

----------------

Ansible

      │

      ▼

Describe End State
```

---

Memory Trick

```text
Humans Think:

HOW

Ansible Thinks:

WHAT
```

---

# Chapter 45: Idempotency Deep Dive

One of the most asked interview topics.

---

Definition:

```text
Running Multiple Times

Produces Same Result
```

---

Example

Playbook:

```yaml
yum:
  name: nginx
  state: present
```

---

Run #1

```text
Nginx Missing
```

Result:

```text
changed
```

---

Run #2

```text
Nginx Exists
```

Result:

```text
ok
```

---

Run #3

```text
Nginx Exists
```

Result:

```text
ok
```

---

ASCII

```text
Run 1

Install

changed

────────────

Run 2

Already Exists

ok

────────────

Run 3

Already Exists

ok
```

---

# Why Idempotency Matters

Without it:

```text
Repeated Runs

Repeated Changes

Repeated Problems
```

---

With it:

```text
Safe Re-Runs

Predictable Results

Consistent Infrastructure
```

---

Interview Answer

```text
Idempotency Means

Repeated Executions Produce
The Same Desired State
Without Unnecessary Changes.
```

---

# Chapter 46: Full Playbook Execution Order

Extremely Important.

---

What happens when you run:

```bash
ansible-playbook site.yml
```

---

Actual Sequence

```text
1. Read Inventory

2. Read Playbook

3. Connect Through SSH

4. Gather Facts

5. Execute Task 1

6. Execute Task 2

7. Execute Task 3

8. Detect Changes

9. Queue Notifications

10. Execute Handlers

11. Generate Summary
```

---

ASCII

```text
Inventory

    │

    ▼

Playbook

    │

    ▼

SSH

    │

    ▼

Facts

    │

    ▼

Tasks

    │

    ▼

Changes?

    │

    ▼

Notify

    │

    ▼

Handlers

    │

    ▼

Summary
```

---

# GOLDEN INTERVIEW QUESTIONS

### What Is A Control Node?

```text
Machine Where Ansible Is Installed.
```

---

### What Is A Managed Node?

```text
Machine Managed By Ansible.
```

---

### Why Is Ansible Called Agentless?

```text
Because It Does Not Require Agents
On Managed Nodes.
```

---

### Difference Between Ad-Hoc And Playbook?

```text
Ad-Hoc
=
One-Time Command

Playbook
=
Reusable Automation
```

---

### What Does Ansible Ping Check?

```text
SSH Connectivity

Python Availability

Module Execution
```

---

### What Are Facts?

```text
Information Collected About Servers.
```

---

### Why Gather Facts?

```text
To Make Decisions Based On
OS, IP, CPU, RAM, Hostname.
```

---

### What Is Desired State?

```text
The Final State Infrastructure
Should Reach.
```

---

### What Is Idempotency?

```text
Repeated Runs Produce
Same End State.
```

---

# DAY 69 FINAL MASTER ARCHITECTURE

```text
                    PLAYBOOK

                        │

                        ▼

                   INVENTORY

                        │

                        ▼

                  CONTROL NODE

                 (Ansible Brain)

                        │

                 SSH (Agentless)

                        │

        ┌───────────────┼───────────────┐

        ▼               ▼               ▼

   Managed-1       Managed-2       Managed-3

        │               │               │

        ▼               ▼               ▼

   Gather Facts    Gather Facts    Gather Facts

        ▼               ▼               ▼

       Tasks          Tasks          Tasks

        ▼               ▼               ▼

      Modules        Modules        Modules

        ▼               ▼               ▼

      Changes        Changes        Changes

              ▼       ▼       ▼

                 Notifications

                        ▼

                    Handlers

                        ▼

                  Final State

                        ▼

                 Idempotent Run

                        ▼

                     SUCCESS
```

***

Yes. Below is the **missing section rewritten in the same style as your Day 69 notes** so you can directly copy-paste after Part F or add as an Appendix.

---

# Day 69 – Appendix

# Additional Important Ansible Concepts

---

# Topic 1 – Gather Facts

---

# What is Gather Facts?

Before executing tasks, Ansible can automatically collect information about managed hosts.

This process is called:

```text
Fact Gathering
```

---

# Default Behavior

```yaml
- hosts: web
  gather_facts: true
```

By default:

```text
gather_facts: true
```

is enabled.

---

# Information Collected

Ansible gathers:

```text
Hostname
IP Address
Operating System
CPU Information
Memory Information
Disk Information
Network Interfaces
Kernel Version
```

---

# Example Variables

Hostname:

```yaml
{{ ansible_hostname }}
```

---

Operating System:

```yaml
{{ ansible_distribution }}
```

---

IP Address:

```yaml
{{ ansible_default_ipv4.address }}
```

---

# Execution Flow

```text
Connect to Host
       ↓
Gather Facts
       ↓
Store Variables
       ↓
Execute Tasks
```

---

# Disable Fact Gathering

```yaml
- hosts: web
  gather_facts: false
```

Useful when:

```text
Facts not required
Faster execution needed
```

---

# Interview Question

What is gather_facts?

```text
Ansible automatically collects system information from managed hosts before executing tasks.
```

---

# Topic 2 – Register Variables

---

# What is Register?

Register stores the output of a task into a variable.

---

# Example

```yaml
- name: Check Uptime
  command: uptime

  register: uptime_output
```

---

Output stored in:

```text
uptime_output
```

---

# Display Output

```yaml
- debug:
    var: uptime_output.stdout
```

---

# Flow

```text
Execute Command
       ↓
Capture Output
       ↓
Store In Variable
       ↓
Reuse Later
```

---

# Real Example

```yaml
- name: Check Disk Usage
  command: df -h

  register: disk_output

- debug:
    var: disk_output.stdout
```

---

# Interview Question

What is register?

```text
Used to store task output into a variable for later use.
```

---

# Topic 3 – Debug Module

---

# Purpose

Display messages and variable values.

---

# Example

```yaml
- debug:
    msg: "Deployment Successful"
```

---

# Display Variable

```yaml
- debug:
    var: ansible_hostname
```

---

# Common Uses

```text
Troubleshooting
Validation
Development
Learning
```

---

# Real Example

```yaml
- debug:
    msg: "Current Host: {{ ansible_hostname }}"
```

---

# Interview Question

Why use debug module?

```text
To display messages and variable values during playbook execution.
```

---

# Topic 4 – Tags

---

# Purpose

Run only selected tasks.

---

# Example

```yaml
- name: Install Nginx

  yum:
    name: nginx
    state: present

  tags:
    - install
```

---

# Run Tagged Tasks

```bash
ansible-playbook site.yml --tags install
```

---

# Skip Tagged Tasks

```bash
ansible-playbook site.yml --skip-tags install
```

---

# Benefits

```text
Faster Execution
Targeted Deployment
Easy Troubleshooting
Selective Operations
```

---

# Execution Flow

```text
Playbook
     ↓
Check Tags
     ↓
Run Matching Tasks
     ↓
Skip Others
```

---

# Interview Question

What are tags?

```text
Tags allow execution of specific tasks without running the entire playbook.
```

---

# Topic 5 – Conditional Execution (when)

---

# Purpose

Execute tasks only when a condition is true.

---

# Example

```yaml
- name: Install Nginx

  yum:
    name: nginx
    state: present

  when: ansible_os_family == "RedHat"
```

---

# Execution Logic

```text
Condition True
       ↓
Execute Task

Condition False
       ↓
Skip Task
```

---

# Another Example

```yaml
- name: Restart Service

  service:
    name: nginx
    state: restarted

  when: nginx_installed == true
```

---

# Benefits

```text
Dynamic Automation
OS-Specific Tasks
Environment-Specific Tasks
Safer Deployments
```

---

# Interview Question

What does when do?

```text
Executes a task only when the specified condition evaluates to true.
```

---

# Topic 6 – Loops

---

# Purpose

Repeat tasks without duplication.

---

# Without Loop

```yaml
- yum:
    name: git

- yum:
    name: wget

- yum:
    name: unzip
```

---

# With Loop

```yaml
- yum:
    name: "{{ item }}"
    state: present

  loop:
    - git
    - wget
    - unzip
```

---

# Execution

```text
git
   ↓
wget
   ↓
unzip
```

using a single task.

---

# Benefits

```text
Less Code
Better Readability
Easy Maintenance
Reusable Logic
```

---

# Interview Question

How do you install multiple packages?

```text
Use loop with the package module.
```

---

# Topic 7 – Play Recap

---

# What is Play Recap?

At the end of every playbook execution Ansible displays a summary.

---

# Example

```text
PLAY RECAP

web-server :

ok=5
changed=2
unreachable=0
failed=0
```

---

# Meaning

### ok

```text
Tasks executed successfully
```

---

### changed

```text
Tasks modified system state
```

---

### unreachable

```text
SSH or connection issue
```

---

### failed

```text
Task execution failure
```

---

# Example Interpretation

```text
ok=5

Five tasks successful
```

---

```text
changed=2

Two tasks made changes
```

---

```text
failed=0

No failures
```

---

# Interview Question

What does changed=0 indicate?

```text
No modifications were required because the desired state already existed.
```

***

# Topic 1 – Facts Gathering

Most playbooks start with:

```yaml
gather_facts: true
```

Example:

```yaml
- hosts: web
  gather_facts: true
```

Ansible automatically collects:

```text
Hostname
OS Version
IP Address
CPU
Memory
Disk Information
Network Interfaces
```

Example variable:

```yaml
{{ ansible_hostname }}
{{ ansible_distribution }}
{{ ansible_default_ipv4.address }}
```

Interview Question:

```text
What is gather_facts?

Ansible automatically collects system information
before executing tasks.
```

---

# Topic 2 – Register Variables

Capturing command output.

Example:

```yaml
- name: Check uptime
  command: uptime
  register: uptime_output
```

Use:

```yaml
- debug:
    var: uptime_output.stdout
```

Flow:

```text
Task
   ↓
Output
   ↓
Register Variable
   ↓
Reuse Later
```

Interview Question:

```text
What is register?

Used to store task output into a variable.
```

---

# Topic 3 – Debug Module

Very common in troubleshooting.

Example:

```yaml
- debug:
    msg: "Deployment Successful"
```

or

```yaml
- debug:
    var: ansible_hostname
```

Useful during:

```text
Troubleshooting
Variable Validation
Development
```

---

# Topic 4 – Tags

Execute only selected tasks.

Example:

```yaml
- name: Install Nginx
  yum:
    name: nginx
    state: present
  tags:
    - install
```

Run:

```bash
ansible-playbook site.yml --tags install
```

Benefits:

```text
Run specific tasks
Faster execution
Easier troubleshooting
```

---

# Topic 5 – Conditional Execution (when)

One of the most asked interview topics.

Example:

```yaml
- name: Install Nginx
  yum:
    name: nginx
    state: present
  when: ansible_os_family == "RedHat"
```

Execution:

```text
Condition True
      ↓
Execute

Condition False
      ↓
Skip
```

---

# Topic 6 – Loops

Instead of repeating tasks.

Without Loop:

```yaml
- yum:
    name: git

- yum:
    name: wget

- yum:
    name: unzip
```

With Loop:

```yaml
- yum:
    name: "{{ item }}"
    state: present
  loop:
    - git
    - wget
    - unzip
```

Interview Question:

```text
How do you install multiple packages?

Using loop.
```

---

# Topic 7 – Ansible Playbook Execution Flow

Useful for interviews.

```text
Inventory
    ↓
Connect via SSH
    ↓
Gather Facts
    ↓
Execute Tasks
    ↓
Detect Changes
    ↓
Notify Handlers
    ↓
Execute Handlers
    ↓
Play Recap
```

---

# Topic 8 – Play Recap

Example:

```text
PLAY RECAP

web-server :

ok=5
changed=2
unreachable=0
failed=0
```

Meaning:

| Field       | Meaning          |
| ----------- | ---------------- |
| ok          | Successful tasks |
| changed     | Modified system  |
| unreachable | SSH issue        |
| failed      | Failed tasks     |

---

# Topic 9 – YAML Best Practices

Useful for real projects.

```text
Use meaningful task names

One task = one purpose

Prefer modules over shell

Use handlers for restarts

Use variables instead of hardcoding

Keep playbooks idempotent
```



---

# Master in 60 Seconds – Missing Concepts

```text
gather_facts = Collect host information

Playbook = YAML Automation File

Inventory = List of Hosts

Play = Which Servers?

Task = What Action?

Module = How Action Happens?

Handler = Runs When Notified

notify = Triggers Handler

become = sudo

register = Store Output

debug = Print Output

when = Conditional Execution

loop = Repeat Tasks

tags = Run Selected Tasks

--check = Dry Run

--diff = Show Changes

-vvvv = Maximum Debugging

Idempotency = Same Result Every Run



PLAY RECAP = Execution summary

ok = Successful

changed = Modified

failed = Error

unreachable = SSH issue - Connection issue

```



