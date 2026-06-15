If your goal is **"I need a DevOps job as soon as possible"**, then we should not spend the next 6 months studying everything.

We should focus on the **20% that gets asked in 80% of DevOps interviews** and make sure you can **do it, explain it, and remember it**.

Looking at your background, I would do this:

```text
Month 1
Linux
Git
Docker

Month 2
Jenkins
AWS
Terraform

Month 3
Kubernetes
CI/CD Project
Interview Preparation
```

Not because Kubernetes is unimportant, but because many junior DevOps interviews start with:

```text
Linux
Git
Docker
Jenkins
AWS
Terraform
```

before they go deep into Kubernetes.

---

# How We Will Learn

For every topic:

### Step 1

I teach concept in simple language.

### Step 2

You do practical exercise.

### Step 3

You intentionally break it.

### Step 4

You explain it back.

### Step 5

I ask interview questions.

This is how knowledge sticks.

---

# Day 1 - Linux (Most Important Foundation)

Imagine a Linux server is a building.

```text
Building
  |
  +-- Rooms (Directories)
  |
  +-- Files
  |
  +-- People (Users)
  |
  +-- Teams (Groups)
```

As a DevOps engineer, your first responsibility is managing:

```text
Users
Groups
Files
Permissions
Processes
Services
Logs
```

---

# Exercise 1 - Explore Linux

Open your Linux VM.

Run:

```bash
pwd
```

Question:

```text
Where am I currently?
```

---

Run:

```bash
ls
```

Question:

```text
What files and folders are here?
```

---

Run:

```bash
ls -la
```

Question:

```text
What extra information do you see?
```

---

# Exercise 2 - Create Directory

Run:

```bash
mkdir devops-practice
```

Verify:

```bash
ls
```

---

Go inside:

```bash
cd devops-practice
```

Verify:

```bash
pwd
```

Question:

```text
What changed after cd?
```

---

# Exercise 3 - Create Files

Run:

```bash
touch file1.txt
touch file2.txt
touch file3.txt
```

Verify:

```bash
ls
```

---

Delete one:

```bash
rm file3.txt
```

Verify:

```bash
ls
```

---

# What You Must Understand

Do not memorize commands.

Understand:

```text
mkdir
=
Make Directory

cd
=
Change Directory

pwd
=
Print Working Directory

touch
=
Create Empty File

rm
=
Remove File

ls
=
List Files
```

---

# Interview Question

If interviewer asks:

### What is pwd?

Bad answer:

```text
It shows path.
```

Good answer:

```text
pwd stands for Print Working Directory.
It displays the absolute path of the current directory where I am working.
I use it to verify my current location in the filesystem.
```

---

# Your First Assignment

Perform these commands:

```bash
pwd

ls -la

mkdir devops-practice

cd devops-practice

touch file1.txt

touch file2.txt

ls -la

rm file2.txt

ls -la
```

Then reply with:

1. The output of `pwd`
2. The output of `ls -la`
3. Your answer to:

   * What does `pwd` do?
   * What does `cd` do?
   * What does `touch` do?
   * What does `rm` do?

<img width="737" height="920" alt="image" src="https://github.com/user-attachments/assets/71316011-984b-44d1-b054-983692a38b45" />

***


Excellent. You actually **performed the commands**, which is much better than just reading about them.

Let's review your answers like an interviewer would.

---

# 1. pwd

Your answer:

```text
current/present working directory where i am working
```

Interview-ready answer:

```text
pwd stands for Print Working Directory.

It displays the absolute path of the current directory where I am currently working in the Linux filesystem.
```

Example:

```bash
pwd
```

Output:

```text
/home/malathi/devops-practice
```

---

# 2. cd

Your answer:

```text
changing the directory where i want my files to be present
```

Almost correct.

Better answer:

```text
cd stands for Change Directory.

It is used to move from one directory to another in the Linux filesystem.
```

Example:

```bash
cd /home/malathi
```

moves you to:

```text
/home/malathi
```

---

# 3. touch

Your answer:

```text
it creates only files
```

Partially correct.

Interview-ready answer:

```text
touch is used to create an empty file.

If the file already exists, it updates the file's timestamp without modifying its contents.
```

Example:

```bash
touch file1.txt
```

creates:

```text
file1.txt
```

---

# 4. rm

Your answer:

```text
it removes the file which is present
```

Correct.

Better answer:

```text
rm stands for Remove.

It is used to delete files and directories from the filesystem.
```

Examples:

```bash
rm file1.txt
```

Delete file.

```bash
rm -r foldername
```

Delete directory recursively.

---

# Very Important Observation

Look at this output:

```text
drwxr-xr-x
```

and

```text
-rw-r--r--
```

Most DevOps interviews ask this.

Let's learn it properly.

---

# Linux Permissions

You saw:

```text
-rw-r--r--
```

Break it:

```text
-
rw-
r--
r--
```

---

First character:

```text
-
```

means:

```text
File
```

If you see:

```text
d
```

means:

```text
Directory
```

Example:

```text
drwxr-xr-x
```

is a directory.

---

Remaining 9 characters:

```text
rwx r-x r-x
```

are divided into 3 groups.

```text
Owner
Group
Others
```

Like:

```text
rwx | r-x | r-x
```

---

# Meaning

```text
r = read
w = write
x = execute
```

---

# Example

```text
-rw-r--r--
```

means:

```text
Owner  = rw-
Group  = r--
Others = r--
```

Owner can:

```text
Read
Write
```

Group can:

```text
Read
```

Others can:

```text
Read
```

---

# Interview Question

Look at:

```text
drwxr-xr-x
```

Question:

```text
Who has write permission?
```

Answer:

```text
Owner only
```

Because:

```text
rwx
```

contains:

```text
w
```

while:

```text
r-x
```

does not.

---

# Practical Exercise 2

Inside `devops-practice` run:

```bash
mkdir testfolder
```

Check permissions:

```bash
ls -ld testfolder
```

Now run:

```bash
chmod 777 testfolder
```

Check again:

```bash
ls -ld testfolder
```

Then run:

```bash
chmod 755 testfolder
```

Check again:

```bash
ls -ld testfolder
```

---

# Your Task

Reply with:

### 1

Output of:

```bash
ls -ld testfolder
```

after:

```bash
chmod 777
```

### 2

Output of:

```bash
ls -ld testfolder
```

after:

```bash
chmod 755
```

### 3

Answer these:

```text
777 means?

755 means?

What is the difference?

Who can write in 755?

Who can write in 777?
```

<img width="606" height="207" alt="image" src="https://github.com/user-attachments/assets/a916a23b-7ceb-4488-b4fe-7fa04a3168fc" />

***


Good. You're understanding the concepts instead of memorizing commands. That's exactly what we want.

Let's correct one small thing.

---

# Permission Numbers

You wrote:

```text
4 = read
2 = write
1 = execute
```

✅ Correct

---

# 777

You said:

```text
owner group others have all permissions
```

✅ Correct

Breakdown:

```text
7 = 4 + 2 + 1

rwx
```

So:

```text
Owner  = rwx
Group  = rwx
Others = rwx
```

---

# 755

You said:

```text
owner has all permissions
group and others don't have write permission
```

✅ Correct

Breakdown:

```text
7 = rwx
5 = r-x
5 = r-x
```

So:

```text
Owner  = rwx
Group  = r-x
Others = r-x
```

---

# Small Correction

You wrote:

```text
4 + 2 + 1 = 777
```

Actually:

```text
4 + 2 + 1 = 7
```

Then:

```text
777

means

7 | 7 | 7

Owner | Group | Others
```

Similarly:

```text
755

means

7 | 5 | 5
```

---

# Interview Question

Suppose:

```text
-rw-r--r--
```

What is the numeric permission?

Answer:

```text
rw- = 6

r-- = 4

r-- = 4

644
```

Because:

```text
r = 4
w = 2
x = 1

rw- = 4 + 2 = 6
r-- = 4
r-- = 4
```

---

# Real DevOps Question

Would you give:

```text
777
```

to production files?

Answer:

```text
No
```

Why?

Because:

```text
Anyone can modify
Anyone can delete
Huge security risk
```

Most production environments avoid 777.

Common permissions:

```text
755  directories
644  files
600  sensitive files
```

---

# Next Topic: Users, Groups & Ownership

This is asked constantly in Linux interviews.

Look at:

```text
drwxr-xr-x 2 malathi malathi 4096 Jun 15 12:15 testfolder
```

Question:

What do these two `malathi` values mean?

```text
drwxr-xr-x 2 malathi malathi 4096 Jun 15 12:15 testfolder
                 ↑       ↑
```

Answer:

```text
1st malathi = Owner/User

2nd malathi = Group
```

Linux permissions are checked in this order:

```text
User
 ↓
Group
 ↓
Others
```

---

# Practical Exercise 3

Run:

```bash
id
```

and

```bash
groups
```

Then run:

```bash
cat /etc/passwd | grep malathi
```

and

```bash
cat /etc/group | grep malathi
```

---

# Before Running

Try to predict.

### Question 1

What do you think `id` will show?

### Question 2

What do you think `groups` will show?

### Question 3

Why do Linux systems have both Users and Groups? Why not just Users?

Think about it first, then run the commands and paste the output.



<img width="1172" height="336" alt="image" src="https://github.com/user-attachments/assets/4ad760c7-e810-4f12-af6f-65452e3de85b" />

***

Excellent. This is the first time we're moving from **commands** to **how Linux actually works internally**.

Your answer to Question 3 is actually quite good.

---

# Review of Your Answers

## What does `id` show?

You said:

```text
id showed all ids
```

Let's make it interview-ready.

```text
id command displays:

UID (User ID)
GID (Primary Group ID)
All groups the user belongs to
```

From your output:

```text
uid=1000(malathi)
gid=1000(malathi)
groups=1000(malathi),4(adm),24(cdrom),...
```

Meaning:

```text
User Name  = malathi
UID        = 1000
Primary Group = malathi
Additional Groups =
adm
cdrom
sudo
dip
plugdev
users
docker
```

---

# What does `groups` show?

Your answer:

```text
all groups are listed
```

✅ Correct.

Interview answer:

```text
groups command shows all groups
the current user belongs to.
```

Your output:

```text
malathi adm cdrom sudo dip plugdev users docker
```

---

# Why Users and Groups?

Your answer:

```text
if there are many users they can be grouped under 1 group
```

✅ Very good.

Let's make it stronger.

Imagine a company.

```text
100 Developers

100 Testers

50 DevOps Engineers
```

Without groups:

```text
Give folder access
to each user individually
```

Nightmare.

Instead:

```text
developers group

testers group

devops group
```

Assign permission once.

Everyone in that group automatically gets access.

This is exactly why groups exist.

---

# Real DevOps Example

Suppose:

```text
/var/www/html
```

should be editable by all developers.

Instead of:

```text
Adding permissions
to 20 developers individually
```

Create:

```text
developers group
```

Add users to group.

Assign folder ownership:

```bash
chgrp developers /var/www/html
```

Done.

---

# Important Concept

Look at:

```text
uid=1000(malathi)
gid=1000(malathi)
```

People often confuse UID and GID.

---

# UID

```text
User ID
```

Unique identity of a user.

For you:

```text
1000
```

Linux internally uses:

```text
1000
```

not:

```text
malathi
```

---

# GID

```text
Group ID
```

Primary group of user.

For you:

```text
1000
```

which corresponds to:

```text
malathi group
```

---

# Very Common Interview Question

Question:

```text
What is the difference between UID and GID?
```

Answer:

```text
UID uniquely identifies a user.

GID identifies a group.

A user can have one primary group
and multiple secondary groups.
```

---

# Next Practical Exercise (Ownership)

Create a file:

```bash
touch project.txt
```

Check ownership:

```bash
ls -l project.txt
```

You should see something similar:

```text
-rw-r--r-- 1 malathi malathi 0 Jun 15 12:30 project.txt
```

Question:

```text
Who owns this file?

Which group owns this file?
```

---

Then run:

```bash
sudo groupadd devops
```

Verify:

```bash
cat /etc/group | grep devops
```

Then run:

```bash
sudo chgrp devops project.txt
```

Check again:

```bash
ls -l project.txt
```

<img width="627" height="342" alt="image" src="https://github.com/user-attachments/assets/bfbe541f-bb01-4e06-922a-355b2d8443b3" />


---

# Your Challenge

After running those commands, answer:

### 1

What did `groupadd devops` do?

### 2

What did `chgrp devops project.txt` do?

### 3

What is the difference between:

```bash
chown
```

and

```bash
chgrp
```

















