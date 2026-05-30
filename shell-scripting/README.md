#  Shell Scripts – Clean Notes (Days 16–21)

This document converts your shell scripting work into clean, readable DevOps notes for revision and interviews.

---

##  Day 16 – Shell Scripting Basics

###  Concepts Covered

- Introduction to Bash/Shell scripting
- Creating and executing `.sh` files
- Variables and user input

###  Key Learnings

- Shebang usage:

```bash
#!/bin/bash
```
- Running scripts:

```bash
bash script.sh
```
- Reading input:

```bash
read name
echo "Hello $name"
```

###  Use Case
Automating simple interactive tasks using shell scripts.

---

##  Day 17 – File Operations

###  Concepts Covered
- File creation, deletion, modification 
- Directory handling 
 
### 🛠 Commands Used 
 
- bash mkdir test 
- bash cd test 
- touch file.txt 
- rmdir file.txt 
- bash cp file1 file2 
- bash mv file1 folder/ 
 
###  Use Case 
Automating file system management tasks.
 
---
 
## Day 18 – User Management Automation 
 
###  Concepts Covered 
 - Adding and removing users 
 - Checking user existence  
day bash sudo useradd username sudo passwd username id username sudo userdel username  3. **💡 Use Case** Automating Linux user provisioning. 

---
 ## Day 19 – System Monitoring Scripts

  ### Concepts Covered 
  * CPU, memory, disk usage monitoring 
  * System health checks 

  ###  Commands Used 
  ```bash 
  top 
  free -m 
  df -h 
  uptime 
  ``` 
  ###  Use Case 
  Basic system health monitoring automation. 

---
 ##  Day 20 – Backup Automation

  ### Concepts Covered 
  * File and directory backup 
  * Compression using tar 

  ###  Commands Used 
  ```bash 
  tar -cvf backup.tar folder/ 
  tar -czvf backup.tar.gz folder/ 
  ``` 

  ###  Use Case 
  Automated backup creation for safety and recovery. 

---

 ## Day 21 – Process Monitoring

  ### Concepts Covered 
  * Running processes 
  * Killing processes 
  * Process status checks 

  ### Commands Used 
  ```bash 
  ps -ef ps aux | grep nginx kill -9 PID 
  ``` 

  ### Use Case 
  Monitoring and controlling system processes. 

  --- 
