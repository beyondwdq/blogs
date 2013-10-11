# A Problem with Python multiprocessing.Queue

Recently I have been working on a server written in Python, which conducts
a lot of CPU intensive jobs for clients. Due to CPython's GIL, it cannot
utilize the power of multi-core with multi-threading. Hence I decided to
implement the server in a one-process-per-connection manner. Everything works
well until I have used `multiprocessing.Queue` for inter-process communication.

Take a look at the following code:

    import multiprocessing
    import threading
    import os
    import time
    import sys

    queue = multiprocessing.Queue()

    def receive():
        global queue
        msg = queue.get()
        while msg is not None:
            print msg
            msg = queue.get()

    # create a thread to receive messages from queue
    t = threading.Thread(target=receive)
    t.daemon = True
    t.start()

    # create a new process
    pid = os.fork()

    if pid == 0:
        queue.put("child")
    else:
        time.sleep(1)
        queue.put("parent")
        queue.put(None)
        time.sleep(1)  # make sure that the receive thread has received all msgs

The script prints as expected:

    child
    parent

However, if we add a `queue.put("before fork")` before creating child process:

    queue.put("before fork")
    pid = os.fork()

The output becomes:

    before fork
    parent

The child's message is lost. Why?

I looked a bit into the source code of `multiprocessing.Queue`. It launches a
thread on the first invocation of `Queue.put()`. The thread is used to send
messages to a pipe, so that the caller of `Queue.put()` can return immediately.
This is good for most cases, but it also raises a problem - mixing threads with
`fork` is complicated. Threads in the parent process will be all dead except for
the main thread. Hence when I call `queue.put("before fork")`, a thread is be
created, and it becomes dead in the child process after `os.fork()`. No thread
will be working to send the messages to pipe.

There could be two solutions to this problem. The first one is to invoke
`queue._after_fork()` in the beginning of the child process. For example:

    if pid == 0:
        queue._after_fork()
        queue.put("child")

`_after_fork()` resets the thread so that the next invocation of `put()`
will create a new thread to send messages.

The second solution is to use `multiprocessing.queues.SimpleQueue` instead.
`SimpleQueue` is simply a wrapper of a pipe and a lock, without any thread
created. Hence it is safe to be used with `fork()`. The advantage is that the
invocation of `SimpleQueue.put()` will not return until the message has been
sent, but it should not be a problem in my case.
