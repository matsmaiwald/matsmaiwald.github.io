---
layout: post
title:  "Parallelisation in Python"
categories: misc
---
I recently had a Python script with a for loop that was taking quite a long time to run, so I thought to myself: "Surely, there's an easy way to parallelise this!" and started looking online how to best go about the task. The **two main things I learned** where

- it **is** easy to parallelise for loops in python
- you can parallelise via **multi-threading** or via **multi-processing**. Which of the two you choose depends on what makes each iteration of the loop slow to beign with.

## Multi-threading
Multi-threading is a way to speed up your script if each iteration of the loop contains parts that are *I/O-bound* i.e. *the speed at which they can be exectuted is primarily limited by an I/O process*. An example of this would be a process that needs input data and has to wait for a database to return the results of a query before it can work with the returned data.

Intuitively, Python uses the time during which the a given iteration of the loop is idle (because, say it is waiting for a database) to free up computational resources and start execution of the next iteration of the loop (let's assume for simplicitly that the different iterations are fully independet of each other).

## Multi-processing
Whereas multi-threading essentially improves the efficiency with which a single CPU is used by rotating CPU-access between multiple iterations of the for loop, multi-processing allocates different iterations of the loop to different CPUs (if they are available). This means it can speed up processes that are *CPU-bound* i.e. those where *the speed of execution is primarily limited by the computational power of the CPU*.

Rather than having to wait for the completion of the first iteration, before starting with the second, you could run e.g. four iterations at the same time -- again assuming that the iterations are independent of each other and that you have four CPUs at your disposal.

## A simple Python example of multi-threading and multi-processing
Rather than taking other people's word for it, I cooked up the following toy example to see how multi-threading and multi-processing could speed up different kind of for loops:

The following function is an example of an **I/O-bound process**, it is idle for a little bit during which you can imagine it waiting for some dependent process such as a database query to complete:

{% highlight python %}

def run_io_bound_process(id: int):
    print(f"Starting io-bound process # {id}")
    time.sleep(1)
    print(f"Finished io-bound process # {id}")

{% endhighlight %}

The next function is an example of a **CPU-bound process**, because for the entire time it takes to execute the fucntion, the CPU is busy computing factorials.

{% highlight python %}

def run_cpu_bound_process(id: int):
    print(f"Starting cpu-bound process # {id}.")
    for i in range(1000, 4000):
        math.factorial(i)
    print(f"Finished cpu-bound process # {id}.")

{% endhighlight %}

To multi-thread the execution of e.g. eight calls to the cpu-bound function from above, I would do the following:

{% highlight python %}
from multiprocessing.dummy import Pool as ThreadPool
id_numbers = list(range(1, 8))

pool = ThreadPool(8)
pool.map(run_cpu_bound_process, id_numbers)
{% endhighlight %}

Similarly, to run eight calls of the same function in multi-processing:

{% highlight python %}

from multiprocessing import Pool, cpu_count
id_numbers = list(range(1, 8))

pool = Pool(cpu_count())
pool.imap(run_cpu_bound_process, id_numbers)
pool.close()
pool.join()
{% endhighlight %}

## Performance benchmarking

Let me show how many seconds it took to run eight iterations of both functions

- without any parallelisation
- with multi-threading
- with multi-processing.


| Execution type   | CPU-bound  function | I/O-bound function |
|------------------|---------------------|--------------------|
| non-parallelised | 5.12                | 7.02               |
| multi-threading  | 5.21                | 1.01               |
| multi-processing | 2.54                | 2.01               |

As expected, **multi-processing speeds up the cpu-bound task the most** and **multi-threading speeds up the I/O bound task the most**. What is also interesting is that while **multi-threading actually slows down the cpu-bound task, multi-processing can speed up the I/O bound task significantly**, though the latter speed improvement will become smaller as the number of iterations increases vs the number of CPUs available.

## Source code
The complete source code can be found [here](https://github.com/matsmaiwald/python_parallelisation).
