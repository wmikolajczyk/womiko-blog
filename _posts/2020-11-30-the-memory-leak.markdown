---
layout: post
title:  "The mystery of the memory leak..."
date:   2020-11-30 10:00:00 +0100
tags: python memory-leak gcs google-cloud-storage
---
Some time ago, when I was developing some data processing script in Python I encountered an interesting problem. I was sure I did everything well - my script worked as intended, all unit tests passed and I even tested the script on small data, and it worked properly. 

Everything seemed to be fine until... I ran it to process a HUGE (1 400 000 files, 50GB) volume of data. The script was massively processing big data and suddenly a container with this script was killed due to Out Of Memory error. It was a script wrapped into a Docker container deployed on Kubernetes with defined some maximum usage of RAM limits. So, what actually happened, and why is that?

In such cases, it's always a good idea to check for CPU and memory usage over time graphs.
I checked the graph of memory usage over time, and it looked like that.
![image](/assets/images/memory_usage_over_time.png "memory usage over time")

I saw that memory usage constantly grows. It means that my script allocates more and more memory for objects, much faster than it frees some memory. This problem is called a *MEMORY LEAK*. So, why is that?...

My script uses multiple threads - maybe it should be the first thing to check. I needed to pass Google Cloud Storage Client object into each thread to be able to perform the required actions. A bunch of threads work in a pool to perform input-output heavy operations in a loop - I bet for every iteration this credentials data is put into memory again for each thread.

I performed a code-review together with my friend and he helped me to find that this actually is a problem. 
Each thread creates a seemingly harmless GCS Client object every time the operation is performed. We've found out that it's not actually harmless - it was the root cause of the memory leak!

The solution was to use threading.local() in Python ([https://docs.python.org/3.8/library/threading.html#threading.local](https://docs.python.org/3.8/library/threading.html#threading.local)) - which allows storing data for each thread. After applying this fix - GCS Client object was created only once for each thread. The memory leak problem is solved now! :)

*Code with Memory Leak issue*
```python
from google.cloud import storage

...

class SomeClass:
    def __init__(project_id):
        self.project_id = project_id

    @property
    def client(self):
        return storage.Client(project=self.project_id)
```

*Code after applying fix - usage of threading.local()*
```python
from google.cloud import storage

...

class SomeClass:
    def __init__(project_id):
        self.project_id = project_id
        self.thread_local = threading.local()

    @property
    def client(self):
        if "gcs_client" not in self.thread_local.__dict__:
            self.thread_local.gcs_client = storage.Client(
                project=self.project_id)
        return self.thread_local.gcs_client
```


After the fix, the script was able to process all this data in around 5 hours, thanks to multi-threaded operations which sped up the whole thing a lot. 

#### Key takeaways:
- some problems will appear ONLY when Python script is run on a MASSIVE scale
- memory leak occurs when your application allocates more RAM than it frees in time
- you can spot a memory leak by inspecting memory usage over time graph
- memory leaks are pretty tough to debug - usually, the only message you get is this "Out Of Memory" error
- when encountered a memory leak - then your goal is to find this piece of code which "eats" memory :)
- GCS Client object isn't thread-safe and leaves some leftovers in the memory
- threading in Python might be tricky - if you encounter some problems it's often good to take a step back to get a better understanding of what's going on under the hood
- threading.local() in Python allows storing some data per thread
