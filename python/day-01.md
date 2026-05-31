
# 🐍 PYTHON FOR DEVOPS — DAY 01 MASTER NOTES

## (Beginner → DevOps → Interview Ready)

---

# 🧠 RULE #1

Do NOT memorize code.

Understand the story.

If you understand the story, you can always rewrite the code.

---

# 🎬 PYTHON IS JUST A STORY

Imagine Python is your assistant.

You tell it:

```text
1. Ask user something
2. Store answer
3. Make decision
4. Repeat if needed
5. Do a task
```

That's programming.

---

# 🧠 BIG PICTURE FIRST

Most beginners see:

```text
Variables
Data Types
Input
Type Casting
Conditions
Loops
Functions
psutil
Monitoring
Logging
```

and think:

```text
OMG 😭
```

But actually:

```text
Everything is one story.
```

---

# 🎯 THE ENTIRE DAY-01 STORY

Imagine you are a DevOps Engineer.

Your manager says:

```text
Monitor my server.
```

To do that, you must:

```text
1. Ask threshold
2. Check server
3. Compare values
4. Alert if problem
5. Repeat
6. Save logs
```

Every Python concept naturally appears while solving this problem.

---

# 1️⃣ WHAT IS PYTHON?

## Interview Answer

Python is a high-level programming language used for:

```text
Automation
Scripting
Web Development
Data Analysis
Artificial Intelligence
DevOps
```

---

## Simple Meaning

Think:

```text
You ↔ English ↔ Human

You ↔ Python ↔ Computer
```

Python is a language used to communicate with computers.

---

## DevOps Example

Instead of manually checking:

```text
CPU
Memory
Disk
```

every day,

you write a Python script that does it automatically.

---

## Short Answer

```text
Python is a programming language used for automation and scripting.
```

---

# 2️⃣ WHAT IS SCRIPTING?

## Definition

```text
Scripting = Writing step-by-step instructions for a computer.
```

---

## Example

```python
print("Start")
print("Check system")
print("End")
```

Python executes instructions one by one.

---

## DevOps Use Cases

```text
Server Monitoring
Deployment Automation
Backup Automation
Log Analysis
Cloud Automation
```

---

# 3️⃣ print()

## Code

```python
print("Hello")
```

---

## Meaning

```text
Python,
show "Hello" on screen.
```

---

## Flow

```text
Python
   |
   v

print("Hello")
   |
   v

Screen

Hello
```

---

## Output

```text
Hello
```

---

# 4️⃣ VARIABLES

## Interview Answer

A variable is a named storage location used to hold data.

---

## Simple Meaning

Variable = Box

```text
┌─────────┐
│ Malathi │
└─────────┘
     ↑
   name
```

---

## Example

```python
name = "Malathi"
```

Meaning:

```text
Create a box called name
Store Malathi inside it
```

---

## Syntax

```python
variable_name = value
```

---

## Visual

```text
name
 |
 v

"Malathi"
```

---

## Example

```python
name = "Malathi"

print(name)
```

---

## Flow

```text
name
 |
 v

"Malathi"

print(name)
     |
     v

Malathi
```

---

## Output

```text
Malathi
```

---

## DevOps Examples

```python
cpu_usage = 80

server_name = "prod-server"

cpu_threshold = 80
```

---

## Short Answer

```text
Variable is a container used to store data.
```

---

# 5️⃣ CONSTANTS (BASIC IDEA)

## Definition

```text
Constant = value that should not change.
```

Python does not enforce constants.

Convention:

```python
PI = 3.14

CPU_THRESHOLD = 80
```

Uppercase indicates constant.

---

# 6️⃣ DATA TYPES

## Interview Answer

A data type defines the kind of value stored in a variable.

---

## Why Data Types Exist

Python needs to know:

```text
Is this a number?
Is this text?
Is this True/False?
```

---

## Main Data Types

### Integer (int)

Whole numbers.

```python
age = 25
```

Examples:

```python
10
20
100
-5
```

Visual:

```text
25
↑
int
```

---

### Float

Decimal numbers.

```python
price = 99.5
```

Examples:

```python
1.5
20.7
3.14
```

Visual:

```text
99.5
 ↑
float
```

---

### String (str)

Text data.

```python
name = "Malathi"
```

Visual:

```text
"Malathi"
   ↑
 string
```

---

### Boolean (bool)

Represents:

```text
Yes / No
True / False
```

Example:

```python
is_running = True
```

Visual:

```text
True
False
```

---

## Combined Example

```python
a = 10
b = 2.5
c = True
d = "devops"
```

---

## Short Answer

```text
Data type tells Python what kind of data is stored.
```

---

# 7️⃣ OPERATORS

## Definition

```text
Operators are symbols used to perform operations.
```

---

## Examples

```python
+   Addition
-   Subtraction
*   Multiplication
/   Division
```

---

## Example

```python
a = 10
b = 5

print(a + b)
```

Output:

```text
15
```

---

# 8️⃣ OPERANDS VS OPERATOR

Example:

```python
a + b
```

| Part | Meaning   |
| ---- | --------- |
| a    | Operand   |
| b    | Operand   |
| +    | Operator  |
| a+b  | Operation |

---

# 9️⃣ INPUT FUNCTION

## Interview Answer

Used to take input from the user.

---

## Syntax

```python
input("message")
```

---

## Example

```python
name = input("Enter name: ")
```

---

## What Happens?

Python asks:

```text
Enter name:
```

User types:

```text
Malathi
```

Python stores:

```python
"Malathi"
```

---

## Flow

```text
Keyboard
    |
    v

Malathi
    |
    v

input()
    |
    v

name
```

---

# 🚨 IMPORTANT RULE

```text
input() ALWAYS returns STRING
```

Even if user types:

```text
10
```

Python stores:

```python
"10"
```

NOT

```python
10
```

---

## Proof

```python
value = input()

print(type(value))
```

Output:

```text
<class 'str'>
```

---

## Visual

```text
Keyboard
   |
   v

10

Python stores

"10"
```

---

## Short Answer

```text
input() always returns a string.
```

---



# PART 2 — TYPE CASTING → CONDITIONS → LOOPS

---

# 🔟 TYPE CASTING (VERY IMPORTANT)

## Interview Answer

Type casting is converting one data type into another.

---

## Why Do We Need Type Casting?

Remember:

```text
input() ALWAYS returns STRING
```

User enters:

```text
10
```

Python stores:

```python
"10"
```

not

```python
10
```

---

## Visual

```text
Keyboard
   |
   v

10

Python stores

"10"

String
```

---

## Convert String To Integer

Code:

```python
int("10")
```

Flow:

```text
"10"
  |
  v

int()

  |
  v

10
```

Result:

```python
10
```

Integer.

---

## Convert String To Float

Code:

```python
float("80")
```

Flow:

```text
"80"
 |
 v

float()

 |
 v

80.0
```

---

## Real Example

Code:

```python
a = int(input("Enter num: "))
b = int(input("Enter num: "))

print(a+b)
```

---

User types:

```text
10
20
```

Flow:

```text
"10"
 |
int()
 |
 v
10

"20"
 |
int()
 |
 v
20

10 + 20
```

Output:

```text
30
```

---

# 🚨 IMPORTANT CONFUSION YOU FACED

Correct:

```python
int("10")
```

Incorrect:

```python
int('"10"')
```

Reason:

```text
Extra quotes become part of the string.
```

---

# RULE

```text
Only clean numeric strings can be converted.
```

Examples:

Valid

```python
int("10")
float("10")
```

Invalid

```python
int("abc")
int("q")
```

---

# Short Answer

```text
Type casting converts one data type into another.
```

---

# 1️⃣1️⃣ CONDITIONAL STATEMENTS (if / elif / else)

## Interview Answer

Conditional statements are used for decision making.

---

## Real Life Thinking

Imagine:

```text
CPU = 90

Threshold = 80
```

Question:

```text
90 > 80 ?
```

Answer:

```text
YES
```

Action:

```text
ALERT
```

This is exactly what if does.

---

# IF STATEMENT

Code:

```python
if cpu > 80:
    print("ALERT")
```

---

## Visual

```text
CPU = 90

90 > 80 ?

YES
 |
 v

ALERT
```

---

## Flow

```text
Condition
    |
    v

TRUE ?

YES ------> Execute Code

NO -------> Skip
```

---

# ENVIRONMENT EXAMPLE

Code:

```python
env = "prd"

if env == "prd":
    print("Don't Deploy")
```

---

Flow

```text
env
 |
 v

"prd"

Is env == prd ?

YES
 |
 v

Don't Deploy
```

---

# IF-ELSE

Code:

```python
env = input()

if env == "prd":
    print("Production")
else:
    print("Other")
```

---

Flow

```text
env = prd

Is prd == prd ?

YES
 |
 v

Production
```

OR

```text
env = dev

Is dev == prd ?

NO
 |
 v

Other
```

---

# IF-ELIF-ELSE

Code:

```python
env = "stg"

if env == "prd":
    print("No deployment")

elif env == "stg":
    print("Test first")

else:
    print("Safe")
```

---

## DevOps Example

| Environment | Action           |
| ----------- | ---------------- |
| prd         | Block deployment |
| stg         | Test first       |
| dev         | Deploy           |

---

# Short Answer

```text
if is used for decision making.
```

---

# 1️⃣2️⃣ LOOPS

## Meaning

```text
Repeat
Repeat
Repeat
```

Instead of writing same code again and again.

---

## Why Loops Exist?

Without loop:

```python
print("Checking")

print("Checking")

print("Checking")
```

Repeated code.

---

With loop:

```python
for i in range(3):
    print("Checking")
```

Cleaner.

---

# 1️⃣3️⃣ FOR LOOP

## Definition

Use FOR when you KNOW the number of repetitions.

---

## Real Life Story

Mother says:

```text
Wash 5 plates.
```

You know count.

```text
Plate 1
Plate 2
Plate 3
Plate 4
Plate 5

STOP
```

That's FOR loop.

---

## Code

```python
for i in range(5):
    print(i)
```

---

## What Python Actually Does

```text
i = 0
print(0)

i = 1
print(1)

i = 2
print(2)

i = 3
print(3)

i = 4
print(4)

STOP
```

---

## Output

```text
0
1
2
3
4
```

---

# FOR LOOP FLOW VISUALIZATION

```text
Start
  |
  v

i = 0
 |
print

  |
  v

i = 1
 |
print

  |
  v

i = 2
 |
print

  |
  v

i = 3
 |
print

  |
  v

i = 4
 |
print

  |
  v

STOP
```

---

# FOR LOOP MENTAL MODEL

```text
Known Count
     |
     v

Repeat
Repeat
Repeat
Repeat

STOP
```

---

# DEVOPS EXAMPLE

Check 5 servers.

Code:

```python
for server in range(5):
    print("Checking server")
```

Thinking:

```text
Checking Server 1
Checking Server 2
Checking Server 3
Checking Server 4
Checking Server 5
```

---

# TABLE EXAMPLE (YOUR PRACTICE)

Code:

```python
num = int(input("Enter number: "))

for i in range(1,11):
    print(num*i)
```

---

Meaning:

```text
Print multiplication table.
```

Example:

```text
Input = 5

5
10
15
20
25
30
35
40
45
50
```

---

# 1️⃣4️⃣ WHILE LOOP

## Definition

Use WHILE when you DO NOT KNOW the number of repetitions.

---

## Real Life Story

Manager says:

```text
Keep watching CCTV.
```

Question:

```text
How many times?
```

Answer:

```text
Nobody knows.
Keep watching.
```

That is WHILE.

---

## Code

```python
while True:
    print("Monitoring")
```

---

## Flow

```text
Monitoring
    |
    v

Monitoring
    |
    v

Monitoring
    |
    v

Monitoring
    |
    v

Forever
```

---

# WHILE LOOP MENTAL MODEL

```text
Repeat
Repeat
Repeat
Repeat
Repeat

Until condition becomes False
```

---

# DEVOPS EXAMPLE

Real monitoring systems work like:

```python
while True:
    check_server()
```

Examples:

```text
Prometheus
Nagios
Datadog Agent
CloudWatch Agent
```

Continuously monitoring.

---

# STOPPING A WHILE LOOP

Code:

```python
while True:

    choice = input()

    if choice == "q":
        break
```

---

## Flow

```text
User Input
    |
    v

Is q ?

YES ------> STOP

NO -------> Continue
```

---

# 1️⃣5️⃣ FOR VS WHILE

## FOR LOOP

Question:

```text
Do I know count?
```

Answer:

```text
YES
```

Use:

```python
for
```

---

Example:

```python
for i in range(10):
```

Meaning:

```text
Run exactly 10 times.
```

---

DevOps Example:

```text
Check 10 servers.
```

---

# WHILE LOOP

Question:

```text
Do I know count?
```

Answer:

```text
NO
```

Use:

```python
while
```

---

Example:

```python
while True:
```

Meaning:

```text
Keep running.
```

---

DevOps Example:

```text
Monitor server forever.
```

---

# VISUAL COMPARISON

FOR

```text
Known Count
     |
     v

Repeat 5 Times
     |
     v

STOP
```

---

WHILE

```text
Unknown Count
      |
      v

Repeat
Repeat
Repeat
Repeat
Repeat

Until condition becomes False
```

---

# 1️⃣6️⃣ COMMON BEGINNER MISTAKES (YOU FACED THESE)

## Mistake 1

Trying to use non-numeric input with int()

Code:

```python
int("q")
```

Result:

```text
Error
```

Reason:

```text
q is not a number.
```

---

## Mistake 2

Input without validation

User enters:

```text
abc
```

Program expects:

```text
10
```

Program crashes.

---

## Mistake 3

Loop with incorrect exit handling

Example:

```python
while True:
```

but no:

```python
break
```

Result:

```text
Infinite loop
```

---

# PROBLEM YOU FACED (IMPORTANT)

Input:

```text
q
```

Program:

```python
int("q")
```

Result:

```text
Crash
```

---

## Fix Idea

```python
if value != "q":
    convert to int
```

Then process.

---

# 🧠 DAY-01 THINKING MAP

Everything learned so far:

```text
Input
  ↓

Type Casting
  ↓

Condition
  ↓

Decision
  ↓

Loop
  ↓

Repeat Work
```

---

#  REVISION SHEET

### Type Casting

```text
Convert one data type to another.
```

Example:

```python
int("10")
```

---

### if

```text
Decision making.
```

---

### if-else

```text
One path if true.
Another path if false.
```

---

### for loop

```text
Known count.
```

---

### while loop

```text
Unknown count.
```

---

### FOR vs WHILE

```text
FOR = known repetitions

WHILE = unknown repetitions
```

---

### Common Errors

```text
int("q") → error

Invalid user input

Infinite loops
```

---

### Real DevOps Flow

```text
Ask User
    ↓
Take Input
    ↓
Convert Type
    ↓
Make Decision
    ↓
Repeat If Needed
```

---


# PART 3 — FUNCTIONS → EXECUTION FLOW → FUNCTION STACK → DEVOPS FUNCTIONS → psutil

---

# 1️⃣7️⃣ FUNCTIONS

## Interview Answer

A function is a reusable block of code that performs a specific task.

---

# Why Functions Exist?

Imagine writing:

```python
print("Backup")
print("Backup")
print("Backup")
```

Repeated code.

---

Instead:

```python
def take_backup():
    print("Backup")
```

Now you can reuse it whenever needed.

---

# Definition

```text
Function = Reusable Machine
```

Think:

```text
Create Machine
      ↓
Call Machine
      ↓
Machine Does Work
      ↓
Return
```

---

# Basic Syntax

```python
def function_name():
    code
```

Example:

```python
def greet():
    print("Hello")
```

---

# Meaning

```text
Create a machine called greet
```

Visual:

```text
        ┌──────────┐
        │ greet()  │
        └──────────┘
```

Nothing runs yet.

You only created the machine.

---

# Calling A Function

Code:

```python
greet()
```

Flow:

```text
greet()
    |
    v

┌──────────┐
│ greet()  │
└──────────┘
    |
    v

Hello
```

---

# Output

```text
Hello
```

---

# Short Answer

```text
Function is a reusable block of code.
```

---

# Why Use Functions?

## Without Functions

```python
CPU code

CPU code

CPU code

CPU code
```

Hundreds of repeated lines.

Messy.

---

## With Functions

```python
check_cpu()
```

Cleaner.

Reusable.

Maintainable.

Readable.

---

# Benefits

```text
Code Reuse
Readability
Maintainability
Less Duplication
```

---

# Short Answer

```text
Functions help reuse code and improve readability.
```

---

# 1️⃣8️⃣ YOUR sum_of_num() FUNCTION

Code:

```python
def sum_of_num():

    num1 = int(input("Enter num 1: "))
    num2 = int(input("Enter num 2: "))

    total = num1 + num2

    print(total)
```

---

# What Happens?

Flow:

```text
Call Function
      |
      v

Enter Number 1
      |
      v

Enter Number 2
      |
      v

Add Numbers
      |
      v

Print Result
```

---

Example:

Input:

```text
10
20
```

Execution:

```text
10 + 20
```

Output:

```text
30
```

---

# 1️⃣9️⃣ FUNCTION EXECUTION FLOW

Most beginners think:

```text
Function runs automatically.
```

Wrong.

---

Functions run only when called.

Example:

```python
def greet():
    print("Hello")
```

At this point:

```text
Function Created

NOT Executed
```

---

To execute:

```python
greet()
```

Now Python runs the code inside.

---

# DEFINE → CALL → EXECUTE

```text
Step 1

Create Function
       |
       v

Step 2

Call Function
       |
       v

Step 3

Execute Function
```

---

# Example

Code:

```python
def greet():
    print("Hello")

greet()
```

Execution:

```text
Function Created
      |
      v

greet()
      |
      v

Hello
```

---

# 2️⃣0️⃣ HOW PYTHON EXECUTES FUNCTIONS

Code:

```python
def greet():
    print("Hello")

print("Start")

greet()

print("End")
```

---

# Execution Flow

Python starts from top to bottom.

```text
Program Start
      |
      v

print("Start")
      |
      v

Output: Start
      |
      v

greet()
      |
      v

Jump To Function
      |
      v

print("Hello")
      |
      v

Return Back
      |
      v

print("End")
      |
      v

Program End
```

---

# Output

```text
Start
Hello
End
```

---

# Visual Representation

```text
MAIN PROGRAM

print("Start")

greet()

print("End")
```

When function is called:

```text
MAIN PROGRAM
      |
      |
      +------ greet()
```

Python jumps.

---

Inside Function:

```text
MAIN PROGRAM
      |
      |
      +------ greet()
                  |
                  |
                  +--- print("Hello")
```

---

Return Back:

```text
MAIN PROGRAM

print("End")
```

---

# 2️⃣1️⃣ FUNCTION STACK VISUALIZATION

This topic is usually taught badly.

Let's simplify it.

---

Code:

```python
def greet():
    print("Hello")

print("Start")

greet()

print("End")
```

---

Visual

```text
MAIN PROGRAM
     |
     |
     +------ greet()
                  |
                  |
                  +--- print("Hello")
                  |
                  |
             Return
```

---

Thinking Model

```text
Function Call
      ↓
Do Work
      ↓
Return
```

---

# Another Example

Code:

```python
def sum_of_num():
    print("Adding")

sum_of_num()
```

Flow:

```text
MAIN PROGRAM
      |
      |
      +------ sum_of_num()
                    |
                    |
                    +--- Adding
                    |
                    |
                 Return
```

---

# 2️⃣2️⃣ REAL DEVOPS FUNCTIONS

Real automation frameworks are built using functions.

Instead of:

```text
Huge 500 Line Script
```

We divide work into functions.

---

Examples:

```python
take_backup()

check_health()

restart_service()

deploy_application()
```

---

Visual

```text
Automation Script

       |
       v

take_backup()

       |
       v

check_health()

       |
       v

restart_service()

       |
       v

deploy_application()
```

---

# Real DevOps Thinking

Manager says:

```text
Take Backup
```

Function:

```python
take_backup()
```

---

Manager says:

```text
Monitor Server
```

Function:

```python
check_system_health()
```

---

Manager says:

```text
Restart Service
```

Function:

```python
restart_service()
```

---

Manager says:

```text
Deploy Application
```

Function:

```python
deploy_application()
```

---

# 2️⃣3️⃣ psutil

## Interview Answer

psutil is a Python library used to retrieve system and process information.

---

# Simple Meaning

Think:

```text
psutil = System Health Inspector
```

or

```text
psutil = Toolbox
```

containing:

```text
CPU
Memory
Disk
Processes
Network
```

---

# Why Do We Need psutil?

Python itself cannot easily read:

```text
CPU Usage
Memory Usage
Disk Usage
```

Need helper library.

---

# Import psutil

Code:

```python
import psutil
```

Meaning:

```text
Python,

Bring me the psutil toolbox.
```

---

# Visual

Before:

```text
Python

X CPU
X Memory
X Disk
```

---

After Import:

```text
Python
   |
   v

┌────────────┐
│ psutil     │
├────────────┤
│ CPU        │
│ Memory     │
│ Disk       │
│ Processes  │
│ Network    │
└────────────┘
```

---

# Short Answer

```text
psutil is used to get system information.
```

---

# 2️⃣4️⃣ HOW TO GET CPU USAGE

Code:

```python
import psutil

cpu = psutil.cpu_percent(interval=1)

print(cpu)
```

---

# What Beginners Think

```text
Magic
```

---

# What Actually Happens

```text
Python
   |
   v

psutil
   |
   v

Operating System
   |
   v

CPU Usage
```

---

# Flow

```text
CPU
 |
 v

Current CPU = 18.4%

 |
 v

psutil

 |
 v

cpu variable
```

Stored as:

```text
┌───────┐
│ 18.4  │
└───────┘
    ↑
   cpu
```

---

# Visual

```text
CPU
 |
 v

psutil

 |
 v

Python Script

 |
 v

CPU %
```

---

# Example Output

```text
12.5
```

Meaning:

```text
12.5% CPU Used
```

---

# Short Answer

```python
psutil.cpu_percent(interval=1)
```

---

# 2️⃣5️⃣ HOW TO GET MEMORY USAGE

Code:

```python
memory = psutil.virtual_memory().percent
```

---

# What Happens?

```text
RAM
 |
 v

Current Usage

 |
 v

psutil

 |
 v

memory variable
```

---

# Visual

```text
RAM
 |
 v

psutil

 |
 v

Memory %
```

---

# Example

```text
71.4%
```

Stored:

```text
┌───────┐
│ 71.4  │
└───────┘
    ↑
 memory
```

---

# Short Answer

```python
psutil.virtual_memory().percent
```

---

# 2️⃣6️⃣ HOW TO GET DISK USAGE

Code:

```python
disk = psutil.disk_usage('/').percent
```

---

# What Happens?

```text
Hard Disk
    |
    v

Current Usage

    |
    v

psutil

    |
    v

disk variable
```

---

# Visual

```text
Disk
 |
 v

psutil

 |
 v

Disk %
```

---

# Example

```text
55%
```

Stored:

```text
┌───────┐
│ 55.0  │
└───────┘
    ↑
   disk
```

---

# Short Answer

```python
psutil.disk_usage('/').percent
```

---

# 2️⃣7️⃣ DAY-01 PYTHON EXECUTION THINKING

When you run:

```bash
python system_health.py
```

Python thinks:

```text
START
```

↓

```text
Load Imports
```

↓

```text
Create Functions
```

↓

```text
Reach Function Call
```

↓

```text
Jump Into Function
```

↓

```text
Execute Statements
```

↓

```text
Return Back
```

↓

```text
END
```

---

# DAY-01 THINKING MAP

```text
Functions
      ↓

Function Call
      ↓

Execution Flow
      ↓

Function Stack
      ↓

psutil
      ↓

CPU
Memory
Disk
      ↓

System Monitoring
```

---

# 🎯 PART 3 REVISION SHEET

### Function

```text
Reusable block of code.
```

---

### Why Functions?

```text
Reuse
Readability
Maintenance
```

---

### Function Flow

```text
Define
 ↓
Call
 ↓
Execute
```

---

### Function Stack

```text
Call
 ↓
Do Work
 ↓
Return
```

---

### psutil

```text
System Information Library
```

---

### CPU Usage

```python
psutil.cpu_percent(interval=1)
```

---

### Memory Usage

```python
psutil.virtual_memory().percent
```

---

### Disk Usage

```python
psutil.disk_usage('/').percent
```

---

### DevOps Functions

```python
take_backup()

check_health()

restart_service()

deploy_application()
```

---



# PART 4 — MONITORING → LOGGING → SYSTEM HEALTH SCRIPT → REAL DEVOPS MONITORING

---

# 2️⃣8️⃣ WHAT IS MONITORING?

## Interview Answer

Monitoring is the process of continuously checking the health and performance of systems, servers, applications, and infrastructure.

---

# Simple Meaning

Imagine your manager says:

```text
Monitor my server.
```

Your job:

```text
Check CPU
Check Memory
Check Disk
Check Services
```

continuously.

---

# Why Monitoring Exists?

Without monitoring:

```text
Server Problem
      ↓
Nobody Knows
```

---

With monitoring:

```text
Server Problem
      ↓
Monitoring Detects It
      ↓
Alert Generated
      ↓
Team Responds
```

---

# Real DevOps Examples

Monitoring:

```text
CPU Usage
Memory Usage
Disk Usage
Application Health
Network Health
Service Status
```

---

# Short Answer

```text
Monitoring means continuously checking system health.
```

---

# 2️⃣9️⃣ WHAT IS AUTOMATION?

## Interview Answer

Automation is using software to perform repetitive tasks without manual intervention.

---

# Manual Work

Every day:

```text
Check CPU
Check Memory
Check Disk
```

Again.

```text
Check CPU
Check Memory
Check Disk
```

Again.

```text
Check CPU
Check Memory
Check Disk
```

Boring and error-prone.

---

# Automation

Instead:

```bash
python monitor.py
```

Everything happens automatically.

---

# DevOps Examples

```text
Monitoring
Deployment
Backup
Testing
Log Collection
Cloud Provisioning
```

---

# Short Answer

```text
Automation means performing tasks automatically.
```

---

# 3️⃣0️⃣ WHAT IS LOGGING?

## Interview Answer

Logging is the process of recording events, errors, warnings, and system activities into a file.

---

# Simple Meaning

Think:

```text
Log File = CCTV Recording
```

---

Example:

```text
CPU ALERT
```

appears on screen and disappears.

How will you know later?

You need logs.

---

# Visual

```text
CPU ALERT
    |
    v

system_health.log
```

---

# Before

```text
system_health.log

(empty)
```

---

# After

```text
system_health.log

CPU ALERT
```

---

# After More Events

```text
system_health.log

CPU ALERT
MEMORY ALERT
CPU ALERT
DISK ALERT
```

---

# Real Example

```text
2026-05-31 02:05 CPU ALERT
2026-05-31 02:07 MEMORY ALERT
2026-05-31 02:08 SERVICE DOWN
```

---

# Why Logging Exists?

Imagine:

```text
2 AM
Server Crashed
```

Boss asks:

```text
What happened?
```

Without logs:

```text
No idea.
```

With logs:

```text
2026-05-31 02:05 CPU ALERT
2026-05-31 02:07 MEMORY ALERT
2026-05-31 02:08 SERVICE DOWN
```

Now you can investigate.

---

# Short Answer

```text
Logging means saving events into a file.
```

---

# 3️⃣1️⃣ WRITING TO A LOG FILE

Code:

```python
with open("system.log", "a") as file:
    file.write("CPU ALERT")
```

---

# What Happens?

Flow:

```text
Python
   |
   v

Write Message
   |
   v

system.log
```

---

# Meaning Of "a"

```python
"a"
```

means:

```text
Append Mode
```

Add new data without deleting old data.

---

# Visual

```text
system.log

CPU ALERT
MEMORY ALERT
DISK ALERT
```

New entries keep getting added.

---

# 3️⃣2️⃣ THE DAY-01 MONITORING STORY

Everything you've learned now connects together.

Manager says:

```text
Monitor my server.
```

---

What do we need?

```text
Variables
Input
Type Casting
Conditions
Loops
Functions
psutil
Monitoring
Logging
```

Everything learned so far.

---

# Complete Monitoring Story

```text
Ask Threshold
      ↓

Read CPU
      ↓

Read Memory
      ↓

Read Disk
      ↓

Compare
      ↓

Generate Alert
      ↓

Save Log
      ↓

Repeat
```

---

# 3️⃣3️⃣ PERFECT DAY-01 MONITORING SCRIPT

This version satisfies the assignment requirements:

```text
input()
functions
if/else
CPU
Memory
Disk
Alerts
```

---

## Code

```python
import psutil

def check_system_health():

    cpu_threshold = float(input("Enter CPU threshold (%): "))
    memory_threshold = float(input("Enter Memory threshold (%): "))
    disk_threshold = float(input("Enter Disk threshold (%): "))

    cpu = psutil.cpu_percent(interval=1)
    memory = psutil.virtual_memory().percent
    disk = psutil.disk_usage('/').percent

    print("\n===== SYSTEM HEALTH =====")

    print(f"CPU Usage: {cpu}%")
    if cpu > cpu_threshold:
        print("CPU ALERT")
    else:
        print("CPU OK")

    print()

    print(f"Memory Usage: {memory}%")
    if memory > memory_threshold:
        print("MEMORY ALERT")
    else:
        print("MEMORY OK")

    print()

    print(f"Disk Usage: {disk}%")
    if disk > disk_threshold:
        print("DISK ALERT")
    else:
        print("DISK OK")

check_system_health()
```

---

# 3️⃣4️⃣ EXECUTION FLOW OF THE SCRIPT

Imagine:

```bash
python system_health.py
```

---

Python thinks:

```text
START
```

↓

```text
Load psutil
```

↓

```text
Create Function
```

↓

```text
Reach Function Call
```

```python
check_system_health()
```

↓

```text
Jump Inside Function
```

↓

```text
Ask CPU Threshold
```

↓

```text
Ask Memory Threshold
```

↓

```text
Ask Disk Threshold
```

↓

```text
Read CPU
```

↓

```text
Read Memory
```

↓

```text
Read Disk
```

↓

```text
Compare CPU
```

↓

```text
Compare Memory
```

↓

```text
Compare Disk
```

↓

```text
Print Result
```

↓

```text
END
```

---

# 3️⃣5️⃣ WHAT ACTUALLY HAPPENS INSIDE THE SCRIPT?

Suppose user enters:

```text
CPU Threshold = 80
Memory Threshold = 80
Disk Threshold = 80
```

---

Stored as:

```text
┌───────┐
│ 80.0  │
└───────┘
    ↑
cpu_threshold
```

---

Suppose psutil reads:

```text
CPU = 90
Memory = 65
Disk = 55
```

---

Stored:

```text
┌───────┐
│ 90.0  │
└───────┘
   ↑
  cpu
```

```text
┌───────┐
│ 65.0  │
└───────┘
   ↑
 memory
```

```text
┌───────┐
│ 55.0  │
└───────┘
   ↑
 disk
```

---

# Comparison

```text
CPU = 90

Threshold = 80

90 > 80 ?
```

Result:

```text
YES
```

Output:

```text
CPU ALERT
```

---

Memory:

```text
65 > 80 ?
```

Result:

```text
NO
```

Output:

```text
MEMORY OK
```

---

Disk:

```text
55 > 80 ?
```

Result:

```text
NO
```

Output:

```text
DISK OK
```

---

# Final Output

```text
CPU ALERT
MEMORY OK
DISK OK
```

---

# 3️⃣6️⃣ REAL DEVOPS VERSION

Real monitoring systems don't run once.

They run forever.

---

Code Pattern:

```python
while True:
    check_system()
```

---

Meaning:

```text
Check
Wait
Check
Wait
Check
Wait
Repeat Forever
```

---

# Visual

```text
Read CPU
    ↓

Read Memory
    ↓

Read Disk
    ↓

Alert?
    ↓

Wait
    ↓

Repeat
```

---

# 3️⃣7️⃣ REAL DEVOPS MONITORING SCRIPT

This is closer to production monitoring.

```python
import psutil
import time
from datetime import datetime

CPU_THRESHOLD = 80
MEMORY_THRESHOLD = 80
DISK_THRESHOLD = 80

def write_log(message):
    with open("system_health.log", "a") as file:
        file.write(message + "\n")

print("SYSTEM MONITOR STARTED")

try:

    while True:

        cpu = psutil.cpu_percent(interval=1)
        memory = psutil.virtual_memory().percent
        disk = psutil.disk_usage('/').percent

        timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")

        print()
        print(f"CPU: {cpu}%")
        print(f"Memory: {memory}%")
        print(f"Disk: {disk}%")

        if cpu > CPU_THRESHOLD:
            msg = f"{timestamp} CPU ALERT {cpu}%"
            print(msg)
            write_log(msg)

        if memory > MEMORY_THRESHOLD:
            msg = f"{timestamp} MEMORY ALERT {memory}%"
            print(msg)
            write_log(msg)

        if disk > DISK_THRESHOLD:
            msg = f"{timestamp} DISK ALERT {disk}%"
            print(msg)
            write_log(msg)

        time.sleep(5)

except KeyboardInterrupt:
    print("\nMonitoring stopped")

print("Program Ended")
```

---

# 3️⃣8️⃣ REAL DEVOPS MONITORING ARCHITECTURE

Your Day-01 script is a tiny version of enterprise monitoring.

---

# Small Version

```text
SERVER

CPU
Memory
Disk

      |
      v

psutil

      |
      v

Python Script

      |
      v

Compare Threshold

      |
      v

Alert

      |
      v

Log File
```

---

# Enterprise Version

```text
Server
   |
   v

Monitoring Agent

   |
   v

Prometheus

   |
   v

Grafana

   |
   v

Dashboard
```

---

# Real Tools

```text
Prometheus
Grafana
Nagios
Datadog
CloudWatch
Zabbix
```

---

# Relationship To Your Script

```text
Day-01 Script
      ↓

Read CPU
Read Memory
Read Disk

      ↓

Alert

      ↓

Log
```

Same idea.

Smaller scale.

---

# 3️⃣9️⃣ COMPLETE DEVOPS FLOW

This is the final story connecting everything learned today.

```text
USER
 |
 v

Enter Threshold

 |
 v

Python

 |
 v

psutil

 |
 v

CPU
Memory
Disk

 |
 v

Compare

 |
 v

Alert

 |
 v

Log File

 |
 v

Wait

 |
 v

Repeat
```

---

# 🎯 PART 4 REVISION SHEET

### Monitoring

```text
Continuously checking system health.
```

---

### Automation

```text
Performing tasks automatically.
```

---

### Logging

```text
Saving events into a file.
```

---

### Log File

```text
CCTV recording of system events.
```

---

### Monitoring Script Flow

```text
Input
 ↓
Read System Data
 ↓
Compare
 ↓
Alert
 ↓
Log
 ↓
Repeat
```

---

### Enterprise Monitoring Flow

```text
Server
 ↓
Prometheus
 ↓
Grafana
 ↓
Dashboard
```

---

### Day-01 Project

```text
System Health Monitoring Script
```

---



# PART 5 — HANDS-ON TASKS → INTERVIEW QUESTIONS → CHEAT SHEET → COMPLETE DAY-01 SUMMARY

---

# 4️⃣0️⃣ HANDS-ON TASKS

These are the practical exercises from Day-01.

The goal is not just writing code.

The goal is understanding:

```text
Input
Variables
Type Casting
Conditions
Loops
Functions
psutil
Monitoring
```

---

# TASK 1 — ADD TWO NUMBERS

## Objective

Learn:

```text
input()
variables
type casting
print()
```

---

## Code

```python
num1 = int(input("Enter First Number: "))
num2 = int(input("Enter Second Number: "))

total = num1 + num2

print("Sum =", total)
```

---

## Flow

```text
User Input
     |
     v

num1
num2

     |
     v

Addition

     |
     v

Output
```

---

# TASK 2 — EVEN OR ODD

## Objective

Learn:

```text
if
else
decision making
```

---

## Code

```python
num = int(input("Enter Number: "))

if num % 2 == 0:
    print("Even")
else:
    print("Odd")
```

---

## Flow

```text
Input
  |
  v

Number

  |
  v

Divide By 2

  |
  v

Remainder = 0 ?

YES ----> Even

NO -----> Odd
```

---

# TASK 3 — MULTIPLICATION TABLE

## Objective

Learn:

```text
for loop
range()
repetition
```

---

## Code

```python
num = int(input("Enter Number: "))

for i in range(1,11):
    print(num * i)
```

---

## Example

Input:

```text
5
```

Output:

```text
5
10
15
20
25
30
35
40
45
50
```

---

# TASK 4 — CONTINUOUS MENU

## Objective

Learn:

```text
while loop
break
continuous execution
```

---

## Code

```python
while True:

    choice = input("Enter q to quit: ")

    if choice == "q":
        break

    print("Running...")
```

---

## Flow

```text
Start
  |
  v

Input

  |
  v

q ?

YES ----> Stop

NO -----> Continue
```

---

# TASK 5 — SUM USING FUNCTION

## Objective

Learn:

```text
functions
reusability
```

---

## Code

```python
def sum_of_num():

    num1 = int(input())
    num2 = int(input())

    print(num1 + num2)

sum_of_num()
```

---

## Flow

```text
Call Function
      |
      v

Take Input
      |
      v

Add
      |
      v

Print
```

---

# TASK 6 — SYSTEM HEALTH MONITOR

## Objective

Learn:

```text
psutil
monitoring
functions
if/else
real DevOps thinking
```

---

## Code

```python
import psutil

def check_system_health():

    cpu = psutil.cpu_percent(interval=1)

    print(cpu)

check_system_health()
```

---

## Extended Version

```python
import psutil

def check_system_health():

    cpu = psutil.cpu_percent(interval=1)
    memory = psutil.virtual_memory().percent
    disk = psutil.disk_usage('/').percent

    print(cpu)
    print(memory)
    print(disk)

check_system_health()
```

---

# 4️⃣1️⃣ INTERVIEW QUESTIONS & ANSWERS

---

## Q1. What is Python?

### Answer

Python is a high-level programming language used for automation, scripting, web development, data analysis, AI, and DevOps.

---

## Q2. What is a Variable?

### Answer

A variable is a named storage location used to store data.

Example:

```python
name = "Malathi"
```

---

## Q3. What is a Data Type?

### Answer

A data type defines the type of value stored inside a variable.

Examples:

```python
int
float
str
bool
```

---

## Q4. Difference Between int and float?

### Answer

| int          | float          |
| ------------ | -------------- |
| Whole Number | Decimal Number |
| 10           | 10.5           |
| 25           | 25.7           |

Example:

```python
age = 25
price = 99.5
```

---

## Q5. What Does input() Return?

### Answer

```text
input() ALWAYS returns string.
```

Example:

User enters:

```text
10
```

Python stores:

```python
"10"
```

not

```python
10
```

---

## Q6. What Is Type Casting?

### Answer

Type casting is converting one data type into another.

Example:

```python
int("10")
float("10")
str(10)
```

---

## Q7. What Is An if Statement?

### Answer

if is used for decision making.

Example:

```python
if cpu > 80:
    print("ALERT")
```

---

## Q8. Difference Between for and while?

### Answer

| for              | while                        |
| ---------------- | ---------------------------- |
| Known Count      | Unknown Count                |
| Fixed Repetition | Condition Based              |
| range() Common   | Continuous Monitoring Common |

Example:

```python
for i in range(5)
```

vs

```python
while True
```

---

## Q9. What Is A Function?

### Answer

A function is a reusable block of code used to perform a specific task.

---

## Q10. Why Use Functions?

### Answer

Functions improve:

```text
Code Reuse
Readability
Maintainability
```

---

## Q11. What Is psutil?

### Answer

psutil is a Python library used to retrieve system and process information.

Examples:

```text
CPU Usage
Memory Usage
Disk Usage
Processes
Network Information
```

---

## Q12. How Do You Get CPU Usage?

### Answer

```python
psutil.cpu_percent(interval=1)
```

---

## Q13. How Do You Get Memory Usage?

### Answer

```python
psutil.virtual_memory().percent
```

---

## Q14. How Do You Get Disk Usage?

### Answer

```python
psutil.disk_usage('/').percent
```

---

## Q15. What Is Logging?

### Answer

Logging is the process of recording system events into a file.

---

## Q16. What Is Automation?

### Answer

Automation is performing repetitive tasks automatically using software.

---

## Q17. Why Is Python Popular In DevOps?

### Answer

Python is:

```text
Easy To Learn
Cross Platform
Large Ecosystem
Excellent Automation Support
Cloud Friendly
DevOps Friendly
```

Used for:

```text
Monitoring
CI/CD
Infrastructure Automation
Cloud Automation
Testing
Scripting
```

---

# 4️⃣2️⃣ COMPLETE DAY-01 MASTER CHEAT SHEET

```text
print()
 ↓
Variables
 ↓
Data Types
 ↓
Input
 ↓
Type Casting
 ↓
Conditions
 ↓
Loops
 ↓
Functions
 ↓
psutil
 ↓
Monitoring
 ↓
Logging
 ↓
Automation
```

---

# 4️⃣3️⃣ 30-SECOND REVISION SHEET

### Variable

```text
Stores data.
```

### Data Type

```text
Type of stored data.
```

### input()

```text
Always returns string.
```

### Type Casting

```text
Convert data type.
```

### if

```text
Decision making.
```

### for

```text
Known count.
```

### while

```text
Unknown count.
```

### Function

```text
Reusable block of code.
```

### psutil

```text
System information library.
```

### Monitoring

```text
Check system health.
```

### Logging

```text
Save events.
```

### Automation

```text
Do work automatically.
```

---

# 4️⃣4️⃣ 1-MINUTE REVISION MAP

```text
User
 |
 v

Input

 |
 v

Variable

 |
 v

Type Casting

 |
 v

Condition

 |
 v

Decision

 |
 v

Loop

 |
 v

Function

 |
 v

psutil

 |
 v

CPU
Memory
Disk

 |
 v

Monitoring

 |
 v

Alert

 |
 v

Logging

 |
 v

Automation
```

---

# 4️⃣5️⃣ DAY-01 COMPLETE STORY

If someone asks:

### "What did you learn on Day-01?"

You can answer:

```text
I learned Python fundamentals including variables,
data types, input handling, type casting,
conditional statements, loops, and functions.

I then used the psutil library to retrieve CPU,
memory, and disk metrics.

Finally, I combined these concepts to build a
basic system health monitoring script that checks
system resources, generates alerts based on
thresholds, and demonstrates real-world DevOps
automation concepts such as monitoring and logging.
```

---

# 🎯 FINAL DAY-01 LEARNING FLOW

```text
Python Basics
      ↓
Variables
      ↓
Data Types
      ↓
Input
      ↓
Type Casting
      ↓
Conditions
      ↓
Loops
      ↓
Functions
      ↓
psutil
      ↓
CPU / Memory / Disk
      ↓
Monitoring
      ↓
Logging
      ↓
Automation
      ↓
System Health Monitoring Project
```


