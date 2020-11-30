---
layout: post
title:  "The mystery of the memory leak..."
date:   2020-11-30 10:00:00 +0100
---
Some time ago, when I was developing some data processing script in Python I encountered an interesting problem. I was sure I did everything well - my script worked as intended, all unit tests passed and I even tested the script on small data, and it worked properly. 

Everything seemed to be fine until... I ran it to process a HUGE volume of data. The script was massively processing big data and suddenly a container with this script was killed due to Out Of Memory error. It was a script wrapped into a Docker container deployed on Kubernetes with defined some maximum usage of RAM limits. So, what actually happened, and why is that?

In such cases that's always a good idea to check for CPU and memory usage over time graphs.
I checked the graph of memory usage over time, and it looked like that.
![image](/assets/images/memory_usage_over_time.png "memory usage over time")

I saw that memory usage constantly grows. It means that my script allocates more and more memory for objects, much faster than it frees some memory. This problem is called a *MEMORY LEAK*. So, why is that?...

My script uses multiple threads - maybe it should be the first thing to check. I needed to pass some credentials data into each thread to be able to perform required actions. Bunch of threads works in a pool to perform input-output heavy operations in a loop - I bet for every iteration this credentials data is put into memory again for each thread.

I made a code-review together with my friend and he helped me to find that this actually is a problem. I did a mistake in the way I was storing data in threads in Python. As a result of that in every next iteration thread didn't have access to previously stored data so it needed to be stored again - and allocated more memory.
```Python3
@property
def client(self):
    thread_local = threading.local()
    if "some_client" not in thread_local.__dict__:
        thread_local.some_client = some.Client(arg=arg)
    return thread_local.some_client
```
Now I was able to fix that and store the thread credentials data properly.
```Python3
@property
def client(self):
    thread_local = threading.local()
    if "some_client" not in thread_local.__dict__:
        self.thread_local.some_client = some.Client(arg=arg)
    return self.some_client
```
After the fix, the script was able to process all this data in around 5 hours, thanks to multi-threaded operations which sped up the whole thing a lot.

#### Key take aways:
- some problems will appear ONLY when script is ran on MASSIVE scale
- memory leak occurs when your application allocates more RAM than it frees in time
- you can spot a memory leak by inspecting memory usage over time graph
- memory leaks are pretty tough to debug - usually the only message you get is this "Out Of Memory" error
- when encountered a memory leak - then your goal is to find this piece of code which "eats" memory :)
- threading in Python might be tricky - if you encounter some problems it's often good to take a step back to get a better understanding of what's going on under the hood
