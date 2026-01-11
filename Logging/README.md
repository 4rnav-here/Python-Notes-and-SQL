
# 📘 Python Logging 

## 1. What is the `logging` module?

The `logging` module is Python’s **built-in framework for event logging**.
It allows applications and libraries to log messages in a **consistent, configurable, and hierarchical** way.

### Why logging instead of `print()`?

* Supports **log levels** (severity)
* Can log to **files, console, network, email**
* Works across **multiple modules**
* Thread-safe
* Can be **centrally configured**
* Integrates third-party libraries automatically

---

## 2. Log Levels

There are **5 standard log levels** (in increasing severity):

| Level    | Numeric | Use Case                   |
| -------- | ------- | -------------------------- |
| DEBUG    | 10      | Detailed diagnostics       |
| INFO     | 20      | Normal operation           |
| WARNING  | 30      | Unexpected but recoverable |
| ERROR    | 40      | Operation failed           |
| CRITICAL | 50      | App may crash              |

### Default behavior

* Only logs **WARNING and above**

```python
import logging

logging.debug("Debug message")     # Ignored by default
logging.info("Info message")       # Ignored by default
logging.warning("Warning message") # Printed
logging.error("Error message")     # Printed
logging.critical("Critical")       # Printed
```

---

## 3. `basicConfig()` – Root Logger Configuration

`basicConfig()` configures the **root logger**.

⚠️ Must be called **once**, preferably at startup.

```python
import logging

logging.basicConfig(
    level=logging.DEBUG,     # Minimum level to log
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    datefmt='%d-%m-%Y %H:%M:%S'
)

logging.debug("Now DEBUG messages are visible")
```

### Important Notes

* If **any logging call happens before `basicConfig()`**, it may have **no effect**
* Affects **all child loggers**

---

## 4. Logging to a File

```python
logging.basicConfig(
    filename='app.log',      # Write logs to file
    level=logging.INFO,
    format='%(asctime)s %(levelname)s %(message)s'
)
```

---

## 5. Logger Hierarchy & `__name__` (BEST PRACTICE)

Each module should create its **own logger** using `__name__`.

```python
# helper.py
import logging

logger = logging.getLogger(__name__)
logger.info("HELLO")
```

```python
# main.py
import logging
logging.basicConfig(level=logging.INFO)

import helper
```

### Output

```
helper - INFO - HELLO
```

### Why this works

* Logger names follow **module hierarchy**
* Enables **fine-grained control**
* Third-party logs integrate automatically

---

## 6. Propagation

By default, log messages **bubble up** to parent loggers.

```python
logger.propagate = False
```

If `propagate = False`, messages **do not reach root logger**, so nothing is printed unless a handler is attached.

---

## 7. Handlers (Where logs go)

Handlers decide **where** logs are sent.

Common handlers:

* `StreamHandler` → console
* `FileHandler` → file
* `RotatingFileHandler`
* `TimedRotatingFileHandler`

### Multiple handlers example

```python
import logging

logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)

# Console handler
stream_handler = logging.StreamHandler()
stream_handler.setLevel(logging.WARNING)

# File handler
file_handler = logging.FileHandler('app.log')
file_handler.setLevel(logging.ERROR)

# Formatters
stream_handler.setFormatter(
    logging.Formatter('%(levelname)s - %(message)s')
)

file_handler.setFormatter(
    logging.Formatter('%(asctime)s %(levelname)s %(message)s')
)

logger.addHandler(stream_handler)
logger.addHandler(file_handler)

logger.warning("Shown on console")
logger.error("Shown on console AND file")
```

---

## 8. Filters

Filters decide **which records pass**.

```python
class InfoFilter(logging.Filter):
    def filter(self, record):
        return record.levelno == logging.INFO

handler.addFilter(InfoFilter())
```

Only INFO logs pass through.

---

## 9. Logging Configuration via `.conf` file

Allows **non-code configuration**.

```ini
[loggers]
keys=root,myLogger

[handlers]
keys=consoleHandler

[formatters]
keys=myFormatter

[logger_root]
level=DEBUG
handlers=consoleHandler

[logger_myLogger]
level=DEBUG
handlers=consoleHandler
qualname=myLogger
propagate=0

[handler_consoleHandler]
class=StreamHandler
level=DEBUG
formatter=myFormatter
args=(sys.stdout,)

[formatter_myFormatter]
format=%(asctime)s %(name)s %(levelname)s %(message)s
```

```python
import logging.config
logging.config.fileConfig("logging.conf")
logger = logging.getLogger("myLogger")
logger.info("Hello")
```

---

## 10. Logging Exceptions & Stack Traces

### Using `exc_info=True`

```python
try:
    a = [1,2,3]
    a[3]
except Exception as e:
    logging.error("Error occurred", exc_info=True)
```

### Using `traceback`

```python
import traceback

try:
    a[3]
except:
    logging.error(traceback.format_exc())
```

---

## 11. RotatingFileHandler (Size-based)

```python
from logging.handlers import RotatingFileHandler

handler = RotatingFileHandler(
    'app.log',
    maxBytes=2000,     # rollover after 2KB
    backupCount=5
)
```

Keeps:

```
app.log
app.log.1
app.log.2
```

---

## 12. TimedRotatingFileHandler (Time-based)

```python
from logging.handlers import TimedRotatingFileHandler

handler = TimedRotatingFileHandler(
    'timed.log',
    when='m',          # every minute
    interval=1,
    backupCount=5
)
```

---

## 13. JSON Logging (Microservices)

Used for **centralized logging systems**.

```python
from pythonjsonlogger import jsonlogger

handler = logging.StreamHandler()
formatter = jsonlogger.JsonFormatter()
handler.setFormatter(formatter)

logger = logging.getLogger()
logger.addHandler(handler)
```

### Benefits

* Machine readable
* Searchable
* Ideal for ELK, Datadog, CloudWatch

---

## 14. Idiomatic Logging Pattern (BEST PRACTICE)

```python
# myapp.py
import logging
logger = logging.getLogger(__name__)

def main():
    logging.basicConfig(level=logging.INFO)
    logger.info("Started")
    do_work()
    logger.info("Finished")

if __name__ == "__main__":
    main()
```

```python
# helper.py
import logging
logger = logging.getLogger(__name__)

def do_work():
    logger.info("Doing work")
```

### Key idea

* Configure logging **once**
* Every module just logs

---

## 15. Thread Safety

* Logging module is **thread-safe**
* Uses locks internally
* Safe for multi-threaded apps
* Not safe inside **signal handlers**

---

## 16. Core Logging Components (Interview Gold)

| Component | Responsibility       |
| --------- | -------------------- |
| Logger    | Creates log records  |
| Handler   | Sends logs somewhere |
| Formatter | Formats logs         |
| Filter    | Decides what to log  |
| LogRecord | Stores log data      |

---

## 17. When to Use Which Level (Very Important)

* DEBUG → Development only
* INFO → Business flow
* WARNING → Something off
* ERROR → Operation failed
* CRITICAL → System unusable

---

## 18. Common Interview Traps

❌ Using `print()`
❌ Calling `basicConfig()` multiple times
❌ Creating loggers manually
❌ Logging without levels
❌ Logging sensitive data

---

## Final Mental Model

> **Logger creates → Handler sends → Formatter formats → Filter decides**

---
