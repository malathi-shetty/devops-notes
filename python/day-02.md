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

info = {
    "name": "Shubham",
    "city": "Pune"
}

Access Value:

info["city"]

Output:
Pune

Safe Access:

info.get("city")

Difference:

info["country"]
→ KeyError

info.get("country")
→ None

info.get("country", "Not Found")
→ Not Found

Add/Update:

info.update({"channel":"TrainWithShubham"})

Loop Through Dictionary:

for key, value in info.items():
    print(key, value)

Useful Methods:

get()
items()
keys()
values()
update()

Why Important?

Most API JSON responses become Python Dictionaries.

---


Mistakes I Made

1. IndentationError

Wrong:

for key,value in info.items():

info["country"]

Reason:
Python expected code inside the loop.

Correct:

for key,value in info.items():
    print(key,value)

------------------------------------------------

2. I thought info.get("country")
would print output automatically.

Wrong understanding:
Calling function = display output

Actual:
Need print()

print(info.get("country"))

---

Dictionary

Stores Key-Value pairs.

Methods:
get()
items()
update()
keys()
values()

Difference:

info["country"]
→ KeyError

info.get("country")
→ None

API JSON responses are converted into Dictionaries.

---

