
What Is the Python Global Interpreter Lock (GIL)? https://realpython.com/python-gil/
- > The Python ***`Global Interpreter Lock`*** or [GIL](https://wiki.python.org/moin/GlobalInterpreterLock), in simple words, is a mutex (or a lock) that ***allows only one [thread](https://realpython.com/intro-to-python-threading/) to hold the control of the Python interpreter***.
- > This means that ***only one thread can be in a state of execution at any point in time***. The impact of the GIL isn’t visible to developers who execute single-threaded programs, but it can be a ***performance bottleneck in <ins>CPU-bound and multi-threaded</ins> code***.
- > Since ***the GIL allows only one thread to execute at a time even in a multi-threaded architecture with more than one CPU core***, the GIL has gained a reputation as an “infamous” feature of Python.
- > In this article you’ll learn how the GIL affects the performance of your Python programs, and how you can mitigate the impact it might have on your code.
- > **What Problem Did the GIL Solve for Python?**
  * > Python uses reference counting for [memory management](https://realpython.com/python-memory-management/). It means that objects created in Python have a reference count variable that keeps track of the number of references that point to the object. When this count reaches zero, the memory occupied by the object is released.
    >
    > Let’s take a look at a brief code example to demonstrate how reference counting works:
    ```py
    >>> import sys
    >>> a = []
    >>> b = a
    >>> sys.getrefcount(a)
    3
    ```
    > In the above example, the reference count for the empty list object `[]` was `3`. The list object was referenced by `a`, `b` and ***the argument passed to `sys.getrefcount()`***.
  * > Back to the GIL:
    + > The problem was that ***this `reference count variable` needed protection from `race conditions`*** where two threads increase or decrease its value simultaneously. If this happens, it can cause either leaked memory that is never released or, even worse, incorrectly release the memory while a reference to that object still exists. This can cause crashes or other “weird” bugs in your Python programs.
    + > This reference count variable can be kept safe by adding locks to all data structures that are shared across threads so that they are not modified inconsistently.
    + > But ***adding a lock to each object or groups of objects means multiple locks will exist which can cause another problem — `Deadlocks`*** (deadlocks can only happen if there is more than one lock). Another side effect would be decreased performance caused by the repeated acquisition and release of locks.
      >> 【[:star:][`*`]】 //notes：所以 Python 中引入GIL的根本原因是 Python 的变量是 [`引用计数`](https://docs.python.org/zh-cn/3.7/c-api/refcounting.html) 方式的，如果没有GIL，就得有 Lock 之类的机制，否则会有`竞态条件(race condition)`问题。但是引入锁机制可能影响更大：一是可能引起死锁；二是锁的获取和释放带来的性能下降（可能更厉害）。
      >>> 此外，下文中还提到 `C extensions required a thread-safe memory management`，也就是 GIL 也是为了解决***一些C语言扩展需要线程安全的内存管理***这一需求。
  * > The GIL, although ***used by interpreters for other languages like `Ruby`***, is not the only solution to this problem. ***Some languages avoid the requirement of a GIL for thread-safe memory management by using approaches other than `reference counting`, such as `garbage collection`***.
  * > On the other hand, this means that those languages often have to compensate for the loss of single threaded performance benefits of a GIL by adding other performance boosting features like `JIT compilers`.
- > **Why Was the GIL Chosen as the Solution?**
  * > Python has been around ***since the days when operating systems did not have a concept of threads***. Python was designed to be easy-to-use in order to make development quicker and more and more developers started using it.
  * > A lot of extensions were being written for the existing C libraries whose features were needed in Python. To prevent inconsistent changes, these C extensions required ***a thread-safe memory management*** which the GIL provided.
  * > The GIL is simple to implement and was easily added to Python. It provides a performance increase to single-threaded programs as ***only one lock needs to be managed***.
  * > C libraries that were not thread-safe became easier to integrate. And these C extensions became one of the reasons why Python was readily adopted by different communities.
- > **The Impact on Multi-Threaded Python Programs**
  * > ***`CPU-bound programs`*** are those that are pushing the CPU to its limit. This includes programs that do mathematical computations like matrix multiplications, searching, image processing, etc.
  * > ***`I/O-bound programs`*** are the ones that spend time waiting for Input/Output which can come from a user, file, database, network, etc. I/O-bound programs sometimes have to wait for a significant amount of time till they get what they need from the source due to the fact that the source may need to do its own processing before the input/output is ready, for example, a user thinking about what to enter into an input prompt or a database query running in its own process.
  * > Let’s have a look at a simple CPU-bound program that performs a countdown:
    ```py
    # single_threaded.py
    import time
    from threading import Thread
    COUNT = 50000000
    
    def countdown(n):
        while n>0:
            n -= 1
    
    start = time.time()
    countdown(COUNT)
    end = time.time()
     
    print('Time taken in seconds -', end - start)
    ```
    > Running this code on my system with 4 cores gave the following output:
    ```sh
    $ python single_threaded.py
    Time taken in seconds - 6.20024037361145
    ```
  * > Now I modified the code a bit to do to the same countdown using two threads in parallel:
    ```py
    # multi_threaded.py
    import time
    from threading import Thread
    COUNT = 50000000
    
    def countdown(n):
        while n>0:
            n -= 1
    
    t1 = Thread(target=countdown, args=(COUNT//2,))
    t2 = Thread(target=countdown, args=(COUNT//2,))
    
    start = time.time()
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    end = time.time()
    
    print('Time taken in seconds -', end - start)
    ```
    > And when I ran it again:
    ```sh
    $ python multi_threaded.py
    Time taken in seconds - 6.924342632293701
    ```
  * > As you can see, both versions take almost same amount of time to finish. ***In the multi-threaded version the GIL prevented the <ins>CPU-bound</ins> threads from executing in parellel***.
  * > The ***GIL does not have much impact on the performance of <ins>I/O-bound</ins> multi-threaded programs*** as the lock is shared between threads while they are waiting for I/O.
  * > But a program whose threads are entirely CPU-bound, e.g., a program that processes an image in parts using threads, ***would not only become single threaded due to the lock but will also see an increase in execution time***, as seen in the above example, in comparison to a scenario where it was written to be entirely single-threaded.
    > 
    > ***This increase is the result of acquire and release overheads added by the lock***.
    >> 【[:star:][`*`]】 //notes：总结一下这一部分，主要说了三点： <br> 1. 在GIL的限制下，计算密集型的多线程任务***基本退化成了单线程***； <br> 2.（尽管）在GIL的限制下，I/O密集型的多线程任务***不太受影响***，因为一旦正在执行的线程停下来等I/O（它是需要等一段时间的，不可能释放GIL后又立即去获得），其他线程能有效的获得GIL并继续执行，从而不浪费这些时间（也就是增加了一定的“并行性”）； <br> 3. 计算密集型的多线程任务不但被GIL限制成了类似单线程执行，其实比完全的单线程版本性能更差（从上面的运行数据也可以看出来），因为还有***运行1000字节码（Py2）或运行15毫秒（Py3）后放GIL和抢GIL的开销***（详情参见《[深入理解Python中的GIL（全局解释器锁）。 - 蜗牛学苑的文章 - 知乎](https://zhuanlan.zhihu.com/p/75780308)》的笔记）。
- > **Why Hasn’t the GIL Been Removed Yet?**
  * > The GIL can obviously be removed and this has been done multiple times in the past by the developers and researchers ***but all those attempts broke the existing C extensions*** which depend heavily on the solution that the GIL provides.
    >> //notes：如果只是（从`CPython`中）移除 GIL，那么现有的依赖 GIL 的那些 C 语言扩展就不能用了。
  * > Of course, there are other solutions to the problem that the GIL solves but some of them ***decrease the performance of <ins>single-threaded</ins> and <ins>multi-threaded I/O-bound</ins> programs*** and some of them are just too difficult. After all, you wouldn’t want your existing Python programs to run slower after a new version comes out, right?
    >> //notes：如果使用其他方法来替换 GIL，就会有性能问题（影响 ***单线程程序*** 或者 ***I/O密集型多线程程序*** 的性能）或者其他问题。
  * > The creator and `BDFL` of Python, Guido van Rossum, gave an answer to the community in September 2007 in his article [“It isn’t Easy to remove the GIL”](https://www.artima.com/weblogs/viewpost.jsp?thread=214235): `“I’d welcome a set of patches into Py3k only if the performance for a single-threaded program (and for a multi-threaded but I/O-bound program) does not decrease”`
  * > And this condition hasn’t been fulfilled by any of the attempts made since.
- > **Why Wasn’t It Removed in Python 3?**
  * > Removing the GIL would have made Python 3 slower in comparison to Python 2 ***in single-threaded performance*** and you can imagine what that would have resulted in. You can’t argue with the single-threaded performance benefits of the GIL. So the result is that Python 3 still has the GIL.
  * > But Python 3 did bring a major improvement to the existing GIL—
  * > We discussed the impact of GIL on “only CPU-bound” and “only I/O-bound” multi-threaded programs but ***what about the programs where some threads are I/O-bound and some are CPU-bound***?
    > 
    > In such programs, Python’s GIL ***was known to starve the I/O-bound threads by not giving them a chance to acquire the GIL from CPU-bound threads***.
    > 
    > This was because of a mechanism built into Python that forced threads to release the GIL **after a fixed interval** of continuous use and if nobody else acquired the GIL, the same thread could continue its use.
    >> 【[:star:][`*`]】 //notes：这里的所谓 ***“饿死”I/O密集型线程*** 的原因是这样的：线程一旦经过了一个执行周期（1000字节码也好，15毫秒也好）就会释放其占有的GIL，然后（除非它是I/O密集型，正在等I/O，无法参与抢锁）会立即参与到释放掉的GIL的争夺中。在 Python 3.2 之前，一般来说释放掉GIL的那个线程大概率还会抢到GIL。
    >>> 那么，对于某个I/O密集型线程：如果它一开始有GIL，那它会因为执行完当前周期或者因为等I/O而释放掉GIL，然后就被某个计算密集型线程抢走，并以很高的概率被那个线程一直占着：执行周期完了释放，释放了再抢回，如此往复；如果它一开始就没有GIL，只要有计算密集型线程在，轮不着它。。。
    ```py
    >>> import sys
    >>> # The interval is set to 100 instructions:
    >>> sys.getcheckinterval()
    100
    ```
  * > The problem in this mechanism was that ***most of the time the CPU-bound thread would reacquire the GIL itself before other threads could acquire it***. This was researched by David Beazley and visualizations can be found [here](http://www.dabeaz.com/blog/2010/01/python-gil-visualized.html).
    > 
    > This problem was fixed in Python 3.2 in 2009 by Antoine Pitrou who [added a mechanism](https://mail.python.org/pipermail/python-dev/2009-October/093321.html) of looking at the number of GIL acquisition requests by other threads that got dropped and not allowing the current thread to reacquire GIL before other threads got a chance to run.
- > **How to Deal With Python’s GIL**
  * > If the GIL is causing you problems, here a few approaches you can try:
  * > **Multi-processing vs multi-threading**: The most popular way is to use a multi-processing approach ***where you use multiple processes instead of threads***. ***<ins>Each Python process gets its own Python interpreter and memory space so the GIL won’t be a problem</ins>***. Python has a [multiprocessing](https://docs.python.org/2/library/multiprocessing.html) module which lets us create processes easily like this:
    >> 【[:star:][`*`]】 //notes：除了 ***GIL问题只在 CPython 解释器中存在*** 这一关键点外，这也是一个关于GIL的关键点：***GIL是每个进程都有一个的***，所谓***抢GIL都是指的在同一个进程下的线程之间***，所以在多进程的情况下倒不用太担心GIL会造成“假并行”。
    ```py
    from multiprocessing import Pool
    import time
    
    COUNT = 50000000
    def countdown(n):
        while n>0:
            n -= 1
    
    if __name__ == '__main__':
        pool = Pool(processes=2)
        start = time.time()
        r1 = pool.apply_async(countdown, [COUNT//2])
        r2 = pool.apply_async(countdown, [COUNT//2])
        pool.close()
        pool.join()
        end = time.time()
        print('Time taken in seconds -', end - start)
    ```
    > Running this on my system gave this output:
    ```sh
    $ python multiprocess.py
    Time taken in seconds - 4.060242414474487
    ```
    > A decent performance increase compared to the multi-threaded version, right?
    > 
    > The time ***didn’t drop to half of what we saw above because process management has its own overheads***. ***Multiple processes are heavier than multiple threads***, so, keep in mind that this could become a scaling bottleneck.
    >> 【[:star:][`*`]】 //notes：这个很好理解，之所以没有完全减少一半的时间是因为进程切换也有开销，并且比线程切换开销更大。所以多进程也不能无限的多下去，也是有扩展性的瓶颈的。
  * > **Alternative Python interpreters**: Python has multiple interpreter implementations. CPython, Jython, IronPython and [PyPy](https://realpython.com/pypy-faster-python/), written in C, Java, C# and Python respectively, are the most popular ones. ***<ins>GIL exists only in the original Python implementation that is `CPython`</ins>***. If your program, with its libraries, is available for one of the other implementations then you can try them out as well.
  * > **Just wait it out**: While many Python users take advantage of the single-threaded performance benefits of GIL. The multi-threading programmers don’t have to fret as some of the brightest minds in the Python community are working to remove the GIL from CPython. One such attempt is known as the Gilectomy.
    > 
    > The Python GIL is often regarded as a mysterious and difficult topic. But keep in mind that as a Pythonista ***you’re usually only affected by it if you are <ins>writing C extensions</ins> or if you’re using <ins>CPU-bound multi-threading</ins> in your programs***.
    > 
    > In that case, this article should give you everything you need to understand what the GIL is and how to deal with it in your own projects. And if you want to understand the low-level inner workings of GIL, I’d recommend you watch the [Understanding the Python GIL](https://www.youtube.com/watch?v=Obt-vMVdM8s) talk by David Beazley.

## 个人实战该文章

***普通版本（无任何并发）***：
```py
import time
COUNT = 300000000

def countdown(n):
    print("inner function start")
    t1 = time.time()
    while n>0:
        n -= 1
    t2 = time.time()
    print("inner function end")
    print("time in inner function is: ", t2 - t1)

start = time.time()
countdown(COUNT)
end = time.time()
print('Time taken in seconds -', end - start)
```
```console
# 可以看到，单进程单线程版本的程序跑完，约需要 12 秒。
inner function start
inner function end
time in inner function is:  12.570079565048218
Time taken in seconds - 12.570316791534424
```

***多线程版本***：
```py
import time
from threading import Thread
COUNT = 300000000

def countdown(n):
    print("inner function start")
    t1 = time.time()
    while n>0:
        n -= 1
    t2 = time.time()
    print("inner function end")
    print("time in inner function is: ", t2 - t1)

t1 = Thread(target=countdown, args=(COUNT//2,))
t2 = Thread(target=countdown, args=(COUNT//2,))

start = time.time()
t1.start()
t2.start()
t1.join()
t2.join()
end = time.time()
print('Time taken in seconds -', end - start)
```
```console
# 跑两遍（防止结果不稳定，这里只贴了最后一次的，两次时间差不多），发现提升很小（其实真实情况是还下降了。。。因为有切换线程的开销）。
inner function start
inner function start
inner function end
time in inner function is:  13.094752311706543
inner function end
time in inner function is:  13.15324592590332
Time taken in seconds - 13.164145469665527
```

***多进程版本***：
```py
from multiprocessing import Pool
import time
COUNT = 300000000

def countdown(n):
    print("inner function start")
    t1 = time.time()
    while n>0:
        n -= 1
    t2 = time.time()
    print("inner function end")
    print("time in inner function is: ", t2 - t1)

if __name__ == '__main__':
    pool = Pool(processes=2)
    start = time.time()
    r1 = pool.apply_async(countdown, [COUNT//2])
    r2 = pool.apply_async(countdown, [COUNT//2])
    pool.close()
    pool.join()
    end = time.time()
    print('Time taken in seconds -', end - start)
```
```console
# 同样跑两遍（防止结果不稳定，这里只贴了最后一次的，两次时间差不多），可以看到明显的提升。
inner function start
inner function start
inner function end
time in inner function is:  6.18486475944519
inner function end
time in inner function is:  6.192872524261475
Time taken in seconds - 6.215625047683716
```
