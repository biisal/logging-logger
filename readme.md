# Logger Python

### 1. Logger - Intro

logger is a built-in module of python that often used for debugging python programs
it provides more features than printing using `print("this is a err")`

1.When the logging level is set to INFO, DEBUG messages are skipped because INFO is higher in priority than DEBUG.

2.The same logic applies to higher levels like WARNING, ERROR, and CRITICAL.

```py
import logging


logging.basicConfig(level=logging.INFO)

logging.info("this is a info message")
logging.debug("this is a message message")
logging.warning("this is a warning message")
logging.error("this is a error message")
logging.critical("this is a critical message")
```

3.When you specify a filename in basicConfig, the logs are written to the file instead of being displayed in the terminal.


4.Logs are saved in append mode by default, meaning new logs are added to the file without overwriting the existing content.


### 2. Specifying formate - logger


```py
import logging


logging.basicConfig(level=logging.INFO,
                    filename="custom_format_logs.log",
                    format='%(asctime)s - %(levelname)s - %(message)s')


logging.info("this is a info message")
logging.debug("this is a message message")
logging.warning("this is a warning message")
logging.error("this is a error message")
logging.critical("this is a critical message")
```
`%(asctime)s` : Exicutaion time

`%(levelname)s` : DEBUG level (info , warning, error ... )

`%(message)s` : Log message

- MORE : `%(filename)s` and `%(lineno)d`.


### 3. Log Handlers

1.Handlers are used to direct log messages to different outputs such as - console , file (mylogs.log) , External systems (e.g., Email, HTTP services)

2.logging module allows to use multiple handlers to log messages to deffernt locations simultaneously

```py
console_handler = logging.StreamHandler()
file_handler = logging.FileHandler("log_with_handler.log")
```

3. Using logger we can set differnt levels for each handlers
```py
console_handler.setLevel(logging.WARNING)
file_handler.setLevel(logging.DEBUG)
```

4. Setting formater for handlers
```py
formater = logging.Formatter(
    '%(asctime)s - %(levelname)s - %(filename)s:%(lineno)d - %(message)s')

console_handler.setFormatter(formater)
file_handler.setFormatter(formater)
```

5. finally adding the handlers in our custom logger
```py
logger.addHandler(console_handler)
logger.addHandler(file_handler)
```
6. More formatters - `%(module)s`, `%(process)d`

### 4. Logging Configuration File

1.Create a json file with all configuration

```json
{
    "version": 1,
    "disable_existing_loggers": false,
    "handlers": {
        "console": {
            "class": "logging.StreamHandler",
            "level": "WARNING",
            "formatter": "detailed",
            "stream": "ext://sys.stdout"
        },
        "file": {
            "class": "logging.handlers.RotatingFileHandler",
            "level": "DEBUG",
            "formatter": "detailed",
            "filename": "MyLoggerFile.log",
            "maxBytes": 200,
            "backupCount": 3
        }
    },
    "loggers": {
        "MyLogger": {
            "handlers": [
                "console",
                "file"
            ],
            "level": "DEBUG",
            "propagate": false
        }
    },
    "formatters": {
        "detailed": {
            "format": "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
        }
    }
}
```

2.Load the configuration file 
```py 
with open("logging_config.json", "r") as f:
    config = json.load(f)
```
3. Configure the logger
```py
logging.config.dictConfig(config)
```

4. Use the logger
```py
logger = logging.getLogger("MyLogger")
logger.info("this is a info message")
logger.debug("this is a message message")
logger.warning("this is a warning message")
logger.error("this is a error message")
logger.critical("this is a critical message")
```


### 5. Logging in Multi-Threaded Environments

1. By default, the logging module is thread-safe,but it's good to know how to structure your logs in such scenarios.

2. To log messages from multiple threads, you can use the `threading` module to create a thread and log messages from each thread.

```py
import logging
import threading
import time

def log_messages(thread_name: str):
    logging.debug(f"{thread_name}: Debug message")
    logging.info(f"{thread_name}: Info message")
    time.sleep(2)
    logging.warning(f"{thread_name}: Warning message")
    time.sleep(2)
    logging.error(f"{thread_name}: Error message")
    time.sleep(2)
    logging.critical(f"{thread_name}: Critical message")
    time.sleep(2)

threads = [
    threading.Thread(target=log_messages, args=(
        f"Thread-{i}",), name=f"Thread-{i}")
    for i in range(4)
]


for thread in threads:
    thread.start()

for thread in threads:
    thread.join()
```
