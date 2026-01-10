
# Python `collections` Module – Complete Notes

The `collections` module in Python provides **specialized container data types** that extend and improve upon Python’s built-in containers such as `list`, `dict`, `tuple`, and `set`.

These containers are not meant to replace built-in types, but to **solve specific problems more efficiently and with cleaner code**.

---

## Why Do We Need the `collections` Module?

Python’s built-in data structures are powerful, but certain use cases appear repeatedly in real-world programs:

* Counting frequencies of elements
* Maintaining insertion order with extra control
* Avoiding repeated key existence checks
* Implementing queues efficiently
* Representing structured records with readable fields
* Extending built-in containers with custom behavior

The `collections` module provides **ready-made, optimized solutions** for these problems, saving both time and code complexity.

Key benefits:

* Cleaner and more readable code
* Better performance for specific use cases
* Reduced boilerplate logic
* Purpose-built data structures

---

## Counter

### What Is a Counter?

A `Counter` is a **specialized dictionary subclass** used to **count hashable objects**.

Instead of manually tracking counts using a dictionary, `Counter` automates this process.

Conceptually:

* Keys → elements
* Values → frequency of those elements

It is equivalent to a **multiset** or **bag** in other programming languages.

---

### How Counter Works

When you pass an iterable to `Counter`, Python:

1. Iterates over each element
2. Uses the element as a key
3. Increments its count automatically

Internally, it behaves like a dictionary:

```python
{'element': count}
```

---

### Ways to Initialize a Counter

#### From an Iterable

```python
from collections import Counter

Counter(['B','B','A','B','C','A','B','B','A','C'])
```

Output:

```python
Counter({'B': 5, 'A': 3, 'C': 2})
```

Each occurrence is counted automatically.

---

#### From a Dictionary

```python
Counter({'A': 3, 'B': 5, 'C': 2})
```

Used when counts are already known.

---

#### Using Keyword Arguments

```python
Counter(A=3, B=5, C=2)
```

Mostly used for readability or small datasets.

---

### Important Characteristics of Counter

* Missing keys return `0` instead of raising `KeyError`
* Counts can be incremented or decremented
* Zero or negative counts are allowed
* Order is not guaranteed (though insertion order may appear preserved)

---

### When to Use Counter

* Frequency counting
* Word counts
* Character counts
* Voting systems
* Inventory tracking

---

## OrderedDict

### What Is an OrderedDict?

An `OrderedDict` is a dictionary that **preserves insertion order explicitly** and provides **additional order-related operations**.

Although regular dictionaries preserve insertion order from Python 3.7+, `OrderedDict` still offers **extra functionality** that normal dictionaries do not.

---

### Key Difference from Normal `dict`

In a normal dictionary:

* Order is preserved
* But reinserting a key does NOT move it

In an `OrderedDict`:

* Reinserting a key moves it to the end
* Keys can be reordered intentionally

---

### Example: Normal Dict vs OrderedDict

```python
d = {}
d['a'] = 1
d['b'] = 2
d['c'] = 3
d['d'] = 4
```

```python
from collections import OrderedDict
od = OrderedDict()
od['a'] = 1
od['b'] = 2
od['c'] = 3
od['d'] = 4
```

Both preserve order initially.

---

### Reinsertion Behavior (Important)

```python
od.pop('a')
od['a'] = 1
```

Result:

```python
b → c → d → a
```

This behavior is **unique to OrderedDict**.

---

### When to Use OrderedDict

* LRU cache implementations
* Order-sensitive data processing
* Reordering elements dynamically
* Algorithms where order manipulation matters

---

## defaultdict

### What Is a defaultdict?

A `defaultdict` is a dictionary subclass that **automatically provides a default value** for missing keys.

It avoids the need to:

```python
if key in dict:
    dict[key] += 1
else:
    dict[key] = 1
```

---

### How defaultdict Works

A `defaultdict` takes a **default factory function**.

When a missing key is accessed:

* The factory function is called
* A default value is created
* The key is inserted automatically

---

### Example: Counting Frequencies

```python
from collections import defaultdict

d = defaultdict(int)
L = [1, 2, 3, 4, 2, 4, 1, 2]

for i in L:
    d[i] += 1
```

Why this works:

* `int()` returns `0`
* Missing keys start at zero automatically

---

### Example: Values as Lists

```python
d = defaultdict(list)

for i in range(5):
    d[i].append(i)
```

No need to initialize empty lists manually.

---

### When to Use defaultdict

* Grouping data
* Frequency counting
* Building adjacency lists
* Accumulating values

---

## ChainMap

### What Is a ChainMap?

A `ChainMap` groups **multiple dictionaries** into a **single logical view**.

Instead of merging dictionaries, it **searches them sequentially**.

---

### How ChainMap Resolves Keys

When you access a key:

1. It checks the first dictionary
2. If not found, checks the second
3. Continues until found

This avoids copying data.

---

### Example

```python
from collections import ChainMap

d1 = {'a': 1, 'b': 2}
d2 = {'c': 3, 'd': 4}
d3 = {'e': 5, 'f': 6}

c = ChainMap(d1, d2, d3)
```

---

### Accessing Data

```python
c['a']      # 1
c.keys()
c.values()
```

---

### Adding a New Dictionary

```python
c.new_child({'x': 100})
```

New dictionary becomes the **first lookup layer**.

---

### When to Use ChainMap

* Configuration layering
* Variable scopes
* Environment overrides
* Combining multiple mappings without copying

---

## namedtuple

### What Is a namedtuple?

A `namedtuple` is an **immutable tuple with named fields**.

It provides:

* Tuple efficiency
* Dictionary-like readability

---

### Why namedtuple Exists

Normal tuples use numeric indexes:

```python
student[0]
```

namedtuple allows:

```python
student.name
```

---

### Example

```python
from collections import namedtuple

Student = namedtuple('Student', ['name', 'age', 'DOB'])
S = Student('Nandini', '19', '2541997')
```

Access:

```python
S[1]      # 19
S.name    # Nandini
```

---

### Conversion Utilities

#### `_make()`

Creates a namedtuple from an iterable.

```python
Student._make(['Manjeet', '19', '411997'])
```

---

#### `_asdict()`

Converts namedtuple to dictionary.

```python
S._asdict()
```

---

### When to Use namedtuple

* Lightweight data records
* Immutable structured data
* Better readability than tuples
* Lower overhead than classes

---

## deque (Doubly Ended Queue)

### What Is a deque?

A `deque` is a **double-ended queue** optimized for fast insertion and removal from both ends.

---

### Why deque Is Better Than list for Queues

| Operation | list   | deque  |
| --------- | ------ | ------ |
| append    | O(1)   | O(1)   |
| pop       | O(1)   | O(1)   |
| pop(0)    | ❌ O(n) | ✅ O(1) |

---

### Example

```python
from collections import deque

queue = deque([1, 2, 3])
queue.append(4)
queue.appendleft(0)
```

---

### Removing Elements

```python
queue.pop()
queue.popleft()
```

---

### When to Use deque

* Queues
* Stacks
* Sliding window problems
* BFS/DFS algorithms

---

## UserDict

### What Is UserDict?

`UserDict` is a **wrapper around dictionary objects** designed for **safe subclassing**.

Directly subclassing `dict` can cause unexpected behavior. `UserDict` avoids this.

---

### Example: Preventing Deletion

```python
from collections import UserDict

class MyDict(UserDict):
    def pop(self, s=None):
        raise RuntimeError("Deletion not allowed")
```

---

### When to Use UserDict

* Custom dictionary behavior
* Validation logic
* Access control
* Safer inheritance than `dict`

---

## UserList

### What Is UserList?

`UserList` is a **wrapper around list objects** that allows safe customization.

---

### Example: Disabling Removal

```python
from collections import UserList

class MyList(UserList):
    def remove(self, s=None):
        raise RuntimeError("Deletion not allowed")
```

---

### When to Use UserList

* Enforcing constraints
* Custom list behavior
* Logging or auditing list operations

---

## UserString

### What Is UserString?

`UserString` is a wrapper around string objects.

Strings are immutable, so `UserString` allows you to **simulate mutable strings**.

---

### Example

```python
from collections import UserString

class MyString(UserString):
    def append(self, s):
        self.data += s
```

---

### When to Use UserString

* Custom string processing
* Mutable string-like behavior
* String transformation utilities

---

## Final Summary (Conceptual, Not TL;DR)

The `collections` module exists to **solve real programming problems** that repeatedly arise when using basic containers. Each structure is optimized for a specific pattern of usage and helps write cleaner, faster, and more expressive Python code.

Understanding when and why to use these containers is a strong indicator of **intermediate-to-advanced Python proficiency**.

