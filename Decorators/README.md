

# Python Decorators

---

## 1. What Does “Decorators Let You Add Extra Behavior” Really Mean?

When we say:

> *Decorators let you add extra behavior to a function without changing the function’s code*

we mean the following very literally:

* You already have a function that **works correctly**
* You do **not want to edit its internal logic**
* You want to **wrap extra logic around it**

  * before it runs
  * after it runs
  * or conditionally instead of it

A decorator **does not modify the function body**.
It **replaces the function reference** with another callable that *calls the original function internally*.

---

## 2. Why Decorators Are Possible in Python

Decorators exist only because **functions in Python are first-class objects**.

This means:

* Functions can be stored in variables
* Functions can be passed as arguments
* Functions can be returned from other functions

Example:

```python
def greet():
    return "Hello"

x = greet
print(x())
```

Here:

* `greet` is not just code
* It is an **object**
* `x` now points to the same function object as `greet`

This ability is the foundation of decorators.

---

## 3. The Core Definition of a Decorator

A decorator is:

> **A function that takes another function as input and returns a new function**

So conceptually:

```python
def decorator(original_function):
    def new_function():
        # extra behavior
        original_function()
    return new_function
```

After decoration:

* The **original function name** now refers to `new_function`
* The original function still exists, but is accessed indirectly

---

## 4. First Basic Decorator Example (Uppercasing Output)

### Code

```python
def changecase(func):
    def myinner():
        return func().upper()
    return myinner
```

### Explanation (Line by Line)

#### Line 1

```python
def changecase(func):
```

* `changecase` is the **decorator**
* `func` is the function being decorated
* `func` is passed **as an object**, not executed

#### Line 2

```python
def myinner():
```

* `myinner` is the **wrapper function**
* This function will **replace the original function**
* It controls *how* and *when* the original function runs

#### Line 3

```python
return func().upper()
```

Step-by-step:

1. `func()` → calls the original function
2. The original function returns a string
3. `.upper()` converts the string to uppercase
4. The modified value is returned

#### Line 4

```python
return myinner
```

* The decorator returns the wrapper function
* **Not executed**, only returned

---

## 5. Applying the Decorator Using `@`

### Code

```python
@changecase
def myfunction():
    return "Hello Sally"
```

This line:

```python
@changecase
```

is **exactly equivalent to**:

```python
myfunction = changecase(myfunction)
```

### What Actually Happens Internally

1. Python creates `myfunction`
2. Python passes `myfunction` into `changecase`
3. `changecase` returns `myinner`
4. `myfunction` now points to `myinner`

So when you call:

```python
print(myfunction())
```

You are actually calling `myinner()`, not the original function.

---

## 6. Multiple Functions Using the Same Decorator

### Code

```python
@changecase
def myfunction():
    return "Hello Sally"

@changecase
def otherfunction():
    return "I am speed!"
```

### Explanation

* Each function is passed **separately** to the decorator
* Each function gets its **own wrapper**
* The decorator logic is **reused without duplication**

This is one of the main reasons decorators exist: **DRY (Don’t Repeat Yourself)**.

---

## 7. Decorators and Function Arguments

### Problem

This decorator breaks:

```python
def changecase(func):
    def myinner():
        return func().upper()
    return myinner
```

If the decorated function requires arguments, calling `func()` without arguments causes a `TypeError`.

---

### Correct Handling of Arguments

### Code

```python
def changecase(func):
    def myinner(x):
        return func(x).upper()
    return myinner
```

### Explanation

* `x` is received by `myinner`
* `x` is passed to `func(x)`
* This works only if the function has **exactly one argument**

---

## 8. Generalizing With `*args` and `**kwargs`

### Why This Is Necessary

You often **do not control**:

* how many arguments a function takes
* what types of arguments it takes

So decorators must be **flexible**.

---

### Code

```python
def changecase(func):
    def myinner(*args, **kwargs):
        return func(*args, **kwargs).upper()
    return myinner
```

### Explanation

* `*args` captures all positional arguments
* `**kwargs` captures all keyword arguments
* They are forwarded exactly as received
* This makes the decorator **universal**

---

## 9. Decorators That Take Their Own Arguments (Decorator Factories)

### Code

```python
def changecase(n):
    def changecase(func):
        def myinner():
            if n == 1:
                a = func().lower()
            else:
                a = func().upper()
            return a
        return myinner
    return changecase
```

---

### Step-by-Step Execution

#### Step 1

```python
@changecase(1)
```

* `changecase(1)` is executed immediately
* It returns the **actual decorator**

#### Step 2

```python
def myfunction():
    return "Hello Linus"
```

* `myfunction` is passed to the inner `changecase(func)`

#### Step 3

* `myinner` becomes the wrapper
* `n` is stored in a **closure**
* The value of `n` remains available forever

This is why **closures are essential for decorators with arguments**.

---

## 10. Multiple Decorators on One Function

### Code

```python
@changecase
@addgreeting
def myfunction():
    return "Tobias"
```

### Execution Order

This is interpreted as:

```python
myfunction = changecase(addgreeting(myfunction))
```

### Important Rule

> Decorators execute **bottom to top**, but wrap **inside out**

---

## 11. Losing Function Metadata (A Subtle but Serious Problem)

### Example

```python
print(myfunction.__name__)
```

After decoration, this prints:

```text
myinner
```

Why?

* The original function is replaced
* Python sees only the wrapper

This breaks:

* Debugging
* Logging
* Documentation
* Frameworks

---

## 12. Fixing Metadata Loss With `functools.wraps`

### Code

```python
import functools

def changecase(func):
    @functools.wraps(func)
    def myinner():
        return func().upper()
    return myinner
```

### What `wraps` Does

It copies:

* `__name__`
* `__doc__`
* `__module__`
* Function annotations

From `func` → `myinner`

This is **not optional in real projects**.

---

## 13. Decorators Explained Using Manual Wrapping

### Code

```python
def start_end_decorator(func):
    def wrapper():
        print("Start")
        func()
        print("End")
    return wrapper
```

### Manual Application

```python
print_name = start_end_decorator(print_name)
```

This proves:

* `@decorator` is only syntax sugar
* No magic exists

---

## 14. Handling Return Values Correctly

### Problem

```python
def wrapper(*args, **kwargs):
    func(*args, **kwargs)
```

Returns `None`

### Correct Version

```python
def wrapper(*args, **kwargs):
    result = func(*args, **kwargs)
    return result
```

Decorators **must explicitly return values**.

---

## 15. Decorators and Function Identity (Deep Dive)

After decoration:

```python
print(add_5.__name__)
help(add_5)
```

Python thinks:

* the function is `wrapper`
* not the original

Using `@functools.wraps` fixes this completely.

---

## 16. Decorators With Arguments (Repeat Example)

### Code

```python
def repeat(num_times):
    def decorator_repeat(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            for _ in range(num_times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator_repeat
```

### Explanation

* `repeat(num_times)` stores `num_times`
* `decorator_repeat` receives the function
* `wrapper` executes it repeatedly
* Closure preserves `num_times`

---

## 17. Nested Decorators and Execution Order

### Code

```python
@debug
@start_end_decorator
def say_hello(name):
    ...
```

Equivalent to:

```python
say_hello = debug(start_end_decorator(say_hello))
```

This affects:

* logging order
* timing
* return values

---

## 18. Class-Based Decorators

### Code

```python
class CountCalls:
    def __init__(self, func):
        functools.update_wrapper(self, func)
        self.func = func
        self.num_calls = 0

    def __call__(self, *args, **kwargs):
        self.num_calls += 1
        return self.func(*args, **kwargs)
```

### Explanation

* Class receives the function in `__init__`
* `__call__` replaces `wrapper`
* State (`num_calls`) persists across calls

---

## 19. Real-World Decorator Use Cases (Explained)

### Logging

Wrap function calls and arguments

### Authentication

Check user permissions before execution

### Caching

Store results to avoid recomputation

### Timing

Measure execution duration

### Validation

Ensure inputs meet requirements

### Plugin Registration

Register functions automatically

---

## 20. Final Mental Model (Critical)

> **Decorators replace functions with other callables that call the original function internally.**

If you understand this sentence, you understand decorators.
=