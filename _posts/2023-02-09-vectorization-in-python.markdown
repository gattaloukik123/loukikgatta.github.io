---
layout: post
title:  "But what the hell is Vectorization?"
date:   2023-01-06 20:16:40 +0530
categories: jekyll update
---

## Reference Links
1. An amazing deep-dive into [vectorization][vectorization-deepdive] in python
2. A detailed explaination of when to use [vectorization][vec-dis] in python
3. [Youtube video][vec-video] on how vectorization can help us by using numpy

## Fasten your seatbelts 🚀
> Lets vectorize some slow python code

I started researching on ways to fasten my python code while I was struggling to perform analytics over a million records and this lead me to *words* on certain articles such as Parallel Processing, SIDM in python and finally to ***Vectorization*** and it is awesome.

To give a proper "definition" to the word Vectorization in this context, it basically means to execute an algorithm to perform on a bunch of values rather than on a single value. To achieve this, Vectorization takes helps of machine-level instructions and hardware capabilities.

Now vectorization in python can be achieved through the Numpy API and to put all my observations on how numpy does it in 3 simple points, 
1. It helps us with reducing the amount of CPU instructions. Now on the machine-level all of the operations we code are gonna be performed as instructions such as Arthmatic Instructions, Logic Instructions etc. Now assuming we have homogeneous data in a two arrays, say float64, and we would want to add them, numpy knows it is dealing with floats and it basically uses a single CPU instruction to add these floats saving us from literally thousands of instructions. To compare this with how normal python interpreter works, considering we have the same arrays of float64's, from the interpreters perpective these arrays will look like 2 arrays of PyObject pointers. For the interpretor to process these and add them, it basically consists of extra CPU instructions to poke the pointer and get its addition function, now imagine doing this on arrays that have millions of data points. 

2. Vectorization helps us with reduced memory reads. As we discussed before, a normal python interpreter such as Cpython stores PyObject* instead of the value itself in the array, now if we look at it from the CPU RAM's perspective, we have a pointer in the RAM and also the value that this pointer is pointing to, meaning the actual value is stored else where. Now Numpy has a huge bump here because it stores the value as float64 directly in the array helping it reduce the memory reads. 

3. Here comes the interesting and a fairly easy point to catch and it is nothing but Parallel Processing. I wanted to start off with this point while I was trying to summarize all the documents I read but since this was an obvious point I wanted to prioritize the other areas where vectorization shines. We do have a seperate instruction set written on the machine-level for such purposes and it is called SIMD (Single Instruction, Multiple Data) and it does exactly what it sounds like. Now the recent versions of Numpy harnesses SIMD in providing better and better results by executing stuff parallelly. 

I am not gonna be super nerdy and speak about how vectorization speeds up a given code by providing information from the machine-level but I guess we can work on having some code snippets and measure the difference between a vectorized and an unvectorized code. So lets see some examples and see how vectorization is in action.


## But how do we measure/compare performances?
> Lets try to use linux perf tool 

Now this section can get a little too hard for some of you guys but what we are really doing here is we are creating a Python context manager to calculate the time spent, memory and cpu resoruces spent on executing a certain piece of code. I am not going to explain the code because I made this up with the help of [this][vectorization-deepdive] article and if you want to learn more you can refer that article. Now remember this code can only be executed on linux based machines since the perf tool is configured only on linux kernels. If you want to run the other stuff you can simply remove the last line and try executing the code.

```
from contextlib import contextmanager
from subprocess import Popen
from os import getpid
from signal import SIGINT
from time import sleep, time
from resource import getrusage, RUSAGE_SELF

events = [
    "instructions",
    "cache-references",
    "cache-misses",
    "avx_insts.all",
]

@contextmanager
def perf():
    """Benchmark this process with Linux's perf util.
    
    Example usage:

        with perf():
            x = run_some_code()
            more_code(x)
            all_this_code_will_be_measured()
    """
    p = Popen([
            # Run perf stat
            "perf", "stat",
            # for the current Python process
            "-p", str(getpid()),
            # record the list of events mentioned above
            "-e", ",".join(events)])
    # Ensure perf has started before running more
    # Python code. This will add ~0.1 to the elapsed
    # time reported by perf, so we also track elapsed
    # time separately.
    sleep(0.1)
    start = time()
    try:
        yield
    finally:
        print(f"Elapsed (seconds): {time() - start}")
        print("Peak memory (MiB):",
            int(getrusage(RUSAGE_SELF).ru_maxrss / 1024))
        p.send_signal(SIGINT)

```

## Vectorization in Action
> Lets compare an example of the native code with vectorized numpy functions

Lets try to do something very basic, say, calculate the mean of an array with float64s and then subract the mean from each data point. Now the perf code will help us with getting the elapsed time, peak memory hit and the perf cli command helps us with sending back the no. of instructions, memory reads and cache miss a particular piece of code contributed to. An example usage and output of this code ran along with a simple python snippet will look like, 

```
from random import random

DATA = [random() for _ in range(30_000_000)]

with perf():
    mean = sum(DATA) / len(DATA)
    result = [DATA[i] - mean for i in range(len(DATA))]
```
And the corresponding outputs looks like, 
```
Elapsed (seconds): 3.3306941986083984
Peak memory (MiB): 2327
...
    31,426,844,839      instructions:u
        11,882,126      cache-references:u
         4,024,256      cache-misses:u
...
```

Now lets try to the run the same code using numpy api, 

```
from benchmark import perf
import numpy as np

DATA = np.random.rand(30_000_000)

with perf():
    mean = DATA.sum() / len(DATA)
    result = DATA - mean
```
And the corresponding outputs looks like,
```
Elapsed (seconds): 0.0806736946105957
Peak memory (MiB): 483
...
       162,356,695      instructions:u
         9,669,587      cache-references:u
         3,366,269      cache-misses:u
...
```
Woahh, thats way fast that what we the native code is doing. As mentioned in the points above, we can see clear improvements in the elapsed time and steep decrease in the no. of instructions and you can see the efficiency around memory reads. 

## Conclusion
> Lets rest for a while!

We have seen various reasons as well as examples on how Vectorization helps us coders to write better and efficient code using Python. I really find the benefit of using numpy as my daily driver now rather on working on the native lists. Now this might not be a deep dive into the world of vectorization but I guess I will be writing more about topics that involve it. I have been reading more about this new OLAP (Online Analytical Processing (OLAP) is a database technology that has been optimized for querying and reporting) database called DuckDB and I heard it utilizes more of such concepts and that it has the potential to replace pandas. I am excited about this!

Feel free to contact me if you think the contents incorrect at any place.


[vectorization-deepdive]: https://pythonspeed.com/articles/vectorization-python/
[vec-dis]: https://pythonspeed.com/articles/pandas-vectorization/
[vec-video]: https://www.youtube.com/watch?v=BR3Qx9AVHZE
