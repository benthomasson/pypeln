# Pypeline

_Pypeline is a python library that enables you to easily create concurrent/parallel data pipelines._

* Pypeline was designed to solve simple _medium_ data tasks that require concurrency and parallelism but where using frameworks like Spark or Dask feel exaggerated or unnatural.
* Pypeline hides away all the boilerplate code required to execute concurrent/parallel tasks and exposes an easy to use, familiar, functional API.
* Pypeline enables you to build concurrent pipelines using all 3 major concurrency/parallelism mechanisms in Python (multiprocessing, threading, and asyncio) via the exact same API.
* Pypeline allows you to have control over the memory and cpu resources used at different stages of your pipeline.

## Instalation

To install Pypeline with pip run
```bash
pip install pypeln
```
If you want the latest development version you can install via
```bash
pip install git+https://github.com/cgarciae/pypeln@develop
```

## Concept
**MAYBE MOVE THIS TO THE GUIDE???**

When creating a program that performs several non-trivial operations other sequences in an efficient manner it common to end up doing the following:

* Breaking the problem into several concurrent parts or **stages**
* Creating several **worker** entities to complete that task of each stage in parallel if possible

Given that you end up having an architectures such as this one

![diagram](docs/diagram.png)

On each stage workers _get_ the data from a **queue** structure from a previous stage, perform certain operations over it, and _put_ the result into the next queue for another stage to consume. As shown in the diagram, the initial stage consumes the _iterable_ source, and a final iterable sink is created to receive the results. 

## Basic Usage
Pypeline has 3 main modules, each module lets you use a specific type of worker:

### Processes
You can create a stage based on [multiprocessing.Process](https://docs.python.org/3.4/library/multiprocessing.html#multiprocessing.Process)es by using the `pr` module:

```python
from pypeln import pr
import time

def slow_add1(x):
    time.sleep(0.2) # <= some slow computation
    return x + 1

data = [1,2,3,4]
stage = pr.map(slow_add1, data, workers = 3) # [2, 3, 4, 5] (sorted)

data = list(stage) # [5, 2, 3, 4]
```
**[EXPLENATION NEEDED]**

### Threads
You can create a stage based on [threading.Thread](https://docs.python.org/3/library/threading.html#threading.Thread)s by using the `th` module:
```python
from pypeln import th
import time

def slow_add1(x):
    time.sleep(0.2) # <= some slow computation
    return x + 1

data = [1,2,3,4]
stage = th.map(slow_add1, data, workers = 3) # [2, 3, 4, 5] (sorted)

data = list(stage) # [5, 2, 3, 4]
```
**[EXPLENATION NEEDED]**

### Tasks
You can create a stage based on [asyncio.Task](https://docs.python.org/3.4/library/asyncio-task.html#asyncio.Task)s by using the `io` module:
```python
from pypeln import io
import asyncio

async def slow_add1(x):
    await asyncio.sleep(0.2) # <= some slow computation
    return x + 1

data = [1,2,3,4]
stage = io.map(slow_add1, data, workers = 3) # [2, 3, 4, 5] (sorted)

data = list(stage) # i.e. [5, 2, 3, 4]
```
**[EXPLENATION NEEDED]**
### Multi-staged Pipelines
You can create pipelines with multiple stages by building upon a previous stage. For example
```python
from pypeln import pr
import time

def slow_add1(x):
    time.sleep(0.2) # <= some slow computation
    return x + 1

def slow_gte3(x):
    time.sleep(0.2) # <= some slow computation
    return x >= 3

data = [1,2,3,4]
stage = pr.map(slow_add1, data, workers = 3) # [2, 3, 4, 5] (sorted)
stage = pr.filter(slow_gte3, stage, workers = 2) # [3, 4, 5] (sorted)

data = list(stage) # [5, 3, 4]
```
**[EXPLENATION NEEDED]**

## Benchmarks
**[COMMING SOON]**

## Resources

* [Pypeline API Documentation](https://cgarciae.github.io/pypeln/) **[WORK IN PROGRESS]**
* Pypeline Guide: **[COMMING SOON]**


## Contributors
* [cgarciae](https://github.com/cgarciae)
* [davidnet](https://github.com/davidnet)