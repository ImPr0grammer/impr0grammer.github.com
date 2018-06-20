---
layout: post
title:  "Priority Queues and Heaps in Python"
date:   2018-06-20 13:56:19 +0300
tags: [ python, programming, "python-tips" ]
---

Let's imagine that you want to solve the following problem:
* You have tasks to process which are pushing to some queue
* Every task has priority and and some information for processing
* Every task should be processed according to it's priority


![Priority Queue]({{ "/assets/priority_queue.png" | absolute_url }})

Data structure that can handle such tasks with priorities is called Priority Queue.
How can we impelemnt it? We can place tasks to array but processing N tasks could take <b>O(N^2)</b> time which isn't a good idea for a real application.

Fortunatelly there is a very well known <b>O(N log N)</b> solution for this problem.
Let's have a look how to do it in Python.

<!--more-->

Priority Queue - is a data structure that has the following operation:
* Add Task
* Extract the most priority task to process
* Change priority for the task

Usually Priority Queues are implemented using <a href="https://en.wikipedia.org/wiki/Heap_(data_structure)">heap data structure</a>.

Python has PriorityQueue out of the box, <a href="https://docs.python.org/3/library/queue.html#queue.PriorityQueue">see docs </a>.
It works good the only problem is that it doesn't allow to change priority of the task.
Let's create simple PriorityQueue that allows to change priority of the task.
We will use standard Python <a href="https://docs.python.org/3.6/library/heapq.html">heapq</a> implementaion of heap.

Let's have a look it this implementation:
* It works in a C way. You have to define an array and pass it to all methods.
* It has method <b>heappush(heap, task)</b> to add task
* It has method <b>heappop(heap)</b> to extract task with the lowest priority 
* It has method <b>heapify(arr)</b> to transform array to correct heap data structure in place

Now, let's create PriorityQueue.

{% highlight python %}
from heapq import *

class PriorityQueue:
    
    def __init__(self):
        self.heap = []
        self.task2heapitem = {} # task -> heap item
        
    def push(self, task, priority):
        heap_entry = [priority, task, False]
        assert task not in self.task2heapitem, "There is already task {} in priority queue".format(task)
        
        self.task2heapitem[task] = heap_entry
        heappush(self.heap, heap_entry)
        
        
    def pop(self):
        while self.heap:
            priority, task, is_deleted = heappop(self.heap)
            
            if not is_deleted:
                del self.task2heapitem[task]
                return (task, priority)
        
        raise KeyError('Pop from an empty priority queue')
        
    
    def update_priority(self, task, priority):
        assert task in self.task2heapitem, "There is no task {} in heap".format(task)
        
        last_item = self.task2heapitem[task]
        last_item[-1] = True
        del self.task2heapitem[task]
        
        self.push(task, priority)
{% endhighlight %}

And then let's test it and compare our implementation with standard Python PriorityQueue

1. Let's create test data
{% highlight python %}
import sys
import random

tasks = []

n = 1000000
for i in range(n):
    priority = random.uniform(0, sys.maxsize)
    tasks.append((priority, i))

to_check = sorted(tasks, key = lambda item: item[0])
{% endhighlight %}

2. Test our implementation
{% highlight python %}
import time
queue = PriorityQueue()

start_time = time.time();
# First add all items
for priority, task in tasks:
    queue.push(task, priority)

# Extract all items in correct order
for i in range(n):
    task, priority = queue.pop()
    # Let's check that tasks returned with respect to priorities
    assert task == to_check[i][1]
    
print("Time: {}".format(time.time() - start_time))
{% endhighlight %}

Time: 10.167301654815674

3. Let's run standard PrioriyQueue
{% highlight python %}

import queue
import time

start_time = time.time();

queue = queue.PriorityQueue()
for priority, i in tasks:
    queue.put((priority, i))

for i in range(n):
    task = queue.get()   
    assert task[1] == to_check[i][1]

print("Time: {}".format(time.time() - start_time))

{% endhighlight %}

Time: 10.465237379074097

We have got the same result because standard <b>PriorityQueue</b> is also based on <b>heapq</b>.

One thing, we don't really update priority of the task. We mark it as deleted and add one more task to the queue. This could lead to larger heap. If we want to really update task priorities we need to implement our own heap data structure.

Recap:
* Use <b>queue.PriorityQueue</b> if you want to process tasks according to it's priority;
* Use my implementation of PriorityQueue or something similar if you need to modify priorities of tasks or you need more control on queue;
* Implement your own heap if you want to have perfect effeciency in priority updates.


Stay tuned, check out [my blog][blog-main] for more useful tips and tricks.

[blog-main]: https://impr0grammer.github.io
[github-repo]: https://github.com/impr0grammer