# Day 02 - Python Dictionary Basics

## What is a Dictionary?

A Dictionary is a collection of data stored as Key-Value pairs.

Syntax:

```python
dictionary_name = {
    "key1": value1,
    "key2": value2
}
```

Example:

```python
info = {
    "name": "Shubham Bhaiya",
    "city": "Pune",
    "age": 29
}
```

---

## Key-Value Pair

Dictionary data is stored in the format:

```text
Key -> Value
```

Example:

```python
{
    "city": "Pune"
}
```

Here:

```text
city = Key
Pune = Value
```

---

## Dictionary Example

```python
info = {
    "name" : "Shubham Bhaiya",
    "city" : "Pune",
    "qualification": "Mtech",
    "age" : 29,
    "salary": 22.5,
    "married": True,
    "favourites" : ["teaching", "movies", 18]
}
```

Stored Data:

| Key           | Value                    | Data Type |
| ------------- | ------------------------ | --------- |
| name          | Shubham Bhaiya           | str       |
| city          | Pune                     | str       |
| qualification | Mtech                    | str       |
| age           | 29                       | int       |
| salary        | 22.5                     | float     |
| married       | True                     | bool      |
| favourites    | ["teaching","movies",18] | list      |

---

## Accessing Values Using []

Syntax:

```python
dictionary["key"]
```

Example:

```python
print(info["city"])
```

Output:

```text
Pune
```

Example:

```python
print("I live in", info["city"])
```

Output:

```text
I live in Pune
```

---

## get() Method

Used to safely retrieve a value from a dictionary.

Syntax:

```python
dictionary.get("key")
```

Example:

```python
info.get("city")
```

Output:

```text
Pune
```

---

## get() With Default Value

Syntax:

```python
dictionary.get("key", "default value")
```

Example:

```python
info.get("country", "Not Found")
```

Output:

```text
Not Found
```

Reason:

```text
country key does not exist in the dictionary.
```

---

## Difference Between [] and get()

### Using []

```python
info["country"]
```

Output:

```text
KeyError: 'country'
```

Program crashes.

---

### Using get()

```python
info.get("country")
```

Output:

```python
None
```

Program continues.

---

### Using get() with default value

```python
info.get("country", "Not Found")
```

Output:

```text
Not Found
```

Program continues.

---

## Important Observation

This code:

```python
info.get("country")
```

does NOT display anything.

Reason:

```text
Python returns the value but you are not printing it.
```

To see the result:

```python
print(info.get("country"))
```

Output:

```text
None
```

---

Similarly:

```python
info.get("country", "Not Found")
```

returns:

```text
Not Found
```

But nothing appears on screen unless:

```python
print(info.get("country", "Not Found"))
```

Output:

```text
Not Found
```

---

## update() Method

Used to add or update key-value pairs.

Syntax:

```python
dictionary.update({"key": "value"})
```

Example:

```python
info.update({"channel": "TrainWithShubham"})
```

Before:

```python
{
    "name":"Shubham Bhaiya"
}
```

After:

```python
{
    "name":"Shubham Bhaiya",
    "channel":"TrainWithShubham"
}
```

---

## dir() Function

Used to display all available methods and attributes of an object.

Example:

```python
print(dir(info))
```

Output contains methods such as:

```python
[
 'clear',
 'copy',
 'get',
 'items',
 'keys',
 'pop',
 'update',
 'values'
]
```

Purpose:

```text
Shows what operations can be performed on the object.
```

---

## items() Method

Used to retrieve all key-value pairs.

Syntax:

```python
dictionary.items()
```

Example:

```python
print(info.items())
```

Output:

```python
dict_items([
('name', 'Shubham Bhaiya'),
('city', 'Pune'),
('qualification', 'Mtech')
])
```

---

## Important Observation

This code:

```python
info.items()
```

produces no visible output.

Reason:

```text
The returned value is not printed.
```

To display it:

```python
print(info.items())
```

---

## Looping Through Dictionary

Syntax:

```python
for key, value in dictionary.items():
    print(key, value)
```

Example:

```python
for key, value in info.items():
    print(key, value)
```

Output:

```text
name Shubham Bhaiya
city Pune
qualification Mtech
age 29
salary 22.5
married True
favourites ['teaching', 'movies', 18]
channel TrainWithShubham
```

---

## How the Loop Works

Iteration 1:

```python
key = "name"
value = "Shubham Bhaiya"
```

Output:

```text
name Shubham Bhaiya
```

Iteration 2:

```python
key = "city"
value = "Pune"
```

Output:

```text
city Pune
```

And so on.

---

## IndentationError

Wrong:

```python
for key, value in info.items():

info["country"]
```

Error:

```text
IndentationError: expected an indented block
```

Reason:

```text
Python expected code inside the for loop.
```

Correct:

```python
for key, value in info.items():
    print(key, value)
```

---

## KeyError

Wrong:

```python
info["country"]
```

Error:

```text
KeyError: 'country'
```

Reason:

```text
The key does not exist in the dictionary.
```

Safer Alternative:

```python
info.get("country")
```

---

## Why Dictionaries Are Important For APIs

Most API responses are returned as JSON.

Example:

```python
{
    "userId": 1,
    "id": 1,
    "title": "delectus aut autem",
    "completed": False
}
```

Python converts JSON into a Dictionary.

Accessing data:

```python
data["title"]
```

Output:

```text
delectus aut autem
```

Therefore, understanding Dictionaries is essential before learning APIs.

---

# Python Dictionary

Dictionary stores data in Key-Value pairs.

Syntax:
```bash
info = {
    "name": "Shubham",
    "city": "Pune"
}
```
Access Value:
```bash
info["city"]
```
Output:
```bash
Pune
```
Safe Access:
```bash
info.get("city")
```
Difference:
```bash
info["country"]
→ KeyError

info.get("country")
→ None

info.get("country", "Not Found")
→ Not Found
```
Add/Update:
```bash
info.update({"channel":"TrainWithShubham"})
```
Loop Through Dictionary:
```bash
for key, value in info.items():
    print(key, value)
```
Useful Methods:
```bash
get()
items()
keys()
values()
update()
```
Why Important?

Most API JSON responses become Python Dictionaries.

---


Mistakes I Made

1. IndentationError

Wrong:
```bash
for key,value in info.items():

info["country"]
```
Reason:
Python expected code inside the loop.

Correct:
```bash
for key,value in info.items():
    print(key,value)
```
------------------------------------------------

2. I thought info.get("country")
would print output automatically.

Wrong understanding:
`Calling function = display output`

Actual:
`Need print()`
```bash
print(info.get("country"))
```
---

Dictionary

Stores Key-Value pairs.
```bash
Methods:
get()
items()
update()
keys()
values()
```
Difference:
```bash
info["country"]
→ KeyError

info.get("country")
→ None
```
API JSON responses are converted into Dictionaries.

---

# Python Dictionary  Questions & Answers

## 1. What is a Dictionary in Python?

A Dictionary is a built-in data structure used to store data in **key-value pairs**.

```python
student = {
    "name": "Shubham",
    "age": 25,
    "city": "Mumbai"
}
```

### Key Points

* Mutable (can be modified)
* Stores data as key-value pairs
* Keys must be unique
* Fast lookup using keys

---

## 2. What are Key-Value Pairs?

A Dictionary stores data in the form:

```python
key : value
```

Example:

```python
student = {
    "name": "Shubham",
    "age": 25
}
```

Here:

```python
"name" -> Key
"Shubham" -> Value

"age" -> Key
25 -> Value
```

Think of it like:

| Key  | Value   |
| ---- | ------- |
| name | Shubham |
| age  | 25      |
| city | Mumbai  |

---

## 3. How do you access a value in a Dictionary?

Using the key.

```python
student = {
    "name": "Shubham",
    "age": 25
}

print(student["name"])
```

Output:

```python
Shubham
```

---

## 4. Difference between `[]` and `get()`?

### Using `[]`

```python
student["name"]
```

If key exists:

```python
Shubham
```

If key does not exist:

```python
student["salary"]
```

Output:

```python
KeyError
```

---

### Using `get()`

```python
student.get("name")
```

Output:

```python
Shubham
```

If key does not exist:

```python
student.get("salary")
```

Output:

```python
None
```

Or:

```python
student.get("salary", "Not Found")
```

Output:

```python
Not Found
```

###  Answer

`[]` throws a KeyError if the key is missing, whereas `get()` safely returns `None` or a default value.

---

## 5. What does `update()` do?

`update()` adds new key-value pairs or updates existing ones.

Example:

```python
student = {
    "name": "Shubham"
}

student.update({"city": "Mumbai"})
```

Output:

```python
{
    "name": "Shubham",
    "city": "Mumbai"
}
```

Updating existing value:

```python
student.update({"city": "Pune"})
```

Output:

```python
{
    "name": "Shubham",
    "city": "Pune"
}
```

---

## 6. What does `items()` return?

`items()` returns all key-value pairs as tuples.

```python
student = {
    "name": "Shubham",
    "age": 25
}

print(student.items())
```

Output:

```python
dict_items([
    ('name', 'Shubham'),
    ('age', 25)
])
```

Useful for looping through both keys and values.

---

## 7. How do you iterate through a Dictionary?

### Iterate through keys

```python
for key in student:
    print(key)
```

Output:

```python
name
age
```

---

### Iterate through values

```python
for value in student.values():
    print(value)
```

Output:

```python
Shubham
25
```

---

### Iterate through key-value pairs

```python
for key, value in student.items():
    print(key, value)
```

Output:

```python
name Shubham
age 25
```

---

## 8. What does `dir()` do?

`dir()` displays all available attributes and methods of an object.

Example:

```python
student = {}

print(dir(student))
```

Output (partial):

```python
['clear',
 'copy',
 'get',
 'items',
 'keys',
 'update',
 'values']
```

###  Answer

`dir()` is used to inspect an object and view all available methods and attributes.

---

## 9. What is KeyError?

A KeyError occurs when trying to access a key that does not exist.

Example:

```python
student = {
    "name": "Shubham"
}

print(student["salary"])
```

Output:

```python
KeyError: 'salary'
```

### Fix

```python
student.get("salary")
```

---

## 10. What is IndentationError?

Python uses indentation (spaces/tabs) to define code blocks.

Wrong:

```python
if True:
print("Hello")
```

Output:

```python
IndentationError
```

Correct:

```python
if True:
    print("Hello")
```

###  Answer

IndentationError occurs when Python code is not properly indented.

---

## 11. Why are Dictionaries important when working with APIs?

Most API responses are returned in JSON format.

JSON data is converted into Python Dictionaries.

Example API response:

```json
{
  "name": "Shubham",
  "city": "Mumbai"
}
```

Python:

```python
response = {
    "name": "Shubham",
    "city": "Mumbai"
}

print(response["name"])
```

### Real-world Usage

* REST API Automation
* DevOps Automation
* Reading JSON files
* Parsing API responses
* Extracting data from web services

###  Answer

Dictionaries are important because JSON API responses are converted into Python dictionaries, making it easy to access and manipulate data.

---

## 12. Can a Dictionary store multiple data types?

**Yes.**

Both keys and values can store different data types.

Example:

```python
data = {
    "name": "Shubham",     # String
    "age": 25,             # Integer
    "salary": 50000.50,    # Float
    "is_active": True,     # Boolean
    "skills": ["Python", "AWS"], # List
    "address": {
        "city": "Mumbai"
    }                      # Dictionary
}
```

###  Answer

Yes, a Python Dictionary can store multiple data types such as strings, integers, floats, booleans, lists, tuples, and even nested dictionaries.

---

# Quick Revision (1-Minute)

| Question                             | Short Answer                                       |
| ------------------------------------ | -------------------------------------------------- |
| What is Dictionary?                  | Collection of key-value pairs                      |
| Keys must be unique?                 | Yes                                                |
| Access value?                        | `dict[key]` or `dict.get(key)`                     |
| Difference between `[]` and `get()`? | `[]` raises KeyError, `get()` returns None/default |
| `update()`?                          | Add or modify key-value pairs                      |
| `items()`?                           | Returns key-value tuples                           |
| Iterate Dictionary?                  | `for key, value in dict.items()`                   |
| `dir()`?                             | Shows available methods and attributes             |
| KeyError?                            | Accessing a missing key                            |
| IndentationError?                    | Incorrect code indentation                         |
| Why important for APIs?              | JSON responses become Python dictionaries          |
| Multiple data types?                 | Yes                                                |

###  One-Liner

> "A Python Dictionary is a mutable collection that stores data in key-value pairs, provides fast lookups using keys, and is heavily used in API automation because JSON responses are converted into dictionaries."


********



## What is a List?

A List is used to store multiple values in a single variable.

Example:

```python
clouds = ["aws", "azure", "gcp"]
```

Instead of creating:

```python
cloud1 = "aws"
cloud2 = "azure"
cloud3 = "gcp"
```

we can store everything in one List.

---

## List Creation - Method 1

```python
a = [100,200,300, True, 4.6]
```

Output:

```python
[100,200,300,True,4.6]
```

A List can store multiple data types:

| Value | Data Type |
| ----- | --------- |
| 100   | int       |
| 200   | int       |
| 300   | int       |
| True  | bool      |
| 4.6   | float     |

---

## Checking Type

```python
print(type(a))
```

Output:

```python
<class 'list'>
```

Meaning:

```text
a is a List.
```

---

## append()

Used to add a single item at the end of the List.

Example:

```python
a.append(500)
```

Before:

```python
[100,200,300,True,4.6]
```

After:

```python
[100,200,300,True,4.6,500]
```

---

## List Creation - Method 2

```python
clouds = list()
```

Creates an empty List.

Equivalent to:

```python
clouds = []
```

Output:

```python
[]
```

---

## Adding Elements

```python
clouds.append("aws")
clouds.append("azure")
clouds.append("gcp")
clouds.append("ibm")
clouds.append("alibaba")
clouds.append("utho")
```

Final List:

```python
['aws', 'azure', 'gcp', 'ibm', 'alibaba', 'utho']
```

---

## len()

Used to count total elements.

Example:

```python
len(clouds)
```

Output:

```python
6
```

Example:

```python
print("Length of list is:", len(clouds))
```

Output:

```text
Length of list is: 6
```

---

## Indexing

Every element gets a position called an Index.

```text
Index

0 → aws
1 → azure
2 → gcp
3 → ibm
4 → alibaba
5 → utho
```

---

## Access First Element

```python
clouds[0]
```

Output:

```python
aws
```

Example:

```python
print(clouds[0])
```

Output:

```python
aws
```

---

## Negative Indexing

Python can count from the end.

```text
-1 → utho
-2 → alibaba
-3 → ibm
```

Example:

```python
clouds[-1]
```

Output:

```python
utho
```

---

## dir()

```python
print(dir(clouds))
```

Purpose:

```text
Shows all methods available for List objects.
```

Useful methods seen:

```python
append
extend
insert
remove
pop
sort
reverse
count
index
```

Ignore methods starting with:

```python
__
```

for now.

---

## **doc**

Every Python method has built-in documentation.

Example:

```python
print(clouds.extend.__doc__)
```

Output (similar):

```text
Extend list by appending elements from the iterable.
```

Meaning:

```text
Show help text for the extend() method.
```

---

## append() vs extend()

### append()

Adds one item.

```python
clouds.append("aws")
```

Result:

```python
['aws']
```

---

### extend()

Adds multiple items.

```python
clouds.extend(["aws","azure"])
```

Result:

```python
['aws','azure']
```

---

## for Loop

```python
for cloud in clouds:
```

Meaning:

```text
Take one item from the list at a time.
```

Python automatically does:

Iteration 1:

```python
cloud = "aws"
```

Iteration 2:

```python
cloud = "azure"
```

Iteration 3:

```python
cloud = "gcp"
```

and so on.

---

## if / elif / else

### If cloud is aws

```python
if cloud == "aws":
```

Output:

```text
aws Market Leader + coverd in course
```

---

### If cloud is azure or gcp

```python
elif cloud == "azure" or cloud == "gcp":
```

Output:

```text
azure DevOps - Zero To Hero Me vo bhi cover karoonga

gcp DevOps - Zero To Hero Me vo bhi cover karoonga
```

---

### If cloud is utho

```python
elif cloud == "utho":
```

Output:

```text
utho Indian Cloud
```

---

### Else

Output:

```text
ibm baaki nahi honge

alibaba baaki nahi honge
```

---

## range()

Comment in code:

```python
# range(5) -> 0,1,2,3,4
```

Example:

```python
for i in range(5):
    print(i)
```

Output:

```text
0
1
2
3
4
```

Rule:

```text
Starts from 0
Stops before 5
```

---

## Why Lists Matter For APIs

Many API responses return:

```python
[
    {"name":"AWS"},
    {"name":"Azure"},
    {"name":"GCP"}
]
```

This is:

```text
List of Dictionaries
```

Understanding Lists is required before learning APIs.

---



### Discovery 1

```python
for cloud in clouds:
```

does not require user input.

Reason:

```text
The loop automatically reads values from the List.
```

---

### Discovery 2

This line:

```python
# ['aws', 'azure', 'gcp', 'ibm', 'alibaba', 'utho']
```

is only a comment.

Python ignores it.

---

### Discovery 3

This line:

```python
# range(5) -> 0,1,2,3,4
```

is only explaining how range() works.

It is not executed.

---

### Discovery 4

```python
dir(clouds)
```

means:

```text
Show all available methods for Lists.
```

---

### Discovery 5

```python
clouds.extend.__doc__
```

means:

```text
Show help/documentation for extend().
```

---

### Discovery 6

Methods beginning with:

```python
__
```

are internal Python methods.

Not important for beginners.

---

### Discovery 7

Difference between append() and extend()

```python
append()
```

adds one item.

```python
extend()
```

adds multiple items.

---


## List

Stores multiple values in a single variable.

Example:

```python
clouds = ["aws","azure","gcp"]
```

---

## Create List

```python
a = []
```

or

```python
a = list()
```

---

## Add Item

```python
a.append(100)
```

---

## Add Multiple Items

```python
a.extend([200,300])
```

---

## Length

```python
len(a)
```

---

## First Item

```python
a[0]
```

---

## Last Item

```python
a[-1]
```

---

## Loop

```python
for item in a:
    print(item)
```

---

## Useful Methods

```python
append()
extend()
remove()
pop()
sort()
reverse()
count()
index()
```

---

## dir()

```python
dir(list)
```

Shows available methods.

---

## **doc**

```python
list.extend.__doc__
```

Shows documentation/help text.

---

##  Questions


# Python List  Questions & Answers

---

# 1. What is a List in Python?

A List is a built-in Python data structure used to store multiple items in a single variable.

```python
clouds = ["aws", "azure", "gcp"]
```

### Key Features

* Ordered collection
* Mutable (can be modified)
* Allows duplicate values
* Supports indexing
* Can store multiple data types

###  Answer

A List is an ordered, mutable collection used to store multiple values in a single variable.

---

# 2. Can a List store multiple data types?

**Yes.**

A Python List can store different data types together.

```python
data = [
    "Shubham",
    25,
    99.5,
    True
]
```

Output:

```python
['Shubham', 25, 99.5, True]
```

###  Answer

Yes, Lists can store strings, integers, floats, booleans, dictionaries, and even other lists.

---

# 3. Difference between append() and extend()?

## append()

Adds a single element to the end of the List.

```python
clouds = ["aws", "azure"]

clouds.append("gcp")

print(clouds)
```

Output:

```python
['aws', 'azure', 'gcp']
```

---

If a List is appended:

```python
clouds = ["aws", "azure"]

clouds.append(["gcp", "utho"])

print(clouds)
```

Output:

```python
['aws', 'azure', ['gcp', 'utho']]
```

Notice the nested List.

---

## extend()

Adds each element individually.

```python
clouds = ["aws", "azure"]

clouds.extend(["gcp", "utho"])

print(clouds)
```

Output:

```python
['aws', 'azure', 'gcp', 'utho']
```

---

###  Answer

* `append()` adds one item as a single element.
* `extend()` adds multiple elements individually.

---

# 4. What is Indexing?

Indexing means accessing elements using their position.

```python
clouds = ["aws", "azure", "gcp"]

print(clouds[0])
```

Output:

```python
aws
```

Index positions:

```python
["aws", "azure", "gcp"]
   0       1       2
```

###  Answer

Indexing is the process of accessing List elements using their position number.

---

# 5. What is Negative Indexing?

Negative indexing starts counting from the end.

```python
clouds = ["aws", "azure", "gcp"]

print(clouds[-1])
```

Output:

```python
gcp
```

Visualization:

```python
["aws", "azure", "gcp"]
   0       1       2

  -3      -2      -1
```

###  Answer

Negative indexing allows access to elements from the end of a List.

---

# 6. What does len() do?

`len()` returns the total number of elements.

```python
clouds = ["aws", "azure", "gcp"]

print(len(clouds))
```

Output:

```python
3
```

###  Answer

`len()` returns the number of items in a List or any collection.

---

# 7. How do you iterate through a List?

## Method 1: Using for loop

```python
clouds = ["aws", "azure", "gcp"]

for cloud in clouds:
    print(cloud)
```

Output:

```python
aws
azure
gcp
```

---

## Method 2: Using range()

```python
clouds = ["aws", "azure", "gcp"]

for i in range(len(clouds)):
    print(clouds[i])
```

Output:

```python
aws
azure
gcp
```

###  Answer

Lists are commonly iterated using `for` loops or `range(len(list))`.

---

# 8. What is range()?

`range()` generates a sequence of numbers.

```python
for i in range(5):
    print(i)
```

Output:

```python
0
1
2
3
4
```

---

### Different Forms

```python
range(stop)
```

```python
range(start, stop)
```

```python
range(start, stop, step)
```

Example:

```python
for i in range(1, 10, 2):
    print(i)
```

Output:

```python
1
3
5
7
9
```

###  Answer

`range()` generates a sequence of numbers and is commonly used in loops.

---

# 9. What does dir() do?

Displays all available methods and attributes of an object.

Example:

```python
clouds = []

print(dir(clouds))
```

Output (partial):

```python
[
'append',
'clear',
'copy',
'count',
'extend',
'index',
'insert',
'pop',
'remove',
'sort'
]
```

###  Answer

`dir()` is used to inspect an object and view all available methods and attributes.

---

# 10. What does **doc** do?

`__doc__` displays the documentation string of an object, function, class, or method.

Example:

```python
print(list.append.__doc__)
```

Output:

```python
Append object to the end of the list.
```

---

Another example:

```python
print(len.__doc__)
```

Output:

```python
Return the number of items in a container.
```

###  Answer

`__doc__` is used to view the built-in documentation of Python objects, functions, classes, and methods.

---

# 11. Why are Lists important when working with APIs?

API responses often contain arrays.

JSON Array:

```json
{
  "servers": [
    "aws",
    "azure",
    "gcp"
  ]
}
```

Python:

```python
response = {
    "servers": [
        "aws",
        "azure",
        "gcp"
    ]
}

print(response["servers"])
```

Output:

```python
['aws', 'azure', 'gcp']
```

---

### Real-world Usage

* API Automation
* DevOps Scripts
* Reading JSON Responses
* Storing Multiple Records
* Processing Server Lists
* Looping Through API Data

###  Answer

Lists are important because API responses frequently contain arrays, which become Python Lists when parsed.

---

# 12. Difference between List and Dictionary?

| Feature          | List              | Dictionary                          |
| ---------------- | ----------------- | ----------------------------------- |
| Storage          | Values only       | Key-Value pairs                     |
| Access           | By index          | By key                              |
| Ordered          | Yes               | Yes (Python 3.7+)                   |
| Mutable          | Yes               | Yes                                 |
| Duplicate Values | Allowed           | Values allowed, keys must be unique |
| Example          | `["aws","azure"]` | `{"cloud":"aws"}`                   |

---

## Example

### List

```python
clouds = ["aws", "azure", "gcp"]

print(clouds[0])
```

Output:

```python
aws
```

---

### Dictionary

```python
cloud = {
    "name": "aws",
    "region": "us-east-1"
}

print(cloud["name"])
```

Output:

```python
aws
```

###  Answer

A List stores values and is accessed using indexes, whereas a Dictionary stores key-value pairs and is accessed using keys.

---

# Quick Revision (1-Minute)

| Question             | Short Answer                                     |
| -------------------- | ------------------------------------------------ |
| What is a List?      | Ordered, mutable collection                      |
| Multiple data types? | Yes                                              |
| append()             | Adds one element                                 |
| extend()             | Adds multiple elements                           |
| Indexing?            | Access using position                            |
| Negative indexing?   | Access from end                                  |
| len()?               | Returns number of elements                       |
| Iterate List?        | `for` loop                                       |
| range()?             | Generates sequence of numbers                    |
| dir()?               | Shows methods and attributes                     |
| **doc**?             | Shows documentation string                       |
| Lists in APIs?       | JSON arrays become Python Lists                  |
| List vs Dictionary?  | List = index-based, Dictionary = key-value based |

###  One-Liner

> "A Python List is an ordered and mutable collection that can store multiple values and data types, supports indexing, and is widely used to process JSON arrays returned by APIs."

********

# 03_set_basics.py



## What is a Set?

A Set is a collection used to store **unique values**.

Properties of a Set:

✅ Stores unique values only

✅ Automatically removes duplicates

❌ Does not support indexing

❌ Does not preserve insertion order (for learning purposes, assume order is not guaranteed)

---

## Creating an Empty Set

```python
days = set()
```

Output:

```python
print(type(days))
```

Output:

```text
<class 'set'>
```

---

## Important Difference

### Empty Dictionary

```python
info = {}
```

Output:

```python
print(type(info))
```

```text
<class 'dict'>
```

---

### Empty Set

```python
days = set()
```

Output:

```python
print(type(days))
```

```text
<class 'set'>
```

---

## Why Can't We Use {}

Many beginners think:

```python
days = {}
```

creates a Set.

Wrong.

Python treats:

```python
{}
```

as an empty Dictionary.

Therefore:

```python
days = set()
```

must be used for an empty Set.

---

## Creating a Set With Values

```python
days = {"saturday","sunday"}
```

Output:

```python
{'saturday','sunday'}
```

Notice:

```text
Curly braces {}
```

are used.

---

## Duplicate Values in Set

Example:

```python
days = {"saturday","sunday","sunday","saturday"}
```

Input:

```text
saturday
sunday
sunday
saturday
```

Python removes duplicates automatically.

Actual Set stored:

```python
{'saturday','sunday'}
```

---

## Why Was This Line Written?

```python
days = {"saturday","sunday","sunday","saturday"}
```

Purpose:

```text
To demonstrate that Sets automatically remove duplicate values.
```

Even though duplicates are provided, only unique values remain.

---

## Printing the Set

Example:

```python
print(days)
```

Output:

```python
{'saturday', 'sunday'}
```

Only one copy of each value remains.

---

# Removing Duplicates From a List

Original List:

```python
nums = [1,1,1,1,2,2,2,3,3,4,6.4,6.4,0,-1,-4]
```

Contains many duplicate values.

---

## Step 1: Convert List to Set

```python
set(nums)
```

Result:

```python
{0,1,2,3,4,6.4,-1,-4}
```

Duplicates removed.

---

## Step 2: Convert Back to List

```python
nums = list(set(nums))
```

Now:

```python
nums
```

contains:

```python
[0,1,2,3,4,6.4,-4,-1]
```

(or a different order)

---

## Why Convert Back To List?

Because Sets do not support indexing.

Example:

```python
days[0]
```

Error:

```text
TypeError
```

If we need:

```text
Indexing
Looping by index
List operations
```

we convert back:

```python
list(set(nums))
```

---

## Understanding Your Output

Output:

```python
[0, 1, 2, 3, 4, 6.4, -4, -1]
```

You may ask:

```text
Where did the duplicates go?
```

Answer:

```text
The Set removed them automatically.
```

---

## Why Did Order Change?

Original:

```python
[1,1,1,1,2,2,2,3,3,4,6.4,6.4,0,-1,-4]
```

Output:

```python
[0,1,2,3,4,6.4,-4,-1]
```

Reason:

```text
Sets do not guarantee insertion order.
```

Therefore:

```python
list(set(nums))
```

removes duplicates but may change order.

---

## Real DevOps Example

Suppose an API returns:

```python
servers = [
    "server1",
    "server1",
    "server2",
    "server2",
    "server3"
]
```

We need only unique servers.

Solution:

```python
unique_servers = list(set(servers))
```

Result:

```python
['server1','server2','server3']
```

---

## type()

Used to check data type.

Example:

```python
print(type(info))
```

Output:

```text
<class 'dict'>
```

---

Example:

```python
print(type(days))
```

Output:

```text
<class 'set'>
```

---

## Why Sets Matter

Sets are commonly used:

* Removing duplicates
* Comparing collections
* Finding unique values
* Processing API data
* Log analysis
* Server inventory cleanup

---



## Discovery 1

```python
{}
```

does NOT create a Set.

It creates a Dictionary.

Example:

```python
info = {}
```

Output:

```text
<class 'dict'>
```

---

## Discovery 2

To create an empty Set:

```python
days = set()
```

Output:

```text
<class 'set'>
```

---

## Discovery 3

This line:

```python
days = {"saturday","sunday","sunday","saturday"}
```

was intentionally written.

Purpose:

```text
To prove that Sets automatically remove duplicates.
```

Stored value becomes:

```python
{'saturday','sunday'}
```

---

## Discovery 4

This line:

```python
set(nums)
```

removes duplicates automatically.

---

## Discovery 5

This line:

```python
list(set(nums))
```

is a common Python trick.

Meaning:

```text
Convert List → Set → Remove Duplicates → Convert Back To List
```

---

## Discovery 6

Output order may change.

Example:

```python
[0,1,2,3,4,6.4,-4,-1]
```

Reason:

```text
Sets do not guarantee insertion order.
```

---

## Discovery 7

Sets do not support indexing.

Wrong:

```python
days[0]
```

Result:

```text
TypeError
```

---

## Discovery 8

Sets are mainly used when uniqueness matters.

Example:

```python
Emails
Server names
Hostnames
User IDs
IP addresses
```

---



## Set

Stores unique values only.

Properties:

```text
✔ No duplicates
✔ Fast lookup
✘ No indexing
✘ Order not guaranteed
```

---

## Create Empty Set

```python
days = set()
```

---

## Create Set With Values

```python
days = {"sat","sun"}
```

---

## Remove Duplicates

```python
nums = list(set(nums))
```

---

## Check Type

```python
type(days)
```

Output:

```text
<class 'set'>
```

---

## Dictionary vs Set

Dictionary:

```python
{}
```

Output:

```text
dict
```

---

Set:

```python
set()
```

Output:

```text
set
```

---

## Common Use Cases

* Remove duplicates
* Unique server names
* Unique IP addresses
* Unique user IDs
* API data cleanup

---

##  Questions

# Python Set  Questions & Answers

---

# 1. What is a Set in Python?

A Set is a built-in Python data structure used to store **unique values**.

```python
clouds = {"aws", "azure", "gcp"}

print(clouds)
```

Output:

```python
{'aws', 'azure', 'gcp'}
```

### Key Features

* Stores unique values only
* Removes duplicates automatically
* Mutable (can add/remove elements)
* Unordered collection
* Faster membership checks

###  Answer

A Set is an unordered collection of unique elements that automatically removes duplicates.

---

# 2. How is Set different from List?

### List

```python
clouds = ["aws", "aws", "azure"]
```

Duplicates are allowed.

Output:

```python
['aws', 'aws', 'azure']
```

---

### Set

```python
clouds = {"aws", "aws", "azure"}
```

Output:

```python
{'aws', 'azure'}
```

Duplicate value removed automatically.

---

###  Answer

A List allows duplicates and supports indexing, whereas a Set stores only unique values and does not support indexing.

---

# 3. Can a Set contain duplicate values?

**No.**

Example:

```python
nums = {1, 2, 3, 1, 2, 3}

print(nums)
```

Output:

```python
{1, 2, 3}
```

Duplicates are automatically removed.

###  Answer

No. A Set automatically removes duplicate values and keeps only unique elements.

---

# 4. How do you create an empty Set?

Wrong:

```python
empty = {}
```

This creates a Dictionary.

Correct:

```python
empty = set()
```

Output:

```python
set()
```

###  Answer

An empty Set is created using `set()` because `{}` creates an empty Dictionary.

---

# 5. Why does {} create a Dictionary instead of a Set?

Python reserves `{}` for Dictionaries.

Example:

```python
data = {}

print(type(data))
```

Output:

```python
<class 'dict'>
```

---

To create a Set:

```python
data = set()

print(type(data))
```

Output:

```python
<class 'set'>
```

###  Answer

Because Python uses `{}` syntax for Dictionaries, an empty Set must be created using `set()`.

---

# 6. What does set(nums) do?

It converts a collection into a Set and removes duplicates.

```python
nums = [1, 2, 2, 3, 3, 4]

result = set(nums)

print(result)
```

Output:

```python
{1, 2, 3, 4}
```

###  Answer

`set(nums)` converts the collection into a Set and removes duplicate values.

---

# 7. What does list(set(nums)) do?

Used to remove duplicates from a List.

```python
nums = [1, 2, 2, 3, 3, 4]

unique_nums = list(set(nums))

print(unique_nums)
```

Output:

```python
[1, 2, 3, 4]
```

###  Answer

`list(set(nums))` removes duplicates from a List and converts the result back into a List.

---

# 8. Why may the order change after converting to a Set?

Sets are unordered collections.

Example:

```python
nums = [5, 1, 4, 2]

print(list(set(nums)))
```

Possible output:

```python
[1, 2, 4, 5]
```

or

```python
[4, 1, 5, 2]
```

The order is not guaranteed.

###  Answer

The order may change because Sets are unordered and do not preserve element positions.

---

# 9. Does a Set support indexing?

**No.**

List:

```python
clouds = ["aws", "azure", "gcp"]

print(clouds[0])
```

Output:

```python
aws
```

---

Set:

```python
clouds = {"aws", "azure", "gcp"}

print(clouds[0])
```

Output:

```python
TypeError
```

###  Answer

No. Sets do not support indexing because they are unordered collections.

---

# 10. When would you use a Set in a DevOps project?

### Example 1: Remove duplicate servers

```python
servers = [
    "web01",
    "web01",
    "web02",
    "web03",
    "web03"
]

unique_servers = set(servers)

print(unique_servers)
```

Output:

```python
{'web01', 'web02', 'web03'}
```

---

### Example 2: Unique IP addresses

```python
ips = [
    "10.0.0.1",
    "10.0.0.1",
    "10.0.0.2"
]

unique_ips = set(ips)
```

---

### Example 3: Unique AWS EC2 instances

API may return duplicate instance IDs.

A Set helps eliminate duplicates before processing.

###  Answer

Sets are useful in DevOps for removing duplicate servers, IP addresses, hostnames, instance IDs, and inventory records.

---

# 11. Difference between List, Set, and Dictionary?

| Feature    | List    | Set            | Dictionary      |
| ---------- | ------- | -------------- | --------------- |
| Stores     | Values  | Unique Values  | Key-Value Pairs |
| Duplicates | Allowed | Not Allowed    | Keys Unique     |
| Ordered    | Yes     | No             |                 |
| Indexing   | Yes     | No             | By Key          |
| Mutable    | Yes     | Yes            | Yes             |
| Syntax     | `[]`    | `{}` / `set()` | `{key:value}`   |

---

### Examples

#### List

```python
clouds = ["aws", "aws", "azure"]
```

Output:

```python
['aws', 'aws', 'azure']
```

---

#### Set

```python
clouds = {"aws", "aws", "azure"}
```

Output:

```python
{'aws', 'azure'}
```

---

#### Dictionary

```python
cloud = {
    "name": "aws",
    "region": "us-east-1"
}
```

Output:

```python
{
 'name': 'aws',
 'region': 'us-east-1'
}
```

###  Answer

* List → Ordered collection, duplicates allowed.
* Set → Unique values only, no indexing.
* Dictionary → Stores data as key-value pairs.

---

# 12. Why are Sets useful when processing API responses or server inventories?

API responses often contain duplicate records.

Example:

```python
servers = [
    "web01",
    "web01",
    "web02",
    "web03",
    "web03"
]
```

Without Set:

```python
web01
web01
web02
web03
web03
```

With Set:

```python
{'web01', 'web02', 'web03'}
```

---

### Real DevOps Use Cases

* Removing duplicate servers from inventories
* Removing duplicate EC2 instance IDs
* Unique Kubernetes node names
* Unique Docker container IDs
* Unique IP addresses
* Unique usernames from API responses

###  Answer

Sets are useful because they automatically remove duplicate records, making API response processing and infrastructure inventory management more efficient.

---

# Quick Revision (1-Minute)

| Question            | Short Answer                        |
| ------------------- | ----------------------------------- |
| What is a Set?      | Collection of unique values         |
| Duplicates allowed? | No                                  |
| Empty Set?          | `set()`                             |
| `{}` creates?       | Dictionary                          |
| `set(nums)`?        | Removes duplicates                  |
| `list(set(nums))`?  | Remove duplicates and return List   |
| Ordered?            | No                                  |
| Supports indexing?  | No                                  |
| DevOps use?         | Remove duplicate servers/IPs        |
| List vs Set?        | List allows duplicates, Set doesn't |
| Dictionary?         | Stores key-value pairs              |
| API usage?          | Deduplicate records                 |

###  One-Liner

> "A Python Set is an unordered collection of unique elements that automatically removes duplicates and is commonly used in DevOps and API automation to deduplicate server inventories, IP addresses, and API response data."


---

### Quick Memory Trick

```python
List  -> Ordered, Duplicates Allowed

Set   -> Unique Values Only

Dict  -> Key : Value Pairs
```

Examples:

```python
my_list = [1,1,2,2]

my_set = {1,2}

my_dict = {"name":"Shubham"}
```

**********

# 04_file_handling.py



## What is File Handling?

File Handling allows Python to:

* Read data from files
* Write data to files
* Append data to files
* Store data permanently

Without files:

```text
Program Runs
↓
Program Ends
↓
Data Lost
```

With files:

```text
Program Runs
↓
Data Saved In File
↓
Program Ends
↓
Data Still Exists
```

---

# Why DevOps Engineers Use File Handling

Common examples:

### Read Configuration Files

```text
config.txt
```

Example:

```text
server=prod
port=8080
```

---

### Read Log Files

```text
app.log
```

Example:

```text
ERROR
WARNING
INFO
```

---

### Store Reports

```text
report.txt
```

Store automation results.

---

### Store API Responses

Later in Day-02:

```python
response.json()
```

can be saved into:

```text
output.json
```

using file handling.

---

# File Handling Lifecycle

Every file operation follows:

```text
OPEN
 ↓
READ / WRITE / APPEND
 ↓
CLOSE
```

Example:

```python
file = open("demo.txt")
print(file.read())
file.close()
```

---

# Opening a File

Code:

```python
file = open("demo.txt")
```

Equivalent to:

```python
file = open("demo.txt","r")
```

Default mode is:

```text
Read Mode
```

---

# Reading a File

Suppose:

```text
demo.txt

hello dosto
```

Code:

```python
print(file.read())
```

Output:

```text
hello dosto
```

---

# Read Mode ("r")

Example:

```python
file = open("demo.txt","r")
```

Allowed:

✅ Read

❌ Write

---

## Common Error

Wrong:

```python
file = open("demo.txt")
file.write("Hello")
```

Error:

```text
io.UnsupportedOperation: not writable
```

Reason:

```text
File opened in Read Mode.
```

---

# Write Mode ("w")

Example:

```python
file = open("demo.txt","w")
```

Purpose:

```text
Write new content
```

Allowed:

✅ Write

❌ Existing content preserved

````

---

## Important Behavior

Before:

```text
demo.txt

Junoon
````

Code:

```python
file = open("demo.txt","w")
```

Immediately:

```text
demo.txt

(empty)
```

Python clears existing content.

---

Then:

```python
file.write("Testing file handling in Python")
```

File becomes:

```text
Testing file handling in Python
```

---

## Memory Trick

```text
w = Wipe + Write
```

Meaning:

```text
Delete old content
Write new content
```

---

# Append Mode ("a")

Example:

```python
file = open("config.txt","a")
```

Purpose:

```text
Keep old content
Add new content at end
```

---

Before:

```text
server=prod
port=8080
```

Code:

```python
file.write("\napp.log.level=DEBUG")
```

After:

```text
server=prod
port=8080
app.log.level=DEBUG
```

---

# Running Again

Append mode adds again.

After second run:

```text
server=prod
port=8080
app.log.level=DEBUG
app.log.level=DEBUG
```

---

After third run:

```text
server=prod
port=8080
app.log.level=DEBUG
app.log.level=DEBUG
app.log.level=DEBUG
```

Append never removes data.

---

## Memory Trick

```text
a = Add
```

Meaning:

```text
Keep old content
Add new content
```

---

# Understanding Your Observation

You observed:

Run 1:

```text
Junoon
```

Run 2:

```text
Testing file handling in Python
```

You thought there might be lag.

Actually:

```text
No lag.
```

It happened because of execution order.

---

## Run 1

Before:

```text
demo.txt

Junoon
```

Python executes:

```python
print(file.read())
```

Output:

```text
Junoon
```

Then:

```python
open("demo.txt","w")
```

overwrites file:

```text
Testing file handling in Python
```

Program ends.

---

## Run 2

Now file already contains:

```text
Testing file handling in Python
```

Python reads it.

Output:

```text
Testing file handling in Python
```

---

# Important Lesson

Python executes code sequentially:

```text
Line 1
↓
Line 2
↓
Line 3
↓
Line 4
```

Order matters.

---

# close()

Example:

```python
file.close()
```

Purpose:

```text
Release file resources
Tell operating system work is finished
```

Always close files after use.

---

# Better Modern Method

Instead of:

```python
file = open("demo.txt")
print(file.read())
file.close()
```

Use:

```python
with open("demo.txt") as file:
    print(file.read())
```

Python automatically closes the file.

We'll learn this later.

---

# Internal Flow

```text
demo.txt
     │
     ▼
open()
     │
     ▼
File Object
     │
     ├── read()
     ├── write()
     ├── close()
```

---

# Real DevOps Use Cases

### Read Configuration

```python
open("config.txt")
```

Read:

```text
server=prod
port=8080
```

---

### Read Logs

```python
open("app.log")
```

Analyze application failures.

---

### Store Reports

```python
open("report.txt","w")
```

Write automation reports.

---

### Store API Data

```python
open("output.json","w")
```

Save API responses.

---



## Discovery 1

```python
open("demo.txt")
```

means:

```python
open("demo.txt","r")
```

Default mode is Read Mode.

---

## Discovery 2

This causes error:

```python
file = open("demo.txt")
file.write("Hello")
```

Error:

```text
io.UnsupportedOperation: not writable
```

Reason:

```text
File opened in Read Mode.
```

---

## Discovery 3

Write Mode:

```python
open("demo.txt","w")
```

deletes existing content.

---

## Discovery 4

Memory Trick:

```text
w = Wipe + Write
```

---

## Discovery 5

Append Mode:

```python
open("config.txt","a")
```

preserves old content.

---

## Discovery 6

Memory Trick:

```text
a = Add
```

---

## Discovery 7

Every time program runs:

```python
file.write("\napp.log.level=DEBUG")
```

in Append Mode,

a new line gets added.

This is why multiple DEBUG lines appeared.

---

## Discovery 8

There was no lag.

Python simply executed:

```text
Read First
Write Later
```

So old content was displayed during that run.

---

## Discovery 9

On the next run:

```text
Previously written content
becomes visible.
```

---

## Discovery 10

Order of execution matters.

Python runs code:

```text
Top
↓
Bottom
```

one line at a time.

---

## Discovery 11

Always close files:

```python
file.close()
```

after use.

---



## File Handling Lifecycle

```text
OPEN
 ↓
READ / WRITE / APPEND
 ↓
CLOSE
```

---

## Open File

```python
file = open("demo.txt")
```

Default:

```python
open("demo.txt","r")
```

---

## Read File

```python
file.read()
```

---

## Write File

```python
file = open("demo.txt","w")
file.write("Hello")
```

Effect:

```text
Deletes old content
Writes new content
```

Memory Trick:

```text
w = Wipe + Write
```

---

## Append File

```python
file = open("demo.txt","a")
file.write("Hello")
```

Effect:

```text
Keeps old content
Adds new content
```

Memory Trick:

```text
a = Add
```

---

## Close File

```python
file.close()
```

---

## Common Error

```python
file = open("demo.txt")
file.write("Hello")
```

Error:

```text
io.UnsupportedOperation: not writable
```

Reason:

```text
Read Mode does not allow writing.
```

---

## Common Modes

| Mode | Meaning | Existing Data |
| ---- | ------- | ------------- |
| r    | Read    | Preserved     |
| w    | Write   | Deleted       |
| a    | Append  | Preserved     |

---

##  Questions

# Python File Handling  Questions & Answers

---

# 1. What is File Handling in Python?

File Handling is the process of:

* Creating files
* Reading files
* Writing files
* Updating files
* Appending data to files

Python provides built-in functions to work with files stored on disk.

Example:

```python
file = open("demo.txt")
print(file.read())
file.close()
```

###  Answer

File Handling is used to read, write, update, and manage data stored in files.

---

# 2. What is the purpose of open()?

`open()` is used to open a file before performing operations on it.

Syntax:

```python
open(filename, mode)
```

Example:

```python
file = open("demo.txt", "r")
```

Here:

```python
demo.txt → filename
r        → mode
```

###  Answer

`open()` establishes a connection between the Python program and the file so that data can be read or written.

---

# 3. What is the default file mode?

If no mode is specified, Python uses:

```python
r
```

Example:

```python
file = open("demo.txt")
```

Same as:

```python
file = open("demo.txt", "r")
```

###  Answer

The default file mode is `r` (Read Mode).

---

# 4. Difference between r, w, and a?

| Mode | Meaning | File Must Exist? | Old Content |
| ---- | ------- | ---------------- | ----------- |
| r    | Read    | Yes              | Preserved   |
| w    | Write   | No               | Deleted     |
| a    | Append  | No               | Preserved   |

---

## Read Mode

```python
file = open("demo.txt", "r")
```

Can read only.

---

## Write Mode

```python
file = open("demo.txt", "w")
```

Deletes old content and writes new content.

---

## Append Mode

```python
file = open("demo.txt", "a")
```

Adds new data at the end.

###  Answer

* `r` = Read existing data
* `w` = Overwrite existing data
* `a` = Add data without deleting existing content

---

# 5. Why did file.write() fail in Read Mode?

Example:

```python
file = open("demo.txt", "r")

file.write("Hello")
```

Output:

```python
UnsupportedOperation: not writable
```

Reason:

Read Mode only allows reading.

###  Answer

`file.write()` fails in Read Mode because the file is opened as read-only.

---

# 6. What happens when a file is opened in Write Mode?

Example:

```python
file = open("demo.txt", "w")
```

Python immediately clears the file contents.

Before:

```text
Hello
World
```

After opening with `"w"`:

```text
(empty)
```

Then new content is written.

###  Answer

Write Mode truncates (erases) existing content before writing new data.

---

# 7. What happens when a file is opened in Append Mode?

Example:

```python
file = open("demo.txt", "a")

file.write("DEBUG\n")
```

Existing content remains.

Before:

```text
Server Started
```

After:

```text
Server Started
DEBUG
```

###  Answer

Append Mode preserves existing content and adds new data to the end of the file.

---

# 8. Why did multiple DEBUG lines appear?

Suppose:

```python
file = open("demo.txt", "a")

file.write("DEBUG\n")
```

Every run adds another line.

Run 1:

```text
DEBUG
```

Run 2:

```text
DEBUG
DEBUG
```

Run 3:

```text
DEBUG
DEBUG
DEBUG
```

Because Append Mode never removes existing content.

###  Answer

Multiple DEBUG lines appeared because Append Mode adds data to the end every time the program runs.

---

# 9. Why was old content printed during the first run?

Example:

```python
file = open("demo.txt", "r")

print(file.read())
```

If the file already contains:

```text
Hello
World
DEBUG
```

Output:

```text
Hello
World
DEBUG
```

Python reads whatever already exists.

###  Answer

Old content was printed because Read Mode reads the file's current contents, including data written during previous runs.

---

# 10. Why is file.close() important?

Example:

```python
file = open("demo.txt", "w")

file.write("Hello")

file.close()
```

Benefits:

* Saves pending changes
* Releases system resources
* Prevents file corruption
* Prevents file locks

###  Answer

`file.close()` ensures data is properly saved and releases the file resource.

---

# 11. What is the modern alternative to open() + close()?

Use:

```python
with open("demo.txt", "r") as file:
    print(file.read())
```

No need:

```python
file.close()
```

Python automatically closes the file.

---

### Why use with?

* Cleaner code
* Safer
* Automatically closes file
* Preferred in production code

###  Answer

The modern approach is the `with open()` statement, which automatically closes the file after use.

---

# 12. How is file handling used in DevOps?

File handling is everywhere in DevOps.

### Log Processing

```python
with open("app.log", "r") as file:
    logs = file.read()
```

---

### Configuration Files

```python
config.yaml
```

Read configuration values.

---

### Server Inventories

```text
server1
server2
server3
```

Read server lists.

---

### Automation Reports

Store automation results.

```text
PASS
FAIL
PASS
```

---

### CI/CD Pipelines

Read and generate reports.

###  Answer

File handling is used in DevOps for logs, configuration files, inventory files, reports, backups, and automation outputs.

---

# 13. How can API responses be stored in files?

Example:

```python
response = """
{
    "name":"Shubham",
    "city":"Mumbai"
}
"""

with open("response.json", "w") as file:
    file.write(response)
```

Stored as:

```json
{
    "name":"Shubham",
    "city":"Mumbai"
}
```

---

### Real-world Usage

* Save API responses
* Archive logs
* Store reports
* Debug failures
* Compare old vs new responses

###  Answer

API responses can be written to text, JSON, CSV, or log files using `write()` and file handling operations.

---

# Quick Memory Summary

```text
r = Read
w = Write (Overwrite)
a = Append (Add at End)

open()  -> Open File
read()  -> Read Content
write() -> Write Content
close() -> Close File

with open() -> Preferred Modern Approach
```

---

# Common  Errors

### Key Error #1

```python
file.write()
```

in

```python
open("demo.txt", "r")
```

Result:

```text
UnsupportedOperation: not writable
```

---

### Key Error #2

```python
open("demo.txt", "w")
```

accidentally deleting content.

---

### Key Error #3

```python
open("demo.txt", "a")
```

creating repeated log entries.

---

# Quick Revision (1-Minute)

| Question                 | Short Answer                        |
| ------------------------ | ----------------------------------- |
| What is File Handling?   | Reading/writing files               |
| Purpose of open()?       | Open a file                         |
| Default mode?            | `r`                                 |
| `r` mode?                | Read only                           |
| `w` mode?                | Overwrite file                      |
| `a` mode?                | Append data                         |
| Why write() failed?      | File opened in Read Mode            |
| Why DEBUG repeated?      | Append Mode adds every run          |
| Why old content printed? | File already contained data         |
| Why close()?             | Save changes and release resources  |
| Modern alternative?      | `with open()`                       |
| DevOps usage?            | Logs, configs, reports, inventories |
| API responses?           | Store in JSON/TXT/CSV files         |

###  One-Liner

> "File Handling in Python allows programs to read, write, append, and manage files. It is heavily used in DevOps for logs, configuration files, inventory management, reports, and storing API responses, with `with open()` being the preferred modern approach."


---

### Quick Memory Summary

```text
r = Read

w = Wipe + Write

a = Add

close() = Finish Work
```

This file taught three core concepts you'll use repeatedly in DevOps automation:

```text
Read Files
Write Files
Append Files
```

These will later be combined with APIs, logs, configuration files, and automation reports.

---

# 05_api_basics_all_todos.py

---



## Purpose

Learn how to:

* Call an API using `requests`
* Receive JSON data
* Understand the difference between:

  * Dictionary (`dict`)
  * List of Dictionaries (`list`)
* Access API data
* Loop through API responses

---

## Code

```python
import requests

response = requests.get(
    "https://jsonplaceholder.typicode.com/todos"
)

data = response.json()
```

---

## What is requests.get()?

Sends a GET request to the server.

```python
response = requests.get(URL)
```

Think:

```text
Python
  ↓
Internet
  ↓
Server
  ↓
Data Returned
```

---

## What is response?

```python
print(response)
```

Output:

```text
<Response [200]>
```

Meaning:

```text
200 = Success
```

---

## What is response.json()?

Converts JSON into Python objects.

```python
data = response.json()
```

---

## What is returned?

URL:

```text
https://jsonplaceholder.typicode.com/todos
```

returns ALL todos.

Example:

```python
[
    {
        "userId": 1,
        "id": 1,
        "title": "delectus aut autem",
        "completed": False
    },

    {
        "userId": 1,
        "id": 2,
        "title": "quis ut nam facilis",
        "completed": False
    }
]
```

---

## Type Check

```python
print(type(data))
```

Output:

```python
<class 'list'>
```

Meaning:

```text
List of Todos
```

---

## Length

```python
print(len(data))
```

Output:

```text
200
```

Meaning:

```text
Total Todos = 200
```

---

## Access First Todo

```python
print(data[0])
```

Output:

```python
{
    'userId': 1,
    'id': 1,
    'title': 'delectus aut autem',
    'completed': False
}
```

---

## Access Fourth Todo

```python
print(data[3])
```

Output:

```python
{
    'userId': 1,
    'id': 4,
    'title': 'et porro tempora',
    'completed': True
}
```

---

## Important

```python
data[0]
```

means:

```text
First Todo
```

NOT

```text
Todo ID 0
```

Because list indexing starts from:

```text
0
1
2
3
```

---

## Print Specific Value

```python
print(data[0]["title"])
```

Output:

```text
delectus aut autem
```

Explanation:

```text
data[0]
    ↓
First Dictionary
    ↓
"title"
```

---

## Loop Through All Todos

```python
for todo in data:
```

Each iteration gets:

```python
{
    "userId":1,
    "id":1,
    ...
}
```

Then:

```python
for key,value in todo.items():
```

prints:

```text
userId 1
id 1
title delectus aut autem
completed False
```

---

## Why Print Dashes?

```python
print("----------")
```

Separates todos.

Without dashes:

```text
userId 1
id 1
userId 1
id 2
userId 1
id 3
```

Confusing.

---

With dashes:

```text
----------
userId 1
id 1

----------
userId 1
id 2
```

Much easier to read.

---



### Q1. What does requests.get() do?

**Answer:**

Sends an HTTP GET request to a server and receives data.

---

### Q2. What does response.json() do?

**Answer:**

Converts JSON response into Python objects such as dictionaries and lists.

---

### Q3. What type is returned by `/todos`?

**Answer:**

```python
<class 'list'>
```

A list of dictionaries.

---

### Q4. What type is returned by `/todos/3`?

**Answer:**

```python
<class 'dict'>
```

A single dictionary.

---

### Q5. Difference between `/todos` and `/todos/3`?

**Answer:**

```text
/todos
    ↓
List of Dictionaries

/todos/3
    ↓
Single Dictionary
```

---

### Q6. How do you access the first todo?

**Answer:**

```python
data[0]
```

---

### Q7. How do you access the title of the first todo?

**Answer:**

```python
data[0]["title"]
```

---

### Q8. Why use todo.items()?

**Answer:**

To iterate through all key-value pairs in a dictionary.

---

### Q9. What does len(data) return?

**Answer:**

Total number of todos in the list.

---

### Q10. What is JSON?

**Answer:**

JSON (JavaScript Object Notation) is a lightweight data format commonly used by APIs.

---



### Mistake 1

Used:

```python
print(data()[3])
```

Error:

```text
TypeError: 'list' object is not callable
```

Reason:

```text
() = Function Call
[] = List Index
```

Correct:

```python
print(data[3])
```

---

### Mistake 2

Thought:

```text
/todos/3
```

means:

```text
userId = 3
```

Reality:

```text
Todo ID = 3
```

Output:

```python
{
    "userId":1,
    "id":3
}
```

---

### Mistake 3

Confused Dictionary and List.

Learned:

```text
dict
    ↓
items()

list
    ↓
for item in list
```

---

### Mistake 4

Expected to see entire output in VS Code terminal.

Reality:

```text
200 todos
×
4 fields
=
800+ lines
```

Solution:

```python
if count == 5:
    break
```

Print only a few records.

---



```text
API Call
    ↓
requests.get(URL)

Response
    ↓
response.json()

/todos
    ↓
list

/todos/3
    ↓
dict

List Access
    ↓
data[0]

Dictionary Access
    ↓
data["title"]

Loop List
    ↓
for todo in data

Loop Dictionary
    ↓
for key,value in todo.items()

len(data)
    ↓
Total Todos

data[0]["title"]
    ↓
First Todo Title

[] = Index

() = Function Call
```

---


```text
05_api_basics_all_todos.py

requests.get()
→ Call API

response.json()
→ Convert JSON to Python

/todos
→ List of Dictionaries

/todos/3
→ Dictionary

type(data)
→ list

len(data)
→ Total Todos

data[0]
→ First Todo

data[0]["title"]
→ First Todo Title

for todo in data
→ Loop List

for key,value in todo.items()
→ Loop Dictionary

[] → Index

() → Function Call

Common Mistake:
data()[3] ❌

data[3] ✅
```

This is the format I'd preserve in your notes repository for **05_api_basics_all_todos.py**.
