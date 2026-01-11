# Python Lambda Functions — Complete Practical Notes

## What is a Lambda Function?

A **lambda function** in Python is a **small, anonymous function** defined using the `lambda` keyword.

* It **does not have a name** (unless assigned to a variable)
* It can take **any number of arguments**
* It can contain **only one expression**
* The expression is **evaluated and returned automatically**

Basic syntax:

```python
lambda arguments: expression
```

Example:

```python
x = lambda a: a + 10
print(x(5))   # 15
```

This is equivalent to:

```python
def x(a):
    return a + 10
```

---

## Core Characteristics of Lambda Functions

* No `return` keyword (implicit return)
* No statements (only expressions)
* No assignments inside
* No loops (`for`, `while`)
* No `try/except`
* No docstrings
* Best suited for **short-lived logic**

---

## Lambdas with Multiple Arguments

```python
add = lambda a, b: a + b
print(add(5, 6))    # 11
```

```python
calc = lambda a, b, c: a + b + c
print(calc(5, 6, 2))  # 13
```

The behavior depends **entirely on argument types**:

```python
add("3", "4")   # '34'
add(3, 4)       # 7
add("3", 4)     # TypeError
```

---

## Lambdas with No Arguments

```python
f = lambda: "Hello"
print(f())   # Hello
```

Equivalent to:

```python
def f():
    return "Hello"
```

Useful for:

* Lazy execution
* Callbacks
* Configuration defaults

---

## Lambda with Conditional Logic

Lambda supports **ternary expressions**.

### Simple condition

```python
check_even = lambda x: "Even" if x % 2 == 0 else "Odd"
```

### Multiple conditions

```python
sign = lambda x: "Positive" if x > 0 else "Negative" if x < 0 else "Zero"
```

---

## Lambda Returning Multiple Values

Lambda can return **tuples**, which allows multiple results:

```python
calc = lambda x, y: (x + y, x * y)
print(calc(3, 4))  # (7, 12)
```

This is very common in data processing pipelines.

---

## Lambdas as Closures (Very Important)

Lambdas **capture variables from outer scope**.

```python
def multiplier(n):
    return lambda x: x * n

double = multiplier(2)
triple = multiplier(3)

print(double(10))   # 20
print(triple(10))   # 30
```

This is heavily used in:

* Configuration
* Factories
* Functional pipelines

---

## Lambda with `map()`

### What `map()` does

* Applies a function to **each element**
* Returns an iterator (lazy)

### Basic example

```python
nums = [1, 2, 3, 4]
result = map(lambda x: x * 2, nums)
print(list(result))   # [2, 4, 6, 8]
```

### Real-world examples

#### Convert strings to integers

```python
data = ["1", "2", "3"]
ints = list(map(int, data))
```

#### Normalize values

```python
values = [10, 20, 30]
normalized = list(map(lambda x: x / max(values), values))
```

#### Apply transformation to dictionaries

```python
prices = [{"price": 100}, {"price": 200}]
updated = list(map(lambda d: {**d, "price": d["price"] * 1.1}, prices))
```

---

## Lambda with `filter()`

### What `filter()` does

* Keeps elements where function returns `True`

### Basic example

```python
nums = [1, 2, 3, 4, 5, 6]
evens = list(filter(lambda x: x % 2 == 0, nums))
```

### Real-world examples

#### Remove empty strings

```python
data = ["apple", "", None, "banana"]
clean = list(filter(lambda x: x, data))
```

#### Validate input values

```python
ages = [12, 18, 25, 8]
valid = list(filter(lambda x: x >= 18, ages))
```

#### Filter dictionaries

```python
users = [
    {"name": "A", "active": True},
    {"name": "B", "active": False}
]

active_users = list(filter(lambda u: u["active"], users))
```

---

## Lambda with `sorted()`

### Sorting by key

```python
students = [("Emil", 25), ("Tobias", 22), ("Linus", 28)]
sorted_students = sorted(students, key=lambda x: x[1])
```

### Real-world sorting patterns

#### Sort strings by length

```python
words = ["apple", "pie", "banana"]
sorted(words, key=len)
```

#### Sort dictionaries by field

```python
products = [
    {"name": "A", "price": 30},
    {"name": "B", "price": 10}
]

sorted(products, key=lambda p: p["price"])
```

#### Sort case-insensitively

```python
names = ["alice", "Bob", "charlie"]
sorted(names, key=lambda x: x.lower())
```

---

## Lambda with `reduce()`

`reduce()` applies a function **cumulatively**.

```python
from functools import reduce

nums = [1, 2, 3, 4]
product = reduce(lambda x, y: x * y, nums)
```

Real-world:

* Aggregations
* Checksums
* Folding data

---

## Lambda Inside List Comprehension (Closure Edge Case)

⚠️ **Common pitfall**

```python
funcs = [lambda: x * 10 for x in range(1, 5)]
```

All lambdas reference the **same `x`**, final value = 4.

### Fix using default argument

```python
funcs = [lambda x=x: x * 10 for x in range(1, 5)]
```

This edge case appears often in:

* Loops
* Callbacks
* Async code

---

## When NOT to Use Lambda (Very Important)

Avoid lambda when:

* Logic is complex
* Multiple steps are needed
* Debugging is required
* Reuse is expected
* Documentation is needed

Bad:

```python
lambda x: (x + 2) * (x - 3) / x if x != 0 else None
```

Better:

```python
def compute(x):
    if x == 0:
        return None
    return (x + 2) * (x - 3) / x
```

---

## Lambda vs `def`

| Feature    | lambda    | def      |
| ---------- | --------- | -------- |
| Name       | Anonymous | Named    |
| Lines      | Single    | Multiple |
| Statements | ❌         | ✅        |
| Docstring  | ❌         | ✅        |
| Debugging  | Hard      | Easy     |
| Use case   | Temporary | Reusable |

---

## Common Real-World Lambda Patterns

### Default sorting key

```python
key=lambda x: x.get("timestamp", 0)
```

### Safe access

```python
lambda d: d["price"] if "price" in d else 0
```

### Validation

```python
lambda x: isinstance(x, int) and x > 0
```

### Feature engineering (ML/Data)

```python
lambda row: row["salary"] / row["experience"]
```

---

## Mental Model (Very Important)

> A lambda is **not special syntax magic**.
> It is just a **function without a name**, evaluated immediately where needed.

If you can mentally replace a lambda with a small `def`, you understand it.

---

## Final Takeaway

* Lambda functions shine in **functional pipelines**
* They are ideal for **map, filter, sorted**
* They should be **short, readable, and disposable**
* Overusing lambdas hurts maintainability

