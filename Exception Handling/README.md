
# Exception Handling in Python 

## What are Errors vs Exceptions (clear distinction)

### Errors

Errors are **serious problems** that prevent the program from running at all.

Examples:

* `SyntaxError`
* `IndentationError`

These happen **before execution**.
You **cannot** catch them with `try-except`.

```python
return "Hello"   # SyntaxError
```

➡ Fix in code, not with exception handling.

---

### Exceptions

Exceptions are **runtime problems** that occur **while the program is running**.

Examples:

* Division by zero
* File not found
* Invalid input
* Wrong data type

These **can and should be handled** using exception handling.

---

## Why Exception Handling Exists

Without exception handling:

* Program crashes immediately
* No cleanup
* Poor user experience
* Production failures

With exception handling:

* Program continues safely
* Errors are handled gracefully
* Resources are cleaned
* Debugging becomes easier

---

## Basic try–except Structure

```python
try:
    # risky code
except ExceptionType:
    # handling code
```

Example:

```python
try:
    x = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero")
```

✔ Program does not crash
✔ Error is handled cleanly

---

## Full try–except–else–finally Structure

```python
try:
    # code that may fail
except ExceptionType:
    # runs if exception occurs
else:
    # runs if NO exception occurs
finally:
    # ALWAYS runs
```

---

## Understanding Each Block (Very Important)

### `try`

Contains code that **might raise an exception**

```python
try:
    result = x / y
```

---

### `except`

Handles the exception **if it occurs**

```python
except ZeroDivisionError:
    print("Invalid division")
```

You can:

* Catch specific exceptions (recommended)
* Catch generic exceptions (last resort)

---

### `else`

Runs **only if try succeeds**

```python
else:
    print("Division successful:", result)
```

Used when:

* You want success logic clearly separated
* You don’t want success code inside `try`

---

### `finally`

Runs **no matter what**

```python
finally:
    print("Cleanup done")
```

Used for:

* Closing files
* Releasing DB connections
* Unlocking resources
* Logging

Even if:

* Exception occurs
* Exception is re-raised
* Program exits

---

## Common Built-in Exceptions (You MUST know these)

| Exception           | When it occurs            |
| ------------------- | ------------------------- |
| `ZeroDivisionError` | Divide by zero            |
| `ValueError`        | Correct type, wrong value |
| `TypeError`         | Wrong type                |
| `IndexError`        | Invalid list index        |
| `KeyError`          | Missing dictionary key    |
| `NameError`         | Variable not defined      |
| `AttributeError`    | Invalid attribute         |
| `FileNotFoundError` | File missing              |
| `PermissionError`   | Access denied             |
| `ImportError`       | Module not found          |
| `MemoryError`       | Out of memory             |

---

## Catching Specific Exceptions (Best Practice)

```python
try:
    x = int("abc")
except ValueError:
    print("Invalid integer")
```

✔ Precise
✔ Predictable
✔ Maintainable

---

## Catching Multiple Exceptions

### Multiple except blocks

```python
try:
    x = int("abc") / 0
except ValueError:
    print("Value error")
except ZeroDivisionError:
    print("Division error")
```

---

### Multiple exceptions in one block

```python
except (ValueError, TypeError):
    print("Invalid input")
```

---

## Catching All Exceptions (Be Careful)

```python
except Exception as e:
    print(e)
```

⚠️ Use only when:

* Logging
* Cleanup
* Re-raising
* Boundary layers (API / CLI / main)

Never silently swallow exceptions.

---

## Accessing Error Details

```python
except ValueError as e:
    print(e)
```

Useful for:

* Debugging
* Logging
* Error messages

---

## Raising Exceptions Manually

### Basic raise

```python
raise ValueError("Invalid value")
```

Used when:

* Input validation fails
* Business rules break
* Program state becomes invalid

---

### Re-raising an exception

```python
except Exception:
    print("Logging error")
    raise
```

Keeps original traceback intact.

---

## Custom Exceptions (Very Common in Real Projects)

```python
class CustomError(Exception):
    pass
```

With message:

```python
class AgeError(Exception):
    def __init__(self, msg):
        super().__init__(msg)
```

Usage:

```python
if age < 18:
    raise AgeError("Age must be >= 18")
```

✔ Improves readability
✔ Makes debugging easier
✔ Essential for large applications

---

## try–except with Files (Real-World Example)

```python
try:
    file = open("data.txt", "r")
    data = file.read()
except FileNotFoundError:
    print("File not found")
finally:
    file.close()
```

Better approach (recommended):

```python
with open("data.txt") as file:
    data = file.read()
```

But `try-except` is still needed for:

* Permission issues
* Invalid paths
* Runtime file errors

---

## Nested try–except (Advanced, Use Carefully)

```python
try:
    try:
        x = int("abc")
    except ValueError:
        print("Inner error")
except Exception:
    print("Outer error")
```

⚠️ Overuse leads to:

* Messy code
* Hard debugging

Prefer **multiple flat try-except blocks** instead.

---

## What if Exception Occurs Inside except or finally?

If:

* Error occurs in `except`
* Error occurs in `finally`

➡ It propagates to the **next outer try-except**

```python
finally:
    print(y)  # NameError
```

Unless handled, program crashes.

---

## Suppressing Exceptions (Not Recommended)

```python
try:
    risky()
except:
    pass
```

❌ Dangerous
❌ Hides bugs
❌ Makes debugging impossible

Only acceptable in **very rare cases**.

---

## Assertions vs Exceptions

```python
assert x > 0
```

* Used for debugging
* Can be disabled
* NOT a replacement for exception handling

---

## New Exception Features (Python 3.10+ / 3.11)

### Pattern matching on exceptions (3.10)

```python
try:
    1 / 0
except Exception as e:
    match e:
        case ZeroDivisionError():
            print("Division error")
```

---

### Exception Groups & `except*` (3.11)

Used in:

* Async code
* Parallel tasks

```python
except* ValueError:
    ...
```

---

### Improved Tracebacks (3.11)

Pinpoints **exact expression** that failed:

```text
(3 / 0)
     ^
```

Huge debugging improvement.

---

### add_note() (3.11)

```python
except ValueError as e:
    e.add_note("User input caused this")
    raise
```

Adds context without losing traceback.

---

## Best Practices (Real-World Rules)

✔ Catch **specific exceptions**
✔ Use `finally` for cleanup
✔ Never hide errors silently
✔ Raise meaningful custom exceptions
✔ Log exceptions in production
✔ Keep try blocks small
✔ Don’t use exceptions for normal control flow

---

## Mental Model (Important)

> **Errors crash programs.
> Exceptions protect programs.**

Exception handling is not about avoiding errors —
it’s about **controlling failure**.

---

